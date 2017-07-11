---
title: "Envoyer des événements à votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Ce didacticiel explique comment envoyer des événements à votre environnement Time Series Insights."
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9f2d3b57a42efb7b04566278d3267b3cdbed713a
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---
<a id="send-events-to-a-time-series-insights-environment-via-event-hub" class="xliff"></a>

# Envoyer des événements à un environnement Time Series Insights via un concentrateur d’événements

Ce didacticiel explique comment créer et configurer le concentrateur d’événements et exécuter un exemple d’application pour envoyer des événements. Si vous disposez d’un concentrateur d’événements existant qui a déjà des événements au format JSON, ignorez ce didacticiel et affichez votre environnement dans [time series insights](https://insights.timeseries.azure.com).

<a id="configure-an-event-hub" class="xliff"></a>

## Configurer un concentrateur d’événements
1. Pour créer un concentrateur d’événements, suivez les instructions de la [documentation](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) relative aux concentrateurs d’événements.

2. Veillez à créer un groupe de consommateurs qui sera utilisé exclusivement par votre source d’événement Time Series Insights.

  > [!IMPORTANT]
  > Assurez-vous que ce groupe de consommateurs n’est pas utilisé par un autre service (par exemple, une tâche Stream Analytics ou un autre environnement Time Series Insights). Si le groupe de consommateurs est utilisé par d’autres services, l’opération de lecture est affectée pour cet environnement et les autres services. Si vous utilisez le groupe de consommateurs « $Default », ceci peut entraîner une réutilisation potentielle par d’autres lecteurs.

  ![Sélectionnez le groupe de consommateurs du concentrateur d’événements](media/send-events/consumer-group.png)

3. Dans le concentrateur d’événements, créez la stratégie « MySendPolicy » utilisée pour envoyer des événements dans l’exemple csharp.

  ![Sélectionnez des stratégies d’accès partagé et cliquez sur le bouton Ajouter](media/send-events/shared-access-policy.png)  

  ![Ajoutez une stratégie d’accès partagé](media/send-events/shared-access-policy-2.png)  

<a id="create-time-series-insights-event-source" class="xliff"></a>

## Créer la source d’événement Time Series Insights
1. Si vous n’avez pas créé de source d’événement, suivez les instructions spécifiées [ici](time-series-insights-add-event-source.md) pour créer une source d’événement.

2. Spécifiez « deviceTimestamp » comme nom de la propriété timestamp. Cette propriété définit l’horodatage réel dans l’exemple csharp. Le nom de la propriété timestamp est sensible à la casse et les valeurs doivent être au format __aaaa-MM-jjTHH:mm:ss.FFFFFFFK__ lors de l’envoi au format JSON au concentrateur d’événements. Si la propriété n’existe pas dans l’événement, le système utilise l’heure à laquelle l’événement a été placé dans la file d’attente du concentrateur d’événements.

  ![Créez la source d’événement](media/send-events/event-source-1.png)

<a id="sample-code-to-push-events" class="xliff"></a>

## Exemple de code pour envoyer des événements
1. Accédez à la stratégie de concentrateur d’événements « MySendPolicy » et copiez la chaîne de connexion avec la clé de stratégie.

  ![Copiez la chaîne de connexion MySendPolicy](media/send-events/sample-code-connection-string.png)

2. Exécutez le code suivant qui envoie 600 événements pour chacun des trois appareils. Mettez à jour `eventHubConnectionString` avec votre chaîne de connexion.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
<a id="supported-json-shapes" class="xliff"></a>

## Structures JSON prises en charge
<a id="sample-1" class="xliff"></a>

### Exemple 1

<a id="input" class="xliff"></a>

#### Entrée

Un objet JSON simple.

```json
{
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
<a id="output---1-event" class="xliff"></a>

#### Sortie - 1 événement

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

<a id="sample-2" class="xliff"></a>

### Exemple 2

<a id="input" class="xliff"></a>

#### Entrée
Un tableau JSON avec deux objets JSON. Chaque objet JSON sera converti en un événement.
```json
[
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    },
    {
        "id":"device2",
        "timestamp":"2016-01-17T01:17:00Z"
    }
]
```
<a id="output---2-events" class="xliff"></a>

#### Sortie - 2 événements

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|
<a id="sample-3" class="xliff"></a>

### Exemple 3

<a id="input" class="xliff"></a>

#### Entrée

Un objet JSON avec un tableau JSON imbriqué contenant deux objets JSON.
```json
{
    "location":"WestUs",
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
<a id="output---2-events" class="xliff"></a>

#### Sortie - 2 événements
Notez que la propriété « location » est copiée dans chacun des événements.

|location|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

<a id="sample-4" class="xliff"></a>

### Exemple 4

<a id="input" class="xliff"></a>

#### Entrée

Un objet JSON avec un tableau JSON imbriqué contenant deux objets JSON. Cette entrée montre que les propriétés globales peuvent être représentées par l’objet JSON complexe.

```json
{
    "location":"WestUs",
    "manufacturer":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z",
            "data":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z",
            "data":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
<a id="output---2-events" class="xliff"></a>

#### Sortie - 2 événements

|location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

* Afficher votre environnement dans le [Portail Time Series Insights](https://insights.timeseries.azure.com)

