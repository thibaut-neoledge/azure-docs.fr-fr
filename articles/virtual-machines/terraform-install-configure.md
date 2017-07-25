---
title: "Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure | Microsoft Docs"
description: "Découvrez comment installer et configurer Terraform pour créer des ressources Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: aa82dda778af927db0a204eb88bae445c086e309
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure 
Cet article décrit les étapes nécessaires à l’installation et la configuration de Terraform afin d’approvisionner des ressources telles que des machines virtuelles dans Azure. Vous allez apprendre à créer et utiliser des informations d’identification Azure pour permettre à Terraform d’approvisionner des ressources cloud de manière sécurisée.

HashiCorp Terraform fournit un moyen simple de définir et déployer l’infrastructure cloud à l’aide d’un langage de création de modèles personnalisé appelé HCL. Ce langage personnalisé est à la fois [facile à écrire et facile à comprendre](terraform-create-complete-vm.md). En outre, grâce à la commande « terraform plan », Terraform vous permet de visualiser les modifications apportées à votre infrastructure avant de les valider. Suivez les étapes ci-dessous pour commencer à utiliser Terraform avec Azure.

## <a name="installing-terraform"></a>Installation de Terraform
Pour installer Terraform, [téléchargez](https://www.terraform.io/downloads.html) le package correspondant à votre système d’exploitation dans un répertoire d’installation distinct. Le téléchargement contient un seul fichier exécutable, pour lequel vous devez également définir un PATH global. Vous trouverez des instructions sur la définition de PATH pour Mac et Linux sur [cette page](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux), tandis que [cette page](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) contient des instructions de définition de PATH sur Windows. Exécutez la commande « terraform » pour vérifier votre installation : la liste des options Terraform disponibles en tant que sortie doit s’afficher.

Ensuite, vous devez autoriser Terraform à accéder à votre abonnement Azure pour effectuer l’approvisionnement de l’infrastructure.

## <a name="setting-up-terraform-access-to-azure"></a>Configuration de l’accès Terraform à Azure
Pour permettre à Terraform d’approvisionner des ressources dans Azure, vous devez créer deux entités dans Azure Active Directory (AAD) : une application AAD et un principal de service AAD. Utilisez ensuite les identificateurs de ces entités dans vos scripts Terraform. Le principal de service est une instance locale d’application AAD globale. Un principal de service permet un contrôle granulaire de l’accès local aux ressources globales.

Il existe plusieurs façons de créer une application AAD et un principal de service AAD. À l’heure actuelle, le moyen le plus simple et le plus rapide consiste à utiliser Azure CLI 2.0, que [vous pouvez télécharger et installer sur Windows/Linux/Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Vous pouvez également utiliser PowerShell ou Azure CLI 1.0 pour créer l’infrastructure de sécurité nécessaire. Les instructions ci-dessous vous montrent comment configurer Terraform pour Azure selon toutes ces approches.

### <a name="windowslinuxmac-users-using-azure-cli-20"></a>Utilisateurs Windows/Linux/Mac se servant d’Azure CLI 2.0
Après avoir téléchargé et installé [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), connectez-vous afin de gérer votre abonnement Azure en émettant la commande suivante

```
az login
```

>[!NOTE]
>Si vous utilisez les clouds Azure Government, Azure - Allemagne ou Azure - Chine, vous devez d’abord configurer Azure CLI pour qu’elle fonctionne avec ce cloud. Pour ce faire, exécutez :

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Si vous avez plusieurs abonnements Azure, leurs détails sont retournés par la commande `az login`. Définissez la variable d’environnement `SUBSCRIPTION_ID` pour qu’elle contienne la valeur du champ `id` retourné à partir de l’abonnement que vous souhaitez utiliser. 

Sélectionnez l’abonnement à utiliser pour cette session.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

Interrogez le compte pour obtenir les valeurs ID d’abonnement et ID locataire.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Créez ensuite des informations d’identification distinctes pour Terraform.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Cette opération génère les valeurs client_id, client_secret (mot de passe), sp_name et tenant. Prenez note des valeurs **client_id** et **client_secret**.

Vous pouvez vérifier vos informations d’identification (principal de service) en ouvrant un nouvel interpréteur de commandes et en exécutant les commandes suivantes ; vous devez alors remplacer les valeurs retournées pour **sp_name**, **client_secret** et **tenant** :

```
az login --service-principal -u SP_NAME -p CLIENT_SECRET --tenant TENANT
az vm list-sizes --location westus
```

### <a name="windows-users-using-powershell"></a>Utilisateurs Windows se servant de PowerShell
Si vous utilisez une machine Windows pour écrire et exécuter vos scripts Terraform et préférez utiliser PowerShell pour les tâches de configuration, vous devez configurer votre machine avec les outils PowerShell appropriés. Pour ce faire, (1) [installez les outils Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) et (2) téléchargez et exécutez le [script azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) à partir de la console PowerShell. Pour exécuter le script azure-setup.ps1, téléchargez-le, exécutez la commande ./azure-setup.ps1 setup à partir de la console PowerShell, et connectez-vous à votre abonnement Azure avec des privilèges administratifs. Puis, fournissez un nom d’application (chaîne arbitraire, obligatoire) lorsque vous y êtes invité et (éventuellement) fournissez un mot de passe sécurisé. Si vous ne fournissez pas de mot de passe, le mot de passe sécurisé est automatiquement généré à l’aide des bibliothèques de sécurité .Net.

### <a name="linuxmac-users-using-azure-cli-10"></a>Utilisateurs Linux/Mac se servant d’Azure CLI 1.0
Pour commencer à utiliser Terraform sur les machines Linux ou Mac avec Azure CLI 1.0, vous devez vous assurer que des bibliothèques appropriées sont installées sur votre machine. Pour ce faire, (1) [installez les outils Azure xPlat CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), (2) [téléchargez et installez le processeur JSON jq](https://stedolan.github.io/jq/download/) et (3) téléchargez et exécutez le script bash [azure-setup.sh](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) à partir de la console. Pour exécuter le script azure-setup.sh, téléchargez-le, exécutez la commande ./azure-setup setup à partir de la console, puis connectez-vous à votre abonnement Azure avec des privilèges administratifs. Fournissez ensuite un nom d’application (chaîne arbitraire, obligatoire) lorsque vous y êtes invité et (éventuellement) indiquez un mot de passe sécurisé lorsque vous y êtes invité. Si vous ne fournissez pas de mot de passe, le mot de passe sécurisé est automatiquement généré à l’aide des bibliothèques de sécurité .Net.

Tous les scripts ci-dessus créent une application AAD et un principal de service, et ils octroient au principal de service un accès de type Collaborateur ou Propriétaire pour l’abonnement. En raison du niveau élevé d’accès octroyé, vous devez toujours protéger les informations de sécurité générées par ces scripts. Prenez note des quatre éléments d’informations de sécurité fournis par ces scripts : client_id, client_secret, subscription_id et tenant_id. 

## <a name="setting-environment-variables"></a>Définition des variables d'environnement
Une fois le principal de service AAD créé et configuré, vous devez communiquer à Terraform l’ID locataire, l’ID d’abonnement, l’ID client et la clé secrète client à utiliser. Vous pouvez le faire en incorporant ces valeurs dans vos scripts Terraform (comme décrit dans la [section suivante](terraform-create-complete-vm.md)). Vous pouvez également définir les variables d’environnement suivantes (et ainsi éviter d’archiver/partager accidentellement vos informations d’identification) :

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Voici un exemple de script shell que vous pouvez utiliser pour définir ces variables :

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_client_id
export ARM_CLIENT_SECRET=your_client_secret
export ARM_TENANT_ID=your_tenant_id
```

En outre, si vous utilisez Terraform avec Azure Government, Azure - Allemagne ou Azure - Chine, vous devez définir la variable ENVIRONMENT de façon appropriée.

## <a name="next-steps"></a>Étapes suivantes
Vous avez installé Terraform et configuré les informations d’identification Azure pour commencer le déploiement d’infrastructure dans votre abonnement Azure. À présent, découvrez comment [créer une infrastructure avec Terraform](terraform-create-complete-vm.md).
