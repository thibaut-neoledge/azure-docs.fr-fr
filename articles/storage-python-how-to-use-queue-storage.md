<properties 
	pageTitle="Utilisation du service de File d'attente (Python) | Microsoft Azure" 
	description="Découvrez comment utiliser le service de File d'attente Azure pour créer et supprimer des files d'attente, ainsi que pour insérer, récupérer et supprimer des messages. Les exemples sont écrits en Python." 
	services="storage" 
	documentationCenter="python" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/19/2014" 
	ms.author="robmcm"/>



# Utilisation du service de stockage de files d'attente à partir de Python
Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage de files d'attente Windows Azure. Les exemples sont écrits en utilisant l'API Python. Les scénarios traités incluent l'**insertion**, la **lecture furtive**, la **récupération** et la **suppression** des messages de file d'attente, ainsi que la **création et la suppression des files d'attente**. Pour plus d'informations sur les files d'attente, consultez la section [Étapes suivantes][].

## Sommaire

[Présentation des files d'attente de stockage][]
 [Concepts][]   
 [Création d'un compte Azure Storage][]   
 [Procédure : Création d'une file d'attente][]   
 [Procédure : Insertion d'un message dans une file d'attente][]   
 [Procédure : Lecture furtive du message suivant][]   
 [Procédure : Enlèvement du message suivant de la file d'attente][]   
 [Procédure : Modification du contenu d'un message en file d'attente][]   
 [Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente][]   
 [Procédure : Obtention de la longueur de file d'attente][]   
 [Procédure : Suppression d'une file d'attente][]   
 [Étapes suivantes][]

[AZURE.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <a name="create-account"></a>Création d'un compte de stockage Azure
[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]


**Remarque :** si vous devez installer Python ou les bibliothèques clientes, veuillez consulter le [Guide d'installation de Python](../python-how-to-install/).

## <a name="create-queue"></a>Procédure : Création d'une file d'attente

L'objet **QueueService** permet d'utiliser des files d'attente. Le code suivant permet de créer un objet **QueueService**. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

	from azure.storage import QueueService

Le code suivant permet de créer un objet **QueueService** en utilisant le nom et la clé du compte de stockage. Remplacez 'myaccount' et 'mykey' par le compte et la clé réels.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## <a name="insert-message"></a>Procédure : Insertion d'un message dans une file d'attente

Pour insérer un message dans une file d'attente, utilisez la méthode **put\_message** afin de créer un nouveau message et de l'ajouter à la file d'attente.

	queue_service.put_message('taskqueue', 'Hello World')


## <a name="peek-message"></a>Procédure : Lecture furtive du message suivant

En appelant la méthode **peek\_messages**, vous pouvez lire furtivement le message au début de la file d'attente sans pour autant l'en retirer. Par défaut,
**peek\_messages** lit un seul message.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## <a name="get-message"></a>Procédure : Enlèvement du message suivant de la file d'attente

Votre code supprime un message d'une file d'attente en deux étapes. Lorsque vous appelez **get\_messages**, vous obtenez le message suivant d'une file d'attente par défaut. Un message renvoyé par **get\_messages** devient invisible pour les autres codes lisant les messages de cette file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. Pour finaliser la suppression du message de la file d'attente, vous devez aussi appeler **delete\_message**. Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Votre code appelle **delete\_message** juste après le traitement du message.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## <a name="change-contents"></a>Procédure : Modification du contenu d'un message en file d'attente

Vous pouvez modifier le contenu d'un message placé dans la file d'attente. Si le message représente une tâche, vous pouvez utiliser cette fonctionnalité pour mettre à jour l'état de la tâche. Le code ci-dessous utilise la méthode **update\_message**
pour mettre à jour un message.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## <a name="advanced-get"></a>Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente

Il existe deux façons de personnaliser l'extraction des messages à partir d'une file d'attente. Premièrement, vous pouvez obtenir un lot de messages (jusqu'à 32). Deuxièmement, vous pouvez définir un délai d'expiration de l'invisibilité plus long ou plus court afin d'accorder à votre code plus ou moins de temps pour traiter complètement chaque message. L'exemple de code suivant utilise la méthode **get\_messages** pour obtenir 16 messages en un appel. Ensuite, il traite
chaque message à l'aide d'une boucle for. Il définit également le délai d'expiration de l'invisibilité sur cinq minutes pour chaque message.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## <a name="get-queue-length"></a>Procédure : Obtention de la longueur de la file d'attente

Vous pouvez obtenir une estimation du nombre de messages dans une file d'attente. La méthode **get\_queue\_metadata** demande au service de File d'attente de renvoyer des métadonnées sur la file d'attente ; **x-ms-approximate-messages-count** doit être utilisé comme index dans le dictionnaire renvoyé pour trouver le décompte.
Le résultat est seulement approximatif, car des messages peuvent être ajoutés ou supprimés
après que le service de file d'attente a répondu à votre demande.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## <a name="delete-queue"></a>Procédure : Suppression d'une file d'attente

Pour supprimer une file d'attente et tous les messages qu'elle contient, appelez la méthode **delete\_queue**.

	queue_service.delete_queue('taskqueue')

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez acquis les bases du stockage de files d'attente, suivez ces liens pour savoir comment effectuer des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][]
-   Visitez le [Blog de l'équipe Azure Storage][]

  [Étapes suivantes]: #next-steps
  [Présentation des files d'attente de stockage]: #what-is
  [Concepts]: #concepts
  [Création d'un compte Azure Storage]: #create-account
  [Procédure : Création d'une file d'attente]: #create-queue
  [Procédure : Insertion d'un message dans une file d'attente]: #insert-message
  [Procédure : Lecture furtive du message suivant]: #peek-message
  [Procédure : Enlèvement du message suivant de la file d'attente]: #get-message
  [Procédure : Modification du contenu d'un message en file d'attente]: #change-contents
  [Procédure : Options supplémentaires pour l'enlèvement des messages de la file d'attente]: #advanced-get
  [Procédure : Obtention de la longueur de file d'attente]: #get-queue-length
  [Procédure : Suppression d'une file d'attente]: #delete-queue
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/library/windowsazure/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/

\<!--HONumber=42-->
