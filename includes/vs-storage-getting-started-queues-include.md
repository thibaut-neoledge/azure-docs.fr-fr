#####Création d'une file d'attente
Un objet **CloudQueueClient** vous permet d'obtenir les objets de référence pour les files d'attente. Le code suivant crée un objet **CloudQueueClient**. Tous les codes présentés dans cette rubrique utilisent une chaîne de connexion de stockage enregistrée dans la configuration de service de l'application Azure. Plusieurs méthodes permettent de créer un objet **CloudStorageAccount**. Consultez la documentation [CloudStorageAccount](http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") pur plus d'informations.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Utilisez l'objet **queueClient** pour obtenir une référence pointant vers la file d'attente à utiliser. Il tente de référencer la file d'attente nommée " myqueue ". S'il ne trouve aucune file d'attente ainsi nommée, il en crée une.

	// Get a reference to a queue named "myqueue".
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn't already there, then create it.
	queue.CreateIfNotExists();

**Remarque :** ajoutez ce bloc de code avant le code indiqué dans les sections suivantes.

#####Insertion d'un message dans une file d'attente
Pour insérer un message dans une file d'attente existante, commencez par créer un nouvel objet **CloudQueueMessage**. Appelez ensuite la méthode AddMessage(). Un objet **CloudQueueMessage** peut être créé à partir d'une chaîne (au format UTF-8) ou d'un tableau d'octets. Voici le code qui crée une file d'attente (si elle n'existe pas) et insère le message 'Hello, World'.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	queue.AddMessage(message);

#####Lecture furtive du message suivant
En appelant la méthode PeekMessage(), vous pouvez lire furtivement le message au début de la file d'attente sans pour autant l'en retirer.

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	Console.WriteLine(peekedMessage.AsString);

#####Suppression du message suivant
Votre code permet de supprimer (retirer) un message d'une file d'attente en deux étapes. 


1. Lorsque vous appelez GetMessage(), vous obtenez le message suivant au sein de la file d'attente. Tout message renvoyé par un appel GetMessage() n'est plus visible par les autres codes lisant les messages de cette même file d'attente. Par défaut, ce message reste invisible pendant 30 secondes. 
2.	Pour finaliser la suppression du message de la file d'attente, vous devez appeler DeleteMessage(). 

Ce processus de suppression d'un message en deux étapes garantit que, si votre code ne parvient pas à traiter un message à cause d'une défaillance matérielle ou logicielle, une autre instance de votre code peut obtenir le même message et réessayer. Le code suivant appelle DeleteMessage() juste après le traitement du message.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = queue.GetMessage();

	// Process the message in less than 30 seconds, and then delete the message.
	queue.DeleteMessage(retrievedMessage);

[En savoir plus sur Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
Voir aussi [Consultation des ressources de stockage avec l'Explorateur de serveurs](http://msdn.microsoft.com/fr-fr/library/azure/ff683677.aspx).


<!--HONumber=42-->
