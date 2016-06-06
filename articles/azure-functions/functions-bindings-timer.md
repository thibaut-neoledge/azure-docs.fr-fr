<properties
	pageTitle="Déclencheur de minuteur Azure Functions| Microsoft Azure"
	description="Découvrez comment utiliser des déclencheurs de minuteur dans Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, fonctions, traitement des événements, calcul dynamique, architecture sans serveur"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Déclencheur de minuteur Azure Functions

Cet article explique comment configurer des déclencheurs de minuteur dans Azure Functions. Les déclencheurs de minuteur appellent des fonctions basées sur une planification ponctuelle ou périodique.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## Fichier function.json pour le déclencheur de minuteur

Le fichier *function.json* fournit une expression schedule et un commutateur qui indique si la fonction doit ou non se déclencher immédiatement.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Le déclencheur de minuteur gère automatiquement l’augmentation de la taille des instances multi-instance : une seule instance d’une fonction de minuteur spécifique s’exécutera dans l’ensemble des instances.

## Format de l’expression schedule

L’expression schedule peut correspondre à une [expression CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) comportant 6 champs : {seconde} {minute} {heure} {jour} {mois} {jour de la semaine}. De nombreux documents d’expression cron disponibles en ligne omettent le champ {seconde} ; par conséquent, si vous copiez une expression de l’un de ces documents, vous devrez adapter votre code pour y inclure ce champ supplémentaire.

L’expression schedule peut également être au format *hh:mm:ss* afin de spécifier le délai entre chaque déclenchement de la fonction.

Voici quelques exemples d’expressions schedule.

Pour déclencher la fonction toutes les 5 minutes :

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Pour déclencher la fonction immédiatement, puis toutes les deux heures par la suite :

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Pour déclencher la fonction toutes les 15 secondes :

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

## Exemple de code C# de déclencheur de minuteur

Cet exemple de code C# écrit un journal spécifique chaque fois que la fonction est déclenchée.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Étapes suivantes

[AZURE.INCLUDE [Étapes suivantes](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0525_2016-->