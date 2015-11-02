<properties 
	pageTitle="Utilisation des files d’attente Service Bus dans Node.js | Microsoft Azure" 
	description="Découvrez comment utiliser les files d'attente Service Bus dans Azure à partir d’une application Node.js." 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="10/06/2015" 
	ms.author="sethm"/>

# Utilisation des files d’attente Service Bus

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Cet article décrit l’utilisation des files d’attente Service Bus. Les exemples sont écrits en JavaScript et utilisent le module Azure Node.js. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente**, **envoi et réception de messages** et **suppression de files d'attente**. Pour plus d’informations sur les files d’attente, consultez la section [Étapes suivantes][].

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir des instructions sur la création d’une application Node.js, consultez les pages [Création et déploiement d’une application Node.js sur un site web Azure][] ou [Service cloud Node.js][] (avec Windows PowerShell).

## Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser Azure Service Bus, téléchargez et utilisez le package Azure Node.js. Ce dernier inclut des bibliothèques permettant de communiquer avec les services REST de Service Bus.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1. Utilisez la fenêtre de commande **Windows PowerShell pour Node.js** pour accéder au dossier **c:\\node\\sbqueues\\WebRole1** dans lequel vous avez créé votre exemple d’application.

2. Tapez **npm install azure** dans la fenêtre de commande, ce qui génère un résultat similaire à ce qui suit :

	```
	azure@0.7.5 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── node-uuid@1.2.0
		├── mime@1.2.9
		├── underscore@1.4.4
		├── validator@1.1.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
	```

3. Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node\_modules** a été créé. Dans ce dossier, recherchez le package **azure**, qui contient les bibliothèques nécessaires pour accéder aux files d'attente Service Bus.

### Importation du module

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application :

```
var azure = require('azure');
```

### Configuration d’une connexion Service Bus Azure

Le module Azure lit les variables d'environnement AZURE\_SERVICEBUS\_NAMESPACE et AZURE\_SERVICEBUS\_ACCESS\_KEY pour obtenir les informations nécessaires à la connexion à Service Bus. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l’appel de **createServiceBusService**.

Pour consulter un exemple de paramétrage de variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez la page [Service cloud Node.js avec stockage][].

Pour obtenir un exemple de configuration des variables d’environnement dans le portail Azure pour un site web Azure, consultez la rubrique [Application web Node.js avec stockage][].

## Création d’une file d’attente

L’objet **ServiceBusService** permet d’utiliser des files d’attente Service Bus. Le code suivant crée un objet **ServiceBusService**. Ajoutez-le vers le début du fichier **server.js**, après l’instruction relative à l’importation du module Azure :

```
var serviceBusService = azure.createServiceBusService();
```

En appelant **createQueueIfNotExists** sur l’objet **ServiceBusService**, la file d’attente spécifiée est renvoyée (si elle existe) ou une file d’attente comportant le nom spécifié est créée. Le code suivant utilise **createQueueIfNotExists** pour créer la file d’attente nommée `myqueue` ou s’y connecter :

```
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

**createServiceBusService** prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de file d’attente par défaut comme la durée de vie du message ou la taille maximale de la file d’attente. L’exemple suivant définit la taille maximale de la file d’attente sur 5 Go et la durée de vie de message sur 1 minute :

```
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées par le biais de **ServiceBusService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

```
function handle (requestOptions, next)
```

Après le prétraitement des options de la requête, la méthode doit appeler `next` en passant un rappel avec la signature suivante :

```
function (returnObject, finalCallback, next)
```

Dans ce rappel, et après le traitement de **returnObject** (la réponse à la requête du serveur), le rappel doit appeler `next`, s’il existe, pour continuer à traiter d’autres filtres ou appeler `finalCallback` pour terminer l’appel du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

```
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## Envoi de messages à une file d'attente

Pour envoyer un message à une file d’attente Service Bus, votre application appelle la méthode **sendQueueMessage** sur l’objet **ServiceBusService**. Les messages envoyés aux files d'attente Service Bus (et reçus de celles-ci) sont les objets **BrokeredMessage**. Ils possèdent un ensemble de propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en passant une chaîne comme message. Toutes les propriétés standard requises sont remplies avec les valeurs par défaut.

L’exemple suivant indique comment envoyer un message test à la file d’attente nommée `myqueue` au moyen de **sendQueueMessage** :

```
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go. Pour plus d’informations sur les quotas, consultez [Files d’attente Azure et files d’attente Service Bus][].

## Réception des messages d'une file d'attente

La méthode **receiveQueueMessage** de l'objet **ServiceBusService** permet de recevoir les messages d'une file d'attente. Par défaut, les messages sont supprimés de la file d'attente au fur et à mesure de leur lecture. Cependant, vous pouvez lire et verrouiller le message sans le supprimer de la file d'attente en définissant le paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

Si le paramètre **isPeekLock** est défini sur **true**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **deleteMessage** et en fournissant le message à supprimer sous la forme d'un paramètre. La méthode **deleteMessage** marque le message comme étant consommé et le supprime de la file d'attente.

L’exemple suivant montre comment recevoir et traiter des messages à l’aide de **receiveQueueMessage**. L’exemple reçoit et supprime d’abord un message, reçoit un message en utilisant **isPeekLock** défini sur **true**, puis supprime le message au moyen de **deleteMessage** :

```
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlockMessage** pour l'objet **ServiceBusService**. Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la méthode **deleteMessage**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

-   [Files d’attente, rubriques et abonnements.][]
-   Référentiel du [Kit de développement logiciel (SDK) Azure pour Node][] sur GitHub.
-   [Centre de développement Node.js](/develop/nodejs/)

  [Kit de développement logiciel (SDK) Azure pour Node]: https://github.com/Azure/azure-sdk-for-node
  [Azure portal]: http://manage.windowsazure.com
  
  [Service cloud Node.js]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Files d’attente, rubriques et abonnements.]: service-bus-queues-topics-subscriptions.md
  [Création et déploiement d’une application Node.js sur un site web Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Service cloud Node.js avec stockage]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
  [Application web Node.js avec stockage]: ../storage/storage-nodejs-how-to-use-table-storage.md
  [Files d’attente Azure et files d’attente Service Bus]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas
 

<!---HONumber=Oct15_HO4-->