<properties
   pageTitle="Scénario d’application logique : créer un déclencheur Service Bus d’Azure Functions | Microsoft Azure"
   description="Utiliser Azure Functions pour créer un déclencheur Service Bus pour une application logique"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# Scénario d’application logique : créer un déclencheur Azure Service Bus à l’aide d’Azure Functions

Vous pouvez utiliser Azure Functions afin de créer un déclencheur pour une application logique lorsque vous avez besoin de déployer un écouteur ou une tâche de longue durée. Par exemple, vous pouvez créer une fonction qui écoute une file d’attente, puis déclenche immédiatement une application logique en tant que déclencheur d’émission.

## Création de l'application logique

Dans cet exemple, une fonction est en cours d’exécution pour chaque application logique à déclencher. Créez d’abord une application logique comportant un déclencheur de demande HTTP. La fonction appelle ce point de terminaison chaque fois qu’un message de la file d’attente est reçu.

1. Créez une application logique puis sélectionnez le déclencheur **Manual - When an HTTP Request is Received** (Manuel - À réception d’une requête HTTP). Si vous le souhaitez, vous pouvez spécifier un schéma JSON à utiliser avec le message de la file d’attente à l’aide d’un outil tel que [jsonschema.net](http://jsonschema.net). Collez le schéma dans le déclencheur. Cela aide le concepteur à comprendre la forme des données et à répercuter plus facilement les propriétés dans le workflow.
1. Ajoutez les étapes supplémentaires à exécuter après réception d’un message de la file d’attente. Par exemple, envoyer un courrier électronique via Office 365.
1. Enregistrez l’application logique pour générer l’URL de rappel du déclencheur sur cette application logique. L’URL s’affiche sur la carte de déclencheur.

![L’URL de rappel s’affiche sur la carte de déclencheur][1]

## Création de la fonction

Vous devez ensuite créer une fonction qui agit comme déclencheur et qui écoute la file d’attente.

1. Dans le [portail Azure Functions](https://functions.azure.com/signin), sélectionnez **Nouvelle fonction** puis choisissez le modèle **ServiceBusQueueTrigger - C#**.

    ![Portail Azure Functions][2]

2. Configurez la connexion à la file d’attente Service Bus (qui utilisera l’écouteur `OnMessageReceive()` du SDK Azure Service Bus).
3. Écrivez une fonction simple pour appeler le point de terminaison de l’application logique (créé précédemment) en utilisant le message de la file d’attente comme déclencheur. Voici un exemple complet d’une fonction. L’exemple utilise un type de contenu de message `application/json`, mais vous pouvez le modifier si nécessaire.

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

Pour tester, ajoutez un message de la file d’attente via un outil tel que l[’Explorateur Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer). Vous remarquerez que l’application logique se déclenche immédiatement dès que la fonction reçoit le message.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0803_2016-->