<properties linkid="develop-python-queue-service" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Python) | Microsoft Azure" metaKeywords="Azure Queue Service messaging Python" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Python." metaCanonical="" services="storage" documentationCenter="Python" title="How to Use the Queue Storage Service from Python" authors="" solutions="" manager="" editor="" />

Utilisation du service de stockage de files d'attente à partir de Python
========================================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de files d'attente Windows Azure. Les exemples sont écrits en utilisant l'API Python. Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

[Présentation du stockage de files d'attente](#what-is)   
 [Concepts](#concepts)   
 [Création d'un compte de stockage Azure](#create-account)   
 [Création d'une file d'attente](#create-queue)   
 [Insertion d'un message dans une file d'attente](#insert-message)   
 [Lecture furtive du message suivant](#peek-message)   
 [Suppression du message suivant de la file d'attente](#get-message)   
 [Modification du contenu d'un message en file d'attente](#change-contents)   
 [Options supplémentaires de suppression de messages d'une file d'attente](#advanced-get)   
 [Obtention de la longueur d'une file d'attente](#get-queue-length)   
 [Suppression d'une file d'attente](#delete-queue)   
 [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Création d'un compte Azure Storage
----------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python](../python-how-to-install/).

Création d'une file d'attente
-----------------------------

L'objet **QueueService** permet d'utiliser des files d'attente. Le code suivant permet de créer un objet **QueueService**. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

    from azure.storage import *

Le code suivant permet de créer un objet **QueueService** en utilisant le nom et la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le compte et la clé réels.

    queue_service = QueueService(account_name='myaccount', account_key='mykey')

    queue_service.create_queue('taskqueue')

Insertion d'un message dans une file d'attente
----------------------------------------------

Pour insérer un message dans une file d'attente, utilisez la méthode **put\_message** pour créer un nouveau message et l'ajouter à la file d'attente.

    queue_service.put_message('taskqueue', 'Hello World')

Lecture furtive du message suivant
----------------------------------

Vous pouvez lire furtivement le message au début de la file d'attente sans le supprimer de la file d'attente en appelant la méthode **peek\_messages**. Par défaut, **peek\_messages** permet de lire furtivement un seul message.

    messages = queue_service.peek_messages('taskqueue')
    for message in messages:
        print(message.message_text)

Suppression du message suivant dans la file d'attente
-----------------------------------------------------

Votre code supprime un message d'une file d'attente en deux étapes. Lorsque vous appelez **get\_messages**, vous obtenez le message suivant d'une file d'attente par défaut. Un message renvoyé par **get\_messages** devient invisible de tout autre code lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **delete\_message**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete\_message** juste après le traitement du message.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        print(message.message_text)
        queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

Modification du contenu d'un message en file d'attente
------------------------------------------------------

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code ci-dessous utilise la méthode **update\_message** pour mettre à jour un message.

    messages = queue_service.get_messages('taskqueue')
    for message in messages:
        queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

Options supplémentaires pour la suppression des messages dans la file d'attente
-------------------------------------------------------------------------------

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **get\_messages** pour obtenir 16 messages en un appel. Ensuite, il traite chaque message à l'aide d'une boucle for. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

    messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
    for message in messages:
        print(message.message_text)
        queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

Obtention de la longueur de la file d'attente
---------------------------------------------

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **get\_queue\_metadata** demande au service de file d'attente de renvoyer des métadonnées sur la file d'attente ; **x-ms-approximate-messages-count** doit être utilisé comme index dans le dictionnaire renvoyé pour trouver le décompte. Le résultat est seulement approximatif, car des messages peuvent être ajoutés ou supprimés après que le service de file d'attente a répondu à votre demande.

    queue_metadata = queue_service.get_queue_metadata('taskqueue')
    count = queue_metadata['x-ms-approximate-messages-count']

Suppression d'une file d'attente
--------------------------------

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **delete\_queue**.

    queue_service.delete_queue('taskqueue')

Étapes suivantes
----------------

Maintenant que vous avez acquis les bases du stockage de files d'attente, suivez ces liens pour savoir comment effectuer des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx)
-   Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)

