---
title: "Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure | Microsoft Docs"
description: "Découvrez comment installer et configurer Terraform pour créer des ressources Azure."
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
ms.translationtype: HT
ms.sourcegitcommit: 8727e15448a74b68277c5bdd82c573e817254f80
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---

# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installer et configurer Terraform pour approvisionner les machines virtuelles et d’autres infrastructures dans Azure 
Cet article décrit les étapes nécessaires à l’installation et la configuration de Terraform afin d’approvisionner des ressources telles que des machines virtuelles dans Azure. Vous allez apprendre à créer et utiliser des informations d’identification Azure pour permettre à Terraform d’approvisionner des ressources cloud de manière sécurisée.

HashiCorp Terraform fournit un moyen simple de définir et déployer l’infrastructure cloud à l’aide d’un langage de création de modèles personnalisé appelé HCL (HashiCorp Configuration Language). Ce langage personnalisé est [facile à écrire et facile à comprendre](terraform-create-complete-vm.md). En outre, à l’aide de la commande `terraform plan`, vous pouvez visualiser les modifications apportées à votre infrastructure avant de les valider. Suivez ces étapes pour commencer à utiliser Terraform avec Azure.

## <a name="install-terraform"></a>Installer Terraform
Pour installer Terraform, [téléchargez](https://www.terraform.io/downloads.html) le package correspondant à votre système d’exploitation dans un répertoire d’installation distinct. Le téléchargement contient un seul fichier exécutable, pour lequel vous devez également définir un chemin d’accès global. Pour obtenir des instructions sur la définition du chemin d’accès sous Linux et Mac, consultez [cette page web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Pour obtenir des instructions sur la définition du chemin d’accès sous Windows, consultez [cette page web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). Pour vérifier votre installation, exécutez la commande `terraform`. Une liste des options Terraform disponibles devrait s’afficher en sortie.

Ensuite, vous devez autoriser Terraform à accéder à votre abonnement Azure pour effectuer l’approvisionnement de l’infrastructure.

## <a name="set-up-terraform-access-to-azure"></a>Configurer l’accès Terraform à Azure
Pour permettre à Terraform d’approvisionner des ressources dans Azure, vous devez créer deux entités dans Azure Active Directory (Azure AD) : une application Azure AD et un principal de service Azure AD. Utilisez ensuite les identificateurs de ces entités dans vos scripts Terraform. Le principal de service est une instance locale d’application Azure AD globale. Un principal de service permet un contrôle granulaire de l’accès local aux ressources globales.

Il existe plusieurs façons de créer une application Azure AD et un principal de service Azure AD. À l’heure actuelle, le moyen le plus simple et le plus rapide consiste à utiliser Azure CLI 2.0, que [vous pouvez télécharger et installer sur Windows, Linux ou un Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Vous pouvez également utiliser PowerShell ou Azure CLI 1.0 pour créer l’infrastructure de sécurité nécessaire. Les instructions ci-dessous vous montrent comment configurer Terraform pour Azure selon toutes ces approches.

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Utiliser Azure CLI 2.0 (pour les utilisateurs Windows, Linux ou Mac) 
Après avoir téléchargé et installé [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), connectez-vous afin de gérer votre abonnement Azure en émettant la commande suivante :

```
az login
```

>[!NOTE]
>Si vous utilisez les clouds Azure Government, Azure - Allemagne ou Azure - Chine, vous devez d’abord configurer Azure CLI pour qu’elle fonctionne avec ce cloud. Pour ce faire, exécutez la commande suivante :

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

Les valeurs appId, password, sp_name et tenant sont renvoyées. Prenez note des valeurs appId et password.

Pour confirmer vos informations d’identification (principal du service), ouvrez un nouveau shell et exécutez les commandes suivantes. Remplacez les valeurs renvoyées pour sp_name, password et tenant :

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>Utiliser PowerShell (pour les utilisateurs Windows) 
Pour utiliser un ordinateur Windows pour écrire et exécuter vos scripts Terraform et effectuer les tâches de configuration dans PowerShell, configurez votre machine avec les outils PowerShell appropriés. 

1. Installez les outils PowerShell en suivant les étapes décrites dans l’article [Install and configure Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps) (Installer et configurer Azure PowerShell). 

2. Téléchargez et exécutez le script [azure-setup.ps1 script](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) à partir de la console PowerShell.

3. Pour exécuter le script azure-setup.ps1, téléchargez-le et exécutez la commande `./azure-setup.ps1 setup` à partir de la console PowerShell. Connectez-vous ensuite à votre abonnement Azure avec des privilèges Administrateur.

4. Indiquez un nom d’application (chaîne arbitraire, requis) lorsque vous y êtes invité. À l’invite, vous pouvez également indiquer un mot de passe sécurisé. Si vous ne fournissez pas de mot de passe, le mot de passe sécurisé est automatiquement généré à l’aide des bibliothèques de sécurité .NET.

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Utiliser Azure CLI 1.0 (pour les utilisateurs Linux ou Mac)
Pour commencer à utiliser Terraform sur les machines Linux ou Mac avec Azure CLI 1.0, installez les bibliothèques appropriées sur votre machine.  

1. Installez les outils d’interface de ligne de commande interplateforme Azure en suivant les étapes décrites dans l’article [Installer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

2. Téléchargez et installez un processeur JSON en suivant les instructions de la page [Download jq](https://stedolan.github.io/jq/download/) (Télécharger jq).

3. Téléchargez et exécutez le script bash [azure-setup.sh script](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) à partir de la console.

4. Pour exécuter le script azure-setup.sh, téléchargez-le et exécutez la commande `./azure-setup setup` à partir de la console. Connectez-vous ensuite à votre abonnement Azure avec des privilèges Administrateur.
 
5. Indiquez un nom d’application (chaîne arbitraire, requis) lorsque vous y êtes invité. À l’invite, vous pouvez également indiquer un mot de passe sécurisé. Si vous ne fournissez pas de mot de passe, le mot de passe sécurisé est automatiquement généré à l’aide des bibliothèques de sécurité .NET.

Tous les scripts précédents créent une application Azure AD et le principal du service. Le principal du service obtient un accès contributeur ou propriétaire à l’abonnement. En raison du niveau élevé d’accès octroyé, vous devez toujours protéger les informations de sécurité générées par ces scripts. Prenez note des quatre éléments d’informations de sécurité fournis par ces scripts : appId, password, subscription_id et tenant_id.

## <a name="set-environment-variables"></a>Définition des variables d'environnement
Après avoir créé et configuré le principal de service Azure AD, vous devez communiquer à Terraform l’ID locataire, l’ID d’abonnement, l’ID client et la clé secrète client à utiliser. Vous pouvez le faire en incorporant ces valeurs dans vos scripts Terraform (comme décrit dans l’article [Créer une infrastructure de base dans Azure à l’aide de Terraform](terraform-create-complete-vm.md). Vous pouvez également définir les variables d’environnement suivantes (et ainsi éviter d’archiver ou de partager accidentellement vos informations d’identification) :

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Vous pouvez utiliser cet exemple de script shell pour définir ces variables :

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

En outre, si vous utilisez Terraform avec Azure - Chine, Azure Government ou Azure - Allemagne, vous devez définir la variable ENVIRONMENT de façon appropriée.

## <a name="next-steps"></a>Étapes suivantes
Vous avez installé Terraform et configuré les informations d’identification Azure pour pouvoir démarrer le déploiement d’infrastructure dans votre abonnement Azure. À présent, découvrez comment [créer une infrastructure avec Terraform](terraform-create-complete-vm.md).

