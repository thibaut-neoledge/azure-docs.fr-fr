<properties 
	pageTitle="Utilisation des files d'attente Service Bus (Node.js) - Azure" 
	description="Découvrez comment utiliser les files d'attente Service Bus dans Azure. Exemples de code écrits en Node.js." 
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
	ms.date="02/10/2015" 
	ms.author="sethm"/>






# Utilisation des files d'attente Service Bus

Ce guide décrit l'utilisation des files d'attente Service Bus. Les exemples sont écrits en JavaScript et utilisent le module Azure Node.js. Les scénarios couverts dans ce guide sont les suivants : **création de files d'attente, envoi et réception de messages** et **suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes].

[AZURE.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure], [Service cloud Node.js][Service cloud Node.js] (avec Windows PowerShell) ou [Site Web avec WebMatrix].

## Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser Azure Service Bus, vous devez télécharger et utiliser le package Azure Node.js. Ce dernier inclut des bibliothèques permettant de communiquer avec les services REST de Service Bus.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez la fenêtre de commande **Windows PowerShell pour Node.js** pour accéder au dossier **c:\node\sbqueues\WebRole1** dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure** dans la fenêtre de commande, ce qui génère un résultat similaire à ce qui suit :

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier qu'un dossier **node_modules** a été créé. Dans ce dossier, recherchez le package **azure** qui contient les bibliothèques dont vous avez besoin pour accéder aux files d'attente Service Bus.

### Importation du module

Utilisez le Bloc-notes ou un autre éditeur de texte pour ajouter le code suivant en haut du fichier **server.js** de l'application :

    var azure = require('azure');

### Configuration d'une connection Service Bus Azure

Le module azure lit les variables d'environnement AZURE_SERVICEBUS_NAMESPACE et AZURE_SERVICEBUS_ACCESS_KEY pour obtenir les informations permettant de se connecter à votre compte Azure Service Bus. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte pendant l'appel de **createServiceBusService**.

Pour un exemple de définition des variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez [Service cloud Node.js avec stockage].

Pour un exemple de définition des variables d'environnement dans le portail de gestion pour un site web Azure, consultez [Application web Node.js avec stockage]

## Création d'une file d'attente

L'objet **ServiceBusService** permet d'utiliser des files d'attente. Le code suivant crée un objet **ServiceBusService**. Ajoutez-le au début du fichier **server.js**, après l'instruction relative à l'importation du module azure :

    var serviceBusService = azure.createServiceBusService();

En appelant **createQueueIfNotExists** dans l'objet **ServiceBusService**, la file d'attente spécifiée est renvoyée (si elle existe) ou une file d'attente avec le nom spécifié est créée. Le code suivant utilise **createQueueIfNotExists** pour créer ou se connecter à la file d'attente nommée " myqueue " :

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de file d'attente par défaut comme la durée de vie du message ou la taille maximale de la file d'attente. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur une minute :

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

### Filtres

Des opérations de filtrage facultatives peuvent être appliquées aux opérations effectuées à l'aide de **ServiceBusService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

		function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler " next " en passant un rappel avec la signature suivante :

		function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres implémentant la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js, **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise **ExponentialRetryPolicyFilter** :

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la méthode **sendQueueMessage** sur l'objet **ServiceBusService**. Les messages envoyés aux files d'attente Service Bus (et reçus de celles-ci) sont les objets **BrokeredMessage**. Ils possèdent un ensemble de propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données d'application arbitraires. Une application peut définir le corps du message en transmettant une valeur de chaîne en tant que message pour remplir toutes les propriétés standard requises avec les valeurs par défaut.

L'exemple suivant indique comment envoyer un message test à la file d'attente nommée 'myqueue' au moyen de **sendQueueMessage** :

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

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Ko). Si une file d'attente n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de file d'attente est définie au moment de la création. La limite maximale est de 5 Go.

## Réception des messages d'une file d'attente

La méthode **receiveQueueMessage** de l'objet **ServiceBusService** permet de recevoir les messages d'une file d'attente. Par défaut, les messages sont supprimés de la file d'attente au fur et à mesure de leur lecture. Toutefois, vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de la file d'attente en définissant le paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

Si le paramètre **isPeekLock** est défini sur **true**, l'opération de réception s'effectue en deux étapes, permettant une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application.
Une fois que l'application a terminé le traitement du message (ou l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **deleteMessage** et en fournissant le message à supprimer en tant que paramètre. La méthode **deleteMessage** marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide de **receiveQueueMessage**. L'exemple reçoit et supprime d'abord un message, puis reçoit un message en utilisant **isPeekLock** défini sur true, puis supprime le message au moyen de **deleteMessage** :

    serviceBusService.receiveQueueMessage('taskqueue', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
        }
    });
    serviceBusService.receiveQueueMessage(queueName, { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                }
            }
        }
    });

## Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne peut pas traiter le message pour une raison quelconque, elle peut appeler la méthode **unlockMessage** sur l'objet **ServiceBusService**. Service Bus déverrouille alors le message dans la file d'attente et le rend à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans une file d'attente est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Au cas où l'application se bloque après le traitement du message, mais avant le l'appel de la méthode **deleteMessage**, le message est redistribué à l'application quand elle redémarre. Dans ce type de traitement, appelé **Au moins une fois**, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Cette opération est souvent réalisée à l'aide de la propriété
**MessageId** qui reste constante pendant les tentatives de remise.

## Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][]
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node] sur GitHub.

  [Kit de développement logiciel (SDK) Azure pour Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente Service Bus]: #what-are-service-bus-queues
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Création d'une application Node.js]: #create-app
  [Configuration de votre application pour l'utilisation de Service Bus]: #configure-app
  [Création Création d'une file d'attente]: #create-queue
  [Création Envoi de messages à une file d'attente]: #send-messages
  [Création Réception des messages d'une file d'attente]: #receive-messages
  [Création Gestion des blocages d'application et des messages illisibles]: #handle-crashes
  [Concepts liés aux files d'attente]: ../../dotNet/Media/sb-queues-08.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Service cloud Node.js]: /documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
  [Site web avec WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
[Ancien portail de gestion]: ../../Shared/Media/previous-portal.png
  [Création et déploiement d'une application Node.js dans un site Web Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Service cloud Node.js avec stockage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Application web Node.js avec stockage]: /develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=47-->
