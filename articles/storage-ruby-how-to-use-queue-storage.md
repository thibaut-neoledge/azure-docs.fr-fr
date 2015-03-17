<properties 
	pageTitle="Utilisation du service de File d'attente (Ruby) | Microsoft Azure" 
	description="Découvrez comment utiliser le service de File d'attente Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, récupérer et supprimer des messages. Les exemples sont écrits en Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>





# Utilisation du service de stockage de files d'attente à partir de Ruby

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de files d'attente Azure. Les exemples sont écrits à l'aide de l'API Ruby Azure.
Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et la suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes)]#next-steps) .

## Sommaire

* [Présentation des files d'attente de stockage](#what-is)
* [Concepts](#concepts)
* [Création d'un compte Azure Storage](#CreateAccount)
* [Création d'une application Ruby](#create-a-ruby-application)
* [Configuration de votre application pour accéder au stockage](#configure-your-application-to-access-storage)
* [Configuration d'une connexion Azure Storage](#setup-a-windows-azure-storage-connection)
* [Procédure : Création d'une file d'attente](#how-to-create-a-queue)
* [Procédure : Insertion d'un message dans une file d'attente](#how-to-insert-a-message-into-a-queue)
* [Procédure : Lecture furtive du message suivant](#how-to-peek-at-the-next-message)
* [Procédure : Enlèvement du message suivant de la file d'attente](#how-to-dequeue-the-next-message)
* [Procédure : Modification du contenu d'un message en file d'attente](#how-to-change-the-contents-of-a-queued-message)
* [Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente](#how-to-additional-options-for-dequeuing-messages)
* [Procédure : Obtention de la longueur de file d'attente](#how-to-get-the-queue-length)
* [Procédure : Suppression d'une file d'attente](#how-to-delete-a-queue)
* [Étapes suivantes](#next-steps)

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a id="CreateAccount"></a>Création d'un compte Azure Storage

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## <a id="create-a-ruby-application"></a>Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, 
consultez le guide [Création d'une application Ruby sur Azure](/fr-FR/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1. Utiliser une interface de ligne de commande comme **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).

2. Tapez " gem install azure " dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Configuration d'une connexion Azure Storage

Le module azure lit les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS_KEY** 
pour obtenir les informations obligatoires pour se connecter à votre compte de stockage Azure. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::QueueService** avec le code suivant :

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

Pour obtenir ces valeurs :

1. Connectez-vous au [Portail de gestion Azure](https://manage.windowsazure.com/).
2. Accédez au compte de stockage que vous voulez utiliser.
3. Cliquez sur **GÉRER LES CLÉS** au bas du volet de navigation.
4. Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.

## <a id="how-to-create-a-queue"></a>Procédure : Création d'une file d'attente

Le code suivant crée un objet **Azure::QueueService**, ce qui vous permet d'utiliser les files d'attente.

	azure_queue_service = Azure::QueueService.new

Utilisez la méthode **create_queue()** pour créer une file d'attente comportant le nom spécifié.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## <a id="how-to-insert-a-message-into-a-queue"></a>Procédure : Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente, utilisez la méthode **create_message()** afin de créer un nouveau message et de l'ajouter à la file d'attente.

	azure_queue_service.create_message("test-queue", "test message")

## <a id="how-to-peek-at-the-next-message"></a>Procédure : Lecture furtive du message suivant

En appelant la méthode **peek\_messages()**, vous pouvez lire furtivement le message au début de la file d'attente sans pour autant l'en retirer. Par défaut, **peek\_messages()** permet de lire furtivement un seul message. Vous pouvez également spécifier le nombre de messages que vous souhaitez lire furtivement.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## <a id="how-to-dequeue-the-next-message"></a>Procédure : Enlèvement du message suivant de la file d'attente

Vous pouvez supprimer un message d'une file d'attente en deux étapes.

1. Lorsque vous appelez **list\_messages()**, vous obtenez le message suivant d'une file d'attente par défaut. Vous pouvez également spécifier le nombre de messages que vous souhaitez obtenir. Les messages renvoyés par **list\_messages()** deviennent invisibles par les autres codes lisant les messages de cette file d'attente. Vous transmettez le délai d'expiration de la visibilité en secondes en tant que paramètre.

2. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **delete_message()**.

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete\_message()** juste après le traitement du message.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## <a id="how-to-change-the-contents-of-a-queued-message"></a>Procédure : Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Le code ci-dessous utilise la méthode **update_message()** pour mettre à jour un message. La méthode renvoie un tuple qui contient l'accusé pop du message de file d'attente et une valeur de date et d'heure TUC représentant le moment où le message sera visible dans la file d'attente.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## <a id="how-to-additional-options-for-dequeuing-messages"></a>Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente.

1. Vous pouvez obtenir un lot de messages.

2. Vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message.

L'exemple de code suivant utilise la méthode **list\_messages()** pour obtenir 15 messages en un appel. Ensuite, il imprime et supprime chaque message. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## <a id="how-to-get-the-queue-length"></a>Procédure : Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans la file d'attente. La méthode **get\_queue\_metadata()** demande au service de File d'attente de renvoyer le nombre de messages approximatif et les métadonnées relatives à la file d'attente.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## <a id="how-to-delete-a-queue"></a>Procédure : Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **delete\_queue()** sur l'objet de file d'attente.

	azure_queue_service.delete_queue("test-queue")

## <a id="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage des files d'attente, suivez ces liens pour apprendre des tâches de stockage plus complexes.

- Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/library/windowsazure/gg433040.aspx)
- Visitez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
- Accédez au [référentiel du Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub.

Pour obtenir une comparaison entre le service de File d'attente Azure abordé dans cette rubrique et les files d'attente Azure Service Bus abordées dans la rubrique [Utilisation des files d'attente Service Bus](/fr-FR/develop/ruby/how-to-guides/service-bus-queues/), consultez la page [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences](http://msdn.microsoft.com/library/windowsazure/hh767287.aspx)

\<!--HONumber=42-->
