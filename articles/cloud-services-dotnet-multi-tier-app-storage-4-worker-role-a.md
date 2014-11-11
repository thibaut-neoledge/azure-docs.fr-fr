<properties linkid="develop-net-tutorials-multi-tier-web-site-4-worker-role-a" pageTitle="Azure Cloud Service Tutorial: Worker Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Création du rôle de travail A (planificateur de messages) pour l'application Azure Email Service : 4 sur 5.

Ce didacticiel, le quatrième d'une série de cinq, explique comment créer et déployer l'exemple d'application Azure Email Service. Pour obtenir des informations sur l'application et la série de didacticiels, consultez le [premier didacticiel de la série][premier didacticiel de la série].

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   Interrogation et mise à jour des tables de stockage Azure.
-   Ajout des éléments de travail à une file d'attente pour le traitement par un autre rôle de travail.
-   Gestion des arrêts planifiés par remplacement de la méthode `OnStop`.
-   Gestion des arrêts non planifiés en s'assurant qu'aucun courrier électronique n'est oublié et qu'aucun courrier électronique n'est envoyé en double.
-   Test d'un rôle de travail utilisant des tables Azure Storage à l'aide de l'Explorateur de serveurs.

Vous avez déjà créé le projet de rôle de travail A lorsque vous avez créé le projet de service cloud. Il ne vous reste donc plus qu'à le programmer.

## Sections de ce didacticiel

-   [Ajout d'une référence au projet Web][Ajout d'une référence au projet Web]
-   [Ajout du modèle SendEmail][Ajout du modèle SendEmail]
-   [Ajout de code qui s'exécute au démarrage du rôle de travail][Ajout de code qui s'exécute au démarrage du rôle de travail]
-   [Test du rôle de travail A][Test du rôle de travail A]
-   [Étapes suivantes][Étapes suivantes]

## <a name="addref"></a><span class="short-header">Ajout d'une référence au projet</span>Ajout d'une référence au projet Web

Vous avez besoin d'une référence au projet Web, car les classes d'entité sont définies dedans. Vous utilisez les mêmes classes d'entité dans le rôle de travail B pour lire et écrire les données dans les tables Azure utilisées par l'application.

**Remarque :** dans une application de production, vous ne définissez pas une référence sur un projet Web à partir d'un projet de rôle de travail, car cela entraîne le référencement d'un nombre d'assemblys dépendants dont vous ne voulez pas ou n'avez pas besoin dans le rôle de travail. Normalement, vous conservez les classes de modèle partagé dans un projet de bibliothèque de classes, et le projet Web ainsi que le projet du rôle de travail référencent le projet de bibliothèque de classes. Pour que la structure de la solution reste simple, les classes de modèle sont stockées dans le projet Web pour ce didacticiel.

1.  Cliquez avec le bouton droit sur le projet WorkerRoleA et choisissez **Ajouter**, puis **Référence**.

2.  Dans le **Gestionnaire de références**, ajoutez une référence au projet MvcWebRole, puis cliquez sur **OK**.

    ![Ajout d'une référence à MvcWebRole][Ajout d'une référence à MvcWebRole]

## <a name="addmodel"></a>Ajout du modèle SendEmail

Le rôle de travail A crée des lignes `SendEmail` dans la table `Message` et le rôle de travail B lit ces lignes pour obtenir les informations nécessaires à l'envoi des courriers électroniques. L'image suivante présente un sous-ensemble de propriétés pour deux lignes `Message` et trois lignes `SendEmail` dans la table `Message`.

![table message avec sendmail][table message avec sendmail]

Ces lignes de la table `Message` ont plusieurs utilités :

-   Elles fournissent toutes les informations dont le rôle de travail B a besoin pour envoyer un simple courrier électronique.
-   Elles effectuent le suivi de l'envoi d'un courrier électronique pour éviter l'envoi de doublons en cas de redémarrage d'un rôle de travail après un échec.
-   Elles permettent au rôle de travail A de savoir quand tous les courriers électroniques d'un message ont été envoyés pour qu'il puisse être marqué comme `Complete`.

Pour la lecture et l'écriture des lignes `SendEmail`, une classe de modèle est requise. Puisqu'elle doit être accessible au rôle de travail A et au rôle de travail B, et puisque toutes les autres classes de modèle sont définies dans le projet Web, il est conseillé de définir également celle-ci dans le projet Web.

1.  Dans le dossier *Models* du projet Web, ajoutez le fichier *SendEmail.cs* depuis le projet téléchargé.

Le code ici est semblable aux autres classes de modèle, sauf qu'aucun attribut DataAnnotations n'est inclus, car aucune interface utilisateur n'est associée à ce modèle ; un contrôleur MVC ne l'utilise pas.

        public class SendEmail : TableEntity
        {
            public long MessageRef { get; set; }
            public string EmailAddress { get; set; }
            public DateTime? ScheduledDate { get; set; }
            public String FromEmailAddress { get; set; }
            public string SubjectLine { get; set; }
            public bool? EmailSent { get; set; }
            public string SubscriberGUID { get; set; }
            public string ListName { get; set; }
        }

## <a name="addcode"></a><span class="short-header">Ajout de code au rôle de travail</span>Ajout de code qui s'exécute lors du démarrage du rôle de travail

Dans le projet WorkerRoleA, le modèle de projet a créé *WorkerRole.cs* avec le code suivant :

        public class WorkerRole : RoleEntryPoint
        {
            public override void Run()
            {
                // This is a sample worker implementation. Replace with your logic.
                Trace.WriteLine("WorkerRole1 entry point called", "Information");

                while (true)
                {
                    Thread.Sleep(10000);
                    Trace.WriteLine("Working", "Information");
                }
            }

            public override bool OnStart()
            {
                // Set the maximum number of concurrent connections 
                ServicePointManager.DefaultConnectionLimit = 12;

                // For information on handling configuration changes
                // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

                return base.OnStart();
            }
        }

Il s'agit du code du modèle par défaut pour le rôle de travail. Vous pouvez placer le code d'initialisation qui s'exécute uniquement lorsqu'une instance du rôle de travail démarre dans la méthode `OnStart` ; la méthode `Run` est appelée une fois la méthode `OnStart` terminée. Vous allez remplacer ce code par votre propre code d'initialisation et d'exécution.

1.  Supprimez *WorkerRole.cs* dans le projet WorkerRoleA, puis ajoutez le fichier *WorkerRoleA.cs* du projet téléchargé.

### Méthode OnStart

La méthode `OnStart` initialise les objets de contexte requis pour utiliser les entités Azure Storage. Elle vérifie également que l'ensemble des tables, des files d'attente et des conteneurs d'objets blob utilisés par la méthode `Run` existent. Le code qui effectue ces tâches est similaire à ce que vous avez observé précédemment dans les constructeurs de contrôleur MVC. Vous allez configurer la chaîne de connexion utilisée par cette méthode ultérieurement.

        public override bool OnStart()
        {
            ServicePointManager.DefaultConnectionLimit = Environment.ProcessorCount * 12;

            ConfigureDiagnostics();
            Trace.TraceInformation("Initializing storage account in WorkerA");
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient(); 
            sendEmailQueue = queueClient.GetQueueReference("azuremailqueue"); 
            var tableClient = storageAccount.CreateCloudTableClient(); 
            mailingListTable = tableClient.GetTableReference("mailinglist"); 
            messageTable = tableClient.GetTableReference("message"); 
            messagearchiveTable = tableClient.GetTableReference("messagearchive"); 

            // Create if not exists for queue, blob container, SentEmail table. 
            sendEmailQueue.CreateIfNotExists(); 
            messageTable.CreateIfNotExists(); 
            mailingListTable.CreateIfNotExists(); 
            messagearchiveTable.CreateIfNotExists(); 

            return base.OnStart();
        }

Dans une documentation antérieure sur l'utilisation d'Azure Storage, il se peut que vous ayez vu le code d'initialisation dans une boucle qui vérifie les erreurs de transport. Ce n'est maintenant plus nécessaire, car l'API intègre désormais un mécanisme de nouvelle tentative qui absorbe les pannes de réseau transitoires à hauteur de 3 tentatives supplémentaire.

La méthode `ConfigureDiagnostics` appelée par la méthode `OnStart` configure le suivi pour que vous puissiez voir le résultat des méthodes `Trace.Information` et `Trace.Error`. Cette méthode est expliquée dans le [deuxième didacticiel][deuxième didacticiel].

Le paramètre [ServicePointManager.DefaultConnectionLimit][ServicePointManager.DefaultConnectionLimit] indique le nombre maximal de connexions TCP simultanées pouvant être ouvertes dans .NET. La documentation sur la classe [ServicePointManager][ServicePointManager] explique que chaque hôte unique auquel l'instance de rôle de travail se connecte est une connexion distincte. Par exemple, ce rôle de travail aurait trois connexions simultanées, une pour les tables, une pour les objets blob et une pour les files d'attente. Pour certaines connexions, notamment pour SQL Server, le logiciel client regroupe les connexions, réduisant ainsi le nombre de connexions simultanées gérées par `ServicePointManager`. Le nombre idéal pour `DefaultConnectionLimit` dépend en partie du service principale auquel vous vous connectez. Certains services peuvent gérer sans problème 500 connexions ouvertes, tandis que d'autres pourraient être dépassés avec à peine 5 connexions. De manière générale, la limite de 12 connexions par processeur convient dans la plupart des cas.

### Méthode OnStop

La méthode `OnStop` définit la variable globale `onStopCalled` sur true, puis attend que la méthode `Run` définisse la variable globale `returnedFromRunMethod` sur true, ce qui signifie qu'elle est prête pour effectuer un arrêt normal.

        public override void OnStop()
        {
            onStopCalled = true;
            while (returnedFromRunMethod == false)
            {
                System.Threading.Thread.Sleep(1000);
            }
        }

La méthode `OnStop` est appelée lorsque le rôle de travail s'arrête pour l'une des raisons suivantes :

-   Azure doit redémarrer la machine virtuelle (l'instance de rôle de travail ou rôle Web) ou la machine physique qui héberge la machine virtuelle.
-   Vous avez arrêté votre service cloud à l'aide du bouton **Stop** sur le portail de gestion Azure.
-   Vous avez déployé une mise à jour vers votre projet de service cloud.

La méthode `Run` surveille la variable `onStopCalled` et stoppe la récupération de nouveaux éléments de travail à traiter lorsque cette variable est définie sur `true`. Cette coordination entre les méthodes `OnStop` et `Run` permet un arrêt approprié du processus de travail.

Azure installe régulièrement des mises à jour pour le système d'exploitation afin de s'assurer que la plateforme est sécurisée, fiable et qu'elle fonctionne correctement. Ces mises à jour requièrent généralement l'arrêt et le redémarrage des machines hébergeant votre service cloud. Pour plus d'informations, consultez la page [Redémarrages d'instances de rôles pour cause de mise à jour du système d'exploitation][Redémarrages d'instances de rôles pour cause de mise à jour du système d'exploitation].

### Méthode Run

La méthode `Run` effectue deux fonctions :

-   Recherche dans la table `message` les messages planifiés devant être envoyés aujourd'hui ou avant, pour lesquels des éléments de travail de file d'attente n'ont pas encore été créés.

-   Recherche dans la table `message` les messages avec un état indiquant que tous les éléments de travail de file d'attente ont été créés, mais que tous les courriers électroniques n'ont pas encore été envoyés. Si elle trouve un message, elle le recherche dans les lignes `SendEmail` pour voir si tous les courriers électroniques ont été envoyés, et si c'est le cas, elle met à jour l'état sur `Completed` et archive la ligne `message`.

La méthode vérifie également la variable globale `onStopCalled`. Lorsque la variable est définie sur `true`, la méthode arrête de récupérer des nouveaux éléments de travail à traiter, et elle reprend lorsque les tâches déjà commencées sont terminées.

        public override void Run()
        {
            Trace.TraceInformation("WorkerRoleA entering Run()");
            while (true)
            {
                try
                {
                    var tomorrow = DateTime.Today.AddDays(1.0).ToString("yyyy-MM-dd");
                    // If OnStop has been called, return to do a graceful shutdown.
                    if (onStopCalled == true)
                    {
                        Trace.TraceInformation("onStopCalled WorkerRoleB");
                        returnedFromRunMethod = true;
                        return;
                    }
                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();
                    TableOperation replaceOperation;
                    // Process each message (queue emails to be sent).
                    foreach (Message messageToProcess in messagesToProcess)
                    {
                        string restartFlag = "0";
                        // If the message is already in Queuing status,
                        // set flag to indicate this is a restart.
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

                        // If the message is in Pending status, change
                        // it to Queuing.
                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        // If the message is in Queuing status, 
                        // process it and change it to Processing status;
                        // otherwise it's already in processing status, and 
                        // in that case check if processing is complete.
                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }
                    }

                    // Sleep for one minute to minimize query costs. 
                    System.Threading.Thread.Sleep(1000 * 60);
                }
                catch (Exception ex)
                {
                    string err = ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException.Message;
                    }
                    Trace.TraceError(err);
                    // Don't fill up Trace storage if we have a bug in queue process loop.
                    System.Threading.Thread.Sleep(1000 * 60);
                }
            }
        }

Notez que tout le travail est effectué dans une boucle infinie dans un bloc `while` et que tout le code du bloc `while` est encapsulé dans un bloc `try`-`catch` pour gérer les exceptions non prises en charge. Si une exception non prise en charge se produit, Azure déclenche un événement [UnhandledException][UnhandledException], le processus de travail s'arrête et le rôle est mis hors connexion. Azure redémarre le rôle de travail au bout de plusieurs minutes. Le bloc `try` appelle `TraceError` pour enregistrer l'erreur, puis se met en veille pendant 60 secondes pour que le message d'erreur ne soit pas trop répété si l'erreur est persistante. Dans une application de production, vous pouvez envoyer un courrier électronique à un administrateur dans le bloc `try`.

La méthode `Run` effectue une requête sur les lignes `message` de la table `message` ayant une date planifiée antérieure à demain :

                    // Retrieve all messages that are scheduled for tomorrow or earlier
                    // and are in Pending or Queuing status.
                    string typeAndDateFilter = TableQuery.CombineFilters(
                        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, "message"),
                        TableOperators.And,
                        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.LessThan, tomorrow));
                    var query = (new TableQuery<Message>().Where(typeAndDateFilter));
                    var messagesToProcess = messageTable.ExecuteQuery(query).ToList();

**Remarque :** un des avantages du déplacement des lignes message dans la table `messagearchive` une fois qu'elles sont traitées est que la requête doit uniquement spécifier `PartitionKey` et `RowKey` comme critères de recherche. Si les lignes traitées n'étaient pas archivées, le requête devrait également spécifier un champ non clé (`Status`) et rechercher dans davantage de lignes. La taille de la table augmenterait et la requête nécessiterait plus de temps et pourrait commencer à recevoir des jetons de continuation.

Si un message présente l'état `Pending`, le traitement n'a pas encore commencé ; s'il présente l'état `Queuing`, le traitement a commencé précédemment, mais il a été interrompu avant que tous les messages des files d'attente aient été créés. Dans ce cas, une vérification supplémentaire doit être effectuée dans le rôle de travail B lors de l'envoi de chaque courrier électronique pour s'assurer que le courrier électronique n'a pas déjà été envoyé. C'est le rôle de la variable `restartFlag`.

                        string restartFlag = "0";
                        if (messageToProcess.Status == "Queuing")
                        {
                            restartFlag = "1";
                        }

Ensuite, le code définit les lignes `message` dans l'état `Pending` sur l'état `Queuing`. Puis, pour ces lignes ainsi que celles déjà dans l'état `Queuing`, la méthode `ProcessMessage` est appelée pour créer les éléments de travail de file d'attente pour envoyer les courriers électroniques pour le message.

                        if (messageToProcess.Status == "Pending")
                        {
                            messageToProcess.Status = "Queuing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }

                        if (messageToProcess.Status == "Queuing")
                        {
                            ProcessMessage(messageToProcess, restartFlag);

                            messageToProcess.Status = "Processing";
                            replaceOperation = TableOperation.Replace(messageToProcess);
                            messageTable.Execute(replaceOperation);
                        }
                        else
                        {
                            CheckAndArchiveIfComplete(messageToProcess);
                        }

Après le traitement d'un message dans l'état `Queuing`, le code définit l'état de la ligne `Message` sur `Processing`. Les lignes de la table `message` qui ne sont pas dans l'état `Pending` ou `Queuing` sont déjà dans l'état `Processing`, et pour ces lignes, le code appelle une méthode qui vérifie si tous les courriers électroniques pour le message ont été envoyés. Si tous les courriers électroniques ont été envoyés, la ligne `message` est archivée.

Après le traitement de tous les enregistrements récupérés par la requête, le code se met en veille pendant une minute.

                // Sleep for one minute to minimize query costs.
                System.Threading.Thread.Sleep(1000*60);

Un coût minimal est appliqué à chaque requête Azure Storage, même si elle ne renvoie aucune donnée. Par conséquent, effectuer des recherches en continu augmenterait inutilement vos dépenses Azure. Au moment où ce didacticiel est écrit, le coût est de 0,10 $ par million de transactions (une requête compte comme une transaction), la durée de mise en veille peut donc être bien inférieure à une minute et le coût de recherche dans les tables pour les messages à envoyer serait toujours minimal. Pour plus d'informations sur la tarification, consultez le [premier didacticiel][premier didacticiel de la série].

**Remarque sur le threading et l'utilisation optimale du CPU :** la méthode `Run` est composée de deux tâches (la mise en file d'attente des courriers électroniques et la vérification des messages terminés) qui sont exécutées séquentiellement dans un thread unique. Une petite machine virtuelle (VM) dispose de 1,75 Go de RAM et d'un seul processeur, il est donc correct d'exécuter ces tâches séquentiellement avec un thread unique. Supposons que votre application nécessite plus de mémoire que celle fournie par une petite machine virtuelle pour être exécutée efficacement. Une machine virtuelle moyenne fournit 3,5 Go de RAM et 2 processeurs, mais cette application utilise uniquement un processeur, car elle utilise un thread unique. Pour tirer parti de tous les processeurs, vous devez créer un thread de travail pour chaque processeur quand bien même un processeur unique n'est pas complètement utilisé par un thread. Lorsqu'un thread effectue des appels réseau ou E/S, le thread doit attendre l'appel réseau ou E/S pour poursuivre, et pendant qu'il attend, il n'effectue pas de tâche utile. Si la méthode `Run` a été implémentée à l'aide de deux threads, lorsqu'un thread attendait qu'une opération réseau ou E/S se termine, l'autre thread pourrait effectuer des tâches utiles.

### Méthode ProcessMessage

La méthode `ProcessMessage` récupère toutes les adresses de messagerie pour la liste de diffusion de destination, puis crée un élément de travail de file d'attente pour chaque adresse de messagerie. En créant des éléments de travail de file d'attente, elle crée également des lignes `SendEmail` dans la table `Message`. Ces lignes fournissent au rôle de travail B les informations dont il a besoin pour envoyer des courriers électroniques et incluent une propriété `EmailSent` qui suit si chaque courrier électronique a bien été envoyé.

        private void ProcessMessage(Message messageToProcess, string restartFlag)
        {
            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }
            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

            foreach (Subscriber subscriber in subscribers)
            {
                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };

                // When we try to add the entity to the SendEmail table, 
                // an exception might happen if this worker role went 
                // down after processing some of the email addresses and then restarted.
                // In that case the row might already be present, so we do an Upsert operation.
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);
            }

            Trace.TraceInformation("ProcessMessage end PK: "
                + messageToProcess.PartitionKey);
        }

Le code commence par récupérer la ligne de la liste de diffusion à partir de la table `mailinglist` pour la liste de diffusion de destination. Cette ligne contient l'adresse de messagerie « De » devant être fournie au rôle de travail B pour l'envoi des courriers électroniques.

            // Get Mailing List info to get the "From" email address.
            var retrieveOperation = TableOperation.Retrieve<MailingList>(messageToProcess.ListName, "mailinglist");
            var retrievedResult = mailingListTable.Execute(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                Trace.TraceError("Mailing list not found: " + messageToProcess.ListName + " for message: " + messageToProcess.MessageRef);
                return;
            }

Ensuite, il interroge la table `mailinglist` pour toutes les lignes d'abonné pour la liste de diffusion de destination.

            // Get email addresses for this Mailing List.
            string filter = TableQuery.CombineFilters(
               TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToProcess.ListName),
               TableOperators.And,
               TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist"));
            var query = new TableQuery<Subscriber>().Where(filter);
            var subscribers = mailingListTable.ExecuteQuery(query).ToList();

Dans la boucle qui traite les résultats de la requête, le code commence par vérifier si l'adresse de messagerie de l'abonné est vérifiée, et si ce n'est pas le cas, aucun courrier électronique n'est mis en file d'attente.

                // Verify that the subscriber email address has been verified.
                if (subscriber.Verified == false)
                {
                    Trace.TraceInformation("Subscriber " + subscriber.EmailAddress + " not Verified, so not queuing ");
                    continue;
                }

Ensuite, le code crée une ligne `SendEmail` dans la table `message`. Cette ligne contient les informations que le rôle de travail B utilise pour envoyer un courrier électronique. La ligne est créée avec la propriété `EmailSent` définie sur `false`.

                // Create a SendEmail entity for this email.              
                var sendEmailRow = new SendEmail
                {
                    PartitionKey = messageToProcess.PartitionKey,
                    RowKey = messageToProcess.MessageRef.ToString() + subscriber.EmailAddress,
                    EmailAddress = subscriber.EmailAddress,
                    EmailSent = false,
                    MessageRef = messageToProcess.MessageRef,
                    ScheduledDate = messageToProcess.ScheduledDate,
                    FromEmailAddress = mailingList.FromEmailAddress,
                    SubjectLine = messageToProcess.SubjectLine,
                    SubscriberGUID = subscriber.SubscriberGUID,
                    ListName = mailingList.ListName
                };
                try
                {
                    var upsertOperation = TableOperation.InsertOrReplace(sendEmailRow);
                    messageTable.Execute(upsertOperation);
                }
                catch (Exception ex)
                {
                    string err = "Error creating SendEmail row:  " + ex.Message;
                    if (ex.InnerException != null)
                    {
                        err += " Inner Exception: " + ex.InnerException;
                    }
                    Trace.TraceError(err);
                }

Le code utilise une opération « upsert », car la ligne peut déjà exister si le rôle de travail A redémarre après un échec.

La dernière tâche à être effectuée pour chaque adresse de messagerie est de créer l'élément de travail de file d'attente qui déclenche le rôle de travail B pour envoyer un courrier électronique. L'élément de travail de file d'attente contient la valeur de la clé de ligne et la clé de partition de la ligne `SendEmail` venant d'être créée, ainsi que l'indicateur de redémarrage défini précédemment. La ligne `SendEmail` contient toutes les informations requises par le rôle de travail B pour envoyer un courrier électronique.

                // Create the queue message.
                string queueMessageString =
                    sendEmailRow.PartitionKey + "," +
                    sendEmailRow.RowKey + "," +
                    restartFlag;
                var queueMessage = new CloudQueueMessage(queueMessageString);
                sendEmailQueue.AddMessage(queueMessage);

### Méthode CheckAndUpdateStatusIfComplete

La méthode `CheckAndUpdateStatusIfComplete` vérifie les messages dont l'état est Processing pour savoir si tous les courriers électroniques ont été envoyés. Si aucun courrier électronique non envoyé n'est trouvé, la méthode met à jour l'état de la ligne sur `Completed` et archive la ligne.

        private void CheckAndArchiveIfComplete(Message messageToCheck)
        {
            // Get the list of emails to be sent for this message: all SendEmail rows
            // for this message.  
            string pkrkFilter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, messageToCheck.PartitionKey),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "message"));
            var query = new TableQuery<SendEmail>().Where(pkrkFilter);
            var emailToBeSent = messageTable.ExecuteQuery(query).FirstOrDefault();

            if (emailToBeSent != null)
            {
                return;
            }

            // All emails have been sent; copy the message row to the archive table.

            // Insert the message row in the messagearchive table
            var messageToDelete = new Message { PartitionKey = messageToCheck.PartitionKey, RowKey = messageToCheck.RowKey, ETag = "*" };
            messageToCheck.Status = "Complete";
            var insertOrReplaceOperation = TableOperation.InsertOrReplace(messageToCheck);
            messagearchiveTable.Execute(insertOrReplaceOperation);

            // Delete the message row from the message table.
            var deleteOperation = TableOperation.Delete(messageToDelete);
            messageTable.Execute(deleteOperation);
        }

## <a name="testing"></a>Test du rôle de travail A

1.  Exécutez l'application en appuyant sur F5.

2.  Utilisez les pages Web administrateur pour créer une liste de diffusion et créer des abonnés à la liste de diffusion. Définissez la propriété `Verified` sur `true` pour au moins l'un des abonnés, puis définissez l'adresse de messagerie sur une adresse sur laquelle vous pouvez recevoir des courriers électroniques.

    Aucun courrier électronique n'est envoyé tant que vous n'implémentez pas le rôle de travail B, mais vous allez utiliser les mêmes données de test pour tester le rôle de travail B.

3.  Créez un message à envoyer à la liste de diffusion que vous avez créée, puis définissez la date planifiée sur aujourd'hui ou sur une date antérieure.

    ![Nouveau message dans l'état Pending][Nouveau message dans l'état Pending]

4.  Dans un peu plus d'une minute (à cause de la minute de mise en veille dans la méthode Run), actualisez la page Web Messages pour voir l'état passer sur Processing. (Vous pouvez d'abord le voir passer sur Queuing, mais il y a des chances qu'il passe si vite de l'état Queuing à Processing que vous ne voyiez pas l'état Queuing.)

    ![Nouveau message dans l'état Processing][Nouveau message dans l'état Processing]

5.  Ouvrez l'**Explorateur de serveurs** et développez le nœud Stockage de développement.

6.  Développez **Files d'attente**, cliquez avec le bouton droit sur **azuremailqueue**, puis cliquez sur **Afficher la file d'attente**.

    Vous voyez un message de file d'attente pour chaque abonné vérifié dans votre liste de diffusion de destination.

    ![Message de file d'attente dans ASE][Message de file d'attente dans ASE]

7.  Double-cliquez sur un message de file d'attente.

    Vous voyez le contenu du message de file d'attente : la clé de partition (la date), la clé de ligne (la valeur MessageRef et l'adresse de messagerie) ainsi que l'indicateur de redémarrage délimité par une virgule.

    ![Contenu d'un message de file d'attente dans ASE][Contenu d'un message de file d'attente dans ASE]

8.  Fermez la boîte de dialogue **Afficher le message**.

9.  Développez le nœud **Tables**, cliquez avec le bouton droit sur la table **Message**, puis cliquez sur **Afficher la table**.

    Vous voyez le message que vous avez planifié, avec « message » dans la clé de ligne, suivi par une ligne pour chaque abonné vérifié, avec l'adresse de messagerie dans la clé de ligne.

10. Double-cliquez sur une ligne ayant « message » dans la clé de ligne pour afficher le contenu de la ligne créée par le rôle Web.

    ![Ligne Message][Ligne Message]

11. Double-cliquez sur une ligne ayant une adresse de messagerie dans la clé de ligne pour afficher le contenu de la ligne `SendEmail` créée par le rôle de travail A.

    ![Ligne SendEmail dans la table Message][Ligne SendEmail dans la table Message]

## <a name="nextsteps"></a>Étapes suivantes

Vous avez créé le rôle de travail A et vérifié qu'il crée les messages de file d'attente et les lignes de table requises par le rôle de travail B pour envoyer des courriers électroniques. Dans le [didacticiel suivant][didacticiel suivant], vous allez créer et tester le rôle de travail B.

Pour obtenir des liens sur les ressources supplémentaires pour utiliser les tables, les files d'attente et les objets blob Azure Storage, consultez le [dernier didacticiel de cette série][didacticiel suivant].

<div><a href="/fr-fr/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/" class="site-arrowboxcta download-cta">Didacticiel&nbsp;5</a></div>

  [premier didacticiel de la série]: /fr-fr/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Ajout d'une référence au projet Web]: #addref
  [Ajout du modèle SendEmail]: #addmodel
  [Ajout de code qui s'exécute au démarrage du rôle de travail]: #addcode
  [Test du rôle de travail A]: #testing
  [Étapes suivantes]: #nextsteps
  [Ajout d'une référence à MvcWebRole]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-reference-manager.png
  [table message avec sendmail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-sendMailTbl.png
  [deuxième didacticiel]: /fr-fr/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [ServicePointManager.DefaultConnectionLimit]: http://msdn.microsoft.com/fr-fr/library/system.net.servicepointmanager.defaultconnectionlimit.aspx
  [ServicePointManager]: http://msdn.microsoft.com/fr-fr/library/system.net.servicepointmanager.aspx
  [Redémarrages d'instances de rôles pour cause de mise à jour du système d'exploitation]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [UnhandledException]: http://msdn.microsoft.com/fr-fr/library/system.appdomain.unhandledexception.aspx
  [Nouveau message dans l'état Pending]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-pending.png
  [Nouveau message dans l'état Processing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-processing.png
  [Message de file d'attente dans ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue.png
  [Contenu d'un message de file d'attente dans ASE]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-queue-detail.png
  [Ligne Message]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-message-table.png
  [Ligne SendEmail dans la table Message]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-worker-role-a/mtas-worker-a-test-ase-sendemail-row.png
  [didacticiel suivant]: /fr-fr/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
