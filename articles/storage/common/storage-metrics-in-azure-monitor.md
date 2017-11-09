---
title: Mesures de Stockage Azure dans Azure Monitor | Microsoft Docs
description: "Découvrez les nouvelles mesures proposées par Azure Monitor."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: d30a99044e335723e5d2c4bbd71fab7e4fd51145
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-metrics-in-azure-monitor-preview"></a>Mesures de Stockage Azure dans Azure Monitor (version préliminaire)

Grâce aux mesures de Stockage Azure, vous pouvez analyser les tendances d’utilisation, suivre les demandes et diagnostiquer les problèmes liés à votre compte de stockage.

Azure Monitor fournit des interfaces utilisateur unifiées pour la surveillance entre différents services Azure. Pour plus d’informations, voir [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). Stockage Azure s’intègre à Azure Monitor en envoyant des données de mesure à la plateforme Azure Monitor.

## <a name="access-metrics"></a>Accéder aux mesures

Azure Monitor propose plusieurs méthodes d’accès aux mesures. Vous pouvez y accéder depuis le [portail Azure](https://portal.azure.com), les API d’Azure Monitor (REST et .Net) et les solutions d’analyse comme Operation Management Suite et Event Hub. Pour plus d’informations, voir [Mesures Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Les mesures sont activées par défaut, et vous pouvez accéder aux 30 derniers jours de données. Si vous souhaitez conserver des données sur une période plus longue, vous pouvez archiver les données de mesures dans un compte de stockage Azure. Celui-ci est configuré dans les [paramètres de diagnostic](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) dans Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Accéder aux mesures dans le portail Azure

Vous pouvez surveiller les mesures au fil du temps dans le portail Azure. Voici un exemple montrant comment afficher **UsedCapacity** au niveau du compte.

![capture d’écran d’accès aux mesures dans le portail Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Pour les mesures prenant en charge des dimensions, vous devez filtrer avec la valeur de dimension souhaitée. Voici un exemple montrant comment afficher **Transactions** au niveau du compte avec le type de réponse **Réussite**.

![capture d’écran d’accès aux mesures avec une dimension dans le portail Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Accéder aux mesures avec l’API REST

Azure Monitor fournit des [API REST](/rest/api/monitor/) pour les définitions et valeurs de mesures. Cette section vous montre comment lire les mesures de stockage. L’ID de ressource est utilisé dans toutes les API REST. Pour plus d’informations, consultez [Présentation de l’ID de ressource pour des services dans Storage](#understanding-resource-id-for-services-in-storage).

L’exemple suivant montre comment utiliser [ArmClient](https://github.com/projectkudu/ARMClient) sur la ligne de commande pour simplifier les tests avec l’API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Répertorier les définitions de mesures au niveau du compte avec l’API REST

L’exemple suivant montre comment répertorier les définition de mesures au niveau du compte :

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Si vous souhaitez répertorier les définitions de mesures d’objet blob, table, fichier ou file d’attente, vous devez spécifier des ID de ressources différents pour chaque service avec l’API.

La réponse contient la définition de mesure au format JSON :

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Lire les valeurs de mesures au niveau du compte avec l’API REST

L’exemple suivant montre comment lire les données de mesures au niveau du compte :

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

Dans l’exemple ci-dessus, si vous souhaitez lire des valeurs de mesures d’objet blob, table, fichier ou file d’attente, vous devez spécifier des ID de ressources différents pour chaque service avec l’API.

La réponse suivante contient des valeurs de mesures au format JSON :

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

## <a name="billing-for-metrics"></a>Facturation des mesures

L’utilisation de mesures dans Azure Monitor est actuellement gratuite. Toutefois, si vous utilisez des solutions supplémentaires absorbant des données de mesures, vous pouvez être facturé par ces solutions. Par exemple, vous êtes facturé par Stockage Azure si vous archivez des données de mesures pour un compte de stockage Azure. Vous êtes également facturé par Operation Management Suite (OMS) si vous diffusez en continu des données de mesures vers OMS pour une analyse avancée.

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Présentation des ID de ressources pour des services dans Stockage Azure

L’ID de ressource est l’identificateur unique d’une ressource dans Azure. Lorsque vous utilisez l’API REST Azure Monitor pour lire des définitions ou valeurs de mesures, vous devez utiliser l’ID de ressource de la ressource sur laquelle vous envisagez de travailler. Le format du modèle d’ID de ressource est le suivant :

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

Storage fournit des mesures au niveau du compte de stockage et au niveau du service avec Azure Monitor. Par exemple, vous pouvez récupérer des mesures pour le stockage d’objets blob uniquement. Chaque niveau possède un ID de ressource propre, qui est utilisé pour récupérer les mesures pour ce niveau uniquement.

### <a name="resource-id-for-a-storage-account"></a>ID de ressource d’un compte de stockage

Le format pour spécifier l’ID de ressource d’un compte de stockage est le suivant.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>ID de ressource pour les services de stockage

Le format pour spécifier l’ID de ressource de chacun des services de stockage est le suivant.

* ID de ressource de service BLOB `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* ID de ressource de service de Table `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* ID de ressource de service de File d’attente `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* ID de ressource de service de fichier `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID de ressource dans l’API REST Azure Monitor

Le modèle utilisé lors de l’appel de l’API REST Azure Monitor est le suivant.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Métriques de capacité

Les valeurs de mesures de capacité sont envoyées à Azure Monitor toutes les heures. Les valeurs sont actualisées chaque jour. Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de mesures sont présentées. Le fragment de temps pris en charge pour toutes les mesures de capacité est d’une heure (PT1H).

Stockage Azure fournit les mesures de capacité suivantes dans Azure Monitor.

### <a name="account-level"></a>Niveau du compte

| Nom de métrique | Description |
| ------------------- | ----------------- |
| UsedCapacity | Quantité de stockage utilisée par le compte de stockage. Pour les comptes de stockage standard, il s’agit de la somme de la capacité utilisée par les objets blob, tables, fichiers et files d’attente. Pour les comptes de stockage Premium et les comptes de stockage Blob, elle équivaut à BlobCapacity. <br/><br/> Unité : octets <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |

### <a name="blob-storage"></a>Stockage d'objets blob

| Nom de métrique | Description |
| ------------------- | ----------------- |
| BlobCapacity | Total de stockage d’objets blob utilisé dans le compte de stockage. <br/><br/> Unité : octets <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 <br/> Dimension : BlobType ([Définition](#metrics-dimensions)) |
| BlobCount    | Nombre d’objets blob stockés dans le compte de stockage. <br/><br/> Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 <br/> Dimension : BlobType ([Définition](#metrics-dimensions)) |
| ContainerCount    | Nombre de conteneurs dans le compte de stockage. <br/><br/> Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |

### <a name="table-storage"></a>Stockage de tables

| Nom de métrique | Description |
| ------------------- | ----------------- |
| TableCapacity | Quantité de stockage de tables utilisée par le compte de stockage. <br/><br/> Unité : octets <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |
| TableCount   | Nombre de tables dans le compte de stockage. <br/><br/> Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |
| TableEntityCount | Nombre d’entités de table dans le compte de stockage. <br/><br/> Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |

### <a name="queue-storage"></a>Stockage de files d'attente

| Nom de métrique | Description |
| ------------------- | ----------------- |
| QueueCapacity | Quantité de stockage de files d’attente utilisée par le compte de stockage. <br/><br/> Unité : octets <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |
| QueueCount   | Nombre de files d’attente dans le compte de stockage. <br/><br/> Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |
| QueueMessageCount | Nombre de messages de file d’attente non expirés dans le compte de stockage. <br/><br/>Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |

### <a name="file-storage"></a>Stockage Fichier

| Nom de métrique | Description |
| ------------------- | ----------------- |
| FileCapacity | Quantité de stockage de fichiers utilisée par le compte de stockage. <br/><br/> Unité : octets <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |
| FileCount   | Nombre de fichiers dans le compte de stockage. <br/><br/> Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |
| FileShareCount | Nombre de partages de fichiers dans le compte de stockage. <br/><br/> Unité : nombre <br/> Type d’agrégation : moyenne <br/> Exemple de valeur : 1024 |

## <a name="transaction-metrics"></a>Mesures de transaction

Les mesures de transaction sont envoyées de Stockage Azure à Azure Monitor toutes les minutes. Toutes les mesures de transaction sont disponibles au niveau du compte et au niveau du service (stockage d’objets blob, stockage de tables, fichiers Azure et stockage de files d’attente). Le fragment de temps définit l’intervalle de temps auquel des valeurs de mesures sont présentées. Les fragments de temps pris en charge pour toutes les mesures de transaction sont PT1H et PT1M.

Stockage Azure fournit les mesures de transaction suivantes dans Azure Monitor.

| Nom de métrique | Description |
| ------------------- | ----------------- |
| Transactions | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. <br/><br/> Unité : nombre <br/> Type d’agrégation : total <br/> Dimensions applicables : ResponseType, GeoType, ApiName ([Définition](#metrics-dimensions))<br/> Exemple de valeur : 1024 |
| Entrée | Quantité de données d’entrée. Ce nombre inclut les entrées d’un client externe dans Stockage Azure ainsi que les entrées dans Azure. <br/><br/> Unité : octets <br/> Type d’agrégation : total <br/> Dimensions applicables : GeoType, ApiName ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1024 |
| Sortie | Quantité de données de sortie. Ce nombre inclut les sorties d’un client externe dans Stockage Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. <br/><br/> Unité : octets <br/> Type d’agrégation : total <br/> Dimensions applicables : GeoType, ApiName ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1024 |
| SuccessServerLatency | Durée moyenne utilisée pour traiter une requête réussie par Stockage Azure. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency. <br/><br/> Unité : millisecondes <br/> Type d’agrégation : moyenne <br/> Dimensions applicables : GeoType, ApiName ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1024 |
| SuccessE2ELatency | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée. Cette valeur inclut le temps de traitement requis au sein de Stockage Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. <br/><br/> Unité : millisecondes <br/> Type d’agrégation : moyenne <br/> Dimensions applicables : GeoType, ApiName ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1024 |
| Disponibilité | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris les requêtes qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. <br/><br/> Unité : pourcentage <br/> Type d’agrégation : moyenne <br/> Dimensions applicables : GeoType, ApiName ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 99.99 |

## <a name="metrics-dimensions"></a>Dimensions de mesures

Stockage Azure prend en charge les dimensions suivantes pour les mesures dans Azure Monitor.

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| /BlobType | Type d’objet blob pour les mesures d’objet Blob uniquement. Les valeurs prises en charge sont **BlockBlob** et **PageBlob**. Append Blob est inclus dans BlockBlob. |
| ResponseType | Type de réponse de transaction. Les valeurs disponibles incluent : <br/><br/> <li>ServerOtherError : toutes les autres erreurs côté serveur sauf celles décrites </li> <li> ServerBusyError : requête authentifiée qui a renvoyé un code d’état HTTP 503. (Pas encore pris en charge) </li> <li> ServerTimeoutError : requête authentifiée arrivée à expiration qui a renvoyé un code d’état HTTP 500. Le délai d’expiration s’est produit en raison d’une erreur serveur. </li> <li> ThrottlingError : somme des erreurs de limitation côté client et côté serveur (elle sera supprimée lorsque ServerBusyError et ClientThrottlingError seront pris en charge) </li> <li> AuthorizationError : requête authentifiée qui a échoué en raison d’un accès aux données non autorisé ou d’un échec d’autorisation. </li> <li> NetworkError : requête authentifiée qui a échoué en raison d’erreurs réseau. Se produit généralement lorsqu’un client ferme une connexion avant la fin du délai d’expiration. </li> <li>  ClientThrottlingError : erreur de limitation côté client (pas encore pris en charge) </li> <li> ClientTimeoutError : requête authentifiée arrivée à expiration qui a renvoyé un code d’état HTTP 500. Si le délai d’expiration réseau du client ou le délai d’expiration de la requête est défini sur une valeur inférieure à ce qui est attendu par le service de stockage, il s’agit d’un délai d’expiration attendu. Sinon, il est signalé comme une erreur ServerTimeoutError. </li> <li> ClientOtherError : toutes les autres erreurs côté client sauf celles décrites. </li> <li> Réussite : requête réussie|
| GeoType | Transaction du cluster principal ou secondaire. Les valeurs disponibles incluent Principal et Secondaire. S’applique au stockage Géo-redondant avec accès en lecture (RA-GRS) lors de la lecture d’objets à partir du locataire secondaire. |
| ApiName | Nom de l’opération. Par exemple : <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Pour tous les noms d’opérations, voir [document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

Pour les mesures prenant en charge des dimensions, vous devez spécifier la valeur de la dimension pour afficher les valeurs de mesures correspondantes. Par exemple, si vous examinez la valeur **Transactions** pour des réponses réussies, vous devez filtrer la dimension **ResponseType** avec **Success**. Si vous examinez la valeur **BlobCount** pour BlockBlob, vous devez filtrer la dimension **BlobType** avec **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuité de service d’anciennes mesures

Les anciennes mesures sont disponibles en parallèle avec les mesures gérées d’Azure Monitor. La prise en charge permet de les conserver jusqu’à ce que Stockage Azure mette fin au service des anciennes mesures. Nous annoncerons le plan de fin de prise en charge après la publication officielle des mesures gérées d’Azure Monitor.

## <a name="see-also"></a>Voir aussi

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
