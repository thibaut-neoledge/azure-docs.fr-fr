---
title: "Schéma d’événement Azure Event Grid"
description: "Décrit les propriétés fournies pour les événements avec Azure Event Grid."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 09/18/2017
ms.author: babanisa
ms.openlocfilehash: a61357b6ba75566e0ad4d3300cc602333ece0563
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Schéma d’événement Azure Event Grid

Cet article fournit les propriétés et les schémas des événements. Les événements se composent de cinq propriétés de chaîne et d’un objet de données obligatoires. Les propriétés sont communes à tous les événements, quel que soit le serveur de publication. L’objet de données contient des propriétés spécifiques à chaque serveur de publication. Dans les rubriques du système, ces propriétés sont spécifiques au fournisseur de ressources tel que Stockage Azure ou Azure Event Hubs.

Les événements sont envoyés à Azure Event Grid sous forme de tableau qui peut contenir plusieurs objets d’événement. S’il n’existe qu’un seul événement, la longueur du tableau est de 1. La taille totale du tableau peut atteindre jusqu’à 1 Mo. Chaque événement du tableau est limité à 64 Ko.
 
## <a name="event-properties"></a>Propriétés d’événement

Tous les événements contiennent les mêmes données de premier niveau suivantes :

| Propriété | Type | Description |
| -------- | ---- | ----------- |
| rubrique | string | Chemin d’accès complet à la source de l’événement. Ce champ n’est pas modifiable. |
| subject | string | Chemin de l’objet de l’événement, défini par le serveur de publication. |
| eventType | string | Un des types d’événements inscrits pour cette source d’événement. |
| eventTime | string | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | string | Identificateur unique de l’événement. |
| données | objet | Données d’événement spécifiques au fournisseur de ressources. |

## <a name="available-event-sources"></a>Sources d’événement disponibles

Les sources d’événements suivantes publient des événements à utiliser via Event Grid :

* Groupes de ressources (opérations de gestion)
* Abonnements Azure (opérations de gestion)
* Hubs d'événements
* Rubriques personnalisées

## <a name="azure-subscriptions"></a>Abonnements Azure

Les abonnements Azure peuvent désormais émettre des événements de gestion à partir d’Azure Resource Manager, par exemple lors de la création d’une machine virtuelle ou de la suppression d’un compte de stockage.

### <a name="available-event-types"></a>Types d’événement disponibles

- **Microsoft.Resources.ResourceWriteSuccess** : événement déclenché lors de l’aboutissement d’une opération de création ou de mise à jour de ressource.  
- **Microsoft.Resources.ResourceWriteFailure** : événement déclenché lors de l’échec d’une opération de création ou de mise à jour de ressource.  
- **Microsoft.Resources.ResourceWriteCancel** : événement déclenché lors de l’annulation d’une opération de création ou de mise à jour de ressource.  
- **Microsoft.Resources.ResourceDeleteSuccess** : événement déclenché lors de la réussite d’une opération de suppression de ressource.  
- **Microsoft.Resources.ResourceDeleteFailure** : événement déclenché lors de l’échec d’une opération de suppression de ressource.  
- **Microsoft.Resources.ResourceDeleteCancel** : événement déclenché lors de l’annulation d’une opération de suppression de ressource. Ce cas de figure se produit lorsque le déploiement d’un modèle est annulé.

### <a name="example-event-schema"></a>Exemple de schéma d’événement

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>Groupes de ressources

Les groupes de ressources peuvent désormais émettre des événements de gestion à partir d’Azure Resource Manager, par exemple lors de la création d’une machine virtuelle ou de la suppression d’un compte de stockage.

### <a name="available-event-types"></a>Types d’événement disponibles

- **Microsoft.Resources.ResourceWriteSuccess** : événement déclenché lors de l’aboutissement d’une opération de création ou de mise à jour de ressource.  
- **Microsoft.Resources.ResourceWriteFailure** : événement déclenché lors de l’échec d’une opération de création ou de mise à jour de ressource.  
- **Microsoft.Resources.ResourceWriteCancel** : événement déclenché lors de l’annulation d’une opération de création ou de mise à jour de ressource.  
- **Microsoft.Resources.ResourceDeleteSuccess** : événement déclenché lors de la réussite d’une opération de suppression de ressource.  
- **Microsoft.Resources.ResourceDeleteFailure** : événement déclenché lors de l’échec d’une opération de suppression de ressource.  
- **Microsoft.Resources.ResourceDeleteCancel** : événement déclenché lors de l’annulation d’une opération de suppression de ressource. Ce cas de figure se produit lorsque le déploiement d’un modèle est annulé.

### <a name="example-event"></a>Exemple d’événement

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Event Hubs

Actuellement, les événements Event Hubs sont générés uniquement lorsqu’un fichier est automatiquement envoyé vers un stockage avec la fonctionnalité Capture.

### <a name="available-event-types"></a>Types d’événement disponibles

- **Microsoft.EventHub.CaptureFileCreated** : événement déclenché lors de la création d’un fichier de capture.

### <a name="example-event"></a>Exemple d’événement

Cet exemple d’événement montre le schéma d’un événement Event Hubs déclenché quand la fonctionnalité Capture stocke un fichier : 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Stockage d'objets blob Azure

>[!IMPORTANT]
>Vous devez être inscrit à la préversion des événements de stockage Blob pour utiliser les événements de stockage Blob. Pour plus d’informations sur le programme en préversion, consultez [Événements de stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview#join-the-preview).  


### <a name="available-event-types"></a>Types d’événement disponibles

- **Microsoft.Storage.BlobCreated** : événement déclenché lors de la création d’un objet blob.
- **Microsoft.Storage.BlobDeleted** : événement déclenché lors de la suppression d’un objet blob.

### <a name="example-event"></a>Exemple d’événement

Cet exemple d’événement montre le schéma d’un événement de stockage déclenché lors de la création d’un objet blob : 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>Rubriques personnalisées

C’est vous qui définissez la charge utile des données de vos événements personnalisés. Elle peut être n’importe quel objet JSON correctement mis en forme. Les données de premier niveau doivent contenir les mêmes champs que les événements standard définis par les ressources. Lors de la publication d’événements dans les rubriques personnalisées, pensez à modéliser l’objet de vos événements pour faciliter le routage et le filtrage.

### <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre un événement pour une rubrique personnalisée :
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Étapes suivantes

* Pour découvrir Azure Event Grid, consultez [Présentation d’Event Grid](overview.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
