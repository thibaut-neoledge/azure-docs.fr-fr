<properties
   pageTitle="Scénario d’application logique : déclencheur Service Bus de fonctions Azure | Microsoft Azure"
   description="Découvrez comment utiliser les fonctions Azure comme déclencheur Service Bus pour vos applications logiques"
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# Scénario d’application logique : déclencheur Service Bus de fonctions Azure

Les fonctions Azure peuvent être utilisées comme déclencheur pour les applications logiques chaque fois que vous avez besoin de déployer des écouteurs ou des tâches de longue durée. Par exemple, vous pouvez créer une fonction Azure qui écoute une file d’attente, puis déclenche immédiatement une application logique en tant que déclencheur d’émission.

## Développement de l’application logique

Dans cet exemple, une fonction est en cours d’exécution pour chaque application logique à déclencher. Vous devez donc tout d’abord créer une application logique avec un déclencheur de requête HTTP (la fonction Azure appellera ce point de terminaison à chaque réception d’un message de la file d’attente).

1. Ouvrez une nouvelle application logique et sélectionnez le déclencheur **Manual - When an HTTP Request is Received** (Manuel - À réception d’une requête HTTP).  
    * Vous pouvez éventuellement spécifier un schéma JSON qu’utilisera le message de la file d’attente via un outil tel que [jsonschema.net](http://jsonschema.net), puis le coller dans le déclencheur. Cela permet au concepteur de comprendre la forme des données et de répercuter facilement les propriétés dans le workflow.
1. Ajouter les étapes que vous souhaitez activer à la réception d’un message de la file d’attente. Par exemple, vous pouvez envoyer un message électronique via Office 365.  
1. Enregistrez l’application logique pour générer l’URL de rappel du déclencheur sur cette application logique. L’URL s’affiche sur la carte de déclencheur.

![][1]

## Création de la fonction Azure

Vous devez ensuite créer une fonction Azure qui agit comme déclencheur et qui écoute la file d’attente.

1. Ouvrez le [portail Azure Functions](https://functions.azure.com/signin) et sélectionnez **Nouvelle fonction** avec un modèle **ServiceBusQueueTrigger - C#**.

    ![][2]

2. Configurez la connexion à la file d’attente Service Bus (qui utilisera l’écouteur `OnMessageReceive()` du SDK Service Bus)
3. Écrivez une fonction simple pour appeler le point de terminaison de l’application logique (ci-dessus) avec le message de la file d’attente. Voici un exemple complet d’une fonction avec un contenu de message de type `application/json`,que vous pouvez cependant modifier selon vos besoins.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Vous pouvez tester en ajoutant un message de la file d’attente via un outil tel que [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) et voir l’application logique se déclencher immédiatement à réception du message.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->