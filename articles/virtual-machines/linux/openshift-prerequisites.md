---
title: "Conditions préalables à l’utilisation d’OpenShift | Microsoft Docs"
description: "Conditions préalables requises au déploiement d’OpenShift dans Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 0c90b8a6d17fa293b6708d942afd35e1333623cb
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="common-prerequisites-for-openshift-in-azure"></a>Éléments prérequis communs pour OpenShift dans Azure

Lorsque vous déployez OpenShift dans Azure, il existe quelques conditions préalables courantes qu’il s’agisse du déploiement d’OpenShift Origin ou d’OpenShift Container Platform.

L’installation d’OpenShift est effectuée via des playbooks Ansible. Ansible utilise SSH pour se connecter à tous les hôtes qui feront partie du cluster afin de terminer les étapes d’installation.
Une fois la connexion SSH lancée vers les hôtes distants, il n’existe aucun moyen d’entrer une phrase secrète. Pour cette raison, aucune phrase secrète ne peut être associée à la clé privée sans provoquer l’échec du déploiement.
Étant donné que toutes les machines virtuelles sont déployées via des modèles Resource Manager, la même clé publique est utilisée pour accéder à l’ensemble des machines virtuelles. Nous devons injecter la clé privée correspondant dans la machine virtuelle qui exécute également tous les playbooks.
Pour ce faire en toute sécurité, nous utilisons un Azure Key Vault pour transmettre la clé privée à la machine virtuelle.

Si un stockage persistant est nécessaire pour les conteneurs, des volumes persistants sont nécessaires. Ces volumes persistants doivent être sauvegardés par une forme de stockage persistant. OpenShift prend en charge les disques Azure (VHD) pour cette fonctionnalité, mais Azure doit d’abord être configuré en tant que fournisseur Cloud. Dans ce modèle, OpenShift :

- Crée un objet de disque dur virtuel dans un compte de stockage Azure
- Monte le disque dur virtuel sur une machine virtuelle et formate le volume
- Monte le volume sur le pod

Pour que cela fonctionne, OpenShift a besoin d’autorisations pour effectuer les tâches précédentes dans Azure. Pour ce faire, un principal de service est nécessaire. Le principal de service est un compte de sécurité dans Azure Active Directory qui dispose d’autorisations sur les ressources.
Le principal de service a besoin d’accéder aux comptes de stockage et aux machines virtuelles qui composent le cluster. Si toutes les ressources de cluster OpenShift sont déployées sur un seul groupe de ressources, des autorisations pour ce groupe de ressources peuvent être affectées au principal de service.

Ce guide décrit comment créer les artefacts associés aux conditions préalables.

> [!div class="checklist"]
> * Créer un Key Vault pour gérer les clés SSH pour le cluster OpenShift.
> * Créer un principal de service pour une utilisation par le fournisseur de Cloud Azure.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure 
Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran ou cliquez sur **Essayer** pour utiliser Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Il est recommandé d’utiliser un groupe de ressources dédié pour héberger le coffre de clés, séparé du groupe de ressources sur lequel les ressources de cluster OpenShift seront déployées. 

L’exemple suivant crée un groupe de ressources nommé *keyvaultrg* à l’emplacement *eastus*.

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Création d’un coffre de clés
Créez un Key Vault pour stocker les clés SSH pour le cluster avec la commande [az keyvault create](/cli/azure/keyvault#create). Le nom du Key Vault doit être globalement unique.

L’exemple suivant crée un coffre de clés nommé *keyvault* à l’emplacement *keyvaultrg*.

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Création d’une clé SSH 
Une clé SSH est nécessaire pour sécuriser l’accès au cluster OpenShift Origin. Créez une paire de clés SSH à l’aide de la commande `ssh-keygen` (sur Linux ou Mac).
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> La paire de clés SSH créée ne doit pas présenter de phrase secrète.

Pour plus d’informations sur les clés SSH sur Windows, consultez [Utilisation de clés SSH avec Windows sur Azure](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Stocker la clé privée SSH dans Key Vault
Le déploiement OpenShift utilise la clé SSH que vous avez créée pour sécuriser l’accès au maître OpenShift. Pour permettre au déploiement de récupérer la clé SSH en toute sécurité, stockez la clé dans Key Vault à l’aide de la commande suivante :

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Créer un principal du service 
OpenShift communique avec Azure à l’aide d’un nom d’utilisateur et d’un mot de passe ou d’un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme OpenShift. Vous contrôlez et définissez les opérations que le principal du service est autorisé à effectuer dans Azure. Pour renforcer la sécurité par rapport à la simple saisie d’un nom d’utilisateur et d’un mot de passe, cet exemple crée un principal de service de base.

Créez un principal de service avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) et affichez les informations d’identification requises par OpenShift.

L’exemple suivant crée un service principal et lui assigne des autorisations de collaborateur à un groupe de ressources nommé myResourceGroup. Si vous utilisez Windows, exécutez ```az group show --name myResourceGroup --query id``` séparément et utilisez la sortie pour alimenter l’option --scopes.

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {Strong Password} \
          --scopes $(az group show --name myResourceGroup --query id)
```

Prenez note de la propriété appId renvoyée par la commande.
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",            
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Ne créez pas un mot de passe non sécurisé.  Suivez les conseils en matière de [Stratégies et restrictions de mot de passe dans Azure Active Directory](/azure/active-directory/active-directory-passwords-policy).

Pour plus d’informations sur les principaux de service, consultez [Créer un principal du service avec Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="next-steps"></a>Étapes suivantes

Cet article a abordé les thèmes suivants :
> [!div class="checklist"]
> * Créer un Key Vault pour gérer les clés SSH pour le cluster OpenShift.
> * Créer un principal de service pour une utilisation par le fournisseur de Cloud Azure.

Déployez maintenant un cluster OpenShift.

- [Déployer OpenShift Origin](./openshift-origin.md)
- [Déployer OpenShift Container Platform](./openshift-container-platform.md)

