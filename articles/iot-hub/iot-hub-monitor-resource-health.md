---
title: "Surveiller l’intégrité de votre Azure IoT Hub | Microsoft Docs"
description: "Utilisez Azure Monitor et Azure Resource Health pour surveiller votre IoT Hub et diagnostiquer rapidement les problèmes"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 3051af03d0c1433db98bcc674a072188e7ce80e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Surveiller l’intégrité d’Azure IoT Hub et diagnostiquer rapidement les problèmes

Les entreprises qui implémentent Azure IoT Hub s’attendent à ce que leurs ressources fournissent des performances fiables. Pour vous aider à garder un œil attentif sur vos opérations, IoT Hub est entièrement intégré dans [Azure Monitor][lnk-AM] et [Azure Resource Health][lnk-ARH]. Ces deux services fonctionnent de concert pour vous fournir les données nécessaires au bon fonctionnement de vos solutions IoT. 

Azure Monitor constitue une source unique pour la surveillance et la journalisation de tous vos services Azure. Vous pouvez envoyer les journaux générés par Azure Monitor à OMS Log Analytics, Event Hubs ou Stockage Azure pour un traitement personnalisé. Les paramètres de mesures et de diagnostic d’Azure Monitor vous offrent une visibilité en temps réel des performances de vos ressources. Poursuivez la lecture de cet article pour savoir comment [utiliser Azure Monitor](#use-azure-monitor) avec votre IoT Hub. 

Azure Resource Health vous aide à diagnostiquer les problèmes et à accéder au support quand un problème Azure a une incidence sur vos ressources. Un tableau de bord personnalisé indique l’état d’intégrité actuel et passé de votre IoT Hub. Poursuivez la lecture de cet article pour savoir comment [utiliser Azure Resource Health](#use-azure-resource-health) avec votre IoT Hub. 

Outre l’intégration dans ces deux services, IoT Hub propose également ses propres mesures pour vous aider à comprendre l’état de vos ressources IoT. Pour plus d’informations, consultez [Comprendre les mesures IoT Hub][lnk-metrics].

## <a name="use-azure-monitor"></a>Utiliser Azure Monitor

Azure Monitor fournit des informations de diagnostic au niveau des ressources, ce qui vous permet de contrôler les opérations qui ont lieu au sein de votre IoT Hub. 

Les paramètres de diagnostic d’Azure Monitor remplacent le moniteur d’opérations d’IoT Hub. Si vous surveillez actuellement les opérations de l’utilisateur, vous devez migrer votre flux de travail. Pour plus d’informations, consultez [Migrer de la surveillance des opérations vers les paramètres de diagnostic][lnk-migrate].

Pour en savoir plus sur les mesures et les événements spécifiques surveillés par Azure Monitor, consultez [Mesures prises en charge avec Azure Monitor][lnk-AM-metrics] et [Services, schémas et catégories pris en charge pour les journaux de diagnostic Azure][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Comprendre les journaux

Azure Monitor effectue le suivi des différentes opérations qui se produisent dans IoT Hub. Chaque catégorie dispose d’un schéma qui définit la façon dont les événements de cette catégorie sont signalés. 

#### <a name="connections"></a>Connexions

La catégorie de connexions effectue le suivi des erreurs provoquées par la connexion des appareils à un hub IoT ou leur déconnexion de celui-ci. Le suivi de cette catégorie est utile pour identifier les tentatives de connexion non autorisées et pour repérer les moments auxquels une connexion est perdue pour les appareils qui se trouvent dans des zones bénéficiant d’une connectivité médiocre.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Commandes cloud-à-appareil

La catégorie de commandes cloud-à-appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de messages cloud-à-appareil. Cette catégorie inclut notamment les erreurs concernant l’envoi de messages cloud-à-appareil (telles qu’un expéditeur non autorisé), la réception des messages cloud-à-appareil (telles que le dépassement du nombre de remises) et la réception des commentaires de message cloud-à-appareil (telles que des commentaires arrivés à expiration). Cette catégorie n’intercepte pas d’erreurs dans le cas d’un appareil qui gère mal un message cloud-à-appareil si le message a été correctement remis.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Opérations d’identité des appareils

La catégorie d’opérations d’identité des appareils effectue le suivi des erreurs qui se produisent quand vous tentez de créer, mettre à jour ou supprimer une entrée dans le registre d’identité de votre hub IoT. Le suivi de cette catégorie est utile pour les scénarios d’approvisionnement.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>Itinéraires

La catégorie de routage des messages assure le suivi des erreurs qui se produisent pendant l’évaluation du routage des messages et de l’intégrité du point de terminaison perçue par IoT Hub. Cette catégorie inclut les événements tels qu’une règle prenant la valeur « Non définie », lorsqu’IoT Hub marque un point de terminaison comme inactif et toute autre erreur reçue à partir d’un point de terminaison. Cette catégorie n’inclut pas les erreurs spécifiques sur les messages eux-mêmes (telles que les erreurs de limitation des appareils) qui sont signalées dans la catégorie « télémétrie des appareils ».

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Télémétrie d’appareil

La catégorie de télémétrie d’appareil effectue le suivi des erreurs qui se produisent au niveau du hub IoT et qui sont liées au pipeline de télémétrie. Cette catégorie inclut notamment les erreurs concernant l’envoi d’événements de télémétrie (par exemple, une limitation) et la réception des événements de télémétrie (par exemple, un lecteur non autorisé). Cette catégorie ne peut pas intercepter les erreurs provoquées par le code en cours d’exécution sur l’appareil lui-même.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>Opérations de téléchargement de fichier

La catégorie de chargement de fichiers effectue le suivi des erreurs qui se produisent au niveau de l’IoT hub et qui sont liées à la fonctionnalité de chargement. Cette catégorie inclut :

* Erreurs qui se produisent avec l’URI SAP, par exemple en cas d’expiration avant qu’un appareil notifie le hub d’un téléchargement terminé.
* Échecs des téléchargements signalés par l’appareil.
* Erreurs qui se produisent lorsqu’un fichier est introuvable dans le stockage lors de la création du message de notification IoT Hub.

Cette catégorie ne peut pas détecter les erreurs qui surviennent directement pendant que l’appareil charge un fichier de stockage.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>Opérations jumelles cloud-à-appareil

La catégorie des opérations jumelles cloud-à-appareil effectue le suivi des événements initiés par le service sur des jumeaux d’appareil. Ces opérations peuvent inclure ; obtenir un jumeau, mettre à jour les propriétés signalées et s’abonner aux propriétés souhaitées

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>Opérations jumelles appareil-à-cloud

La catégorie des opérations jumelles appareil-à-cloud effectue le suivi des événements initiés par l’appareil sur des jumeaux d’appareil. Ces opérations peuvent inclure : obtenir un jumeau, mettre à jour ou remplacer des balises, et mettre à jour ou remplacer les propriétés souhaitées. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>Requêtes de jumeaux

La catégorie des requêtes de jumeaux génère un rapport sur les demandes de requêtes pour jumeaux d’appareil lancées dans le cloud. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>Opérations de travaux

La catégorie des opérations de travaux génère un rapport sur les demandes de travaux pour mettre à jour des jumeaux d’appareil ou appeler des méthodes directes sur plusieurs appareils. Ces demandes sont lancées dans le cloud. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>Méthodes directes

La catégorie des méthodes directes effectue le suivi des interactions demande-réponse envoyées à des appareils individuels. Ces demandes sont lancées dans le cloud. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Lecture de journaux à partir d’Azure Event Hubs

Après avoir configuré la journalisation des événements via les paramètres de diagnostic, vous pouvez créer des applications qui lisent les journaux, pour vous permettre d’agir en fonction des informations qu’ils contiennent. Cet exemple de code extrait les journaux à partir d’un concentrateur d’événements :

```
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
    static void Main(string[] args) 
    { 
        Console.WriteLine("Monitoring. Press Enter key to exit.\n"); 
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName); 
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds; 
        CancellationTokenSource cts = new CancellationTokenSource(); 
        var tasks = new List<Task>(); 
        foreach (string partition in d2cPartitions) 
        { 
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token)); 
        } 
        Console.ReadLine(); 
        Console.WriteLine("Exiting..."); 
        cts.Cancel(); 
        Task.WaitAll(tasks.ToArray()); 
    } 
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct) 
    { 
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow); 
        while (true) 
        { 
            if (ct.IsCancellationRequested) 
            { 
                await eventHubReceiver.CloseAsync(); 
                break; 
            } 
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10)); 
            if (eventData != null) 
            { 
                string data = Encoding.UTF8.GetString(eventData.GetBytes()); 
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data); 
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Utilisation d’Azure Resource Health

Utilisez Azure Resource Health pour vérifier si votre IoT Hub est opérationnel. Vous pouvez également vérifier si une panne régionale affecte l’intégrité de votre IoT Hub. Pour obtenir des détails spécifiques sur l’état d’intégrité de votre Azure IoT Hub, nous vous recommandons d[’utiliser Azure Monitor](#use-azure-monitor). 

Azure IoT Hub indique l’intégrité au niveau régional. Si une panne régionale affecte votre IoT Hub, l’état d’intégrité apparaît sous la forme **Unknown**. Pour en savoir plus sur les vérifications d’intégrité spécifique effectuées par Azure Resource Health, consultez [Types de ressources et contrôles d’intégrité dans Azure Resource Health][lnk-ARH-checks].

Pour vérifier l’intégrité de vos IoT Hubs, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Service Health** > **Resource Health**.
1. Dans les listes déroulantes, sélectionnez votre abonnement et **IoT Hub**.

Pour en savoir plus sur la façon d’interpréter les données d’intégrité, consultez [Vue d’ensemble d’Azure Resource Health][lnk-ARH]

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre les mesures IoT Hub][lnk-metrics]
- [Surveillance à distance IoT et notifications avec Azure Logic Apps connectant votre IoT Hub et votre boîte aux lettres][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md