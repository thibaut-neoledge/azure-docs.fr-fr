<properties urlDisplayName="Service Bus Queues" pageTitle="Utilisation des files d'attente Service Bus (Node.js) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Node.js" description="Learn how to use Service Bus queues in Azure. Code samples written in Node.js." metaCanonical="" services="service-bus" documentationCenter="nodejs" title="How to Use Service Bus Queues" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />






# Utilisation des files d'attente Service Bus

Ce guide vous montre comment utiliser les files d'attente Service Bus. Les exemples sont
écrits en JavaScript et utilisent le module Azure Node.js. Les scénarios
couverts comprennent **la création de files d'attente, l'envoi et la réception de messages** et
**la suppression de files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes
suivantes].

## Sommaire

-   [Présentation des files d'attente Service Bus][]
-   [Création d'un espace de noms de service][]
-   [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms][]
-   [Création d'une application Node.js](#create-app)
-   [Configuration de votre application pour l'utilisation de Service Bus](#configure-app)
-   [ Création d'une file d'attente](#create-queue)
-   [ Envoi de messages à une file d'attente](#send-messages)
-   [ Réception des messages d'une file d'attente](#receive-messages)
-   [ Gestion des blocages d'application et des messages illisibles](#handle-crashes)
-   [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

## <a name="create-app"> </a>Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure], [Service cloud Node.js][Service cloud Node.js] (avec Windows PowerShell) ou [Site Web avec WebMatrix].

## <a name="configure-app"> </a>Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser Azure Service Bus, vous devez télécharger et utiliser le
package Azure Node.js. Ce dernier inclut des bibliothèques permettant de
communiquer avec les services REST de Service Bus.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez la fenêtre de commande **Windows PowerShell pour Node.js** pour
    accéder au dossier **c:\\node\\sbqueues\\WebRole1** où vous avez
    créé votre exemple d'application.

2.  Tapez **npm install azure** dans la fenêtre de commande, ce qui doit
    générer un résultat semblable au suivant :

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier
    **node\_modules** a été créé. Dans ce dossier, recherchez le package
    **azure** qui contient les bibliothèques dont vous avez besoin pour accéder
    aux files d'attente Service Bus.

### Importation du module

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier
**server.js** de l'application :

    var azure = require('azure');

### Configuration d'une connexion Azure Service Bus

Le module Azure lit les variables d'environnement AZURE\_SERVICEBUS\_NAMESPACE et AZURE\_SERVICEBUS\_ACCESS\_KEY pour obtenir les informations nécessaires à la connexion à votre compte Azure Service Bus. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createServiceBusService**.

Pour consulter un exemple de paramétrage de variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez la page [Service cloud Node.js avec stockage].

Pour obtenir un exemple de configuration des variables d'environnement dans le portail de gestion pour un site web Azure, consultez la rubrique [Application web Node.js avec stockage]

## <a name="create-queue"> </a>Création d'une file d'attente

L'objet **ServiceBusService** permet d'utiliser des files d'attente. 
Le code suivant crée un objet **ServiceBusService**. Ajoutez-le vers le
début du fichier **server.js**, après l'instruction relative à l'importation du module
Azure :

    var serviceBusService = azure.createServiceBusService();

En appelant **createQueueIfNotExists** dans l'objet **ServiceBusService**
, la rubrique spécifiée est renvoyée (si elle existe) ou une nouvelle
file d'attente comportant le nom spécifié est créée. Le code suivant utilise
**createQueueIfNotExists** pour créer ou se connecter à la file d'attente nommée
" myqueue " :

    serviceBusService.createQueueIfNotExists('myqueue', function(error){
        if(!error){
            // Queue exists
        }
    });

**createServiceBusService** prend également en charge des options supplémentaires, qui
permettent de remplacer les paramètres de file d'attente par défaut comme la durée de vie de message
ou la taille de file d'attente maximale. L'exemple suivant montre comment définir la
taille de file d'attente maximale sur 5 Go et la durée de vie de message sur une minute :

    var queueOptions = {
          MaxSizeInMegabytes: '5120',
          DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
        if(!error){
            // Queue exists
        }
    });

###Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées via **ServiceBusService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

		function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler " next " en passant un rappel avec la signature suivante :

		function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="send-messages"> </a>Envoi de messages à une file d'attente

Pour envoyer un message à une file d'attente Service Bus, votre application appelle la
méthode **sendQueueMessage** de l'objet **ServiceBusService**.
Les messages à destination (et en provenance) des files d'attente Service Bus sont
des objets **BrokeredMessage**. Ils possèdent un ensemble de propriétés standard (telles que
**Label** et **TimeToLive**, un dictionnaire servant à conserver
les propriétés personnalisées propres à une application, ainsi qu'un corps de
données d'application arbitraires. Une application peut définir le corps du message en
transmettant une valeur de chaîne en tant que message pour remplir toutes les propriétés standard
requises avec les valeurs par défaut.

L'exemple suivant montre comment envoyer un message de test à la
file d'attente nommée " myqueue " à l'aide de **sendQueueMessage** :

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

Les files d'attente Service Bus prennent en charge une taille de message maximale de 256 Ko (l'en-tête,
qui inclut les propriétés d'application standard et personnalisées, peut avoir
une taille maximale de 64 Ko). Il n'existe aucune limite pour le nombre de messages
dans une file d'attente, mais il en existe une, en revanche, pour la taille totale des messages
dans une file d'attente. Cette taille de file d'attente est définie lors de la création, avec une
limite maximale de 5 Go.

## <a name="receive-messages"> </a>Réception des messages d'une file d'attente

Les messages sont réceptionnés à partir d'une file d'attente à l'aide de la méthode **receiveQueueMessage**
de l'objet **ServiceBusService**. Par défaut, les messages sont
supprimés de la file d'attente au fur et à mesure de leur lecture. Toutefois, vous pouvez lire (afficher un aperçu)
et verrouiller le message sans le supprimer de la file d'attente en définissant le
paramètre facultatif **isPeekLock** sur **True**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de
l'opération de réception constitue le modèle le plus simple et le mieux adapté aux scénarios pour
lesquels une application peut tolérer l'absence de traitement d'un message en cas
d'échec. Pour comprendre, imaginez un scénario dans lequel le
consommateur émet la demande de réception, puis se bloque avant le traitement
. Comme Service Bus a marqué le message comme étant consommé,
lorsque l'application redémarre et recommence à consommer des messages,
elle manque le message consommé avant l'incident.

Si le paramètre **isPeekLock** est défini sur **True**, la réception devient
une opération en deux étapes, qui autorise une prise en charge des applications
qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une
demande, il recherche le message suivant à consommer et le verrouille pour empêcher
d'autres consommateurs de le recevoir, puis le renvoie à l'application.
Dès que l'application a terminé le traitement du message (ou qu'elle l'a stocké
de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du
processus de réception en appelant la méthode **deleteMessage** et en fournissant le
message à supprimer en tant que paramètre. La méthode **deleteMessage**
marque le message comme étant consommé et le supprime de la file d'attente.

L'exemple ci-dessous montre comment les messages peuvent être reçus et
traités à l'aide de **receiveQueueMessage**. Dans l'exemple, un message est d'abord reçu
, puis supprimé. Un message est ensuite reçu en définissant **isPeekLock**
sur True, puis le message est supprimé à l'aide de **deleteMessage** :

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

## <a name="handle-crashes"> </a>Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération
à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une
application réceptrice ne parvient pas à traiter le message pour une raison quelconque,
elle appelle la méthode **unlockMessage** de l'objet 
**ServiceBusService**. Cela amène Service Bus à déverrouiller le
message dans la file d'attente de messages et à le rendre à nouveau disponible en réception,
pour la même application consommatrice ou pour une autre.
l'application.

Il existe également un délai d'expiration pour un message verrouillé dans la
file d'attente et si l'application ne parvient pas à traiter le message avant l'
expiration du délai d'attente du verrou (par exemple, si l'application se bloque), Service
Bus déverrouille le message automatiquement et le rend à nouveau disponible
en réception.

Dans le cas où l'application se bloque après le traitement du message
, mais avant l'appel de la méthode **deleteMessage**, le message est
à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement,
**souvent appelé Au moins une fois**, chaque message est traité au moins une fois.
Toutefois, dans certaines circonstances, un même message peut être
remis une nouvelle fois. Si le scénario ne peut pas tolérer un traitement en double,
les développeurs d'applications doivent ajouter une logique supplémentaire à leur application
pour traiter la remise de messages en double. Cette opération est souvent réalisée à l'aide de la
**propriété MessageId** d'un message, qui reste constante pendant
les tentatives de remise.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez appris les principes de base des files d'attente Service Bus, consultez ces
liens pour en savoir plus.

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements.][]
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node] sur GitHub.

  [Kit de développement logiciel (SDK) Azure pour Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente Service Bus]: #what-are-service-bus-queues
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Création d'une application Node.js]: #create-app
  [Configuration de votre application pour l'utilisation de Service Bus]: #configure-app
  [ Création d'une file d'attente]: #create-queue
  [ Envoi de messages à une file d'attente]: #send-messages
  [ Réception des messages d'une file d'attente]: #receive-messages
  [ Gestion des blocages d'application et des messages illisibles]: #handle-crashes
  [Concepts de la file d'attente]: ../../dotNet/Media/sb-queues-08.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  
  
  
  [Service cloud Node.js]: /fr-fr/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Files d'attente, rubriques et abonnements.]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh367516.aspx
  [Site web avec WebMatrix]: /fr-fr/develop/nodejs/tutorials/web-site-with-webmatrix/
[Ancien portail de gestion]: ../../Shared/Media/previous-portal.png
  [Création et déploiement d'une application Node.js sur un site web Azure]: /fr-fr/develop/nodejs/tutorials/create-a-website-(mac)/
  [Service cloud Node.js avec stockage]: /fr-fr/develop/nodejs/tutorials/web-app-with-storage/
  [Application web Node.js avec stockage]: /fr-fr/develop/nodejs/tutorials/web-site-with-storage/
