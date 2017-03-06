---
title: "Déployer un cluster de conteneur Docker dans Azure | Microsoft Docs"
description: "Déployez un cluster Azure Container Service à l’aide du portail Azure ou du modèle Azure Resource Manager."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, conteneurs, micro-services, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 716a6f4507b05b8a8548cd34f8227e8366a91645
ms.openlocfilehash: 17a4ab1920e020ddf453e9b42319ba260e9700a5
ms.lasthandoff: 02/24/2017


---
# <a name="deploy-an-azure-container-service-cluster"></a>Déploiement d’un cluster Azure Container Service



Azure Container Service assure le déploiement rapide des principales solutions de mise en cluster et d’orchestration de containers open source. Ce document vous guide lors du déploiement d’un cluster Azure Container Service à l’aide du portail Azure ou d’un modèle de démarrage rapide Azure Resource Manager. 

Vous pouvez également déployer un cluster Azure Container Service à l’aide [d’Azure CLI 2.0](container-service-create-acs-cluster-cli.md) ou des API Azure Container Service.

Pour obtenir du contexte, consultez [Présentation d’Azure Container Service](container-service-intro.md).


## <a name="prerequisites"></a>Composants requis

* **Abonnement Azure** : si vous n’en avez pas, inscrivez-vous pour un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

* **Clé publique SSH RSA** : lorsque vous déployez via le portail ou l’un des modèles de démarrage rapide Azure, vous devez fournir la clé publique pour l’authentification sur les machines virtuelles d’Azure Container Service. Pour créer des clés Secure Shell (SSH) RSA, consultez les conseils [OS X et Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) ou [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md). 

* **ID client principal du service et clé secrète** (Kubernetes uniquement) : pour plus d’informations et des conseils sur la création d’un principal du service Azure Active Directory, consultez [À propos du principal du service pour un cluster Kubernetes](container-service-kubernetes-service-principal.md).



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Créer un cluster à l’aide du portail Azure
1. Connectez-vous au Portail Azure, sélectionnez **Nouveau** et recherchez **Azure Container Service** dans Azure Marketplace.

    ![Azure Container Service dans Marketplace](media/container-service-deployment/acs-portal1.png)  <br />

2. Cliquez sur **Azure Container Service**, puis cliquez sur **Créer**.

3. Dans le panneau **De base**, entrez les informations suivantes :

    * **Orchestrator** : sélectionnez l’un des orchestrators du conteneur à déployer sur le cluster.
        * **DC/OS**: déploie un cluster DC/OS.
        * **Swarm**: déploie un cluster Docker Swarm.
        * **Kubernetes** : déploie un cluster Kubernetes.
    * **Abonnement**: sélectionnez un abonnement Azure.
    * **Groupe de ressources** : entrez le nom d’un nouveau groupe de ressources pour le déploiement.
    * **Emplacement**: sélectionnez une région Azure pour le déploiement d’Azure Container Service. Pour connaître la disponibilité, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).
    
    ![Paramètres de base](media/container-service-deployment/acs-portal3.png)  <br />
    
    Cliquez sur **OK** lorsque vous souhaitez continuer.

4. Dans le panneau **Configuration maître**, entrez les paramètres suivants pour le ou les nœuds maîtres Linux du cluster (certains paramètres sont spécifiques à chaque orchestrator) :

    * **Nom DNS maître** : le préfixe utilisé pour créer un nom de domaine complet (FQDN) unique pour le maître. Le nom de domaine complet du maître est au format *préfixe*mgmt.*emplacement*.cloudapp.azure.com.
    * **Nom d’utilisateur** : le nom d’utilisateur d’un compte sur chacune des machines virtuelles Linux dans le cluster.
    * **Clé publique SSH RSA** : ajoutez la clé publique à utiliser pour l’authentification sur les machines virtuelles Linux. Vous devez impérativement vérifier que cette clé ne contient aucun saut de ligne et qu’elle inclut le préfixe `ssh-rsa`. Le postfix `username@domain` est facultatif. Vous devriez obtenir une clé du type : **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**. 
    * **Principal du service** : si vous avez sélectionné l’orchestrator Kubernetes, entrez **l’ID client du principal du service** (également appelé appId) et la **clé secrète client du principal du service** (mot de passe) d’Azure Active Directory. Pour en savoir plus, consultez [À propos du principal du service pour un cluster Kubernetes](container-service-kubernetes-service-principal.md) (en anglais).
    * **Nombre de maîtres**: quantité de maîtres dans le cluster.
    * **Diagnostics de la machine virtuelle** : pour certains orchestrators, vous pouvez activer les diagnostics sur les maîtres.

    ![Configuration maître](media/container-service-deployment/acs-portal4.png)  <br />

    Cliquez sur **OK** lorsque vous souhaitez continuer.

5. Dans le panneau **Configuration d’agent**, entrez les informations suivantes :

    * **Nombre d’agents** : pour Docker Swarm et Kubernetes, cette valeur correspond au nombre initial d’agents dans le groupe identique d’agent. Pour DC/OS, il s’agit du nombre initial d’agents dans un groupe identique privé. En outre, un groupe identique public est créé pour DC/OS et contient un nombre prédéterminé d’agents. Le nombre d’agents dans ce groupe identique public est déterminé par le nombre de maîtres dans le cluster : un agent public pour un maître, et deux agents publics pour trois ou cinq maîtres.
    * **Taille de la machine virtuelle de l’agent**: taille des machines virtuelles des agents.
    * **Système d’exploitation** : ce paramètre est actuellement disponible uniquement si vous avez sélectionné l’orchestrator Kubernetes. Choisissez une distribution Linux ou un système d’exploitation Windows à exécuter sur les agents. Ce paramètre détermine si votre cluster peut exécuter des applications de conteneur Windows ou Linux. 

        > [!NOTE]
        > La prise en charge de conteneur Windows est en version préliminaire pour les clusters Kubernetes. Pour les clusters DC/OS et Swarm, seuls les agents Linux sont actuellement pris en charge dans Azure Container Service.

    * **Informations d’identification de l’agent** : si vous avez sélectionné le système d’exploitation Windows, entrez un **nom d’utilisateur** et un **mot de passe** d’administrateur pour les machines virtuelles de l’agent. 

    ![Configuration de l’agent](media/container-service-deployment/acs-portal5.png)  <br />

    Cliquez sur **OK** lorsque vous souhaitez continuer.

6. Cliquez sur **OK** une fois la validation du service terminée.

    ![Validation](media/container-service-deployment/acs-portal6.png)  <br />

7. Passez en revue les conditions. Cliquez sur **Créer** pour entamer le processus de déploiement.

    Si vous avez choisi d’épingler le déploiement au Portail Azure, vous pouvez visualiser l’état du déploiement.

    ![état du déploiement](media/container-service-deployment/acs-portal8.png)  <br />

Le déploiement prend plusieurs minutes. Le cluster Azure Container Service est ensuite prêt à l’emploi.


## <a name="create-a-cluster-by-using-a-quickstart-template"></a>Créer un cluster à l’aide d’un modèle de démarrage rapide
Des modèles de démarrage rapide Azure sont disponibles pour déployer un cluster dans Azure Container Service. Les modèles de démarrage rapide fournis peuvent être modifiés pour inclure une configuration Azure supplémentaire ou avancée. Pour créer un cluster Azure Container Service à l’aide d’un modèle de démarrage rapide Azure, vous avez besoin d’un abonnement Azure. Si ce n’est pas le cas, inscrivez-vous dès aujourd’hui pour un [essai gratuit](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935). 

Suivez ces étapes pour déployer un cluster à l’aide d’un modèle et d’Azure CLI 2.0 (consultez les [instructions d’installation et de configuration](/cli/azure/install-az-cli2.md)).

> [!NOTE] 
> Si vous êtes sur un système Windows, vous pouvez utiliser une procédure similaire pour déployer un modèle à l’aide d’Azure PowerShell. Consultez les étapes plus loin dans cette section. Vous pouvez également déployer un modèle via le [portail](../azure-resource-manager/resource-group-template-deploy-portal.md) ou d’autres méthodes.

1. Sélectionnez l’un des modèles de démarrage rapide disponibles dans GitHub pour déployer un cluster DC/OS, Docker Swarm ou Kubernetes. Une liste partielle s’affiche. Les modèles DC/OS et Swarm sont identiques, à l’exception de la sélection de l’orchestrator par défaut.

    * [Modèle DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modèle Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modèle Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Connectez-vous à votre compte Azure (`az login`) et assurez-vous qu’Azure CLI est associé à votre abonnement Azure. Vous pouvez voir l’abonnement par défaut à l’aide de la commande suivante :

    ```azurecli
    az account show
    ```
    
    Si vous avez plusieurs abonnements et que vous devez définir un abonnement par défaut différent, exécutez `az account set --subscription` et spécifier l’ID d’abonnement ou le nom.

3. En tant que meilleure pratique, utilisez un nouveau groupe de ressources pour le déploiement. Pour créer un groupe de ressources, utilisez la commande `az group create` et spécifiez un nom de groupe de ressources ainsi qu’un emplacement : 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. Créez un fichier JSON contenant les paramètres du modèle requis. Téléchargez le fichier de paramètres nommé `azuredeploy.parameters.json` qui accompagne le modèle Azure Container Service `azuredeploy.json` dans GitHub. Entrez les valeurs de paramètre requises pour votre cluster. 

    Par exemple, pour utiliser le [modèle DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos), fournissez des valeurs de paramètre pour `dnsNamePrefix` et `sshRSAPublicKey`. Consultez les descriptions dans `azuredeploy.json` et des options pour les autres paramètres.  
 

5. Créez un cluster de service de conteneur en transmettant le fichier de paramètres de déploiement avec la commande suivante, où :

    * **RESOURCE_GROUP** est le nom du groupe de ressources que vous avez créé à l’étape précédente.
    * **DEPLOYMENT_NAME** (facultatif) est un nom que vous donnez au déploiement.
    * **TEMPLATE_URI** est l’emplacement du fichier de déploiement `azuredeploy.json`. Cette URI doit être le fichier Raw, et non un pointeur vers l’interface utilisateur de GitHub. Pour trouver cette URI, sélectionnez le fichier `azuredeploy.json` dans GitHub, puis cliquez sur le bouton **Brut** .  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    Vous pouvez également fournir des paramètres sous forme de chaîne au format JSON sur la ligne de commande. Utilisez une commande similaire à la suivante :

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > Le déploiement prend plusieurs minutes.
    > 

### <a name="equivalent-powershell-commands"></a>Commandes PowerShell équivalentes
Vous pouvez également déployer un modèle de cluster Azure Container Service avec PowerShell. Ce document est basé sur la version 1.0 du [module Azure PowerShell](https://azure.microsoft.com/blog/azps-1-0/).

1. Sélectionnez l’un des modèles de démarrage rapide disponibles dans GitHub pour déployer un cluster DC/OS, Docker Swarm ou Kubernetes. Une liste partielle s’affiche. Notez que les modèles DC/OS et Swarm sont identiques, à l’exception de la sélection de l’orchestrator par défaut.

    * [Modèle DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Modèle Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Modèle Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Avant de créer un cluster dans votre abonnement Azure, vérifiez que votre session PowerShell a bien été connectée à Azure. Pour cela, utilisez la commande `Get-AzureRMSubscription` :

    ```powershell
    Get-AzureRmSubscription
    ```

3. Si vous avez besoin de vous connecter à Azure, utilisez la commande `Login-AzureRMAccount` :

    ```powershell
    Login-AzureRmAccount
    ```

4. En tant que meilleure pratique, utilisez un nouveau groupe de ressources pour le déploiement. Pour créer un groupe de ressources, utilisez la commande `New-AzureRmResourceGroup` et spécifiez un nom de groupe de ressources ainsi qu’une région de destination :

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. Une fois le groupe de ressources créé, vous pouvez créer votre cluster à l’aide de la commande suivante. L’URI du modèle souhaité est spécifié avec le paramètre `-TemplateUri`. Lorsque vous exécutez cette commande, PowerShell vous invite à saisir les valeurs des paramètres de déploiement.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>Indication des paramètres du modèle
Si vous êtes familiarisé avec PowerShell, vous savez que vous pouvez parcourir les paramètres disponibles pour une applet de commande en utilisant le signe moins (-), puis en appuyant sur la touche TAB. Cette fonctionnalité fonctionne également avec les paramètres que vous définissez dans votre modèle. Dès que vous entrez le nom du modèle, l’applet de commande récupère le modèle, analyse ses paramètres et ajoute les paramètres du modèle à la commande de façon dynamique. Il est ainsi plus facile de spécifier les valeurs des paramètres du modèle. En outre, si vous oubliez une valeur obligatoire, PowerShell vous invite à spécifier cette valeur.

Voici l’intégralité de la commande contenant les paramètres. Renseignez vos propres valeurs pour les noms des ressources.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>Étapes suivantes
À présent que vous disposez d’un cluster opérationnel, consultez les documents suivants pour obtenir des informations supplémentaires concernant la connexion et la gestion du cluster :

* [Connexion à un cluster Azure Container Service](container-service-connect.md)
* [Gestion de conteneur via l’API REST](container-service-mesos-marathon-rest.md)
* [Gestion des conteneurs avec Docker Swarm](container-service-docker-swarm.md)
* [Gestion des conteneurs avec Kubernetes](container-service-kubernetes-walkthrough.md)

