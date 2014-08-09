<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="" solutions="" manager="" editor="" />

Utilisation du service de file d'attente à partir de Node.js
============================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de file d'attente Azure. Les exemples sont écrits en utilisant l'API Node.js. Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

-   [Présentation du service de file d'attente](#what-is)
-   [Concepts](#concepts)
-   [Création d'un compte de stockage Azure](#create-account)
-   [Création d'une application Node.js](#create-app)
-   [Configuration de votre application pour accéder au stockage](#configure-access)
-   [Configuration d'une chaîne de connexion Azure Storage](#setup-connection-string)
-   [Création d'une file d'attente](#create-queue)
-   [Insertion d'un message dans une file d'attente](#insert-message)
-   [Lecture furtive du message suivant](#peek-message)
-   [Suppression du message suivant dans la file d'attente](#get-message)
-   [Modification du contenu d'un message en file d'attente](#change-contents)
-   [Options supplémentaires pour la suppression des messages dans la file d'attente](#advanced-get)
-   [Obtention de la longueur de la file d'attente](#get-queue-length)
-   [Suppression d'une file d'attente](#delete-queue)
-   [Étapes suivantes](#next-steps)

Présentation du service de file d'attente
-----------------------------------------

Le service de file d'attente est un service permettant de stocker un grand nombre de messages accessibles depuis n'importe où dans le monde via des appels authentifiés avec HTTP ou HTTPS. Un simple message de file d'attente peut avoir une taille de 64 Ko et une file d'attente peut contenir des millions de messages, jusqu'à la limite de capacité totale d'un compte de stockage, à savoir 100 To. Voici quelques utilisations courantes du service de file d'attente :

-   Création d'un journal des travaux en souffrance de travail à traiter de manière asynchrone
-   Transmission des messages d'un rôle Web Azure à un rôle de travail

Concepts
--------

Le service de file d'attente contient les composants suivants :

![Queue1](./media/storage-nodejs-how-to-use-queues/queue1.png)

-   **Format d'URL :** les files d'attente sont adressables à l'aide du format d'URL suivant :

        http://storageaccount.queue.core.windows.net/queue  

    L'URL suivante désigne l'une des files d'attente du diagramme :

        http://myaccount.queue.core.windows.net/imagesToDownload

-   **Compte de stockage :** tous les accès à Azure Storage passent par un compte de stockage. Un compte de stockage est le plus haut niveau d'espace de noms permettant d'accéder aux files d'attente. Dans un compte de stockage, la taille totale du contenu des objets blob, des tables et des files d'attente ne doit pas dépasser 100 To.

-   **File d'attente :** une file d'attente contient un ensemble de messages. Tous les messages doivent être dans une file d'attente.

-   **Message :** un message, dans n'importe quel format, jusqu'à 64 Ko.

Création d'un compte Azure Storage
----------------------------------

Pour utiliser les opérations de stockage, vous avez besoin d'un compte Azure Storage. Pour en créer un, procédez comme suit : (Vous pouvez également créer un compte de stockage [à l'aide de l'API REST](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh264518.aspx).)

1.  Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com).

2.  En bas du volet de navigation, cliquez sur **+NOUVEAU**.

    ![+nouveau](./media/storage-nodejs-how-to-use-queues/plus-new.png)

3.  Cliquez sur **Compte de stockage**, puis sur **Création rapide**.

    ![Boîte de dialogue Création rapide](./media/storage-nodejs-how-to-use-queues/quick-storage.png)

4.  Dans URL, tapez un nom de sous-domaine à utiliser dans l'URI du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Cette valeur devient le nom d'hôte contenu dans l'URI utilisé pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

5.  Choisissez la région ou le groupe d'affinités dans lequel localiser le stockage. Si vous utilisez le stockage à partir de votre application Azure, sélectionnez la même région que celle du déploiement de votre application.

6.  Cliquez sur **Créer un Compte de stockage**.

Création d'une application Node.js
----------------------------------

Créez une application Node.js vide. Pour obtenir les instructions permettant de créer une application Node.js, consultez les pages [Création et déploiement d'une application Node.js dans un site Web Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Service cloud Node.js]({localLink:2221} "Application Web avec Express") (avec Windows PowerShell) ou [Site Web avec WebMatrix](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/).

Configuration de votre application pour accéder au stockage
-----------------------------------------------------------

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Node.js, qui inclut des bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de Node Package Manager (NPM) pour obtenir le package

1.  Utilisez une interface de ligne de commande telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix) pour accéder au dossier dans lequel vous avez créé votre exemple d'application.

2.  Tapez **npm install azure** dans la fenêtre de commande, ce qui génère la sortie suivante :

        azure@0.7.5 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@1.1.1
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.7 (sax@0.5.2)
        |-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  Vous pouvez exécuter manuellement la commande **ls** pour vérifier que le dossier **node\_modules** a été créé. Dans ce dossier, vous trouverez le package **azure**, qui contient les bibliothèques nécessaires pour accéder au stockage.

### Importation du package

À l'aide d'un éditeur de texte, comme le Bloc-notes, ajoutez la commande suivante au début du fichier **server.js** de l'application dans laquelle vous souhaitez utiliser le stockage :

    var azure = require('azure');

Configuration d'une connexion Azure Storage
-------------------------------------------

Le module Azure lit les variables d'environnement AZURE\_STORAGE\_ACCOUNT et AZURE\_STORAGE\_ACCESS\_KEY pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte lors de l'appel de **createQueueService**.

Pour consulter un exemple de paramétrage de variables d'environnement dans un fichier de configuration pour un service cloud Azure, consultez la page [Service cloud Node.js avec stockage](/en-us/develop/nodejs/tutorials/web-app-with-storage/).

Pour consulter un exemple de paramétrage de variables d'environnement dans le portail de gestion d'un site Web Azure, consultez la page [Application Web Node.js avec stockage](/en-us/develop/nodejs/tutorials/web-site-with-storage/).

Création d'une file d'attente
-----------------------------

Le code suivant crée un objet **QueueService**, ce qui vous permet d'utiliser les files d'attente.

    var queueService = azure.createQueueService();

Utilisez la méthode **createQueueIfNotExists**, qui renvoie la file d'attente spécifiée si elle existe déjà ou qui en crée une avec le nom spécifié dans le cas contraire.

    queueService.createQueueIfNotExists(queueName, function(error){
        if(!error){
            // La file d'attente existe
        }
    });

### Filtres

Des opérations facultatives de filtrage peuvent être appliquées aux opérations exécutées par le biais de **QueueService**. Il peut s'agir d'opérations de journalisation, de relance automatique, etc. Les filtres sont des objets qui implémentent une méthode avec la signature :

     function handle (requestOptions, next)

Après le prétraitement des options de la requête, la méthode doit appeler « next » en transmettant un rappel avec la signature suivante :

     function (returnObject, finalCallback, next)

Dans ce rappel, et après le traitement de returnObject (la réponse de la requête au serveur), le rappel doit appeler la fonction next, si elle existe, pour continuer à traiter d'autres filtres ou simplement appeler finalCallback pour terminer l'utilisation du service.

Deux filtres qui implémentent la logique de relance sont inclus dans le Kit de développement logiciel (SDK) Azure pour Node.js : **ExponentialRetryPolicyFilter** et **LinearRetryPolicyFilter**. Le code suivant crée un objet **QueueService** qui utilise le filtre **ExponentialRetryPolicyFilter** :

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueService = azure.createQueueService().withFilter(retryOperations);

Insertion d'un message dans une file d'attente
----------------------------------------------

Pour insérer un message dans une file d'attente, utilisez la méthode **createMessage** afin de créer un message et de l'ajouter dans la file d'attente.

    queueService.createMessage(queueName, "Hello world!", function(error){
        if(!error){
            // Message inséré
        }
    });

Lecture furtive du message suivant
----------------------------------

Vous pouvez lire furtivement le message au début de la file d'attente sans l'enlever de la file d'attente en appelant la méthode **peekMessages**. Par défaut, **peekMessages** lit un seul message.

    queueService.peekMessages(queueName, function(error, messages){
        if(!error){
            // Messages lus furtivement
            // Le texte est disponible dans messages[0].messagetext
        }
    });

> [WACOM.NOTE] L'utilisation de **peekMessage** lorsqu'il n'y a pas de messages dans la file d'attente ne renvoie pas d'erreur, mais aucun message n'est renvoyé.

Suppression du message suivant dans la file d'attente
-----------------------------------------------------

Votre code supprime un message d'une file d'attente en deux étapes. Lorsque vous appelez **getMessages**, vous obtenez le message suivant dans une file d'attente par défaut. Un message renvoyé par **getMessages** devient invisible par les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **deleteMessage**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **deleteMessage** juste après le traitement du message.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Traite le message en moins de 30 secondes, le texte du
            // message est disponible dans messages[0].messagetext 
            var message = messages[0]
            queueService.deleteMessage(queueName
                , message.messageid
                , message.popreceipt
                , function(error){
                    if(!error){
                        // Message supprimé
                    }
                });
        }
    });

> [WACOM.NOTE] L'utilisation de **getMessages** lorsqu'il n'y a pas de messages dans la file d'attente ne renvoie pas d'erreur, mais aucun message n'est renvoyé.

Modification du contenu d'un message en file d'attente
------------------------------------------------------

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code ci-dessous utilise la méthode **updateMessage** pour mettre à jour un message.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Message reçu
            var message = messages[0];
            queueService.updateMessage(queueName
                , message.messageid
                , message.popreceipt
                , 10
                , { messagetext: 'in your message, doing stuff.' }
                , function(error){
                    if(!error){
                        // Message correctement mis à jour
                    }
                });
        }
    });

Options supplémentaires pour la suppression des messages dans la file d'attente
-------------------------------------------------------------------------------

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **getMessages** pour obtenir 15 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle for. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

    queueService.getMessages(queueName
        , {numofmessages: 15, visibilitytimeout: 5 * 60}
        , function(error, messages){
        if(!error){
            // Messages récupérés
            for(var index in messages){
                // le texte est disponible dans messages[index].messagetext
                var message = messages[index];
                queueService.deleteMessage(queueName
                    , message.messageid
                    , message.popreceipt
                    , function(error){
                        if(!error){
                            // Message supprimé
                        }
                    });
            }
        }
    });

Obtention de la longueur de la file d'attente
---------------------------------------------

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **getQueueMetadata** demande au service de file d'attente de renvoyer des métadonnées concernant la file d'attente, et la propriété **approximatemessagecount** de la réponse comptabilise le nombre de messages figurant dans une file d'attente. Ce nombre est approximatif étant donné que des messages peuvent être ajoutés ou supprimés une fois que le service de file d'attente a répondu à votre demande.

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
        if(!error){
            // La longueur de la file d'attente est disponible dans queueInfo.approximatemessagecount
        }
    });

Suppression d'une file d'attente
--------------------------------

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **deleteQueue** sur l'objet file d'attente.

    queueService.deleteQueue(queueName, function(error){
        if(!error){
            // La file d'attente a été supprimée
        }
    });

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage des files d'attente, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx).
-   Consultez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Node](https://github.com/WindowsAzure/azure-sdk-for-node) sur GitHub.

