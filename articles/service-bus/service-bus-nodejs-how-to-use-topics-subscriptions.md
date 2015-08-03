<properties 
	pageTitle="Utilisation des rubriques Service Bus (Node.js) - Azure" 
	description="Découvrez comment utiliser les rubriques et abonnements Service Bus dans Azure à partir d’une application Node.js." 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="mwasson"/>


# Utilisation des rubriques et abonnements Service Bus

Ce guide décrit l’utilisation des rubriques et des abonnements Service Bus depuis les applications Node.js. Les scénarios couverts dans ce guide sont les suivants : **création de rubriques et d'abonnements, création de filtres d'abonnement, envoi de messages** à une rubrique, **réception de messages en provenance d'un abonnement** et **suppression de rubriques et d'abonnements**. Pour plus d'informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#next-steps).

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Création d'une application Node.js

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure], [Service cloud Node.js][Node.js Cloud Service] (avec Windows PowerShell) ou Site Web avec WebMatrix.

## Configuration de votre application pour l'utilisation de Service Bus

Pour utiliser Service Bus, téléchargez le package azure Node.js. Ce dernier inclut des bibliothèques permettant de communiquer avec les services REST de Service Bus.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure** dans la fenêtre de commande, ce qui génère la sortie suivante :

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

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node_modules** a été créé. Dans ce dossier, recherchez le package **azure**, qui contient les bibliothèques nécessaires pour accéder aux rubriques de Service Bus.

### Importation du module

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application :

    var azure = require('azure');

### Configuration d’une connexion Service Bus

Le module Azure lit les variables d'environnement AZURE_SERVICEBUS_NAMESPACE et AZURE_SERVICEBUS_ACCESS_KEY pour obtenir les informations nécessaires à la connexion à votre Azure Service Bus. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createServiceBusService**.

Pour consulter un exemple de paramétrage de variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez la page [Service cloud Node.js avec stockage].

Pour obtenir un exemple de configuration des variables d'environnement dans le portail de gestion pour un site web Azure, consultez la rubrique [Application web Node.js avec stockage]

## Création d’une rubrique

L'objet **ServiceBusService** permet d'utiliser des rubriques. Le code suivant crée un objet **ServiceBusService**. Ajoutez-le vers le début du fichier **server.js**, après l'instruction relative à l'importation du module Azure :

    var serviceBusService = azure.createServiceBusService();

En appelant **createTopicIfNotExists** dans l'objet **ServiceBusService**, la rubrique spécifiée est renvoyée (si elle existe) ou une nouvelle rubrique comportant le nom spécifié est créée. Le code suivant utilise **createTopicIfNotExists** pour créer la rubrique intitulée « MyTopic » ou s'y connecter :

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService** prend également en charge des options supplémentaires qui vous permettent de remplacer les paramètres de rubrique par défaut comme la durée de vie de message ou la taille de rubrique maximale. L'exemple suivant montre comment définir la taille maximale de la rubrique sur 5 Go et la durée de vie de message sur une minute :

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

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées par le biais de **ServiceBusService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

		function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler « next » en passant un rappel avec la signature suivante :

		function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **ServiceBusService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## Création d’abonnements

Les abonnements de rubrique sont également créés à l'aide de l'objet **ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

> [AZURE.NOTE]Les abonnements sont persistants et continuent à exister jusqu'à leur suppression ou celle de la rubrique à laquelle ils sont associés. Si votre application contient une logique pour la création d'un abonnement, elle doit d'abord vérifier si l'abonnement existe déjà en utilisant la méthode **getSubscription**.

### Création d'un abonnement avec le filtre par défaut (MatchAll)

Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n'est spécifié lors de la création d'un abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d'attente virtuelle de l'abonnement. Dans l'exemple suivant, l'abonnement « AllMessages » qui est créé utilise le filtre par défaut **MatchAll**.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### Création d'abonnements avec des filtres

Vous pouvez également créer des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement de rubrique spécifique.

Parmi les types de filtre pris en charge par les abonnements, **SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus de détails sur les expressions utilisables avec un filtre SQL, examinez la syntaxe[SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Il est possible d'ajouter des filtres à un abonnement en utilisant la méthode **createRule** de l'objet **ServiceBusService**. Cette méthode vous permet d'ajouter de nouveaux filtres à un abonnement existant.

> [AZURE.NOTE]

> Étant donné que le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d'abord supprimer le filtre par défaut ou le filtre **MatchAll** remplacera tous les autres filtres spécifiés. Vous pouvez supprimer la règle par défaut en utilisant la méthode **deleteRule** de l'objet **ServiceBusService**.

Dans l'exemple ci-dessous, l'abonnement « HighMessages » est créé avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **messagenumber** a une valeur supérieure à 3 :

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

De la même manière, l'exemple suivant créé l'abonnement « LowMessages » avec un filtre **SqlFilter** qui sélectionne uniquement les messages dont la propriété **messagenumber** a une valeur inférieure ou égale à 3 :

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

À présent, dès lors qu'un message est envoyé vers la rubrique « MyTopic », il est toujours remis aux destinataires abonnés à l'abonnement de rubrique « AllMessages » et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique « HighMessages » et « LowMessages » (en fonction du contenu du message).

## Envoi de messages à une rubrique

Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode **sendTopicMessage** de l'objet **ServiceBusService**. Les messages envoyés aux rubriques Service Bus sont des objets **BrokeredMessage**. Les objets **BrokeredMessage** possèdent un ensemble de propriétés standard (telles que **Label** et **TimeToLive**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu'un corps de données de chaîne. Une application peut définir le corps du message en transmettant une valeur de chaîne à la méthode **sendTopicMessage** pour remplir toutes les propriétés standard requises avec les valeurs par défaut.

L'exemple suivant montre comment envoyer cinq messages de test à la rubrique MyTopic. Notez que la valeur de la propriété **messagenumber** de chaque message varie au niveau de l'itération de la boucle (détermine les abonnements qui le reçoivent) :

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

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Mo (l'en-tête, qui comprend les propriétés d'application standard et personnalisées, peut avoir une taille maximale de 64 Mo). Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go.

## Réception des messages d’un abonnement

La méthode **receiveSubscriptionMessage** de l'objet **ServiceBusService** permet de recevoir les messages d'un abonnement. Par défaut, les messages sont supprimés de l'abonnement au fur et à mesure de leur lecture. Cependant, vous pouvez lire et verrouiller le message sans le supprimer de l'abonnement en définissant le paramètre facultatif **isPeekLock** sur **true**.

Le comportement par défaut de lecture et de suppression du message dans le cadre de l'opération de réception est le modèle le plus simple et le mieux adapté aux scénarios dans lesquels une application est capable de tolérer le non-traitement d'un message en cas d'échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l'application redémarre et recommence à consommer des messages, elle manque le message consommé avant l'incident.

Si le paramètre **isPeekLock** est défini sur **true**, la réception devient une opération en deux étapes, qui autorise une prise en charge des applications qui ne peuvent pas tolérer les messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l'application a terminé le traitement du message (ou qu'elle l'a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **deleteMessage** et en fournissant le message à supprimer sous la forme d'un paramètre. La méthode **deleteMessage** marque le message comme étant consommé et le supprime de l'abonnement.

L'exemple ci-dessous montre comment les messages peuvent être reçus et traités à l'aide de **receiveSubscriptionMessage**. Dans l'exemple, le message est d'abord réceptionné, puis supprimé de l'abonnement « LowMessages ». Un message envoyé par l'abonnement « HighMessages » est ensuite réceptionné en définissant **isPeekLock** sur true. La suppression du message s'effectue ensuite à l'aide de **deleteMessage** :

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

## Gestion des blocages d’application et des messages illisibles

Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d'erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlockMessage** pour l'objet **ServiceBusService**. Cela amène Service Bus à déverrouiller le message dans l'abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu'un message verrouillé dans un abonnement est assorti d'un délai d'expiration et que si l'application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l'application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l'application subit un incident après le traitement du message, mais avant l'émission de la méthode **deleteMessage**, le message est à nouveau remis à l'application lorsqu'elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Si le scénario ne peut pas tolérer le traitement en double, les développeurs d'application doivent ajouter une logique supplémentaire à leur application pour traiter la remise de messages en double, ce qui est souvent obtenu grâce à la propriété **MessageId** du message, qui reste constante pendant les tentatives de remise.

## Suppression des rubriques et des abonnements

Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite par le biais du portail de gestion Azure ou par programme. L'exemple suivant montre comme supprimer la rubrique intitulée « MyTopic » :

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

La suppression d'une rubrique a également pour effet de supprimer les abonnements inscrits au niveau de la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l'abonnement HighMessages de la rubrique myTopic :

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## Étapes suivantes

Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus :

-   Consultez la référence MSDN suivante : [Files d'attente, rubriques et abonnements][].
-   Référence d'API pour [SqlFilter][].
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node] sur GitHub.

  [Kit de développement logiciel (SDK) Azure pour Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [Azure Management Portal]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Files d'attente, rubriques et abonnements]: http://msdn.microsoft.com/library/azure/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Création et déploiement d'une application Node.js dans un site Web Azure]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Service cloud Node.js avec stockage]: /develop/nodejs/tutorials/web-app-with-storage/
  [Application web Node.js avec stockage]: /develop/nodejs/tutorials/web-site-with-storage/
 

<!---HONumber=July15_HO4-->