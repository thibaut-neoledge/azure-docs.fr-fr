---
title: "Intégrer Azure Service Fabric à la Gestion des API | Microsoft Docs"
description: "Apprenez à bien démarrer avec la Gestion des API Azure et Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: 705212675fc0a869a4374f621d5f2d7e035294dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Déployer la Gestion des API avec Service Fabric
Ce didacticiel est le deuxième d’une série. Il vous montre comment configurer la [Gestion des API Azure](../api-management/api-management-key-concepts.md) avec Service Fabric pour acheminer le trafic vers un service principal dans Service Fabric.  Lorsque vous aurez terminé, vous aurez déployé la Gestion des API sur un réseau virtuel et configuré une opération d’API pour qu’elle envoie le trafic aux services sans état principaux. Pour en savoir plus sur les scénarios de gestion des API Azure avec Service Fabric, consultez l’article [Vue d’ensemble](service-fabric-api-management-overview.md).

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déploiement de Gestion des API
> * Configuration de Gestion des API
> * Création d’une opération d’API
> * Configuration d’une stratégie de principal
> * Ajouter l’API à un produit

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure à l’aide d’un modèle ;
> * déployer la Gestion des API avec Service Fabric.

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez le [module Azure PowerShell version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Se connecter à Azure et sélectionner un abonnement
Ce didacticiel utilise [Azure PowerShell][azure-powershell]. Lorsque vous démarrez une nouvelle session PowerShell, connectez-vous à votre compte Azure et sélectionnez votre abonnement avant d’exécuter des commandes Azure.
 
Connectez-vous à votre compte Azure et sélectionnez votre abonnement :

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>Déploiement de Gestion des API
Les applications cloud ont généralement besoin d’une passerelle frontale afin de fournir un point d’entrée unique pour les utilisateurs, les appareils ou d’autres applications. Dans Service Fabric, une passerelle peut être n’importe quel service sans état, comme une application ASP.NET Core, ou un autre service conçu pour l’entrée de trafic, par exemple Event Hubs, IoT Hub ou la Gestion des API Azure. Ce didacticiel présente l’utilisation de la Gestion des API Azure en tant que passerelle vers des applications Service Fabric. Gestion des API s’intègre directement dans Service Fabric, ce qui vous permet de publier des API avec un ensemble complet de règles de routage vers vos services Service Fabric principaux. 

Maintenant que vous avez un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure, déployez la Gestion des API sur le réseau virtuel (VNET) dans le sous-réseau et le Groupe de sécurité réseau désignés pour la Gestion des API. Dans le cadre de ce didacticiel, le modèle Resource Manager de la Gestion des API est préconfiguré pour utiliser les noms du réseau virtuel, du sous-réseau et du Groupe de sécurité réseau que vous avez configurés dans le [didacticiel du cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou le [didacticiel du cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) précédent. 

Téléchargez le modèle Resource Manager et le fichier de paramètres suivants :
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

Renseignez les paramètres vides dans `apim.parameters.json` pour votre déploiement.

Utilisez le script suivant pour déployer le modèle Resource Manager et les fichiers de paramètres nécessaires à la Gestion des API :

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>Configuration de Gestion des API

Une fois la Gestion des API et votre cluster Fabric Service déployés, vous pouvez configurer les paramètres de sécurité et un principal Service Fabric dans la Gestion des API. Cela vous permet de créer une stratégie de service principal qui envoie le trafic vers votre cluster Service Fabric.

### <a name="configure-api-management-security"></a>Configurer la sécurité de la Gestion des API

Pour configurer le principal de Service Fabric, vous devez d’abord configurer les paramètres de sécurité de Gestion des API. Pour configurer les paramètres de sécurité, accédez à votre service Gestion des API dans le portail Azure.

#### <a name="enable-the-api-management-rest-api"></a>Activation de l’API REST de Gestion des API

L’API REST de Gestion des API est actuellement la seule solution permettant de configurer un service principal. La première étape consiste à activer l’API REST de Gestion des API et à la sécuriser.

 1. Dans le service Gestion des API, sélectionnez **API Gestion** sous **Sécurité**.
 2. Cochez la case **Activer l’API REST Gestion des API**.
 3. Notez **l’URL de l’API Gestion**, que nous utiliserons ultérieurement pour configurer le principal Service Fabric.
 4. Générez un **Jeton d’accès** en sélectionnant une date d’expiration et une clé, puis cliquez sur le bouton **Générer** en bas de la page.
 5. Copiez le **jeton d’accès** et enregistrez-le.  Nous l’utiliserons dans les étapes suivantes. Notez qu’il est différent de la clé primaire et de la clé secondaire.

#### <a name="upload-a-service-fabric-client-certificate"></a>Chargement d’un certificat client Service Fabric

Gestion des API doit s’authentifier avec votre cluster Service Fabric pour la détection de service à l’aide d’un certificat client ayant accès à votre cluster. Par souci de simplicité, ce didacticiel utilise le certificat déjà spécifié lors de la création du [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) ou du [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), qui par défaut peut être utilisé pour accéder à votre cluster.

 1. Dans le service Gestion des API, sélectionnez **Certificats clients** sous **Sécurité**.
 2. Cliquez sur le bouton **+ Ajouter** .
 2. Sélectionnez le fichier de clé privée (.pfx) du certificat de cluster que vous avez spécifié lors de la création de votre cluster Service Fabric, attribuez-lui un nom et fournissez le mot de passe de la clé privée.

> [!NOTE]
> Ce didacticiel utilise le même certificat pour l’authentification de client et la sécurité nœud à nœud du cluster. Si vous en possédez un, vous pouvez utiliser un certificat client distinct configuré pour accéder à votre cluster Service Fabric.

### <a name="configure-the-backend"></a>Configuration du principal

Maintenant que la sécurité de Gestion des API est configurée, vous pouvez configurer le principal de Service Fabric. Pour les principaux de Service Fabric, le cluster Service Fabric est le serveur principal plutôt qu’un service Service Fabric spécifique. Ainsi, une stratégie unique suffit pour acheminer le trafic vers plusieurs services dans le cluster.

Cette étape nécessite le jeton d’accès généré précédemment et l’empreinte du certificat de votre cluster, que vous avez chargée sur la Gestion des API.

> [!NOTE]
> Si vous avez utilisé un certificat client distinct à l’étape précédente pour Gestion des API, dans cette étape vous avez besoin de l’empreinte du certificat client en plus de l’empreinte du certificat de cluster.

Envoyez la requête HTTP PUT suivante vers l’URL d’API de Gestion des API notée précédemment lors de l’activation de l’API REST de Gestion des API pour configurer le service principal de Service Fabric. Si la commande réussit, vous devriez voir une réponse `HTTP 201 Created`. Pour plus d’informations sur chaque champ, consultez la [documentation de référence sur l’API du serveur principal](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) de Gestion des API.

Commande HTTP et URL :
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

En-têtes de requête :
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

Corps de la requête :
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

Ici, le paramètre **url** est le nom de service complet d’un service dans votre cluster vers lequel toutes les requêtes sont acheminées par défaut si aucun nom de service n’est spécifié dans une stratégie de principal. Vous pouvez utiliser un nom de service factice, tel que « fabric:/fake/service » si vous ne souhaitez pas disposer d’un service de secours.

Pour plus d’informations sur chaque champ, consultez la [documentation de référence sur l’API du serveur principal](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) de Gestion des API.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Déploiement d’un service principal de Service Fabric

Maintenant que Service Fabric est configuré comme principal pour Gestion des API, vous pouvez créer des stratégies de serveur principal pour vos API qui envoient le trafic vers vos services Service Fabric. Pour accepter les requêtes, vous devez d’abord avoir un service qui s’exécute dans Service Fabric.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>Création d’un service Service Fabric avec un point de terminaison HTTP

Pour ce didacticiel, nous allons créer un service fiable ASP.NET Core sans état basique à l’aide du modèle de projet API Web par défaut. Vous créerez ainsi un point de terminaison HTTP pour votre service, que vous exposerez via Gestion des API Azure :

```
/api/values
```

Commencez par [configurer votre environnement de développement pour le développement d’ASP.NET Core](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

Une fois votre environnement de développement configuré, démarrez Visual Studio en tant qu’administrateur et créez un service ASP.NET Core :

 1. Dans Visual Studio, sélectionnez Fichier -> Nouveau projet.
 2. Sélectionnez le modèle d’application Service Fabric sous Cloud et nommez-le **« ApiApplication »**.
 3. Sélectionnez le modèle de service ASP.NET Core et nommez le projet **« WebApiService »**.
 4. Sélectionnez le modèle de projet API Web ASP.NET Core 1.1.
 5. Une fois le projet créé, ouvrez `PackageRoot\ServiceManifest.xml` et supprimez l’attribut `Port` de la configuration des ressources du point de terminaison :
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Ainsi, Service Fabirc peut spécifier un port dynamique à partir de la plage de ports d’application, que nous avons ouverte via le groupe de sécurité réseau dans le modèle Resource Manager du cluster, autorisant le trafic à y accéder à partir de Gestion des API.
 
 6. Appuyez sur F5 dans Visual Studio pour vérifier que l’API web est disponible localement. 

    Ouvrez Service Fabric Explorer et accédez à une instance spécifique du service ASP.NET Core pour voir l’adresse de base écoutée par le service. Ajoutez `/api/values` à l’adresse de base et ouvrez-la dans un navigateur. Cette action appelle la méthode Get sur ValuesController dans le modèle d’API web. Elle retourne la réponse par défaut fournie par le modèle, un tableau JSON contenant deux chaînes :

    ```json
    ["value1", "value2"]`
    ```

    Il s’agit du point de terminaison que vous exposerez via Gestion des API dans Azure.

 7. Enfin, déployez l’application sur votre cluster dans Azure. [Dans Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box), cliquez avec le bouton droit sur le projet Application, puis sélectionnez **Publier**. Fournissez le point de terminaison de votre cluster (par exemple, `mycluster.westus.cloudapp.azure.com:19000`) pour déployer l’application sur votre cluster Service Fabric dans Azure.

Un service ASP.NET Core sans état nommé `fabric:/ApiApplication/WebApiService` doit maintenant s’exécuter dans votre cluster Service Fabric dans Azure.

## <a name="create-an-api-operation"></a>Création d’une opération d’API

Nous pouvons maintenant créer une opération dans Gestion des API que les clients externes utilisent pour communiquer avec le service ASP.NET Core sans état en cours d’exécution dans le cluster Service Fabric.

1. Connectez-vous au portail Azure et accédez à votre déploiement de service Gestion des API.
2. Dans le panneau du service Gestion des API, sélectionnez **API**.
3. Ajoutez une nouvelle API en cliquant sur **+API**, puis sur la zone **API vide**, et en renseignant la boîte de dialogue :

    - **URL du service Web** : pour les principaux de Service Fabric, cette valeur d’URL n’est pas utilisée. Vous pouvez placer n’importe quelle valeur ici. Dans le cadre de ce didacticiel, utilisez : « http://servicefabric ».
    - **Nom d’affichage** : entrez un nom pour votre API. Dans le cadre de ce didacticiel, utilisez « Service Fabric App ».
    - **Nom** : entrez « service-fabric-app ».
    - **Modèle d’URL** : sélectionnez **HTTP**, **HTTPS** ou **les deux**. Dans le cadre de ce didacticiel, utilisez **les deux**.
    - **API URL Suffix** (Suffixe d’URL d’API) : spécifiez un suffixe pour notre API. Dans le cadre de ce didacticiel, utilisez « myapp ».
 
4. Sélectionnez **Application Service Fabric** dans la liste des API et cliquez sur **+ Ajouter une opération** pour ajouter une opération d’API frontale. Renseignez les valeurs :
    
    - **URL** : sélectionnez **GET** et entrez un chemin d’accès de l’URL pour l’API. Dans le cadre de ce didacticiel, utilisez « /api/values ».  Par défaut, le chemin d’accès de l’URL spécifié ici est celui envoyé au service principal Service Fabric. Si vous utilisez ici le même chemin d’accès que votre service, dans le cas présent « /api/values », l’opération fonctionne sans modification supplémentaire. Vous pouvez également spécifier ici un chemin d’accès d’URL différent de celui utilisé par votre service Service Fabric principal, auquel cas vous devrez également spécifier une réécriture de chemin d’accès dans votre stratégie d’opération ultérieurement.
    - **Nom d’affichage** : entrez un nom pour l’API. Dans le cadre de ce didacticiel, utilisez « Values ».

5. Cliquez sur **Enregistrer**.

## <a name="configure-a-backend-policy"></a>Configuration d’une stratégie de principal

La stratégie de principal lie tout ensemble. Elle permet de configurer le service principal Service Fabric vers lequel les requêtes sont acheminées. Vous pouvez appliquer cette stratégie à une opération d’API. La [configuration du principal de Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) fournit les contrôles de routage de requête suivants : 
 - Sélection d’une instance de service en spécifiant un nom d’instance de service Service Fabric, codé en dur (par exemple, `"fabric:/myapp/myservice"`) ou généré à partir de la requête HTTP (par exemple, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Résolution de partition en générant une clé de partition à l’aide d’un modèle de partitionnement de Service Fabric.
 - Sélection de réplica pour les services avec état.
 - Conditions de nouvelle tentative de résolution vous permettant de spécifier les conditions pour résoudre à nouveau un emplacement de service et renvoyer une requête.

Pour ce didacticiel, créez une stratégie de principal qui achemine les requêtes directement vers le service ASP.NET Core sans état déployé précédemment :

 1. Sélectionnez et modifiez les **stratégies entrantes** de l’opération Values en cliquant sur l’icône de modification, puis en sélectionnant **Mode Code**.
 2. Dans l’éditeur de code de stratégie, ajoutez une stratégie `set-backend-service` sous les stratégies entrantes, comme indiqué ici, et cliquez sur le bouton **Enregistrer** :
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
           sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
      </inbound>
      <backend>
        <base/>
      </backend>
      <outbound>
        <base/>
      </outbound>
    </policies>
    ```

Pour un ensemble complet d’attributs de stratégie de principal de Service Fabric, reportez-vous à la [documentation sur le principal de Gestion des API](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

### <a name="add-the-api-to-a-product"></a>Ajouter l’API à un produit 

Avant de pouvoir appeler l’API, vous devez l’ajouter à un produit auquel les utilisateurs doivent être autorisés à accéder. 

 1. Dans le service Gestion des API, sélectionnez **Produits**.
 2. Par défaut, Gestion des API fournit deux produits : Starter et Unlimited. Sélectionnez le produit Unlimited.
 3. Sélectionnez **+Ajouter des API**.
 4. Sélectionnez l’API `Service Fabric App` que vous avez créée dans les étapes précédentes et cliquez sur le bouton **Sélectionner**.

### <a name="test-it"></a>Testez-le

Vous pouvez maintenant essayer d’envoyer une requête à votre service principal dans Service Fabric via Gestion des API directement à partir du portail Azure.

 1. Dans le service Gestion des API, sélectionnez **API**.
 2. Dans l’API **Application Service Fabric** que vous avez créée aux étapes précédentes, sélectionnez l’onglet **Test**, puis l’opérateur **Values**.
 3. Cliquez sur le bouton **Envoyer** pour envoyer une requête test au service principal.  La réponse HTTP devrait être de ce type :

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Supprimer des ressources

Un cluster est composé d’autres ressources Azure en plus de la ressource de cluster elle-même. Le plus simple pour supprimer le cluster et toutes les ressources qu’il consomme consiste à supprimer le groupe de ressources.

Connectez-vous à Azure et sélectionnez l’ID d’abonnement pour lequel vous souhaitez supprimer le cluster.  Vous pouvez trouver votre ID d’abonnement en vous connectant au [portail Azure](http://portal.azure.com). Pour supprimer un groupe de ressources et toutes les ressources de cluster, utilisez la cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Déploiement de Gestion des API
> * Configuration de Gestion des API
> * Création d’une opération d’API
> * Configuration d’une stratégie de principal
> * Ajouter l’API à un produit


[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json