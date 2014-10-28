<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande"></tags>

# Création du rôle de travail B (expéditeur de messages) pour l'application Azure Email Service : 5 sur 5.

Voici le cinquième didacticiel de notre série sur le développement et le déploiement de l'exemple d'application Azure Email Service. Pour obtenir des informations sur l'application et la série de didacticiels, consultez le [premier didacticiel de la série][].

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   ajouter un rôle de travail dans un projet de service cloud ;
-   interroger une file d'attente et traiter ses éléments de travail ;
-   envoyer des messages électroniques avec SendGrid ;
-   gérer les arrêts planifiés par remplacement de la méthode `OnStop` ;
-   gérer les arrêts non planifiés en s'assurant qu'aucun courrier électronique n'est envoyé en double.

## Sections de ce didacticiel

-   [Ajout d'un projet de rôle de travail B à la solution][]
-   [Ajout d'une référence au projet Web][]
-   [Ajout du package NuGet SendGrid au projet][]
-   [Ajout des paramètres du projet][]
-   [Ajout de code qui s'exécute au démarrage du rôle de travail][]
-   [Test du rôle de travail B][]
-   [Étapes suivantes][]

## <a name="addworkerrole"></a><span class="short-header">Ajout du rôle de travail B</span>Ajout d'un projet de rôle de travail B à la solution

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de service cloud, puis sélectionnez **Nouveau projet de rôle de travail**.

    ![Menu Nouveau projet de rôle de travail][]

2.  Dans la boîte de dialogue **Ajouter un nouveau projet de rôle**, sélectionnez **C#**, **Rôle de travail**, nommez le projet WorkerRoleB, puis cliquez sur **Ajouter**.

    ![Boîte de dialogue Nouveau projet de rôle][]

## <a name="addreference"></a>Ajout d'une référence au projet Web

Vous avez besoin d'une référence au projet Web, car les classes d'entité sont définies dedans. Vous utilisez les classes d'entité du rôle de travail B pour lire et écrire les données dans les tables Azure utilisées par l'application.

1.  Cliquez avec le bouton droit sur le projet WorkerRoleB, puis choisissez **Ajouter - Référence**.

2.  Dans le **Gestionnaire de références**, ajoutez une référence au projet MvcWebRole.

    ![Ajout d'une référence à MvcWebRole][]

## <a name="addsendgrid"></a>Ajout du package NuGet SendGrid au projet

Pour envoyer des messages électroniques avec SendGrid, vous devez installer le package NuGet SendGrid.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet WorkerRoleB, puis sélectionnez **Gérer les packages NuGet**.

    ![Gérer les packages NuGet][]

2.  Dans la boîte de dialogue **Gérer les packages NuGet**, sélectionnez l'onglet **En ligne**, entrez « sendgrid » dans la zone de recherche, puis appuyez sur Entrée.

3.  Cliquez sur le bouton **Installer** du package **SendGrid**.

    ![Installer le package Sendgrid][]

4.  Fermez la boîte de dialogue.

## <a name="addsettings"></a>Ajout des paramètres du projet

Comme le rôle de travail A, le rôle de travail B a besoin des informations d'identification du compte de stockage pour pouvoir fonctionner avec les tables, les files d'attente et les objets blob. De plus, pour pouvoir envoyer des courriers électroniques, le rôle de travail doit posséder des informations d'identification pour intégrer des appels entrants au service SendGrid. Et pour pouvoir créer un lien de désabonnement à inclure dans les messages électroniques qu'il envoie, le rôle de travail doit connaître l'URL de l'application. Ces valeurs sont stockées dans les paramètres du projet.

Pour obtenir les informations d'identification du compte de stockage, suivez la procédure présentée dans [le troisième didacticiel][].

1.  Dans l'**Explorateur de solutions**, sous **Rôles** dans le projet cloud, cliquez avec le bouton droit sur **WorkerRoleB**, puis sélectionnez **Propriétés**.

2.  Sélectionnez l'onglet **Paramètres**.

3.  Assurez-vous que l'option **Toutes les configurations** est sélectionnée dans la liste déroulante **Configuration du service**.

4.  Sélectionnez l'onglet **Paramètres**, puis cliquez sur **Ajouter un paramètre**.

5.  Entrez « StorageConnectionString » dans la colonne **Nom**.

6.  Sélectionnez **Chaîne de connexion** dans la liste déroulante **Type**.

7.  Cliquez sur le bouton **...** situé à l'extrême droite de la ligne pour ouvrir la boîte de dialogue **Chaîne de connexion de compte de stockage**.

8.  Dans la boîte de dialogue **Créer une chaîne de connexion de stockage**, cliquez sur l'option **Émulateur de stockage Azure**, puis sur **OK**.

Ensuite, créez et configurez les trois nouveaux paramètres utilisés uniquement par le rôle de travail B.

1.  Sous l'onglet **Paramètres** de la fenêtre **Propriétés**, cliquez sur **Ajouter un paramètre**, puis ajoutez trois nouveaux paramètres au type **Chaîne** :

    -   **Nom** : SendGridUserName, **Valeur** : le nom d'utilisateur SendGrid que vous avez choisi dans le [deuxième didacticiel][].

    -   **Nom** : SendGridPassword, **Valeur** : le mot de passe SendGrid.

    -   **Nom** : AzureMailServiceURL, **Valeur** : l'URL de base de l'application lorsque vous la déployez, par exemple : <http://sampleurl.cloudapp.net>.

    ![Nouveaux paramètres du projet WorkerRoleB][]

## <a name="addcode"></a>Ajout de code qui s'exécute au démarrage du rôle de travail

1.  Dans le projet WorkerRoleB, supprimez WorkerRole.cs.

2.  Dans le projet WorkerRoleB, ajoutez le fichier WorkerRoleB.cs du projet téléchargé.

3.  Générez la solution.

    Si vous obtenez une erreur de génération, vous aurez peut-être plusieurs versions du package NuGet d'Azure Storage. Pour résoudre ce problème, accédez au Gestionnaire de package NuGet de la solution, sélectionnez Packages installés et faites défiler l'écran vers le bas pour voir s'il y a deux instances du package Azure Storage. Sélectionnez l'entrée Azure Storage 4.0.0 et supprimez-la des projets dans lesquels elle est installée. Sélectionnez ensuite l'entrée Azure Storage 3.0.x et installez-la dans les projets où elle est manquante. Fermez et redémarrez Visual Studio, puis générez à nouveau la solution.

4.  Assurez-vous que le projet de service cloud se trouve toujours dans le projet de démarrage de la solution.

### Méthode OnStart

Comme pour le rôle de travail A, la méthode `OnStart` initialise les classes de contexte requises pour utiliser les entités de stockage Azure. Elle vérifie également que l'ensemble des tables, des files d'attente et des conteneurs d'objets blob requis pour la méthode `Run` existent.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            // Read storage account configuration settings
            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in worker role B");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            // Initialize queue storage 
            Trace.TraceInformation("Creating queue client.");
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
            this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

            // Initialize blob storage
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
            this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

            // Initialize table storage
            var tableClient = storageAccount.CreateCloudTableClient();
            tableServiceContext = tableClient.GetDataServiceContext();

            Trace.TraceInformation("WorkerB: Creating blob container, queue, tables, if they don't exist.");
            this.blobContainer.CreateIfNotExists();
            this.sendEmailQueue.CreateIfNotExists();
            this.subscribeQueue.CreateIfNotExists();
            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            return base.OnStart();
        }

La différence avec le rôle de travail A est l'ajout d'un conteneur d'objets blob et d'une file d'attente d'abonnement aux ressources à créer, s'ils n'existent pas déjà. Vous allez utiliser le conteneur d'objets blob pour obtenir les fichiers contenant du HTML et du texte clair pour le corps du message électronique. La file d'attente d'abonnement permet d'envoyer des messages électroniques de confirmation d'abonnement.

### Méthode Run

La méthode `Run` traite les éléments de travail à partir de deux files d'attente : la file d'attente utilisée pour les messages envoyés aux listes de diffusion (éléments de travail créés par le rôle de travail A) et la file d'attente utilisée pour les messages électroniques de confirmation d'abonnement (éléments de travail créés par la méthode API d'abonnement dans le projet MvcWebRole).

        public override void Run()
        {
            CloudQueueMessage msg = null;

            Trace.TraceInformation("WorkerRoleB start of Run()");
            while (true)
            {
                try
                {
                    bool messageFound = false;

                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    if (msg != null)
                    {
                        err += " Last queue message retrieved: " + msg.AsString;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in either process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Ce code s'exécute dans une boucle infinie jusqu'à l'arrêt du rôle de travail. Si un élément de travail est trouvé dans la file d'attente principale, le code le traite, puis vérifie la file d'attente d'abonnement.

                    // Retrieve and process a new message from the send-email-to-list queue.
                    msg = this.sendEmailQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessQueueMessage(msg);
                        messageFound = true;
                    }

                    // Retrieve and process a new message from the subscribe queue.
                    msg = this.subscribeQueue.GetMessage();
                    if (msg != null)
                    {
                        ProcessSubscribeQueueMessage(msg);
                        messageFound = true;
                    }

S'il n'y a pas d'élément en attente dans l'une des files, le code passe en veille pendant 60 secondes avant de continuer la boucle.

                    if (messageFound == false)
                    {
                        System.Threading.Thread.Sleep(1000 * 60);
                    }

Cette veille permet de réduire les frais de transaction Azure Storage, comme cela est expliqué dans [le didacticiel précédent][].

Lorsqu'un élément de file d'attente est extrait de la file par la méthode [GetMessage][], il devient invisible durant 30 secondes pour tous les autres rôles de travail et Web qui accèdent à la file d'attente. Cela permet de garantir que seule une instance de rôle de travail choisit un message de file d'attente spécifique pour le traiter. Vous pouvez définir ce délai *exclusif* (durée d'invisibilité de l'élément) de manière explicite en transmettant un paramètre de [délai d'expiration de la visibilité][] à la méthode `GetMessage`. Si le traitement d'un message de file d'attente peut prendre plus de 30 secondes au rôle de travail, vous devez augmenter le délai exclusif pour éviter que le même message soit traité par d'autres instances de rôle.

Cependant, il ne vaut mieux pas définir le délai exclusif sur une valeur trop grande. Par exemple, si le délai exclusif est défini sur 48 heures et que votre rôle de travail s'arrête de manière imprévue après l'extraction d'un message d'une file d'attente, les autres rôles de travail ne pourront pas traiter le message durant les 48 prochaines heures. Le délai exclusif maximum est de 7 jours.

La méthode [GetMessages][] (remarquez le « s » à la fin du nom) permet d'extraire jusqu'à 32 messages de la file d'attente par appel. Chaque accès à la file d'attente implique des frais de transaction réduits, qui ne changent pas, peu importe le nombre de messages renvoyés. Le code suivant récupère et traite jusqu'à 32 messages en un appel.

        foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))
        {
            ProcessQueueMessage(msg);
            messageFound = true;
        }

Lors de l'utilisation de la méthode `GetMessages` pour supprimer plusieurs messages, vérifiez que le délai d'expiration de la visibilité laisse assez de temps à votre application pour traiter tous les messages. Une fois ce délai expiré, les autres instances de rôle peuvent accéder au message. Lorsqu'elles y accèdent, la première instance ne peut plus supprimer le message lorsqu'elle termine le traitement de l'élément de travail.

### Méthode ProcessQueueMessage

La méthode `Run` appelle `ProcessQueueMessage` lorsqu'elle trouve un élément de travail dans la file d'attente principale :

        private void ProcessQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.           
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                    msg.ToString(), GetRoleInstance());
                sendEmailQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue.
            // Example:  2012-01-01,0123456789email@domain.com,0
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];
            Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                partitionKey, rowKey, GetRoleInstance());
            // If this is a restart, verify that the email hasn't already been sent.
            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    // SendEmail row is in archive, so email is already sent. 
                    // If there's a SendEmail Row in message table, delete it,
                    // and delete the queue message.
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }
                        // Get the row in the Message table that has data we need to send the email.
            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }
            // Derive blob names from the MessageRef.
            var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
            var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
            // If the email hasn't already been sent, send email and archive the table row.
            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

            // Delete the queue message.
            sendEmailQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessQueueMessage complete:  partitionKey {0} rowKey {1} Role Instance {2}.",
               partitionKey, rowKey, GetRoleInstance());
        }

Les messages empoisonnés sont ceux qui poussent l'application à lever une exception lorsqu'elle les traite. Si un message est extrait de la file d'attente à plus de cinq reprises, nous partons du principe qu'il ne peut pas être traité et nous le supprimons de la file d'attente pour ne plus avoir à le traiter. Les applications de production doivent déplacer un message empoisonné vers une file d'attente de « messages morts » pour analyser le message au lieu de le supprimer.

Le code analyse le message en file d'attente dans les clés de partition et de ligne nécessaires pour récupérer la ligne SendEmail, ainsi qu'un indicateur de redémarrage.

            var messageParts = msg.AsString.Split(new char[] { ',' });
            var partitionKey = messageParts[0];
            var rowKey = messageParts[1];
            var restartFlag = messageParts[2];

Si le traitement de ce message a été redémarré après un arrêt inattendu, le code vérifie la table `messagearchive` pour déterminer si ce message électronique a déjà été envoyé. Si c'est le cas, le code supprime la ligne `SendEmail` existante, ainsi que le message en file d'attente.

            if (restartFlag == "1")
            {
                var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                if (messagearchiveRow != null)
                {
                    Trace.TraceInformation("Email already sent: partitionKey=" + partitionKey + " rowKey= " + rowKey);
                    var deleteOperation = TableOperation.Delete(new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" });
                    try
                    {
                        messageTable.Execute(deleteOperation);
                    }
                    catch
                    {
                    }
                    sendEmailQueue.DeleteMessage(msg);
                    return;
                }
            }

Ensuite, nous obtenons la ligne `SendEmail` dans la table `message`. Cette ligne contient toutes les informations requises pour l'envoi du message électronique, à l'exception des objets blob contenant le HTML et le texte clair du corps du message.

            var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
            var retrievedResult = messageTable.Execute(retrieveOperation);
            var emailRowInMessageTable = retrievedResult.Result as SendEmail;
            if (emailRowInMessageTable == null)
            {
                Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                    partitionKey, rowKey, GetRoleInstance());
                return;
            }

Le code envoie alors le message électronique et archive la ligne `SendEmail`.

            if (emailRowInMessageTable.EmailSent != true)
            {
                SendEmailToList(emailRowInMessageTable, htmlMessageBodyRef, textMessageBodyRef);

                var emailRowToDelete = new SendEmail { PartitionKey = partitionKey, RowKey = rowKey, ETag = "*" };
                emailRowInMessageTable.EmailSent = true;

                var upsertOperation = TableOperation.InsertOrReplace(emailRowInMessageTable);
                messagearchiveTable.Execute(upsertOperation);
                var deleteOperation = TableOperation.Delete(emailRowToDelete);
                messageTable.Execute(deleteOperation);
            }

Dans une transaction, il est impossible de déplacer la ligne dans la table messagearchive, car cela a une incidence sur plusieurs tables.

Finalement, si tout se passe correctement, le message en file d'attente est supprimé.

            sendEmailQueue.DeleteMessage(msg);

### Méthode SendEmailToList

Le travail d'envoi du message électronique avec SendGrid est effectué par la méthode `SendEmailToList`. Si vous voulez utiliser un autre service que SendGrid, tout ce que vous avez à faire est de modifier le code de cette méthode.

**Remarque :** si les informations d'identification contenues dans les paramètres du projet ne sont pas correctes, l'appel vers SendGrid échoue mais l'application n'indique pas les raisons de l'échec. Si vous utilisez SendGrid dans une application de production, pensez à configurer des informations d'identification distinctes pour l'API Web, afin d'éviter des échecs silencieux lorsqu'un administrateur modifie le mot de passe de son compte d'utilisateur SendGrid. Pour plus d'informations, consultez la page [SendGrid MultiAuth : informations d'identification de plusieurs comptes][]. Vous pouvez configurer vos informations d'identification sur la page [][]<https://sendgrid.com/credentials></a>.

        private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
            string htmlMessageBodyRef, string textMessageBodyRef)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(fromEmailAddress);
            email.AddTo(emailAddress);
            email.Html = GetBlobText(htmlMessageBodyRef);
            email.Text = GetBlobText(textMessageBodyRef);
            email.Subject = subjectLine;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

        private string GetBlobText(string blogRef)
        {
            var blob = blobContainer.GetBlockBlobReference(blogRef);
            blob.FetchAttributes();
            var blobSize = blob.Properties.Length;
            using (var memoryStream = new MemoryStream((int)blobSize))
            {
                blob.DownloadToStream(memoryStream);
                return System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }
        }

Dans la méthode `GetBlobText`, le code obtient la taille des objets blob, puis utilise cette valeur pour initialiser l'objet `MemoryStream` pour des raisons de performances. Si vous ne fournissez pas la taille, l'objet `MemoryStream` alloue 256 octets, puis 512 octets lorsque le téléchargement dépasse cette taille, et ainsi de suite, en doublant la quantité allouée à chaque fois. Ce processus n'est pas adapté à un objet blob volumineux, auquel il vaut mieux allouer une quantité suffisante dès le début du téléchargement.

### Méthode ProcessSubscribeQueueMessage

La méthode `Run` appelle `ProcessSubscribeQueueMessage` lorsqu'elle trouve un élément de travail dans la file d'attente d'abonnement :

        private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
        {
            // Log and delete if this is a "poison" queue message (repeatedly processed
            // and always causes an error that prevents processing from completing).
            // Production applications should move the "poison" message to a "dead message"
            // queue for analysis rather than deleting the message.  
            if (msg.DequeueCount > 5)
            {
                Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                    msg.AsString, GetRoleInstance());
                subscribeQueue.DeleteMessage(msg);
                return;
            }
            // Parse message retrieved from queue. Message consists of
            // subscriber GUID and list name.
            // Example:  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
            var messageParts = msg.AsString.Split(new char[] { ',' });
            var subscriberGUID = messageParts[0];
            var listName = messageParts[1];
            Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                subscriberGUID, listName, GetRoleInstance());
            // Get subscriber info. 
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
            // Get mailing list info.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(subscriber.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;

            SendSubscribeEmail(subscriberGUID, subscriber, mailingList);

            subscribeQueue.DeleteMessage(msg);

            Trace.TraceInformation("ProcessSubscribeQueueMessage complete: subscriber GUID {0} Role Instance {1}.",
                subscriberGUID, GetRoleInstance());
        }

Cette méthode effectue les tâches suivantes :

-   Si le message est « empoisonné », elle crée une entrée de journal puis supprime le message.
-   Elle obtient le GUID d'abonné depuis le message en file d'attente.
-   Elle utilise le GUID pour obtenir des informations d'abonné depuis la table MailingList.
-   Elle envoie un message électronique de confirmation au nouvel abonné.
-   Elle supprime le message en file d'attente.

Comme pour les messages électroniques envoyés aux listes, l'envoi du message électronique est effectué par une méthode distincte, ce qui vous permet de changer rapidement de service de messagerie électronique si vous en avez besoin.

        private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
        {
            var email = SendGrid.GetInstance();
            email.From = new MailAddress(mailingList.FromEmailAddress);
            email.AddTo(subscriber.EmailAddress);
            string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
            email.Html = String.Format("<p>Click the link below to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.</p>" +
                "<a href=\"{1}\">Confirm Subscription</a>", mailingList.Description, subscribeURL);
            email.Text = String.Format("Copy and paste the following URL into your browser in order to subscribe to {0}. " +
                "If you don't confirm your subscription, you won't be subscribed to the list.\n" +
                "{1}", mailingList.Description, subscribeURL);
            email.Subject = "Subscribe to " + mailingList.Description;
            var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
            var transportREST = Web.GetInstance(credentials);
            transportREST.Deliver(email);
        }

## <a name="testing"></a>Test du rôle de travail B

1.  Exécutez l'application en appuyant sur F5.

2.  Accédez à la page **Messages** pour afficher le message que vous avez créé pour le test du rôle de travail A. Après une minute ou deux, actualisez la page Web pour constater que la ligne a disparu de la liste, car elle a été archivée.

3.  Consultez la boîte de réception de votre messagerie (là où le message électronique doit arriver). Remarque : un certain temps peut s'écouler entre l'envoi de messages électroniques avec SendGrid et la réception par votre client de messagerie. Vous devez également consulter votre dossier de messages indésirables.

## <a name="nextsteps"></a>Étapes suivantes

Vous avez créé intégralement l'application Azure Email Service, et le résultat est identique au projet complet que vous avez téléchargé. Pour le déployer et le tester dans le cloud, puis le faire passer en production, vous pouvez utiliser les mêmes procédures que celles du [deuxième didacticiel][].

Pour un exemple d'application illustrant l'utilisation de LINQ dans les requêtes du service de table Azure Storage, consultez ce scénario [PhluffyFotos][].

Pour en savoir plus sur Azure Storage, consultez les ressources suivantes :

-   [Informations essentielles sur Azure Storage][] (blog de Bruno Terkaly)

Pour en savoir plus sur le service de stockage de tables Azure, consultez les ressources suivantes :

-   [Informations essentielles sur le stockage de tables Azure][] (blog de Bruno Terkaly)
-   [Comment tirer le meilleur parti du stockage de tables Azure][] (blog de l'équipe Azure Storage)
-   [Utilisation du service de stockage de tables dans .NET][]
-   [Présentation approfondie des tables de la bibliothèque cliente de stockage 2.0][] (blog de l'équipe Azure Storage)
-   [Environnement réel : conception d'une stratégie de partition extensible pour le stockage de tables Azure][]

Pour en savoir plus sur le service de files d'attente Azure et les files d'attente Azure Service Bus, consultez les ressources suivantes :

-   [Modèle de travail centré sur les files d'attente (développement d'applications de cloud plus concrètes avec Azure).][]
-   [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences][]
-   [Utilisation du service de stockage de files d'attente dans .NET][]

Pour en savoir plus sur le service de stockage d'objets blob Azure, consultez les ressources suivantes :

-   [Stockage d'objets blob non structurés (développement d'applications de cloud plus concrètes avec Azure).][]
-   [Utilisation du service de stockage d'objets blob Azure dans .NET][]

Pour en savoir plus sur la mise à l'échelle automatique des rôles de service cloud Azure, consultez les ressources suivantes :

-   [Utilisation du bloc applicatif de mise à l'échelle automatique][]
-   [Mise à l'échelle automatique et Azure][]
-   [Développement de solutions flexibles pouvant être mises à l'échelle automatiquement avec Azure][] (vidéo MSDN Channel 9)

## <a name="Acknowledgments"></a><span class="short-header">Remerciements</span>Remerciements

Ces didacticiels et exemples d'applications ont été écrits par [Rick Anderson][] et Tom Dykstra. Nous aimerions remercier les personnes suivantes pour leur aide :

-   Barry Dorrans (Twitter [@blowdart][])
-   [Cory Fowler][] (Twitter [@SyntaxC4][] )
-   [Joe Giardino][]
-   Don Glover
-   Jai Haridas
-   [Scott Hunter][] (Twitter [@coolcsh][])
-   [Brian Swan][]
-   [Daniel Wang][]
-   Merci aux membres du Conseil consultatif des développeurs pour leurs commentaires :
	-   Damir Arh
	-   Jean-Luc Boucho
	-   Carlos dos Santos
	-   Mike Douglas
	-   Robert Fite
	-   Gianni Rosa Gallina
	-   Fabien Lavocat
	-   Karl Ots
	-   Carlos-Alejandro Perez
	-   Sunao Tomita
	-   Perez Jones Tsisah
	-   Michiel van Otegem

  [premier didacticiel de la série]: /fr-fr/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Ajout d'un projet de rôle de travail B à la solution]: #addworkerrole
  [Ajout d'une référence au projet Web]: #addreference
  [Ajout du package NuGet SendGrid au projet]: #addsendgrid
  [Ajout des paramètres du projet]: #addsettings
  [Ajout de code qui s'exécute au démarrage du rôle de travail]: #addcode
  [Test du rôle de travail B]: #testing
  [Étapes suivantes]: #nextsteps
  [Menu Nouveau projet de rôle de travail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png
  [Boîte de dialogue Nouveau projet de rôle]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png
  [Ajout d'une référence à MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png
  [Gérer les packages NuGet]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png
  [Installer le package Sendgrid]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png
  [le troisième didacticiel]: /fr-fr/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage
  [deuxième didacticiel]: /fr-fr/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Nouveaux paramètres du projet WorkerRoleB]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png
  [le didacticiel précédent]: /fr-fr/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [GetMessage]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee741827.aspx
  [délai d'expiration de la visibilité]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758454.aspx
  [GetMessages]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx
  [SendGrid MultiAuth : informations d'identification de plusieurs comptes]: http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials
  []: https://sendgrid.com/credentials
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Informations essentielles sur Azure Storage]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx
  [Informations essentielles sur le stockage de tables Azure]: http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx
  [Comment tirer le meilleur parti du stockage de tables Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Utilisation du service de stockage de tables dans .NET]: http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/table-services/
  [Présentation approfondie des tables de la bibliothèque cliente de stockage 2.0]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Environnement réel : conception d'une stratégie de partition extensible pour le stockage de tables Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh508997.aspx
  [Modèle de travail centré sur les files d'attente (développement d'applications de cloud plus concrètes avec Azure).]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern
  [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh767287.aspx
  [Utilisation du service de stockage de files d'attente dans .NET]: /fr-fr/develop/net/how-to-guides/queue-service/
  [Stockage d'objets blob non structurés (développement d'applications de cloud plus concrètes avec Azure).]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage
  [Utilisation du service de stockage d'objets blob Azure dans .NET]: /fr-fr/develop/net/how-to-guides/blob-storage/
  [Utilisation du bloc applicatif de mise à l'échelle automatique]: /fr-fr/develop/net/how-to-guides/autoscaling/
  [Mise à l'échelle automatique et Azure]: http://msdn.microsoft.com/fr-fr/library/hh680945(v=PandP.50).aspx
  [Développement de solutions flexibles pouvant être mises à l'échelle automatiquement avec Azure]: http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04
  [Rick Anderson]: http://blogs.msdn.com/b/rickandy/
  [@blowdart]: https://twitter.com/blowdart
  [Cory Fowler]: http://blog.syntaxc4.net/
  [@SyntaxC4]: https://twitter.com/SyntaxC4
  [Joe Giardino]: http://blogs.msdn.com/b/windowsazurestorage/
  [Scott Hunter]: http://blogs.msdn.com/b/scothu/
  [@coolcsh]: http://twitter.com/coolcsh
  [Brian Swan]: http://blogs.msdn.com/b/brian_swan/
  [Daniel Wang]: http://blogs.msdn.com/b/daniwang/
