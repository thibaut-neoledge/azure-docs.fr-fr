---
title: "Déclencheur de minuteur Azure Functions| Microsoft Docs"
description: "Découvrez comment utiliser des déclencheurs de minuteur dans Azure Functions."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: b41a5aacec6748af5ee05b01487310cc339af1f9
ms.openlocfilehash: 542e5378aff893741a68c979bc2c5e8bfe58ba26


---
# <a name="azure-functions-timer-trigger"></a>Déclencheur de minuteur Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Cet article explique comment configurer et coder des déclencheurs de minuteur dans Azure Functions. Azure Functions prend en charge le déclencheur pour les minuteurs. Les déclencheurs de minuteur appellent des fonctions basées sur une planification ponctuelle ou périodique. 

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

La valeur de `schedule` est une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) qui contient 6 champs : `{second} {minute} {hour} {day} {month} {day of the week}`. De nombreuses expressions CRON disponibles en ligne omettent le champ `{second}`. Si vous effectuez une copie à partir de l’une d’entre elles, vous devez l’adapter de façon à prendre en compte le champ `{second}` supplémentaire. Pour obtenir des exemples spécifiques, consultez la section [Exemples de planification](#examples) plus bas.

Le fuseau horaire par défaut utilisé avec les expressions CRON est le Temps universel coordonné (UTC). Si vous souhaitez que votre expression CRON soit basée sur un autre fuseau horaire, créez un nouveau paramètre d’application pour votre application de fonction, nommé `WEBSITE_TIME_ZONE`. Définissez la valeur sur le nom du fuseau horaire souhaité comme indiqué dans le [l’index des fuseaux horaires de Microsoft](https://msdn.microsoft.com/library/ms912391.aspx). 

Par exemple, *l’heure de l’Est* correspond à UTC-05:00. Si vous souhaitez que votre déclencheur de minuteur se déclenche à 10h00 selon l’heure de l’Est chaque jour, vous pouvez utiliser l’expression CRON suivante, qui tient compte du fuseau horaire UTC :

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

Pour déclencher la fonction toutes les 5 minutes :

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




<!--HONumber=Nov16_HO3-->


