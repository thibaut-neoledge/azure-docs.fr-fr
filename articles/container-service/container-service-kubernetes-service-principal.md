---
title: Principal du service de cluster Azure Kubernetes | Microsoft Docs
description: "Création et gestion d’un principal du service Azure Active Directory dans un cluster Azure Container Service avec Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 5af9b5fdaf228edd54900855d0eac5d90ea3db38
ms.openlocfilehash: 0121896aa27677080d6b240fdafff3c7e19683d9


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>À propos de l’entité du principal du service Azure Active Directory pour un cluster Kubernetes dans Azure Container Service



Dans Azure Container Service, Kubernetes nécessite un [principal du service Azure Active Directory](../active-directory/active-directory-application-objects.md) en tant que compte de service pour interagir avec des API Azure. Le principal du service est nécessaire pour gérer dynamiquement des ressources telles que des itinéraires définis par l’utilisateur et Azure Load Balancer de couche 4.

Cet article explique les différentes options pour spécifier un principal du service pour votre cluster Kubernetes. Par exemple, si vous avez installé et configuré [Azure CLI 2.0 (version préliminaire)](https://docs.microsoft.com/cli/azure/install-az-cli2), vous pouvez exécuter la commande [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) pour créer le cluster Kubernetes et le principal du service en même temps.

> [!NOTE]
> La prise en charge de Kubernetes dans l’Azure Container Service est actuellement en version préliminaire.


## <a name="requirements-for-the-service-principal"></a>Configuration requise pour le principal du service

Les éléments suivants sont la configuration requise du principal du service Azure Active Directory dans un cluster Kubernetes dans Azure Container Service. 

* **Étendue** : l’abonnement Azure dans lequel le cluster est déployé

* **Rôle** : **Collaborateur**

* **Clé secrète client** : doit être un mot de passe. Actuellement, vous ne pouvez pas utiliser de principal du service configuré pour l’authentification par certificat.

> [!NOTE]
> Chaque principal du service est associé à une application Azure Active Directory. Le principal du service pour un cluster Kubernetes peut être associé à n’importe quel nom d’application Azure Active Directory valide.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Options de principal du service pour un cluster Kubernetes

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>Option 1 : transmettez l’ID client du principal du service client et la clé secrète client

Fournissez **l’ID client** (souvent appelé `appId`, pour l’ID de l’application) et la **clé secrète client** (`password`) d’un principal du service existant en tant que paramètres lors de la création du cluster Kubernetes. Si vous utilisez un principal du service existant, assurez-vous qu’il répond aux spécifications de la section précédente. Si vous devez créer un principal du service, consultez la rubrique [Créer un principal du service](#create-a-service-principal-in-azure-active-directory) plus loin dans cet article.

Vous pouvez spécifier ces paramètres lors du [déploiement du cluster Kubernetes](./container-service-deployment.md) à l’aide du portail, de l’interface de ligne de commande (CLI) Azure 2.0 (Version préliminaire), d’Azure PowerShell ou d’autres méthodes.

>[!TIP] 
>Lorsque vous spécifiez **l’ID client**, veillez à utiliser l’`appId`, et non l’`ObjectId`, du principal du service.
>

L’exemple suivant montre une manière de transmettre les paramètres avec l’interface de ligne de commande (CLI) de la version préliminaire d’Azure 2.0 (consultez les [instructions d’installation et de configuration](/cli/azure/install-az-cli2)). Cet exemple utilise le [modèle de démarrage rapide Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Téléchargez](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) le fichier de paramètres de modèle `azuredeploy.parameters.json` à partir de GitHub.

2. Pour spécifier le principal du service, entrez les valeurs pour `servicePrincipalClientId` et `servicePrincipalClientSecret` dans le fichier (vous devez également fournir vos propres valeurs pour `dnsNamePrefix` et `sshRSAPublicKey`. La clé en question est la clé publique SSH pour accéder au cluster). Enregistrez le fichier .

    ![Transmettez les paramètres du principal du service](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Exécutez la commande suivante à l’aide de `--parameters` pour définir le chemin d’accès au fichier azuredeploy.parameters.json. Cette commande déploie le cluster dans un groupe de ressources que vous créez appelé `myResourceGroup` dans la région États-Unis de l’Ouest.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus" 
    
    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>Option 2 : générez le principal du service lors de la création du cluster avec la CLI d’Azure 2.0 (Version préliminaire)

Si vous avez installé et configuré [la CLI d’Azure 2.0 (Version préliminaire)](https://docs.microsoft.com/cli/azure/install-az-cli2), vous pouvez exécuter la commande [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) pour [créer le cluster](./container-service-create-acs-cluster-cli.md).

Comme avec d’autres options de création de clusters Kubernetes, vous pouvez spécifier des paramètres pour un principal du service existant lorsque vous exécutez `az acs create`. Toutefois, lorsque vous omettez ces paramètres, Azure Container Service crée un principal du service automatiquement. Cette procédure est réalisée en toute transparence au cours du déploiement. 

La commande suivante crée un cluster Kubernetes et génère des clés SSH et des informations d’identification du principal du service :

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Création d’un principal du service dans Azure Active Directory

Si vous souhaitez créer un principal du service dans Azure Active Directory à utiliser dans votre cluster Kubernetes, Azure propose plusieurs méthodes. 

L’exemple de commande suivant vous montre comment procéder avec [l’Azure CLI 2.0 (version préliminaire)](https://docs.microsoft.com/cli/azure/install-az-cli2). Vous pouvez également créer un principal du service à l’aide d’[Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md), du [portail classique](../azure-resource-manager/resource-group-create-service-principal-portal.md) ou d’autres méthodes.

> [!IMPORTANT]
> Tenez bien compte de la configuration requise pour le principal du service présentée plus haut dans cet article.
>

```azurecli
az login

az account set --subscription "mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

L’opération renvoie un résultat semblable à celui-ci (illustré ici de manière rédigée) :

![Créer un principal du service](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

**L’ID client** (`appId`) et la **clé secrète client** (`password`) que vous utilisez en tant que paramètres du principal du service pour le déploiement du cluster sont mis en surbrillance.


Confirmez votre principal du service en ouvrant un nouveau shell et exécutez les commandes suivantes en remplaçant `appId`, `password` et `tenant` :

```azurecli 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Considérations supplémentaires


* Lorsque vous spécifiez **l’ID Client** du principal du service, vous pouvez utiliser la valeur de `appId` (comme indiqué dans cet article) ou le principal du service correspondant `name` (par exemple, `https://www.contoso.org/example`).

* Si vous utilisez la commande `az acs create` pour générer automatiquement un principal du service, les informations d’identification du principal du service sont écrites dans le fichier ~/.azure/acsServicePrincipal.json sur la machine utilisée pour exécuter la commande.

* Sur les machines virtuelles principales et de nœud du cluster Kubernetes, les informations d’identification du principal du service sont stockées dans le fichier /etc/kubernetes/azure.json.

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de Kubernetes](container-service-kubernetes-walkthrough.md) dans votre cluster de service de conteneur.



<!--HONumber=Feb17_HO1-->


