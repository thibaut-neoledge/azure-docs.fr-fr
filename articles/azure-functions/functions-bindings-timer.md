---
title: "Déclencheur de minuteur Azure Functions| Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs de minuteur dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6a97ab8508f889b77d064a5da70e3c726d62900c
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="azure-functions-timer-trigger"></a>Déclencheur de minuteur Azure Functions

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des déclencheurs de minuteur dans Azure Functions. Azure Functions offre une liaison de déclencheur de minuteur qui vous permet d’exécuter votre code de fonction selon une planification définie. 

Le déclencheur du minuteur prend en charge la montée en charge multi-instance. Une instance unique d’une fonction de minuteur spécifique est exécutée sur toutes les instances.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Déclencheur de minuteur
Le déclencheur de minuteur d’une fonction utilise l’objet JSON suivant dans le tableau `bindings` de function.json :

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

La valeur de `schedule` est une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) qui contient les six champs suivants : 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>De nombreuses expressions CRON disponibles en ligne omettent le champ `{second}`. Si vous effectuez une copie à partir de l’une d’entre elles, vous devez l’adapter de façon à prendre en compte le champ `{second}` supplémentaire. Pour obtenir des exemples spécifiques, consultez la section [Exemples de planification](#examples) plus bas.

Le fuseau horaire par défaut utilisé avec les expressions CRON est le Temps universel coordonné (UTC). Pour baser votre expression CRON sur un autre fuseau horaire, créez un nouveau paramètre d’application nommé `WEBSITE_TIME_ZONE` pour votre application de fonction. Définissez la valeur sur le nom du fuseau horaire souhaité comme indiqué dans l’[index des fuseaux horaires de Microsoft](https://msdn.microsoft.com/library/ms912391.aspx). 

Par exemple, *l’heure de l’Est* correspond à UTC-05:00. Pour que votre déclencheur de minuteur se déclenche chaque jour à 10 h 00 (heure de l’Est), vous pouvez utiliser l’expression CRON suivante, qui tient compte du fuseau horaire UTC :

```json
"schedule": "0 0 15 * * *",
``` 

Sinon, vous pouvez ajouter un nouveau paramètre d’application pour votre application de fonction nommé `WEBSITE_TIME_ZONE` et définir la valeur sur **Est**.  L’expression CRON suivante peut alors être utilisée pour 10h00 EST : 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>Exemples de planification :
Voici quelques exemples d’expressions CRON que vous pouvez utiliser pour la propriété `schedule`. 

Pour déclencher la fonction toutes les cinq minutes :

```json
"schedule": "0 */5 * * * *"
```

Pour déclencher la fonction toutes les heures :

```json
"schedule": "0 0 * * * *",
```

Pour déclencher la fonction toutes les deux heures:

```json
"schedule": "0 0 */2 * * *",
```

Pour déclencher la fonction toutes les heures entre 9h et 17h :

```json
"schedule": "0 0 9-17 * * *",
```

Pour déclencher la fonction à 9h30 tous les jours :

```json
"schedule": "0 30 9 * * *",
```

Pour déclencher la fonction à 9h30 tous les jours de la semaine :

```json
"schedule": "0 30 9 * * 1-5",
```

<a name="usage"></a>

## <a name="trigger-usage"></a>Utilisation du déclencheur
Lorsqu’une fonction de déclenchement du minuteur est appelée, [l’objet minuteur](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) est transmis à la fonction. Le code JSON suivant est un exemple de représentation de l’objet minuteur. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

<a name="sample"></a>

## <a name="trigger-sample"></a>Exemple de déclencheur
Supposez que le tableau `bindings` de function.json contient le déclencheur de minuteur suivant :

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Consultez l’exemple, dans le langage de votre choix, où l’objet minuteur est lu pour déterminer s’il s’exécute avec du retard.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>Exemple de déclencheur en C# #
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Exemple de déclencheur en F# #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Exemple de déclencheur en Node.js
```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


