<properties 
	pageTitle="Utilisation des rubriques Service Bus (Node.js) - Azure" 
	description="Découvrez comment utiliser les rubriques et abonnements Service Bus dans Azure. Les exemples de code sont écrits pour les applications Node.js." 
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






# Utilisation des rubriques et abonnements Service Bus

Ce guide décrit l'utilisation des rubriques et des abonnements Service Bus depuis les applications Node.js. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements, création de filtres d'abonnement, envoi de messages** à une rubrique, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**. Pour plus d'informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes][].

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure], [Service cloud Node.js][Service cloud Node.js] (avec Windows PowerShell) ou [Site Web avec WebMatrix].

## Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser Service Bus, téléchargez le package azure Node.js. Ce dernier inclut des bibliothèques permettant de communiquer avec les services REST de Service Bus.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure** dans la fenêtre de commande, ce qui doit
    aboutir à la sortie suivante :

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier qu'un dossier **node_modules** a été créé. Dans ce dossier, recherchez le package **azure**, qui contient les bibliothèques nécessaires pour accéder aux rubriques Service Bus.

### Importation du module

Utilisez le Bloc-notes ou un autre éditeur de texte pour ajouter le code suivant en haut du fichier **server.js** de l'application :

    var azure = require('azure');

### Configuration d'une connexion Service Bus

Le module azure lit les variables d'environnement AZURE_SERVICEBUS_NAMESPACE et AZURE_SERVICEBUS_ACCESS_KEY pour obtenir les informations permettant de se connecter à votre compte Azure Service Bus. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte pendant l'appel de **createServiceBusService**.

Pour un exemple de définition des variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez [Service cloud Node.js avec stockage].

Pour un exemple de définition des variables d'environnement dans le portail de gestion pour un site web Azure, consultez [Application web Node.js avec stockage]

## Création d'une rubrique

L'objet **ServiceBusService** vous permet d'utiliser des rubriques. Le code suivant crée un objet **ServiceBusService**. Ajoutez-le au début du fichier **server.js**, après l'instruction relative à l'importation du module azure :

    var serviceBusService = azure.createServiceBusService();

En appelant **createTopicIfNotExists** sur l'objet **ServiceBusService**, la rubrique spécifiée est renvoyée (si elle existe) ou une nouvelle rubrique avec le nom spécifié est créée. Le code suivant utilise **createTopicIfNotExists** pour créer ou se connecter à la file d'attente nommée " MyTopic " :

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** prend également en charge des options supplémentaires, qui vous permettent de remplacer les paramètres de rubrique par défaut comme la durée de vie du message ou la taille maximale de la rubrique. L'exemple suivant montre comment définir la taille maximale de la rubrique sur 5 Go et la durée de vie de message sur une minute :

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
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

## Création d'abonnements

Les abonnements de rubrique sont également créés avec l'objet **ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

> [AZURE.NOTE] Les abonnements sont persistants et continuent à exister jusqu'à leur suppression ou celle de la rubrique à laquelle ils sont associés. Si votre application contient une logique de création d'abonnement, elle doit d'abord vérifier que l'abonnement existe à l'aide de la méthode
méthode **getSubscription**.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Quand le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement 'AllMessages' qui est créé utilise le filtre par défaut **MatchAll**.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Création d'abonnements avec des filtres

Vous pouvez également créer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtre pris en charge par les abonnements, **SqlFilter**, est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus de détails sur les expressions utilisables avec un filtre SQL, examinez la syntaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Des filtres peuvent être ajoutés à un abonnement à l'aide de la méthode **createRule** de l'objet **ServiceBusService**. Cette méthode vous permet d'ajouter de nouveaux filtres à un abonnement existant.

> [AZURE.NOTE]

> Le filtre par défaut étant appliqué automatiquement à tous les nouveaux abonnements, vous devez d'abord le supprimer, sinon le filtre <strong>MatchAll</strong> remplacera tous les filtres que vous spécifiez. Vous pouvez supprimer la règle par défaut à l'aide de la méthode <strong>deleteRule</strong> de l'objet <strong>ServiceBusService</strong> .

Dans l'exemple ci-dessous, l'abonnement 'HighMessages' est créé avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **messagenumber** a une valeur supérieure à 3 :

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber > 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'HighMessages', 
                'HighMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

De même, l'exemple suivant crée l'abonnement 'LowMessages' avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété **messagenumber**a une valeur inférieure ou égale à 3 :

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber <= 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'LowMessages', 
                'LowMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

Lorsqu'un message est envoyé à 'MyTopic', il est toujours remis aux destinataires abonnés à l'abonnement à la rubrique 'AllMessages' et remis de manière sélective aux destinataires abonnés aux abonnements aux rubriques 'HighMessages' et " LowMessages " (en fonction du contenu du message).

## Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode **sendTopicMessage** de l'objet **ServiceBusService**. Les messages envoyés aux rubriques Service Bus sont des objets **BrokeredMessage**. Les objets **BrokeredMessage** sont associés à un ensemble de propriétés standard (comme **Label** et **TimeToLive**), un dictionnaire qui permet de conserver les propriétés spécifiques d'application personnalisée et un corps de données de chaîne. Une application peut définir le corps du message en transmettant une valeur de chaîne à **sendTopicMessage** pour remplir toutes les propriétés standard requises avec les valeurs par défaut.

L'exemple suivant montre comment envoyer cinq messages de test à " MyTopic ". Notez que la valeur de la propriété **messagenumber** de chaque message varie au niveau de l'itération de la boucle (ce qui détermine les abonnements qui le reçoivent) :

    var message = {
        body: '',
        customProperties: {
            messagenumber: 0
        }
    }

    for (i = 0;i < 5;i++) {
        message.customProperties.messagenumber=i;
        message.body='This is Message #'+i;
        serviceBusService.sendTopicMessage(topic, message, function(error) {
          if (error) {
            console.log(error);
          }
        });
    }

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Mo (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Mo). Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go.

## Réception des messages d'un abonnement

Les messages sont reçus d'un abonnement à l'aide de la méthode **receiveSubscriptionMessage** sur l'objet **ServiceBusService**. Par défaut, les messages sont supprimés de l'abonnement dès qu'ils sont lus. Toutefois, vous pouvez lire (afficher un aperçu) et verrouiller le message sans le supprimer de l'abonnement en définissant le paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

Si le paramètre **isPeekLock** est défini sur **true**, l'opération de réception s'effectue en deux étapes, permettant une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Une fois que l'application a terminé le traitement du message (ou l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **deleteMessage** et en fournissant le message à supprimer en tant que paramètre. La méthode **deleteMessage** marque le message comme étant consommé et le supprime de l'abonnement.

L'exemple ci-dessous illustre comment les messages peuvent être reçus et traités à l'aide de **receiveSubscriptionMessage**. Dans l'exemple, le message est d'abord reçu, puis supprimé de l'abonnement 'LowMessages'. Un message envoyé par l'abonnement 'HighMessages' est ensuite reçu en définissant **isPeekLock** sur true. Le message est ensuite supprimé à l'aide de **deleteMessage** :

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

## Gestion des blocages d'application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne peut pas traiter le message pour une raison quelconque, elle peut appeler la méthode **unlockMessage** sur l'objet **ServiceBusService**. Cela amène Service Bus à déverrouiller le message dans l'abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans un abonnement est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible

Au cas où l'application se bloque après le traitement du message, mais avant le l'appel de la méthode **deleteMessage**, le message est redistribué à l'application quand elle redémarre. Dans ce type de traitement, appelé **Au moins une fois**, chaque message est traité au moins une fois, mais dans certaines situations le même message peut être redistribué. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, Cette opération est souvent réalisée à l'aide de la propriété **MessageId** qui reste constante pendant les tentatives de remise.

## Suppression des rubriques et des abonnements

Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite par le biais du portail de gestion Azure ou par programme. L'exemple suivant montre comme supprimer la rubrique intitulée 'MyTopic' :

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l'abonnement " HighMessages " de la rubrique 'MyTopic' :

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements][].
-   Référence d'API pour [SqlFilter][].
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node] sur GitHub.

  [Kit de développement logiciel (SDK) Azure pour Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Étapes suivantes]: #nextsteps
  [Présentation des rubriques et des abonnements Service Bus]: #what-are-service-bus-topics
  [Création d'un espace de noms de service]: #create-a-service-namespace
  [Obtention d'informations d'identification de gestion par défaut pour l'espace de noms]: #obtain-default-credentials
  [Création d'une application Node.js]: #Create_a_Nodejs_Application
  [Configuration de votre application pour l'utilisation de Service Bus]: #Configure_Your_Application_to_Use_Service_Bus
  [Création d'une rubrique]: #How_to_Create_a_Topic
  [Création d'abonnements]: #How_to_Create_Subscriptions
  [Envoi de messages à une rubrique]: #How_to_Send_Messages_to_a_Topic
  [Réception des messages d'un abonnement]: #How_to_Receive_Messages_from_a_Subscription
  [ Gestion des blocages d'application et des messages illisibles]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [Suppression de rubriques et d'abonnements]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Portail de gestion Azure]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Files d'attente, rubriques et abonnements]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Site Web avec WebMatrix]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Service cloud Node.js]: /documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Création et déploiement d'une application Node.js dans un site Web Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [Service cloud Node.js avec stockage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Application web Node.js avec stockage]: /develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=47-->
