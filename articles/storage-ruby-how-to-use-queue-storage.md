<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Ruby) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Ruby" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Queue Storage Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Utilisation du service de stockage de files d'attente à partir de Ruby

This guide shows you how to perform common scenarios using the Windows
Azure Queue Storage service. The samples are written using the Ruby Azure API.
The scenarios covered include **inserting**, **peeking**, **getting**,
and **deleting** queue messages, as well as **creating and deleting
queues**. For more information on queues, refer to the [Next
Steps][] section.

## Sommaire

-   [Présentation des files d’attente de stockage][]
-   [Concepts][]
-   [Création d'un compte de stockage Azure][]
-   [Création d'une application Ruby][]
-   [Configuration de votre application pour accéder au stockage][]
-   [Configuration d'une connexion Azure Storage][]
-   [Création d'une file d'attente][]
-   [Insertion d'un message dans une file d'attente][]
-   [Lecture furtive du message suivant][]
-   [Enlèvement du message suivant de la file d'attente][]
-   [Modification du contenu d'un message en file d'attente][]
-   [Options supplémentaires pour l'enlèvement des messages de la file d'attente][]
-   [Obtention de la longueur de la file d'attente][]
-   [Suppression d'une file d'attente][]
-   [Étapes suivantes][Next
    Steps]

[WACOM.INCLUDE [howto-queue-storage][]]

## <span id="CreateAccount"></span></a>Création d'un compte de stockage Azure

[WACOM.INCLUDE [create-storage-account][]]

## <span id="create-a-ruby-application"></span></a>Création d'une application Ruby

Créez une application Ruby. For instructions,
see [Create a Ruby Application on Azure][].

## <span id="configure-your-application-to-access-storage"></span></a>Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1.  Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2.  Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

    require "azure"

## <span id="setup-a-windows-azure-storage-connection"></span></a>Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY**
 pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. If these environment variables are not set,
you must specify the account information before using **Azure::QueueService** with the following code:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your Azure storage access key>"

Pour obtenir ces valeurs :

1.  Connectez-vous au [portail de gestion Azure][].
2.  Accédez au compte de stockage que vous voulez utiliser.
3.  Cliquez sur **GÉRER LES CLÉS** au bas du volet de navigation.
4.  Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.

## <span id="how-to-create-a-queue"></span></a> Création d'une file d'attente

Le code suivant crée un objet **Azure::QueueService**, ce qui vous permet d'utiliser les files d'attente.

    azure_queue_service = Azure::QueueService.new

Utilisez la méthode **create\_queue()** pour créer une file d'attente comportant le nom spécifié.

    begin
      azure_queue_service.create_queue("test-queue")
    rescue
      puts $!
    end

## <span id="how-to-insert-a-message-into-a-queue"></span></a> Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente, utilisez la méthode **create\_message()** pour créer un nouveau message et l'ajouter à la file d'attente.

    azure_queue_service.create_message("test-queue", "test message")

## <span id="how-to-peek-at-the-next-message"></span></a> Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans le supprimer de la file d'attente en appelant la méthode **peek\_messages()**. Par défaut, **peek\_messages()** permet de lire furtivement un seul message. Vous pouvez également spécifier le nombre de messages que vous souhaitez lire furtivement.

    result = azure_queue_service.peek_messages("test-queue",
      {:number_of_messages => 10})

## <span id="how-to-dequeue-the-next-message"></span></a> Enlèvement du message suivant de la file d'attente

Vous pouvez supprimer un message d'une file d'attente en deux étapes.

1.  Lorsque vous appelez **list\_messages()**, vous obtenez le message suivant dans une file d'attente par défaut. Vous pouvez également spécifier le nombre de messages que vous souhaitez obtenir. Les messages renvoyés par **list\_messages()** deviennent invisibles par les autres codes lisant les messages de cette file d'attente. Vous transmettez le délai d'expiration de la visibilité en secondes en tant que paramètre.

2.  Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **delete\_message()**.

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete\_message()** juste après le traitement du message.

    messages = azure_queue_service.list_messages("test-queue", 30)
    azure_queue_service.delete_message("test-queue", 
      messages[0].id, messages[0].pop_receipt)

## <span id="how-to-change-the-contents-of-a-queued-message"></span></a> Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Le code ci-dessous utilise la méthode **update\_message()** pour mettre à jour un message. La méthode renvoie un tuple qui contient l'accusé pop du message de file d'attente et une valeur de date et d'heure TUC représentant le moment où le message sera visible dans la file d'attente.

    message = azure_queue_service.list_messages("test-queue", 30)
    pop_receipt, time_next_visible = azure_queue_service.update_message(
      "test-queue", message.id, message.pop_receipt, "updated test message", 
      30)

## <span id="how-to-additional-options-for-dequeuing-messages"></span></a> Options supplémentaires pour l'enlèvement des messages de la file d'attente

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente.

1.  Vous pouvez obtenir un lot de messages.

2.  Vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message.

L'exemple de code suivant utilise la méthode **list\_messages()** pour obtenir 15 messages en un appel. Ensuite, il imprime et supprime chaque message. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

    azure_queue_service.list_messages("test-queue", 300
      {:number_of_messages => 15}).each do |m|
      puts m.message_text
      azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
    end

## <span id="how-to-get-the-queue-length"></span></a> Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans la file d'attente. La méthode **get\_queue\_metadata()** demande au service de File d'attente de renvoyer le nombre de messages approximatif et les métadonnées relatives à la file d'attente.

    message_count, metadata = azure_queue_service.get_queue_metadata(
      "test-queue")

## <span id="how-to-delete-a-queue"></span></a> Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **delete\_queue()** sur l'objet file d'attente.

    azure_queue_service.delete_queue("test-queue")

## <span id="next-steps"></span></a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage des files d'attente, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][]
-   Consultez le [blog de l'équipe Azure Storage][]
-   Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby][] sur GitHub.

Pour obtenir une comparaison entre le service de File d'attente Azure abordé dans cette rubrique et les files d'attente Azure Service Bus abordées dans la rubrique [Utilisation des files d'attente Service Bus][], consultez la page [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences][].

  [Présentation des files d’attente de stockage]: #what-is
  [Concepts]: #concepts
  [Création d'un compte de stockage Azure]: #CreateAccount
  [Création d'une application Ruby]: #create-a-ruby-application
  [Configuration de votre application pour accéder au stockage]: #configure-your-application-to-access-storage
  [Configuration d'une connexion Azure Storage]: #setup-a-windows-azure-storage-connection
  [Création d'une file d'attente]: #how-to-create-a-queue
  [Insertion d'un message dans une file d'attente]: #how-to-insert-a-message-into-a-queue
  [Lecture furtive du message suivant]: #how-to-peek-at-the-next-message
  [Enlèvement du message suivant de la file d'attente]: #how-to-dequeue-the-next-message
  [Modification du contenu d'un message en file d'attente]: #how-to-change-the-contents-of-a-queued-message
  [Options supplémentaires pour l'enlèvement des messages de la file d'attente]: #how-to-additional-options-for-dequeuing-messages
  [Obtention de la longueur de la file d'attente]: #how-to-get-the-queue-length
  [Suppression d'une file d'attente]: #how-to-delete-a-queue
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Create a Ruby Application on Azure]: /en-us/develop/ruby/tutorials/web-app-with-linux-vm/
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
  [Kit de développement logiciel (SDK) Azure pour Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
  [Utilisation des files d'attente Service Bus]: /en-us/develop/ruby/how-to-guides/service-bus-queues/
  [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences]: http://msdn.microsoft.com/en-us/library/windowsazure/hh767287.aspx
