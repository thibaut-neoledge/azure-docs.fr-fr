<properties linkid="develop-net-tutorials-multi-tier-web-site-5-worker-role-b" urlDisplayName="Step 5: Worker Role B" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 5: Worker role B" metaKeywords="Azure tutorial, adding working role cloud service, C# worker role" description="The fifth tutorial in a series that teaches how to configure your computer for Azure development and deploy the Email Service app." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building worker role B (email sender) for the Azure Email Service application - 5 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Création du rôle de travail B (expéditeur de messages) pour l'application Azure Email Service : 5 sur 5.
========================================================================================================

Voici le cinquième didacticiel de notre série sur le développement et le déploiement de l'exemple d'application Azure Email Service. Pour obtenir des informations sur l'application et la série de didacticiels, consultez le [premier didacticiel de la série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   ajouter un rôle de travail dans un projet de service cloud ;
-   interroger une file d'attente et traiter ses éléments de travail ;
-   envoyer des messages électroniques avec SendGrid ;
-   gérer les arrêts planifiés par remplacement de la méthode `OnStop` ;
-   gérer les arrêts non planifiés en s'assurant qu'aucun courrier électronique n'est envoyé en double.

Ajout du rôle de travail BAjout d'un projet de rôle de travail B à la solution
------------------------------------------------------------------------------

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet de service cloud, puis sélectionnez **Nouveau projet de rôle de travail**.

    ![Menu Nouveau projet de rôle de travail](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-new-worker-role-project.png)

2.  Dans la boîte de dialogue **Ajouter un nouveau projet de rôle**, sélectionnez **C\#**, **Rôle de travail**, nommez le projet WorkerRoleB, puis cliquez sur **Ajouter**.

    ![Boîte de dialogue Nouveau projet de rôle](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-add-new-role-project-dialog.png)

Ajout d'une référenceAjout d'une référence au projet Web
--------------------------------------------------------

Vous avez besoin d'une référence au projet Web, car les classes d'entité sont définies dedans. Vous utilisez les classes d'entité du rôle de travail B pour lire et écrire les données dans les tables Azure utilisées par l'application.

1.  Cliquez avec le bouton droit sur le projet WorkerRoleB, puis choisissez **Ajouter une référence**.

    ![Ajouter une référence au projet WorkerRoleB](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-reference-menu.png)

2.  Dans le **Gestionnaire de références**, ajoutez une référence au projet MvcWebRole (ou au projet d'application Web si vous exécutez l'interface utilisateur Web dans un site Web Azure).

    ![Ajout d'une référence à MvcWebRole](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-reference-manager.png)

Ajout d'un package SCL 2.0Ajout du package NuGet de la bibliothèque cliente de stockage (version 2.0) au projet
---------------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Avec Visual Studio 2013, vous pouvez ignorer cette section, car le package Azure Storage actuel est installé dans le nouveau projet de rôle de travail.

Lorsque vous avez ajouté le projet, il n'a pas obtenu automatiquement la version mise à jour du package NuGet de bibliothèque cliente de stockage. Il a obtenu la version 1.7, car celle-ci est incluse dans le modèle du projet. À présent, la solution a deux versions du package NuGet de bibliothèque cliente de stockage Azure : la version 2.0 dans les projets MvcWebRole et WorkerRoleA et la version 1.7 dans le projet WorkerRoleB. Vous devez désinstaller la version 1.7 et installer la version 2.0 dans le projet WorkerRoleB.

1.  Dans le menu **Outils**, sélectionnez **Gestionnaire de package de bibliothèques**, puis **Gérer les packages NuGet pour la solution**.

2.  Tout en sélectionnant **Packages installés** dans le volet de gauche, faites défiler la page pour obtenir le package Azure Storage.

    Le package est répertorié deux fois : une fois pour la version 1.7 et une fois pour la version 2.0.

3.  Sélectionnez la version 1.7 du package, puis cliquez sur **Gérer**.

    Les cases à cocher pour MvcWebRole et WorkerRoleB sont désactivées alors que la case à cocher pour WorkerRoleB est activée.

4.  Désactivez la case à cocher pour WorkerRoleB, puis cliquez sur **OK**.

5.  À l'invite vous demandant si vous voulez désinstaller les packages liés, cliquez sur **Non**.

    Une fois la désinstallation terminée, seule la version 2.0 du package est affichée dans la boîte de dialogue NuGet.

6.  Cliquez sur **Gérer** pour la version 2.0 du package.

    Les cases à cocher pour MvcWebRole et WorkerRoleA sont sélectionnées alors que la case à cocher pour WorkerRoleA est désactivée.

7.  Désactivez la case à cocher pour WorkerRoleA, puis cliquez sur **OK**.

Ajout d'une référence SCL 1.7Ajout d'une référence à un assembly SCL 1.7
------------------------------------------------------------------------

> [WACOM.NOTE] Ignorez cette section si vous avez installé le dernier Kit de développement logiciel (SDK) et que vous utilisez Visual Studio 2013

La version 2.0 de la bibliothèque cliente de stockage (SCL) ne possédant pas tous les éléments requis pour le diagnostic, vous devez ajouter une référence à un des assemblys 1.7, comme vous l'avez fait précédemment pour les deux autres projets.

1.  Cliquez avec le bouton droit sur le projet WorkerRoleB, puis choisissez **Ajouter une référence**.

2.  Cliquez sur le bouton **Parcourir...** en bas de la boîte de dialogue.

3.  Accédez au dossier suivant :

         C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Sélectionnez *Microsoft.WindowsAzure.StorageClient.dll*, puis cliquez sur **Ajouter**.

5.  Dans la boîte de dialogue **Gestionnaire de références**, cliquez sur **OK**.

Ajout du package SendGridAjout du package NuGet SendGrid au projet
------------------------------------------------------------------

Pour envoyer des messages électroniques avec SendGrid, vous devez installer le package NuGet SendGrid.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet WorkerRoleB, puis sélectionnez **Gérer les packages NuGet**.

    ![Gérer les packages NuGet](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-manage-nuget.png)

2.  Dans la boîte de dialogue **Gérer les packages NuGet**, sélectionnez l'onglet **En ligne**, entrez « sendgrid » dans la zone de recherche, puis appuyez sur Entrée.

3.  Cliquez sur le bouton **Installer** du package **SendGrid**.

    ![Installer le package Sendgrid](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-install-sendgrid.png)

4.  Fermez la boîte de dialogue.

Ajout des paramètres du projetAjout des paramètres du projet
------------------------------------------------------------

Comme le rôle de travail A, le rôle de travail B a besoin des informations d'identification du compte de stockage pour pouvoir fonctionner avec les tables, les files d'attente et les objets blob. De plus, pour pouvoir envoyer des courriers électroniques, le rôle de travail doit posséder des informations d'identification pour intégrer des appels entrants au service SendGrid. Et pour pouvoir créer un lien de désabonnement à inclure dans les messages électroniques qu'il envoie, le rôle de travail doit connaître l'URL de l'application. Ces valeurs sont stockées dans les paramètres du projet.

Pour obtenir les informations d'identification du compte de stockage, suivez la procédure présentée dans [le troisième didacticiel](/en-us/develop/net/tutorials/multi-tier-web-site/3-web-role/#configstorage).

1.  Dans l'**Explorateur de solutions**, sous **Rôles** dans le projet cloud, cliquez avec le bouton droit sur **WorkerRoleB**, puis sélectionnez **Propriétés**.

2.  Sélectionnez l'onglet **Paramètres**.

3.  Assurez-vous que l'option **Toutes les configurations** est sélectionnée dans la liste déroulante **Configuration du service**.

4.  Sélectionnez l'onglet **Paramètres**, puis cliquez sur **Ajouter un paramètre**.

5.  Entrez « StorageConnectionString » dans la colonne **Nom**.

6.  Sélectionnez **Chaîne de connexion** dans la liste déroulante **Type**.

7.  Cliquez sur le bouton **...** situé à l'extrême droite de la ligne pour ouvrir la boîte de dialogue **Chaîne de connexion de compte de stockage**.

8.  Dans la boîte de dialogue **Créer une chaîne de connexion de stockage**, cliquez sur la case d'option **Votre abonnement**.

9.  Sélectionnez les mêmes **abonnement** et **nom de compte** que pour le rôle Web et le rôle de travail A.

10. Suivez la même procédure pour configurer les paramètres de la chaîne de connexion **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**.

    Ensuite, créez et configurez les trois nouveaux paramètres utilisés uniquement par le rôle de travail B.

11. Sous l'onglet **Paramètres** de la fenêtre **Propriétés**, cliquez sur **Ajouter un paramètre**, puis ajoutez trois nouveaux paramètres au type **Chaîne** :

    -   **Nom** : SendGridUserName, **Valeur** : le nom d'utilisateur SendGrid que vous avez choisi dans le [deuxième didacticiel](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

    -   **Nom** : SendGridPassword, **Valeur** : le mot de passe SendGrid.

    -   **Nom** : AzureMailServiceURL, **Valeur** : l'URL de base de l'application lorsque vous la déployez, par exemple : http://exempleurl.cloudapp.net.

    ![Nouveaux paramètres du projet WorkerRoleB](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-settings.png)

### Ajout de code qui s'exécute au démarrage du rôle de travail

1.  Dans le projet WorkerRoleB, supprimez WorkerRole.cs.

2.  Cliquez avec le bouton droit sur le projet WorkerRoleB, puis choisissez **Ajouter un élément existant**.

    ![Ajouter un élément existant au rôle de travail B](./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-b/mtas-worker-b-add-existing.png)

3.  Accédez au dossier où vous avez téléchargé l'exemple d'application puis, dans le projet WorkerRoleB, sélectionnez le fichier WorkerRoleB.cs et cliquez sur **Ajouter**.

> [WACOM.NOTE] Si vous utilisez Visual Studio 2013 avec le dernier Kit de développement logiciel (SDK) et le dernier package NuGet SendGrid, ouvrez *WorkerRoleB.cs* et apportez les modifications suivantes au code : (1) Supprimez l'instruction `using` pour `SendGridMail.Transport`. (2) Remplacez les deux instances de `SendGrid.GenerateInstance` par `SendGrid.GetInstance`. (3) Remplacez les deux instances de `REST.GetInstance` par `Web.GetInstance`.

1.  Ouvrez WorkerRoleB.cs et examinez le code.

    Comme pour le rôle de travail A, la méthode `OnStart` initialise les classes de contexte requises pour utiliser les entités de stockage Azure. Elle vérifie également que l'ensemble des tables, des files d'attente et des conteneurs d'objets blob requis pour la méthode `Run` existent.

    La différence avec le rôle de travail A est l'ajout d'un conteneur d'objets blob et d'une file d'attente d'abonnement aux ressources à créer, s'ils n'existent pas déjà. Vous allez utiliser le conteneur d'objets blob pour obtenir les fichiers contenant du HTML et du texte clair pour le corps du message électronique. La file d'attente d'abonnement permet d'envoyer des messages électroniques de confirmation d'abonnement.

         public override bool OnStart()
         {
             ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount;

             // Lit les paramètres de configuration du compte de stockage
             ConfigureDiagnostics();
             Trace.TraceInformation("Initialisation du compte de stockage dans le rôle de travail B");
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

             // Initialise le stockage de file d'attente 
             Trace.TraceInformation("Creating queue client.");
             CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
             this.sendEmailQueue = queueClient.GetQueueReference("azuremailqueue");
             this.subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");

             // Initialise le stockage d'objets blob
             CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
             this.blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

             // Initialise le stockage de table
             var tableClient = storageAccount.CreateCloudTableClient();
             tableServiceContext = tableClient.GetDataServiceContext();

             Trace.TraceInformation("WorkerB : création du conteneur d'objets blob, de la file d'attente et des tables s'ils n'existent pas.");
             this.blobContainer.CreateIfNotExists();
             this.sendEmailQueue.CreateIfNotExists();
             this.subscribeQueue.CreateIfNotExists();
             var messageTable = tableClient.GetTableReference("Message");
             messageTable.CreateIfNotExists();
             var mailingListTable = tableClient.GetTableReference("MailingList");
             mailingListTable.CreateIfNotExists();

             return base.OnStart();
         }

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

                     // Si OnStop a été appelée, retour pour effectuer un arrêt approprié.
                     if (onStopCalled == true)
                     {
                         Trace.TraceInformation("onStopCalled WorkerRoleB");
                         returnedFromRunMethod = true;
                         return;
                     }
                     // Récupère et traite un nouveau message depuis la file d'attente d'envoi de messages électroniques à une liste (send-email-to-list)
                     msg = sendEmailQueue.GetMessage();
                     if (msg != null)
                     {
                         ProcessQueueMessage(msg);
                         messageFound = true;
                     }

                     // Récupère et traite un nouveau message depuis la file d'attente d'abonnement
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
                     // Ne remplit pas le stockage de trace s'il s'agit d'un bogue dans la boucle de traitement.
                     System.Threading.Thread.Sleep(1000 * 60);
                 }
             }
         }

    Ce code s'exécute dans une boucle infinie jusqu'à l'arrêt du rôle de travail. Si un élément de travail est trouvé dans la file d'attente principale, le code le traite, puis vérifie la file d'attente d'abonnement.

                     // Récupère et traite un nouveau message depuis la file d'attente d'envoi de messages électroniques à une liste (send-email-to-list).
                     msg = this.sendEmailQueue.GetMessage();
                     if (msg != null)
                     {
                         ProcessQueueMessage(msg);
                         messageFound = true;
                     }

                     // Récupère et traite un nouveau message depuis la file d'attente d'abonnement
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

    Cette veille permet de réduire les frais de transaction Azure Storage, comme cela est expliqué dans [le didacticiel précédent](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/).

    Lorsqu'un élément de file d'attente est extrait de la file par la méthode [GetMessage](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee741827.aspx), il devient invisible durant 30 secondes pour tous les autres rôles de travail et Web qui accèdent à la file d'attente. Cela permet de garantir que seule une instance de rôle de travail choisit un message de file d'attente spécifique pour le traiter. Vous pouvez définir ce délai *exclusif* (durée d'invisibilité de l'élément) de manière explicite en transmettant un paramètre de [délai d'expiration de la visibilité](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee758454.aspx) à la méthode `GetMessage`. Si le traitement d'un message de file d'attente peut prendre plus de 30 secondes au rôle de travail, vous devez augmenter le délai exclusif pour éviter que le même message soit traité par d'autres instances de rôle.

    Cependant, il ne vaut mieux pas définir le délai exclusif sur une valeur trop grande. Par exemple, si le délai exclusif est défini sur 48 heures et que votre rôle de travail s'arrête de manière imprévue après l'extraction d'un message d'une file d'attente, les autres rôles de travail ne pourront pas traiter le message durant les 48 prochaines heures. Le délai exclusif maximum est de 7 jours.

    La méthode [GetMessages](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storageclient.cloudqueue.getmessages.aspx) (remarquez le « s » à la fin du nom) permet d'extraire jusqu'à 32 messages de la file d'attente par appel. Chaque accès à la file d'attente implique des frais de transaction réduits, qui ne changent pas, peu importe le nombre de messages renvoyés. Le code suivant récupère et traite jusqu'à 32 messages en un appel.

    foreach (CloudQueueMessage msg in sendEmailQueue.GetMessages(32))

         {
             ProcessQueueMessage(msg);
             messageFound = true;
         }

    Lors de l'utilisation de la méthode `GetMessages` pour supprimer plusieurs messages, vérifiez que le délai d'expiration de la visibilité laisse assez de temps à votre application pour traiter tous les messages. Une fois ce délai expiré, les autres instances de rôle peuvent accéder au message. Lorsqu'elles y accèdent, la première instance ne peut plus supprimer le message lorsqu'elle termine le traitement de l'élément de travail.

    La méthode `Run` appelle `ProcessQueueMessage` lorsqu'elle trouve un élément de travail dans la file d'attente principale :

         private void ProcessQueueMessage(CloudQueueMessage msg)
         {
             // Crée une entrée de journal et supprime le message s'il s'agit d'un message de file d'attente « empoisonné » (déjà traité
             // et provoquant toujours une erreur empêchant la fin du traitement).
             // Les applications de production doivent déplacer le message « empoisonné » vers une file d'attente
             // « messages morts » pour analyser le message au lieu de le supprimer.           
             if (msg.DequeueCount > 5)
             {
                 Trace.TraceError("Deleting poison message:    message {0} Role Instance {1}.",
                     msg.ToString(), GetRoleInstance());
                 sendEmailQueue.DeleteMessage(msg);
                 return;
             }
             // Analyse le message récupéré dans la file d'attente.
             // Exemple :  2012-01-01,0123456789email@domain.com,0
             var messageParts = msg.AsString.Split(new char[] { ',' });
             var partitionKey = messageParts[0];
             var rowKey = messageParts[1];
             var restartFlag = messageParts[2];
             Trace.TraceInformation("ProcessQueueMessage start:  partitionKey {0} rowKey {1} Role Instance {2}.",
                 partitionKey, rowKey, GetRoleInstance());
             // En cas de redémarrage, vérifiez que le message électronique n'a pas déjà été envoyé.
             if (restartFlag == "1")
             {
                 var retrieveOperationForRestart = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
                 var retrievedResultForRestart = messagearchiveTable.Execute(retrieveOperationForRestart);
                 var messagearchiveRow = retrievedResultForRestart.Result as SendEmail;
                 if (messagearchiveRow != null)
                 {
                     // La ligne SendEmail est archivée, donc le message électronique a déjà été envoyé. 
                     // S'il y a une ligne SendEmail dans la table du message, supprimez-la,
                     // puis supprimez le message de la file d'attente.
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
                         // Obtient la ligne de la table Message qui contient les données nécessaires à l'envoi du message électronique.
             var retrieveOperation = TableOperation.Retrieve<SendEmail>(partitionKey, rowKey);
             var retrievedResult = messageTable.Execute(retrieveOperation);
             var emailRowInMessageTable = retrievedResult.Result as SendEmail;
             if (emailRowInMessageTable == null)
             {
                 Trace.TraceError("SendEmail row not found:  partitionKey {0} rowKey {1} Role Instance {2}.",
                     partitionKey, rowKey, GetRoleInstance());
                 return;
             }
             // Dérive les noms d'objets blob du MessageRef.
             var htmlMessageBodyRef = emailRowInMessageTable.MessageRef + ".htm";
             var textMessageBodyRef = emailRowInMessageTable.MessageRef + ".txt";
             // Envoie le message électronique s'il n'a pas déjà été envoyé, et archive la ligne de table.
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

             // Supprime le message de la file d'attente.
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

    Le travail d'envoi du message électronique avec SendGrid est effectué par la méthode `SendEmailToList`. Si vous voulez utiliser un autre service que SendGrid, tout ce que vous avez à faire est de modifier le code de cette méthode.

    **Remarque :** si les informations d'identification contenues dans les paramètres du projet ne sont pas correctes, l'appel vers SendGrid échoue mais l'application n'indique pas les raisons de l'échec. Si vous utilisez SendGrid dans une application de production, pensez à configurer des informations d'identification distinctes pour l'API Web, afin d'éviter des échecs silencieux lorsqu'un administrateur modifie le mot de passe de son compte d'utilisateur SendGrid. Pour plus d'informations, consultez la page [SendGrid MultiAuth : informations d'identification de plusieurs comptes](http://support.sendgrid.com/entries/21658978-sendgrid-multiauth-multiple-account-credentials). Vous pouvez configurer vos informations d'identification sur la page <https://sendgrid.com/credentials>.

         private void SendEmailToList(string emailAddress, string fromEmailAddress, string subjectLine,
             string htmlMessageBodyRef, string textMessageBodyRef)
         {
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(fromEmailAddress);
             email.AddTo(emailAddress);
             email.Html = GetBlobText(htmlMessageBodyRef);
             email.Text = GetBlobText(textMessageBodyRef);
             email.Subject = subjectLine;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"),
                 RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
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

    Dans la méthode `GetBlobText`, le code obtient la taille des objets blob, puis utilise cette valeur pour initialiser l'objet `MemoryStream` pour des raisons de performance. Si vous ne fournissez pas la taille, l'objet `MemoryStream` alloue 256 octets, puis 512 octets lorsque le téléchargement dépasse cette taille, et ainsi de suite, en doublant la quantité allouée à chaque fois. Ce processus n'est pas adapté à un objet blob volumineux, auquel il vaut mieux allouer une quantité suffisante dès le début du téléchargement.

    La méthode `Run` appelle `ProcessSubscribeQueueMessage` lorsqu'elle trouve un élément de travail dans la file d'attente d'abonnement :

         private void ProcessSubscribeQueueMessage(CloudQueueMessage msg)
         {
             // Crée une entrée de journal et supprime le message s'il s'agit d'un message de file d'attente « empoisonné » (déjà traité
             // et provoquant toujours une erreur empêchant la fin du traitement).
             // Les applications de production doivent déplacer le message « empoisonné » vers une file d'attente
             // « messages morts » pour analyser le message au lieu de le supprimer.  
             if (msg.DequeueCount > 5)
             {
                 Trace.TraceError("Deleting poison subscribe message:    message {0}.",
                     msg.AsString, GetRoleInstance());
                 subscribeQueue.DeleteMessage(msg);
                 return;
             }
             // Analyse le message récupéré dans la file d'attente. Le message se compose du 
             // GUID de l'abonné et du nom de la liste.
             // Exemple :  57ab4c4b-d564-40e3-9a3f-81835b3e102e,contoso1
             var messageParts = msg.AsString.Split(new char[] { ',' });
             var subscriberGUID = messageParts[0];
             var listName = messageParts[1];
             Trace.TraceInformation("ProcessSubscribeQueueMessage start:    subscriber GUID {0} listName {1} Role Instance {2}.",
                 subscriberGUID, listName, GetRoleInstance());
             // Obtient les informations sur l'abonné. 
             string filter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
             // Obtient les informations sur la liste de diffusion.
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
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe
         id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Cliquez sur le lien ci-dessous pour vous abonner à {0}. " +
                 "Si vous ne confirmez pas votre abonnement, il n'aura pas lieu.</p>" +
                 "<a href= private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
         {
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Cliquez sur le lien ci-dessous pour vous abonner à {0}. " +
                 "Si vous ne confirmez pas votre abonnement, il n'aura pas lieu.</p>" +
                 "<a href=\"{1}\">Confirmer l'abonnement</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Copiez et collez l'URL suivante dans votre navigateur pour vous abonner à {0}. " +
                 "Si vous ne confirmez pas votre abonnement, il n'aura pas lieu.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = "Abonnement à " + mailingList.Description;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }
        quot;{1} private static void SendSubscribeEmail(string subscriberGUID, Subscriber subscriber, MailingList mailingList)
         {
             var email = SendGrid.GenerateInstance();
             email.From = new MailAddress(mailingList.FromEmailAddress);
             email.AddTo(subscriber.EmailAddress);
             string subscribeURL = RoleEnvironment.GetConfigurationSettingValue("AzureMailServiceURL") +
                 "/subscribe?id=" + subscriberGUID + "&listName=" + subscriber.ListName;
             email.Html = String.Format("<p>Cliquez sur le lien ci-dessous pour vous abonner à {0}. " +
                 "Si vous ne confirmez pas votre abonnement, il n'aura pas lieu.</p>" +
                 "<a href=\"{1}\">Confirmer l'abonnement</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Copiez et collez l'URL suivante dans votre navigateur pour vous abonner à {0}. " +
                 "Si vous ne confirmez pas votre abonnement, il n'aura pas lieu.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = "Abonnement à " + mailingList.Description;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }
        quot;>Confirmer l'abonnement</a>", mailingList.Description, subscribeURL);
             email.Text = String.Format("Copiez et collez l'URL suivante dans votre navigateur pour vous abonner à {0}. " +
                 "Si vous ne confirmez pas votre abonnement, il n'aura pas lieu.\n" +
                 "{1}", mailingList.Description, subscribeURL);
             email.Subject = "Abonnement à " + mailingList.Description;
             var credentials = new NetworkCredential(RoleEnvironment.GetConfigurationSettingValue("SendGridUserName"), RoleEnvironment.GetConfigurationSettingValue("SendGridPassword"));
             var transportREST = REST.GetInstance(credentials);
             transportREST.Deliver(email);
         }

TestTest du rôle de travail B
-----------------------------

1.  Exécutez l'application en appuyant sur F5.

2.  Accédez à la page **Messages** pour afficher le message que vous avez créé pour le test du rôle de travail A. Après une minute ou deux, actualisez la page Web pour constater que la ligne a disparu de la liste, car elle a été archivée.

3.  Consultez la boîte de réception de votre messagerie (là où le message électronique doit arriver). Remarque : un certain temps peut s'écouler entre l'envoi de messages électroniques avec SendGrid et la réception par votre client de messagerie. Vous devez également consulter votre dossier de messages indésirables.

Étapes suivantesÉtapes suivantes
--------------------------------

Vous avez créé intégralement l'application Azure Email Service, et le résultat est identique au projet complet que vous avez téléchargé. Pour le déployer et le tester dans le cloud, puis le faire passer en production, vous pouvez utiliser les mêmes procédures que celles du [deuxième didacticiel](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/). Si vous voulez développer une autre architecture, consultez le didacticiel [Prise en main des sites Web Azure](/en-us/develop/net/tutorials/get-started) pour obtenir plus d'informations sur le déploiement du projet MVC vers un site Web Azure.

Pour en savoir plus sur Azure Storage, consultez les ressources suivantes :

-   [Informations essentielles sur Azure Storage](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-windows-azure-storage.aspx) (blog de Bruno Terkaly)

Pour en savoir plus sur le service de stockage de tables Azure, consultez les ressources suivantes :

-   [Informations essentielles sur le stockage de tables Azure](http://blogs.msdn.com/b/brunoterkaly/archive/2012/11/08/essential-knowledge-for-azure-table-storage.aspx) (blog de Bruno Terkaly)
-   [Comment tirer le meilleur parti du stockage de tables Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) (blog de l'équipe Azure Storage)
-   [Utilisation du service de stockage de tables dans .NET](http://www.windowsazure.com/fr-fr/develop/net/how-to-guides/table-services/)
-   [Présentation approfondie des tables de la bibliothèque cliente de stockage 2.0](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx) (blog de l'équipe Azure Storage)
-   [Environnement réel : conception d'une stratégie de partition extensible pour le stockage de tables Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh508997.aspx)

Pour en savoir plus sur le service de files d'attente Azure et les files d'attente Azure Service Bus, consultez les ressources suivantes :

-   [Modèle de travail centré sur les files d'attente (développement d'applications de cloud plus concrètes avec Azure).](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern)
-   [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences](http://msdn.microsoft.com/fr-fr/library/windowsazure/hh767287.aspx)
-   [Utilisation du service de stockage de files d'attente dans .NET](/en-us/develop/net/how-to-guides/queue-service/)

Pour en savoir plus sur le service de stockage d'objets blob Azure, consultez les ressources suivantes :

-   [Stockage d'objets blob non structurés (développement d'applications de cloud plus concrètes avec Azure).](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)
-   [Utilisation du service de stockage d'objets blob Azure dans .NET](/en-us/develop/net/how-to-guides/blob-storage/)

Pour en savoir plus sur la mise à l'échelle automatique des rôles de service cloud Azure, consultez les ressources suivantes :

-   [Utilisation du bloc applicatif de mise à l'échelle automatique](/en-us/develop/net/how-to-guides/autoscaling/)
-   [Mise à l'échelle automatique et Azure](http://msdn.microsoft.com/fr-fr/library/hh680945(v=PandP.50).aspx)
-   [Développement de solutions flexibles pouvant être mises à l'échelle automatiquement avec Azure](http://channel9.msdn.com/Events/WindowsAzureConf/2012/B04) (vidéo MSDN Channel 9)

RemerciementsRemerciements
--------------------------

Ces didacticiels et exemples d'applications ont été écrits par [Rick Anderson](http://blogs.msdn.com/b/rickandy/) et Tom Dykstra. Nous aimerions remercier les personnes suivantes pour leur aide :

-   Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart))
-   [Cory Fowler](http://blog.syntaxc4.net/) (Twitter [@SyntaxC4](https://twitter.com/SyntaxC4) )
-   [Joe Giardino](http://blogs.msdn.com/b/windowsazurestorage/)
-   Don Glover
-   Jai Haridas
-   [Scott Hunter](http://blogs.msdn.com/b/scothu/) (Twitter [@coolcsh](http://twitter.com/coolcsh))
-   [Brian Swan](http://blogs.msdn.com/b/brian_swan/)
-   [Daniel Wang](http://blogs.msdn.com/b/daniwang/)
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

