---
title: "Créer un espace de noms Azure Event Hubs et activer Capture à l’aide d’un modèle | Microsoft Docs"
description: "Créer un espace de noms Azure Event Hubs avec un concentrateur d’événements et activer Capture à l’aide d’un modèle Azure Resource Manager"
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017


---
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>

# Créer un espace de noms Event Hubs avec un concentrateur d’événements et activer Capture à l’aide d’un modèle Azure Resource Manager
Cet article explique comment utiliser un modèle Azure Resource Manager qui crée un espace de noms Event Hubs avec une instance de concentrateur d’événements, et active la fonctionnalité Capture sur le concentrateur d’événements. L’article décrit comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins

Pour en savoir plus sur la création de modèles, consultez [Création de modèles Azure Resource Manager][Authoring Azure Resource Manager templates].

Pour plus d’informations sur les pratiques et les modèles des conventions d’affectation de noms des ressources Azure, consultez [Conventions d’affectation de noms des ressources Azure][Azure Resources Naming Conventions].

Pour accéder au modèle complet, consultez la section relative à [Event Hubs et au modèle d’activation de Capture][Event Hub and enable Capture template] sur GitHub.

> [!NOTE]
> Pour rechercher les derniers modèles, recherchez Event Hubs dans la galerie de [modèles de démarrage rapide Azure][Azure Quickstart Templates].
> 
> 

<a id="what-will-you-deploy" class="xliff"></a>

## Qu'allez-vous déployer ?
Avec ce modèle, vous déployez un espace de noms Event Hubs avec un concentrateur d’événements, et activez [Event Hubs Capture](event-hubs-capture-overview.md).

[Event Hubs](event-hubs-what-is-event-hubs.md) est un service de traitement des événements utilisé pour fournir des entrées d’événements et de télémétrie dans Azure à grande échelle, avec faible latence et fiabilité élevée. Event Hubs Archive vous permet de transmettre automatiquement les données de streaming de vos instances Event Hubs au stockage Blob Azure de votre choix et ce, dans l’intervalle de temps ou de taille que vous spécifiez.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

<a id="parameters" class="xliff"></a>

## Paramètres
Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Ce modèle inclut une section appelée `Parameters` , qui contient toutes les valeurs des paramètres. Vous devez définir un paramètre pour les valeurs qui varient en fonction du projet que vous déployez ou de l’environnement dans lequel vous effectuez le déploiement. Ne définissez pas de paramètres pour les valeurs qui restent inchangées. Chaque valeur de paramètre est utilisée dans le modèle pour définir les ressources déployées.

Le modèle définit les paramètres suivants.

<a id="eventhubnamespacename" class="xliff"></a>

### eventHubNamespaceName
Nom de l’espace de noms Event Hubs à créer.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

<a id="eventhubname" class="xliff"></a>

### eventHubName
Nom du concentrateur d’événements créé dans l’espace de noms Event Hubs.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

<a id="messageretentionindays" class="xliff"></a>

### messageRetentionInDays
Nombre de jours pendant lesquels conserver les messages dans le concentrateur d’événements. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

<a id="partitioncount" class="xliff"></a>

### partitionCount
Nombre de partitions à créer dans le concentrateur d’événements.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

<a id="captureenabled" class="xliff"></a>

### captureEnabled
Active la fonction Capture sur le concentrateur d’événements.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
<a id="captureencodingformat" class="xliff"></a>

### captureEncodingFormat
Format de codage que vous spécifiez pour sérialiser les données d’événement.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

<a id="capturetime" class="xliff"></a>

### captureTime
Intervalle de temps pendant lequel la fonctionnalité Event Hubs Capture démarre la capture des données dans le stockage Blob Azure.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

<a id="capturesize" class="xliff"></a>

### captureSize
Intervalle de taille appliqué par la fonctionnalité Event Hubs Capture lorsqu’elle démarre la capture des données dans le stockage Blob Azure.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

<a id="destinationstorageaccountresourceid" class="xliff"></a>

### destinationStorageAccountResourceId
La fonctionnalité Capture nécessite un ID de ressource de compte de stockage Azure pour activer la capture dans le compte de stockage souhaité.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

<a id="blobcontainername" class="xliff"></a>

### blobContainerName
Conteneur d’objets blob dans lequel vos données d’événement sont capturées.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


<a id="apiversion" class="xliff"></a>

### apiVersion
Version d’API du modèle.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

<a id="resources-to-deploy" class="xliff"></a>

## Ressources à déployer
Crée un espace de noms de type **EventHubs** avec un concentrateur d’événements, et active Capture.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

<a id="commands-to-run-deployment" class="xliff"></a>

## Commandes pour l’exécution du déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

<a id="powershell" class="xliff"></a>

## PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

<a id="azure-cli" class="xliff"></a>

## Interface de ligne de commande Azure
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Vous pouvez également configurer la fonctionnalité Event Hubs Capture via le [portail Azure](https://portal.azure.com). Pour en savoir plus, voir [Activer Event Hubs Capture à l’aide du portail Azure](event-hubs-capture-enable-through-portal.md).

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Créer un concentrateur d’événements](event-hubs-create.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

