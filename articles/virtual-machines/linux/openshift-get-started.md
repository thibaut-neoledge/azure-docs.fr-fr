---
title: "Déployer OpenShift Origin sur Azure | Microsoft Docs"
description: "Découvrez comment déployer OpenShift Origin sur des machines virtuelles Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.contentlocale: fr-fr
ms.lasthandoff: 06/21/2017

---

# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>Déployer OpenShift Origin sur des machines virtuelles Azure 

[OpenShift Origin](https://www.openshift.org/) est une plateforme de conteneurs open source basée sur [Kubernetes](https://kubernetes.io/). Elle simplifie le processus de déploiement, de mise à l’échelle et d’exploitation d’applications mutualisées. 

Ce guide décrit comment déployer OpenShift Origin sur des machines virtuelles Azure à l’aide de l’interface de ligne de commande (CLI) Azure et de modèles Azure Resource Manager. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un Key Vault pour gérer les clés SSH pour le cluster OpenShift.
> * Déployer un cluster OpenShift sur les machines virtuelles Azure. 
> * Installer et configurer l’interface [CLI OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) pour gérer le cluster.
> * Personnaliser le déploiement OpenShift.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce démarrage rapide requiert la version 2.0.8 ou ultérieure de l’interface Azure CLI. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure 
Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran ou cliquez sur **Essayer** pour utiliser Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>Créer un coffre de clés
Créez un Key Vault pour stocker les clés SSH pour le cluster avec la commande [az keyvault create](/cli/azure/keyvault#create).  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Création d’une clé SSH 
Une clé SSH est nécessaire pour sécuriser l’accès au cluster OpenShift Origin. Créez une paire de clés SSH à l’aide de la commande `ssh-keygen`. 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> La paire de clés SSH créée ne doit pas présenter de phrase secrète.

Pour plus d’informations sur les clés SSH sur Windows, consultez [Utilisation de clés SSH avec Windows sur Azure](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Stocker la clé privée SSH dans Key Vault
Le déploiement OpenShift utilise la clé SSH que vous avez créée pour sécuriser l’accès au maître OpenShift. Pour permettre au déploiement de récupérer la clé SSH en toute sécurité, stockez la clé dans Key Vault à l’aide de la commande suivante.

# <a name="enabled-for-template-deployment"></a>Activé pour le déploiement de modèle
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Créer un principal du service 
OpenShift communique avec Azure à l’aide d’un nom d’utilisateur et d’un mot de passe ou d’un principal de service. Un principal de service Azure est une identité de sécurité que vous pouvez utiliser avec des applications, des services et des outils d’automatisation comme OpenShift. Vous contrôlez et définissez les opérations que le principal du service est autorisé à effectuer dans Azure. Pour renforcer la sécurité par rapport à la simple saisie d’un nom d’utilisateur et d’un mot de passe, cet exemple crée un principal de service de base.

Créez un principal de service avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) et affichez les informations d’identification requises par OpenShift :

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
Prenez note de la propriété appId renvoyée par la commande.
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Ne créez pas un mot de passe non sécurisé.  Suivez les conseils en matière de [Stratégies et restrictions de mot de passe dans Azure Active Directory](/azure/active-directory/active-directory-passwords-policy).

Pour plus d’informations sur les principaux de service, consultez [Créer un principal du service avec Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="deploy-the-openshift-origin-template"></a>Déployer le modèle OpenShift Origin
Vous allez maintenant déployer OpenShift Origin à l’aide d’un modèle Azure Resource Manager. 

> [!NOTE] 
> La commande suivante requiert la version 2.0.8 ou ultérieure de l’interface Azure CLI. Pour vérifier la version de l’interface Azure CLI, exécutez la commande `az --version`. Pour mettre à jour l’interface, consultez [Installer Azure CLI 2.0]( /cli/azure/install-azure-cli).

Utilisez la valeur de `appId` du principal de service créé précédemment pour le paramètre `aadClientId`.

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
Le déploiement peut prendre jusqu’à 20 minutes. L’URL de la console OpenShift et le nom DNS du maître OpenShift est affiché sur le terminal à l’issue du déploiement.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>Se connecter au cluster OpenShift
Une fois le déploiement terminé, connectez-vous à la console OpenShift dans un navigateur à l’aide de la valeur de `OpenShift Console Uri`. Vous avez également la possibilité de vous connecter au maître OpenShift à l’aide de la commande suivante :

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, le cluster OpenShift et toutes les ressources associées.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un Key Vault pour gérer les clés SSH pour le cluster OpenShift.
> * Déployer un cluster OpenShift sur les machines virtuelles Azure. 
> * Installer et configurer l’interface [CLI OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) pour gérer le cluster.

Maintenant que le cluster OpenShift Origin est déployé, vous pouvez suivre les didacticiels OpenShift pour découvrir comment déployer votre première application et utiliser les outils OpenShift. Pour commencer, consultez [Prise en main d’OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html). 

