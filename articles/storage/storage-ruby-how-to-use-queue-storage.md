<properties 
	pageTitle="Utilisation du stockage de files d’attente à partir de Ruby | Microsoft Azure" 
	description="Découvrez comment utiliser le service de File d'attente Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, récupérer et supprimer des messages. Les exemples sont écrits en Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="cbrooks;robmcm"/>


# Utilisation du stockage de files d'attente à partir de Ruby

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage de files d’attente Microsoft Azure. Les exemples sont écrits à l'aide de l'API Ruby Azure. Les scénarios traités incluent l’**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d’attente, ainsi que la **création et suppression des files d’attente**.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d'une application Ruby

Créez une application Ruby. Pour obtenir des instructions, consultez [Application web Ruby on Rails sur une machine virtuelle Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).

## Configuration de votre application pour accéder au stockage

Pour utiliser Azure Storage, vous devez télécharger et utiliser le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### Utilisation de RubyGems pour obtenir le package

1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).

2. Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### Importation du package

À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

	require "azure"

## Configuration d'une connexion Azure Storage

Le module Azure lit les variables d'environnement **AZURE\_STORAGE\_ACCOUNT** et **AZURE\_STORAGE\_ACCESS\_KEY** pour obtenir les informations nécessaires à la connexion à votre compte Azure Storage. Si ces variables d'environnement ne sont pas définies, vous devez spécifier les informations de compte avant d'utiliser **Azure::QueueService** avec le code suivant :

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your Azure storage access key>"

 
Pour obtenir ces valeurs à partir d’un compte de stockage classique ou Resource Manager sur le portail Azure :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au compte de stockage que vous souhaitez utiliser.
3. Dans le panneau Paramètres à droite, cliquez sur **Clés d’accès**.
4. Dans le panneau Clés d’accès qui apparaît, la clé d’accès 1 et la clé d’accès 2 sont affichées. Vous pouvez utiliser les deux.
5. Cliquez sur l'icône de copie pour copier la clé dans le Presse-papiers.

Pour obtenir ces valeurs à partir d’un compte de stockage classique sur le portail Azure Classic :

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Accédez au compte de stockage que vous souhaitez utiliser.
3. Cliquez sur **GÉRER LES CLÉS D’ACCÈS** en bas du volet de navigation.
4. Dans la boîte de dialogue contextuelle, vous voyez le nom du compte de stockage et la clé d'accès primaire ou secondaire. Vous pouvez utiliser soit la clé d'accès primaire, soit la clé d'accès secondaire.
5. Cliquez sur l'icône de copie pour copier la clé dans le Presse-papiers.

## Création d'une file d'attente

Le code suivant crée un objet **Azure::QueueService**, ce qui vous permet d'utiliser les files d'attente.

	azure_queue_service = Azure::QueueService.new

Utilisez la méthode **create\_queue()** pour créer une file d'attente comportant le nom spécifié.

	begin
	  azure_queue_service.create_queue("test-queue")
	rescue
	  puts $!
	end

## Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente, utilisez la méthode **create\_message()** pour créer un nouveau message et l'ajouter à la file d'attente.

	azure_queue_service.create_message("test-queue", "test message")

## Lecture furtive du message suivant

Vous pouvez lire furtivement le message au début de la file d'attente sans le supprimer de la file d'attente en appelant la méthode **peek\_messages()**. Par défaut, **peek\_messages()** permet de lire furtivement un seul message. Vous pouvez également spécifier le nombre de messages que vous souhaitez lire furtivement.

	result = azure_queue_service.peek_messages("test-queue",
	  {:number_of_messages => 10})

## Suppression du message suivant dans la file d'attente

Vous pouvez supprimer un message d'une file d'attente en deux étapes.

1. Lorsque vous appelez **list\_messages()**, vous obtenez le message suivant dans une file d'attente par défaut. Vous pouvez également spécifier le nombre de messages que vous souhaitez obtenir. Les messages renvoyés par **list\_messages()** deviennent invisibles par les autres codes lisant les messages de cette file d'attente. Vous transmettez le délai d'expiration de la visibilité en secondes en tant que paramètre.

2. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **delete\_message()**.

Ce processus de suppression d’un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d’une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete\_message()** juste après le traitement du message.

	messages = azure_queue_service.list_messages("test-queue", 30)
	azure_queue_service.delete_message("test-queue", 
	  messages[0].id, messages[0].pop_receipt)

## Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Le code ci-dessous utilise la méthode **update\_message()** pour mettre à jour un message. La méthode renvoie un tuple qui contient l'accusé pop du message de file d'attente et une valeur de date et d'heure TUC représentant le moment où le message sera visible dans la file d'attente.

	message = azure_queue_service.list_messages("test-queue", 30)
	pop_receipt, time_next_visible = azure_queue_service.update_message(
	  "test-queue", message.id, message.pop_receipt, "updated test message", 
	  30)

## Options supplémentaires pour la suppression des messages dans la file d'attente

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente.

1. Vous pouvez obtenir un lot de messages.

2. Vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message.

L'exemple de code suivant utilise la méthode **list\_messages()** pour obtenir 15 messages en un appel. Ensuite, il imprime et supprime chaque message. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

	azure_queue_service.list_messages("test-queue", 300
	  {:number_of_messages => 15}).each do |m|
	  puts m.message_text
	  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
	end

## Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans la file d'attente. La méthode **get\_queue\_metadata()** demande au service de File d'attente de renvoyer le nombre de messages approximatif et les métadonnées relatives à la file d'attente.

	message_count, metadata = azure_queue_service.get_queue_metadata(
	  "test-queue")

## Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **delete\_queue()** sur l'objet file d'attente.

	azure_queue_service.delete_queue("test-queue")

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des files d'attente, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

- Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
- Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) sur GitHub.

Pour obtenir une comparaison entre le service de File d’attente Azure abordé dans cette rubrique et les files d’attente Azure Service Bus abordées dans la rubrique [Utilisation des files d’attente Service Bus](/develop/ruby/how-to-guides/service-bus-queues/), consultez la page [Files d’attente Microsoft Azure et files d’attente Azure Service Bus : comparaison et différences](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).
 

<!---HONumber=AcomDC_0928_2016-->