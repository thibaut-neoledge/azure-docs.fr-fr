<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" pageTitle="Azure Cloud Service Tutorial: ASP.NET Web Role with Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, ASP.NET MVC tutorial, Azure web role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Développement du rôle Web pour l'application Azure Email Service: 3 sur 5.

Voici le troisième didacticiel de notre série sur le développement et le déploiement de l'exemple d'application Azure Email Service. Pour plus d'informations sur l'application et notre série de didacticiels, consultez [le premier didacticiel de cette série][le premier didacticiel de cette série].

Ce didacticiel vous apprendra à effectuer les opérations suivantes:

-   Création d'une solution contenant un projet de service cloud avec un rôle Web et un rôle de travail.
-   Utilisation des tables Azure, des objets blob et des files d'attentes dans les contrôleurs et les vues MVC 5
-   Gestion des conflits d'accès concurrentiels lors de l'utilisation des tables Azure

## Sections de ce didacticiel

-   [Création de la solution Visual Studio][Création de la solution Visual Studio]
-   [Mise à jour du package NuGet de la bibliothèque cliente de stockage][Mise à jour du package NuGet de la bibliothèque cliente de stockage]
-   [Configuration des projets pour utiliser l'émulateur de stockage][Configuration des projets pour utiliser l'émulateur de stockage]
-   [Configuration du suivi et gestion des redémarrages][Configuration du suivi et gestion des redémarrages]
-   [Ajout de code pour créer des tables, une file d'attente et un conteneur d'objets blob dans la méthode Application\_Start][Ajout de code pour créer des tables, une file d'attente et un conteneur d'objets blob dans la méthode Application\_Start]
-   [Création et test de la liste de diffusion][Création et test de la liste de diffusion]
-   [Création et test du contrôleur et des vues Subscriber][Création et test du contrôleur et des vues Subscriber]
-   [Création et test du contrôleur et des vues Message][Création et test du contrôleur et des vues Message]
-   [Création et test du contrôleur et des vues Unsubscribe][Création et test du contrôleur et des vues Unsubscribe]
-   [Étapes suivantes][Étapes suivantes]

## <a name="cloudproject"></a>Création de la solution Visual Studio

Commencez par créer une solution Visual Studio avec un projet pour votre application Web frontale et un projet pour l'un des rôles de travail Azure principaux. Vous ajouterez le second rôle de travail ultérieurement.

### Création d'un projet de service cloud avec un rôle Web et un rôle de travail

1.  Démarrez Visual Studio.

2.  Dans le menu **File**, sélectionnez **New Project**.

    ![Menu New Project][Menu New Project]

3.  Développez **C\#** puis sélectionnez **Cloud** sous **Installed Templates**, puis sélectionnez **Azure Cloud Service**.

4.  Nommez l'application **AzureEmailService** puis cliquez sur **OK**.

    ![New Project dialog box][New Project dialog box]

5.  Dans la boîte de dialogue **New Azure Cloud Service**, sélectionnez **ASP.NET Web Role** puis cliquez sur la flèche pointant vers la droite.

    ![New Azure Cloud Project dialog box][New Azure Cloud Project dialog box]

6.  Dans la colonne de droite, passez le pointeur de la souris sur **WebRole1**, puis cliquez sur l'icône en forme de crayon et renommez le rôle web.

7.  Nommez-le MvcWebRole , puis appuyez sur Entrée.

    ![New Azure Cloud Project dialog box - renaming the web role][New Azure Cloud Project dialog box - renaming the web role]

8.  Suivez la même procédure pour ajouter un **Worker Role**, nommez-le WorkerRoleA, puis cliquez sur **OK**.

    ![Boîte de dialogue New Azure Cloud Project: ajouter un rôle de travail][Boîte de dialogue New Azure Cloud Project: ajouter un rôle de travail]

9.  Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **MVC**, activez la case à cocher **API web**, puis cliquez sur **Modifier l'authentification**.

    ![New Project dialog box][1]

10. Dans la boîte de dialogue **Modifier l'authentification**, cliquez sur **Aucune authentification**, puis sur **OK**.

    ![No authentication][No authentication]

11. Dans la boîte de dialogue **New ASP.NET Project**, cliquez sur **OK**.

### Définition de l'en-tête, du menu et du pied de page

Dans cette section, vous allez mettre à jour les en-têtes, pieds de page et éléments de menu affichés sur chaque page de l'interface Web d'administration. L'application aura trois ensembles de pages Web d'administration: une pour les listes de diffusion, une pour les abonnés aux listes de diffusion et une pour les messages.

1.  Si vous n'avez pas encore téléchargé la [solution complète][solution complète], veuillez le faire avant de passer à l'étape suivante.

    Rappel: lorsque vous devez ajouter des éléments à votre code, copiez les fichiers du projet téléchargé dans le nouveau projet, au lieu de copier et de coller des extraits de code. Ce didacticiel affiche et explique les principales parties du code que vous copiez.

    Pour ajouter un fichier à partir du projet téléchargé, cliquez avec le bouton droit sur le projet auquel vous voulez ajouter un fichier, ou sur le dossier à ajouter, puis sélectionnez **Ajouter un élément existant** à partir du menu contextuel. Accédez ensuite à l'emplacement où vous avez téléchargé le projet complet, sélectionnez les fichiers de votre choix, puis cliquez sur **Ajouter**. Si la boîte de dialogue **Le fichier de destination existe** s'affiche, cliquez sur **Oui**.

2.  Dans le projet MvcWebRole, ajoutez le fichier *Views\\Shared\_Layout.cshtml* à partir du projet téléchargé (cliquez avec le bouton droit sur le dossier *Partagé* sous *Views* pour ajouter le fichier).

    Le code suivant ajoute un en-tête, un pied de page et des entrées de menu pour les pages Mailing List, Message et Subscriber:

        <ul class="nav navbar-nav">
            <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
            <li>@Html.ActionLink("Messages", "Index", "Message")</li>
            <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
        </ul>

### Exécution locale de l'application

1.  Appuyez sur Ctrl+F5 pour exécuter l’application.

    Si vous êtes habitué à démarrer des projets web qui ne sont pas des projets de service cloud Azure, vous remarquerez que l'affichage de la page d'accueil prend un peu plus de temps.

    ![page d'accueil][page d'accueil]

    Ce délai est dû au fait que Visual Studio démarre l'émulateur de calcul Azure et l'émulateur de stockage Azure. Vous pouvez afficher l'icône d'émulateur de calcul dans la barre d'état système Windows:

    ![Émulateur de calcul dans la barre d'état système][Émulateur de calcul dans la barre d'état système]

2.  Fermez le navigateur.

## <a name="updatescl"></a>Mise à jour du package NuGet de la bibliothèque cliente de stockage

L'infrastructure d'API utilisée avec les tables, les files d'attente et les objets blob Azure Storage est la bibliothèque du client de stockage (SCL, Storage Client Library). Cette API est incluse dans un package NuGet dans le modèle de projet de service cloud. Cependant, les mises à jour de la bibliothèque cliente de stockage (SCL) sont souvent publiées après la création des modèles de projet. C'est pourquoi nous vous recommandons de vérifier si une mise à jour est disponible pour le package NuGet de votre SCL.

1.  Dans le menu **Outils** de Visual Studio, passez la souris sur **Library Package Manager**, puis cliquez sur **Manage NuGet Packages for Solution**.

    ![Manage NuGet Packages for Solution dans le menu][Manage NuGet Packages for Solution dans le menu]

2.  Dans le volet gauche de la boîte de dialogue **Manage NuGet Packages**, sélectionnez **Mises à jour**, puis faites défiler jusqu'au package **Azure Storage** et cliquez sur **Mettre à jour**.

    ![Package Azure Storage dans la boîte de dialogue Manage NuGet Packages][Package Azure Storage dans la boîte de dialogue Manage NuGet Packages]

3.  Dans la boîte de dialogue **Select Projects**, vérifiez que les deux projets sont sélectionnés, puis cliquez sur **OK**.

    ![Sélectionner les deux projets dans la boîte de dialogue Select Projects][Sélectionner les deux projets dans la boîte de dialogue Select Projects]

4.  Acceptez les termes du contrat de licence pour terminer l'installation du package, puis fermez la boîte de dialogue **Manage NuGet Packages**.

## <a name="configurestorage"></a>Configuration des projets pour utiliser l'émulateur de stockage

Le code du rôle web et du rôle de travail que vous allez ajouter utiliseront une chaîne de connexion nommée StorageConnectionString pour se connecter à Azure Storage. Dans cette section, vous allez configurer les propriétés de rôle pour utiliser l'émulateur de stockage. Le second didacticiel de la série montre comment configurer la chaîne de connexion pour utiliser un compte de stockage Azure.

1.  Dans l'**Explorateur de solution**, cliquez avec le bouton droit sur **MvcWebRole** sous **Rôles** dans le projet cloud **AzureEmailService**, puis sélectionnez **Propriétés**.

    ![Propriétés de rôle Web][Propriétés de rôle Web]

2.  Assurez-vous que l'option **Toutes les configurations** est sélectionnée dans la liste déroulante **Configuration du service**.

3.  Sélectionnez l'onglet **Paramètres**, puis cliquez sur **Ajouter un paramètre**.

4.  Entrez « StorageConnectionString » dans la colonne **Nom**.

5.  Sélectionnez **Chaîne de connexion** dans la liste déroulante **Type**.

6.  Cliquez sur le bouton **...** situé à l'extrême droite de la ligne pour ouvrir la boîte de dialogue **Chaîne de connexion de compte de stockage**.

    ![Clic droit, Propriétés][Clic droit, Propriétés]

7.  Dans la boîte de dialogue **Créer une chaîne de connexion de stockage**, cliquez sur l'option **Émulateur de stockage Azure**, puis sur **OK**.

    La chaîne de connexion `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` est définie par défaut sur l'émulateur de stockage, vous n'avez donc pas à la modifier.

8.  Suivez la même procédure que celle utilisée pour MvcWebRole pour ajouter la chaîne de connexion de stockage au rôle WorkerRoleA.

Lorsque vous ajoutez un nouveau paramètre avec le bouton **Add Settings**, il est ajouté au code XML dans le fichier *ServiceDefinition.csdf* et dans chacun des deux fichiers de configuration *.cscfg*. Le code XML suivant est ajouté par Visual Studio au fichier *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

Le code XML suivant est ajouté à chaque fichier de configuration *.cscfg*.

       <Setting name="StorageConnectionString"
       value="UseDevelopmentStorage=true" />

Vous pouvez ajouter manuellement des paramètres au fichier *ServiceDefinition.csdf* ainsi qu'aux deux fichiers de configuration *.cscfg*, mais l'utilisation de l'éditeur de propriétés procure les avantages suivants pour les chaînes de connexion:

-   Vous n'avez qu'à ajouter le nouveau paramètre à un seul endroit, puis le code XML correspondant est ajouté aux trois fichiers.
-   Le code XML adéquat est généré pour les trois fichiers de paramètres. Le fichier *ServiceDefinition.csdf* définit les paramètres de chaque fichier de configuration *.cscfg*. Si le fichier *ServiceDefinition.csdf* et les deux fichiers de configuration *.cscfg* ne sont pas cohérents, Visual Studio affiche le message d'erreur suivant: *Le modèle de service actuel n'est pas synchronisé.. Veuillez vérifier que la configuration du service et les fichiers de définition sont corrects.*

    ![Erreur Configuration de service et fichiers de définition incorrects][Erreur Configuration de service et fichiers de définition incorrects]

Si vous obtenez cette erreur, l'éditeur de propriétés ne fonctionnera pas tant que le problème de cohérence n'aura pas été résolu manuellement en modifiant les fichiers.

## <a name="tracing"></a>Configuration du suivi et gestion des redémarrages

1.  Dans le projet MvcWebRole, ajoutez le fichier *WebRole.cs* à partir du projet téléchargé.

Ceci ajoute une méthode qui configure la journalisation et l'appelle à partir de la méthode `OnStart` qui s'exécute lorsque le rôle web démarre. Le code de la nouvelle méthode `ConfigureDiagnostics` est expliqué dans le [second didacticiel][second didacticiel].

Ceci ajoute également du code qui s'exécute lorsque le rôle web reçoit une notification qui l'informe qu'il doit s'arrêter. Les applications Azure Cloud Service sont redémarrées environ deux fois par mois pour des mises à jour du système d'exploitation (pour plus d'informations sur ces mises à jour, consultez le billet [Redémarrages d'instances de rôle pour cause de mise à jour du système d'exploitation][Redémarrages d'instances de rôle pour cause de mise à jour du système d'exploitation]). Lorsqu'une application web va être arrêtée, un événement `OnStop` est généré. Le rôle web réutilisable créé par Visual Studio ne remplace pas la méthode `OnStop`, l'application aura uniquement quelques secondes pour terminer le traitement des requêtes HTTP avant son arrêt. Vous pouvez ajouter du code pour remplacer la méthode `OnStop` pour assurer une gestion appropriée des arrêts.

Le fichier que vous venez d'ajouter contient le remplacement de méthode `OnStop` suivant.

        public override void OnStop()
        {
            Trace.TraceInformation("OnStop called from WebRole");
            var rcCounter = new PerformanceCounter("ASP.NET", "Requests Current", "");
            while (rcCounter.NextValue() > 0)
            {
                Trace.TraceInformation("ASP.NET Requests Current = " + rcCounter.NextValue().ToString());
                System.Threading.Thread.Sleep(1000);
            }
        }

La méthode `OnStop` a jusqu'à 5 minutes pour se terminer avant l'arrêt de l'application. Pour pouvez ajouter un appel de veille pour 5 minutes à la méthode `OnStop` afin de laisser à votre application la durée maximum pour traiter les demandes en cours, mais si l'application est correctement mise à l'échelle, elle doit pouvoir traiter les demandes restantes en moins de 5 minutes. Nous vous conseillons de procéder à l'arrêt dès que possible, pour que l'application puisse redémarrer aussi rapidement que possible et continuer le traitement des demandes.

Lorsqu'un rôle est placé en hors connexion par Azure, le programme d'équilibrage de la charge arrête l'envoi des demandes vers l'instance de rôle, après quoi la méthode `OnStop` est appelée. Si vous n'avez pas d'autre instance pour votre rôle, aucune demande ne sera traitée jusqu'au redémarrage de votre rôle (ce qui peut prendre quelques minutes). C'est pourquoi le contrat de niveau de service Azure nécessite que vous ayez au moins deux instances de chaque rôle pour profiter de la garantie de disponibilité.

Dans le code affiché pour la méthode `OnStop`, un compteur de performances ASP.NET est créé pour `Requests Current`. La valeur de compteur `Requests Current` contient le nombre actuel de demandes, notamment celles en file d'attente, en cours d'exécution ou en attente d'écriture vers le client. La valeur `Requests Current` est vérifiée chaque seconde: lorsqu'elle atteint zéro, la méthode `OnStop` est renvoyée. Une fois la méthode `OnStop` renvoyée, le rôle s'arrête.

Les données de suivi ne sont pas enregistrées lorsqu'elles sont appelées depuis la méthode `OnStop` sans effectuer de [transfert à la demande][transfert à la demande]. Vous pouvez afficher les informations de suivi `OnStop` en temps réel avec l'utilitaire [dbgview][dbgview] à partir d'une connexion Bureau à distance.

## <a name="createifnotexists"></a>Ajout de code pour créer des tables, une file d'attente et un conteneur d'objets blob dans la méthode Application\_Start

L'application web utilisera la table `MailingList`, la table `Message`, la file d'attente `azuremailsubscribequeue` et le conteneur d'objets blob `azuremailblobcontainer`. Vous pouvez créer ces éléments manuellement en utilisant un outil tel que l'Explorateur de stockage Azure, mais cela vous obligerait à répéter cette opération à chaque démarrage de l'application avec un nouveau compte de stockage. Dans cette section, vous allez ajouter un code qui s'exécute au démarrage de l'application, contrôle la présence des tables, files d'attente et conteneurs d'objets blob, et crée les éléments manquants.

Vous pouvez ajouter ce code de démarrage unique à la méthode `OnStart` dans le fichier *WebRole.cs* ou dans le fichier *Global.asax*. Pour ce didacticiel, vous allez initialiser Azure Storage dans le fichier *Global.asax*.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet MvcWebRole, puis ajoutez le fichier *Global.asax.cs* à partir du projet téléchargé.

Vous avez ajouté une nouvelle méthode appelée à partir de la méthode `Application_Start`:

        private static void CreateTablesQueuesBlobContainers()
        {
            var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

            var tableClient = storageAccount.CreateCloudTableClient();
            var mailingListTable = tableClient.GetTableReference("MailingList");
            mailingListTable.CreateIfNotExists();

            var messageTable = tableClient.GetTableReference("Message");
            messageTable.CreateIfNotExists();

            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
            blobContainer.CreateIfNotExists();

            var queueClient = storageAccount.CreateCloudQueueClient();
            var subscribeQueue = queueClient.GetQueueReference("azuremailsubscribequeue");
            subscribeQueue.CreateIfNotExists();
        }

Dans les sections suivantes, vous allez développer les composants de l'application Web, puis vous pourrez les tester avec le stockage de développement ou votre compte de stockage sans avoir à créer d'abord des tables, des files d'attente ou un conteneur d'objets blob manuellement.

## <a name="mailinglist"></a>Création et test du contrôleur et des vues Mailing List

L'interface utilisateur Web **Liste de diffusion** permet aux administrateurs de créer, modifier et afficher les listes de diffusion, telles que « Annonces du département d'histoire de l'université Contoso » et « Offres d'emploi Fabrikam Engineering ».

### Ajout de la classe d'entité MailingList au dossier Models

La classe d'entité `MailingList` est utilisée pour les lignes de la table `MailingList` contenant des informations sur la liste, comme ses descriptions et l'adresse électronique « De » pour les courriers électroniques envoyés à la liste.

1.  Dans le dossier `Models` du projet MVC, ajoutez le fichier *MailingList.cs* à partir du projet téléchargé.

    Vous utilisez la classe d'entité `MailingList` pour la lecture et l'écriture des lignes de liste de diffusion dans la table mailinglist.

        public class MailingList : TableEntity
        {
            public MailingList()
            {
                this.RowKey = "mailinglist";
            }

            [Required]
            [RegularExpression(@"[\w]+",
             ErrorMessage = @"Only alphanumeric characters and underscore (_) are allowed.")]
            [Display(Name = "List Name")]
            public string ListName
            {
                get
                {
                    return this.PartitionKey;
                }
                set
                {
                    this.PartitionKey = value;
                }
            }

            [Required]
            [Display(Name = "'From' Email Address")]
            public string FromEmailAddress { get; set; }

            public string Description { get; set; }
        }

    L'API Azure Storage requiert que les classes d'entité pour les opérations de table dérivent de [TableEntity][TableEntity]. `TableEntity` définit les champs `PartitionKey`, `RowKey`, `TimeStamp` et `ETag`. Les propriétés `TimeStamp` et `ETag` sont utilisées par le système. L'utilisation de la propriété `ETag` pour la gestion des conflits d'accès concurrentiels est abordée plus tard dans ce didacticiel.

    Il existe également une classe [DynamicTableEntity][DynamicTableEntity], que vous pouvez utiliser lorsque vous voulez utiliser les lignes de table en tant que collections Dictionnaire de paires clé-valeur au lieu d'utiliser des classes de modèle prédéfinies. Pour plus d'informations, consultez le billet [Présentation approfondie des tables de bibliothèque du client de stockage Azure 2.0][Présentation approfondie des tables de bibliothèque du client de stockage Azure 2.0].

    La clé de partition de table `mailinglist` est le nom de la liste. Dans cette classe d'entité, vous pouvez accéder à la valeur de clé de partition en utilisant la propriété `PartitionKey` (définie dans la classe `TableEntity`) ou la propriété `ListName` (définie dans la classe `MailingList`). La propriété `ListName` utilise `PartitionKey` en tant que variable de stockage. Le fait de définir la propriété `ListName` vous permet d'utiliser un nom de variable plus explicite dans le code et facilite la programmation de l'interface utilisateur web, car les attributs de mise en forme et de validation DataAnnotations peuvent être ajoutés à la propriété `ListName`, mais ils ne peuvent pas être ajoutés directement dans la propriété `PartitionKey`.

    L'attribut `RegularExpression` de la propriété `ListName` oblige MVC à valider les entrées utilisateur pour vérifier que la valeur du nom de la liste entrée contient uniquement des caractères alphanumérique ou des traits de soulignement. Cette restriction a été implémentée pour que les noms de liste restent simples et faciles à utiliser dans les chaînes de requête des URL.

    > [WACOM.NOTE] Si vous voulez que la mise en forme des noms de liste soit moins restrictive, vous pouvez autoriser d'autres caractères et noms de liste URL-encode lorsqu'ils sont utilisés dans des chaînes de requête. Cependant, certains caractères sont interdits dans les clés de partition ou de lignes de tables Azure, et vous devrez exclure au moins ces caractères. Pour plus d'informations sur les caractères interdits ou posant problème dans les champs de clé de partition ou de ligne, consultez l'article [Présentation du modèle de données du service de table][Présentation du modèle de données du service de table] et le billet [Caractère % dans PartitionKey ou RowKey][Caractère % dans PartitionKey ou RowKey].

    La classe `MailingList` définit un constructeur par défaut qui définit `RowKey` sur la chaîne « mailinglist » codée en dur, car toutes les lignes MailingList de cette table contiennent cette valeur en tant que clé de ligne. Pour plus d'informations sur la structure de table, consultez le [premier didacticiel de cette série][le premier didacticiel de cette série]. Vous pouvez choisir n'importe quelle valeur constante dans ce cas, tant qu'elle reste toujours différente d'une valeur d'adresse électronique, qui est la clé de ligne des lignes subscriber de cette table.

    Le nom de la liste et l'adresse électronique « De » doivent toujours être entrées lors de la création d'une nouvelle entité `MailingList`, pour qu'ils possèdent les attributs `Required`.

    Les attributs `Display` indiquent la légende par défaut à utiliser pour un champ dans l'interface utilisateur MVC.

### Ajout au contrôleur MVC MailingList

1.  Dans le dossier *Controllers* du projet MVC, ajoutez le fichier *MailingListController.cs* à partir du projet téléchargé.

    Le constructeur par défaut du contrôleur crée un objet `CloudTable` à utiliser pour faire fonctionner la table `mailinglist`.

        public class MailingListController : Controller
        {
            private CloudTable mailingListTable;

            public MailingListController()
            {
                var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));

                var tableClient = storageAccount.CreateCloudTableClient();
                mailingListTable = tableClient.GetTableReference("mailinglist");

    Le code permet d'obtenir les informations d'identification de votre compte Azure Storage à partir du fichier de paramètres du projet de service cloud pour établir une connexion avec le compte de stockage. La configuration de ces paramètres est abordée plus tard dans ce didacticiel, avant le test du contrôleur.

    La méthode suivante, `FindRowAsync`, est appelée lorsque le contrôleur doit rechercher une entrée spécifique de la liste de diffusion dans la table `MailingList` (par exemple, lorsque vous voulez modifier une entrée de cette liste). Le code récupère une seule entité `MailingList` en utilisant la clé de partition et les valeurs de clé de ligne qui lui sont transmises. Les lignes modifiées par ce contrôleur sont celles dont la clé de ligne est « MailingList ». Vous auriez donc pu coder « MailingList » en dur, mais le fait de spécifier une clé de partition et une clé de ligne est un modèle utilisé pour les méthodes `FindRow` dans tous les contrôleurs.

    > [WACOM.NOTE] L'application utilise un code asynchrone ASP.NET 4.5 pour les opérations d'E/S dans le rôle web afin d'utiliser les ressources serveur plus efficacement. Pour plus d'informations sur le code asynchrone dans l'application web, consultez [Utilisation de la prise en charge asynchrone .NET 4.5 pour éviter le blocage des appels][Utilisation de la prise en charge asynchrone .NET 4.5 pour éviter le blocage des appels].

        private async Task<MailingList> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var mailingList = retrievedResult.Result as MailingList;
            if (mailingList == null)
            {
                throw new Exception("No mailing list found for: " + partitionKey);
            }
            return mailingList;
        }

    Le code de cette méthode `FindRow` renvoie une ligne de liste de diffusion. Le code de la méthode `FindRow` du contrôleur `Subscriber` renvoie une ligne d'abonné à partir de la même table `mailinglist`. Le code des deux méthodes est identique, à l'exception du type de modèle utilisé avec la méthode [TableOperation.Retrieve][TableOperation.Retrieve].

        private async Task<Subscriber> FindRowAsync(string partitionKey, string rowKey)
        {
            var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
            var retrievedResult = await mailingListTable.ExecuteAsync(retrieveOperation);
            var subscriber = retrievedResult.Result as Subscriber;
            if (subscriber == null)
            {
                throw new Exception("No subscriber found for: " + partitionKey + ", " + rowKey);
            }
            return subscriber;
        }

    L'objet `TableOperation` renvoyé par la méthode `TableOperation.Retrieve` indique le schéma (les propriétés) d'une ou plusieurs lignes que la requête doit renvoyer. Une seule table peut avoir différents schémas dans différentes lignes. Généralement, le type de modèle utilisé pour la lecture d'une ligne est le même que celui utilisé pour sa création.

    La page **Index** affiche toutes les lignes MailingList. La requête de la méthode `Index` renvoie donc toutes les entités `MailingList` dont la clé de ligne est « mailinglist » (les autres lignes de la table ont une adresse de messagerie comme clé de ligne, et contiennent des informations sur les abonnés).

        var query = new TableQuery<MailingList>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.Equal, "mailinglist"));
        TableContinuationToken token = null;
        OperationContext ctx = new OperationContext();
        TableQuerySegment<MailingList> currentSegment = null;
        while (currentSegment == null || currentSegment.ContinuationToken != null)
        {
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync
                (query, token, webUIRetryPolicy, ctx);
            lists.AddRange(currentSegment.Results);
            token = currentSegment.ContinuationToken;
        }

    La méthode `ExecuteQuerySegmentedAsync` décompose les résultats volumineux en segments. Elle renvoie jusqu'à 1 000 lignes. Lorsque vous exécutez une requête qui doit extraire plus de 1 000 lignes, vous obtenez 1 000 lignes et un jeton de liaison. Vous pouvez utiliser ce jeton pour exécuter une autre requête qui démarre là où la précédente s'est terminée. Le code affiché est simplifié pour un exemple d'application: il agrège tous les segments en une seule liste. Pour une application de production, vous devez implémenter le code de pagination. Pour plus d'informations sur les grands ensembles de résultats et les jetons de liaison, consultez les billets [Comment tirer le meilleur parti des tables Azure][Comment tirer le meilleur parti des tables Azure] et [Tables Azure: utilisation des jetons de liaison][Tables Azure: utilisation des jetons de liaison].

    Lorsque vous créez l'objet `OperationContext`, vous pouvez définir la valeur de propriété `ClientID` pour fournir un identificateur unique qui sera inclus dans les journaux écrits par Azure Storage. Vous pouvez utiliser cet identificateur pour suivre les journaux d'opération de stockage dans le code ayant provoqué l'activité du service de stockage. Pour plus d'informations sur la journalisation d'Azure Storage, consultez [Journalisation d'Azure Storage: utilisation de journaux pour suivre les requêtes de stockage][Journalisation d'Azure Storage: utilisation de journaux pour suivre les requêtes de stockage].

    Avec l'API SCL 2.1 et ses versions ultérieures, vous pouvez également utiliser LINQ pour vos requêtes de table. Pour obtenir un exemple de code illustrant l'utilisation de LINQ, consultez ce scénario [PhluffyFotos][PhluffyFotos].

    Si vous n'indiquez pas de stratégie de nouvelle tentative, l'API effectue automatiquement trois nouvelles tentatives en augmentant progressivement le délai d'expiration. Dans le cas d'une interface Web où un utilisateur attend l'affichage d'une page, ce délai serait trop long. C'est pourquoi ce code indique des nouvelles tentatives linéaires (pour que la limite de délai n'augmente pas à chaque fois) ainsi qu'une limite de délai raisonnable pour l'utilisateur. La stratégie de nouvelle tentative est indiquée dans l'objet `webUIRetryPolicy` transmis à la méthode `ExecuteQuerySegmentedAsync`. L'objet `webUIRetryPolicy` est défini dans le constructeur du contrôleur:

        private TableRequestOptions webUIRetryPolicy;

        public MailingListController()
        {
            // Other constructor code not shown.

            webUIRetryPolicy = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
        }

    La méthode `Index` inclut un bloc try-catch conçu pour gérer les conditions de délai d'expiration.

        try
        {
            // Code not shown for retrieving MailingList rows.
        }
        catch (StorageException se)
        {
            ViewBag.errorMessage = "Timeout error, try again. ";
            Trace.TraceError(se.Message);
            return View("Error");
        }

    Lorsque l'utilisateur clique sur le bouton **Créer** de la page **Créer**, le classeur de modèles MVC crée une entité `MailingList` à partir des entrées saisies dans la vue, puis la méthode `HttpPost Create` ajoute l'entité à la table.

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(MailingList mailingList)
        {
            if (ModelState.IsValid)
            {
                var insertOperation = TableOperation.Insert(mailingList);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }

            return View(mailingList);
        }

    Pour la page **Modifier**, la méthode `HttpPost Edit` met à jour la ligne.

        var replaceOperation = TableOperation.Replace(mailingList);
        await mailingListTable.ExecuteAsync(replaceOperation);

    Dans la méthode `HttpPost Edit`, un bloc catch gère les erreurs d'accès concurrentiel.

        catch (StorageException ex)
        {
            if (ex.RequestInformation.HttpStatusCode == 412)
            {
                // Concurrency error
                var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
                var retrievedResult = mailingListTable.Execute(retrieveOperation);
                var currentMailingList = retrievedResult.Result as MailingList;
                if (currentMailingList == null)
                {
                    ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                        + "was deleted by another user after you got the original value. The "
                        + "edit operation was canceled. Click the Back to List hyperlink.");
                }
                if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                {
                    ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                }
                if (currentMailingList.Description != editedMailingList.Description)
                {
                    ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                }
                ModelState.AddModelError(string.Empty, "The record you attempted to edit "
                    + "was modified by another user after you got the original value. The "
                    + "edit operation was canceled and the current values in the database "
                    + "have been displayed. If you still want to edit this record, click "
                    + "the Save button again. Otherwise click the Back to List hyperlink.");
                 ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
            }
            else
            {
                throw; 
            }
        }

    Une exception d'accès concurrentiel est générée si un utilisateur sélectionne une liste de diffusion pour la modifier, et pendant que la page **Modifier** est affichée dans le navigateur, un autre utilisateur modifie la même liste de diffusion. Lorsque cela se produit, le code affiche un message d'avertissement et indique les champs modifiés par l'autre utilisateur. L'API TSL utilise le champ `ETag` pour vérifier les conflits d'accès concurrentiels. Lorsqu'une ligne de table est mise à jour, la valeur `ETag` est modifiée. Lorsque vous obtenez une ligne pour la modifier, vous enregistrez la valeur `ETag`, puis, lorsque vous procédez à une mise à jour ou à une suppression, vous transmettez cette valeur enregistrée (la vue `Edit` a un champ caché pour la valeur ETag). Si l'opération de mise à jour détecte une différence entre la valeur `ETag` de l'enregistrement que vous mettez à jour et la valeur `ETag` transmise, une exception d'accès concurrentiel est générée. Si vous ne vous souciez pas des conflits d'accès concurrentiels, vous pouvez définir la valeur du champ ETag en entrant un astérisque (\*) dans l'entité transmise à l'opération de mise à jour: tout conflit sera alors ignoré.

    Remarque: l'erreur HTTP 412 n'est pas propre aux erreurs d'accès concurrentiels. L'API SCL peut la générer pour d'autres types d'erreurs.

    Pour la page **Supprimer**, la méthode `HttpPost Delete` supprime la ligne `MailingList` ainsi que chaque ligne `Subscriber` qui lui est associée dans la table `MailingList`.

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> DeleteConfirmed(string partitionKey)
        {
            // Delete all rows for this mailing list, that is, 
            // Subscriber rows as well as MailingList rows.
            // Therefore, no need to specify row key.

            // Get all rows for this mailing list. For a production app where this 
            // could return too many split the work up into segments.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            List<MailingList> listRows = new List<MailingList>();
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                listRows.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }

            // Delete the rows in batches of 100.
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    await mailingListTable.ExecuteBatchAsync(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
            return RedirectToAction("Index");
        }

    Si vous devez supprimer un grand nombre d'abonnés, le code supprime les enregistrements par lots. Le coût de transaction de la suppression d'une ligne est identique à celui de la suppression de 100 lignes dans un lot. Le nombre maximum d'opérations que vous pouvez effectuer dans un lot est 100.

    Même si la boucle traite les lignes `MailingList` et `Subscriber`, elle les lit toutes dans la classe d'entité `MailingList`, car les seuls champs requis pour l'opération `Delete` sont les champs `PartitionKey`, `RowKey` et `ETag`.

### Ajout des vues MVC MailingList

1.  Dans le dossier *Views* du projet MVC, créez un dossier et nommez-le *MailingList*.

2.  Dans le nouveau dossier *Views\\MailingList*, ajoutez les quatre fichiers *.cshtml* depuis le projet téléchargé.

Dans le fichier *Edit.cshtml*, notez le champ masqué inclus pour conserver la valeur `ETag` utilisée pour la gestion des conflits d'accès concurrentiels.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
        
            @Html.HiddenFor(model => model.ETag)

Notez également que le champ `ListName` a une application auxiliaire `DisplayFor` et non `EditorFor`.

        <div class="form-group">
            @Html.LabelFor(model => model.ListName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.DisplayFor(model => model.ListName, new { htmlAttributes = new { @class = "form-control" } })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            </div>
        </div>
        
        <div class="form-group">
            @Html.LabelFor(model => model.FromEmailAddress, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FromEmailAddress, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FromEmailAddress, "", new { @class = "text-danger" })
            </div>
        </div>

Nous n'avons pas activé la page **Modifier** pour modifier le nom de la liste, car cela requiert un code trop complexe dans le contrôleur: La méthode `HttpPost Edit` aurait dû supprimer la ligne MailingList existante et toutes les lignes subscriber associées, avant de toutes les réinsérer avec la nouvelle valeur de clé. Dans une application de production, il est possible qu'une telle complexité vaille la peine. Comme nous le verrons plus tard, le contrôleur `Subscriber` permet de modifier le nom de la liste, mais cela n'affecte qu'une ligne à la fois.

Dans le fichier *Index.cshtml*, les liens hypertexte **Edit** et **Delete** spécifient des paramètres de chaîne de requête de clés de partition et de ligne pour identifier une ligne spécifique. Pour les entités `MailingList`, seule la clé de partition est vraiment nécessaire, car la clé de ligne est toujours « MailingList », mais les deux types de clés sont conservés pour que le code de vue MVC soit cohérent pour l'ensemble des contrôleurs et des vues.

        <td>
            @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
            @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
        </td>

### Définition de MailingList comme contrôleur par défaut

1.  Ouvrez *Route.config.cs* dans le dossier *App\_Start*.

2.  Dans la ligne spécifiant la valeur par défaut, remplacez le contrôleur par défaut « Home » par « MailingList ».

         routes.MapRoute(
             name: "Default",
             url: "{controller}/{action}/{id}",
             defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5.

    ![Page d'index MailingList vide][Page d'index MailingList vide]

2.  Utilisez la fonction **Create** pour ajouter des listes de diffusion, puis essayez les fonctions **Edit** et **Delete** pour vérifier qu'elles fonctionnent correctement.

    ![Page d'index MailingList avec des lignes][Page d'index MailingList avec des lignes]

## <a name="subscriber"></a><span class="short-header">Subscriber</span>Création et test du contrôleur et des vues Subscriber

L'interface utilisateur web **Subscriber** permet aux administrateurs d'ajouter des abonnés à une liste de diffusion, mais aussi de modifier, d'afficher et de supprimer les abonnés existants.

### Ajout de la classe d'entité Subscriber au dossier Models

La classe d'entité `Subscriber` est utilisée pour les lignes de la table `MailingList` qui contiennent des informations sur les abonnés à une liste. Ces lignes contiennent des informations telles que l'adresse électronique de chaque abonné et un champ indiquant si chaque adresse a été vérifiée.

1.  Dans le dossier *Models* du projet MVC, ajoutez le fichier *Subscriber.cs* depuis le projet téléchargé.

Comme la classe d'entité `MailingList`, la classe d'entité `Subscriber` permet de lire et d'écrire des lignes dans la table `mailinglist`.

            public class Subscriber : TableEntity
            {
                [Required]
                public string ListName
                {
                    get
                    {
                        return this.PartitionKey;
                    }
                    set
                    {
                        this.PartitionKey = value;
                    }
                }
        
                [Required]
                [Display(Name = "Email Address")]
                public string EmailAddress
                {
                    get
                    {
                        return this.RowKey;
                    }
                    set
                    {
                        this.RowKey = value;
                    }
                }
        
                public string SubscriberGUID { get; set; }
        
                public bool? Verified { get; set; }
            }
        

Les lignes `Subscriber` utilisent l'adresse électronique au lieu de la constante « mailinglist » comme clé de ligne. Pour plus d'informations sur la structure de table, consultez le [premier didacticiel de cette série][le premier didacticiel de cette série]. Par conséquent, une propriété `EmailAddress` est définie. Elle utilise la propriété `RowKey` en tant que champ de stockage tout comme `ListName` utilise `PartitionKey` comme champ de stockage. Comme nous l'avons vu précédemment, ceci vous permet de placer des attributs de mise en forme et de validation DataAnnotations dans les propriétés.

La valeur `SubscriberGUID` est générée lors de la création d'une entité `Subscriber`. Elle est utilisée dans les liens d'abonnement et de désabonnement, pour garantir que seules les personnes autorisées peuvent s'abonner ou se désabonner en utilisant leurs adresses électroniques.

Lors de la création initiale de la ligne pour le nouvel abonné, la valeur `Verified` est définie sur `false`. La valeur `Verified` passe à `true` uniquement lorsque le nouvel abonné clique sur le lien **Confirmer** dans le courrier électronique de bienvenue. Si un message est envoyé à la liste alors que la valeur `Verified` d'un abonné est `false`, aucun message n'est envoyé à cet abonné.

La propriété `Verified` de l'entité `Subscriber` est définie sur Nullable. Lorsque vous indiquez qu'une requête doit renvoyer des entités `Subscriber`, il est possible que certaines lignes récupérées n'aient pas une propriété `Verified`. C'est pourquoi l'entité `Subscriber` définit sa propriété `Verified` comme Nullable, pour qu'elle puisse refléter plus précisément le contenu d'une ligne si la requête renvoie des lignes de table sans propriété *Verified*. Vous êtes peut-être habitué à utiliser des tables SQL Server, dans lesquelles chaque ligne a le même schéma. Dans une table Azure Storage, chaque ligne est une collection de propriétés différente. Par exemple, dans l'exemple d'application Azure Email Service, les lignes dont la clé est « MailingList » n'ont pas de propriété `Verified`. Si une requête renvoie une ligne de table sans propriété `Verified`, lors de l'instanciation de la classe d'entité `Subscriber`, la propriété `Verified` de l'objet d'entité sera null. Si la propriété n'était pas nullable, vous obtiendriez la même valeur `false` pour les lignes dont la propriété `Verified` est définie sur `false` et pour les lignes sans propriété `Verified`. C'est pourquoi nous vous conseillons d'utiliser les tables Azure pour définir chaque propriété d'une classe d'entité sur nullable afin de préciser la lecture des lignes créés en utilisant différentes classes d'entité ou différentes versions de la classe d'entité actuelle.

### Ajout du contrôleur MVC Subscriber

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Controllers* dans le projet MVC, puis sélectionnez **Ajouter un élément existant**.

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *SubscriberController.cs* dans le dossier *Controllers*, puis cliquez sur **Ajouter**. Vérifiez que vous avez bien sélectionné *Subscriber.cs* et non pas *Subscribe.cs*: nous ajouterons *Subscribe.cs* ultérieurement.

La plupart du code de ce contrôleur est semblable à celui du contrôleur `MailingList`. Même le nom de la table est le même, car les informations sur les abonnés sont conservées dans la table `MailingList`.

Outre la méthode `FindRowAsync`, il existe une méthode `FindRow`, car vous avez besoin de l'appeler à partir d'un bloc catch et vous ne pouvez pas appeler une méthode asynchrone à partir de ce type de bloc.

Après les méthodes `FindRow` s'affiche une méthode `GetListNamesAsync`. Cette méthode permet d'obtenir les données pour une liste déroulante sur les pages **Create** et **Edit**, dans laquelle vous pouvez sélectionner la liste de diffusion à laquelle abonner l'adresse électronique.

        private async Task<List<MailingList>> GetListNamesAsync()
        {
            List<MailingList> lists = new List<MailingList>();
            var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext();
            TableQuerySegment<MailingList> currentSegment = null;
            while (currentSegment == null || currentSegment.ContinuationToken != null)
            {
                currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, webUIRetryPolicy, ctx);
                lists.AddRange(currentSegment.Results);
                token = currentSegment.ContinuationToken;
            }
            return lists;
        }

Il s'agit de la même requête que celle du contrôleur `MailingList`. Les lignes de cette liste déroulante doivent contenir des informations sur les listes de diffusion: sélectionnez donc uniquement celles dont la clé est « mailinglist ».

Les lignes de la méthode de récupération des données pour la page **Index** doivent contenir des informations sur les abonnées: sélectionnez donc les lignes dont la clé n'est pas « MailingList ».

        var query = (new TableQuery<Subscriber>()
            .Where(TableQuery.GenerateFilterCondition
                ("RowKey", QueryComparisons.NotEqual, "mailinglist")));

Notez que la requête spécifie que les données seront lues dans des objets `Subscriber` (en indiquant `<Subscriber>`) mais que les données seront lues depuis la table `mailinglist`.

Le nombre d'abonnés peut augmenter jusqu'à devenir trop volumineux pour pouvoir être géré de cette manière, avec une seule requête. Comme indiqué précédemment, dans une application de production, vous implémentez simplement la pagination en utilisant des jetons de liaison.

Dans la méthode `HttpGet Create`, configurez les données pour la liste déroulante et dans la méthode `HttpPost`, définissez les valeurs par défaut avant d'enregistrer la nouvelle entité.

        public async Task<ActionResult> Create()
        {
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            var model = new Subscriber() { Verified = false };
            return View(model);
        }
        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Subscriber subscriber)
        {
            if (ModelState.IsValid)
            {
                subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                if (subscriber.Verified.HasValue == false)
                {
                    subscriber.Verified = false;
                }
        
                var insertOperation = TableOperation.Insert(subscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);
        
            return View(subscriber);
        }

La page `HttpPost Edit` est plus complexe que celle du contrôleur `MailingList`, car la page `Subscriber` vous permet de modifier le nom de la liste ou l'adresse électronique, qui sont tous deux des champs clés. Si l'utilisateur change l'un de ces champs, vous devez supprimer l'enregistrement existant et en ajouter un nouveau au lieu de mettre à jour l'enregistrement existant. Le code suivant concerne la partie de la méthode edit qui gère les différentes procédures pour des modifications de clés par rapport à des non-clés:

        UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
        if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
        {
            //Keys didn't change -- Update the row
            var replaceOperation = TableOperation.Replace(editedSubscriber);
            await mailingListTable.ExecuteAsync(replaceOperation);
        }
        else
        {
            // Keys changed, delete the old record and insert the new one.
            if (editedSubscriber.PartitionKey != partitionKey)
            {
                // PartitionKey changed, can't do delete/insert in a batch.
                var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                await mailingListTable.ExecuteAsync(deleteOperation);
                var insertOperation = TableOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteAsync(insertOperation);
            }
            else
            {
                // RowKey changed, do delete/insert in a batch.
                var batchOperation = new TableBatchOperation();
                batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                batchOperation.Insert(editedSubscriber);
                await mailingListTable.ExecuteBatchAsync(batchOperation);
            }
        }

Les paramètres que le classeur de modèles MVC transmet à la méthode `Edit` incluent le nom de liste et les valeurs d'adresses électroniques d'origine (dans les paramètres `partitionKey` et `rowKey`) ainsi que les valeurs entrées par l'utilisateur (dans les paramètres `listName` et `emailAddress`):

        public async Task<ActionResult> Edit(string partitionKey, string rowKey, string listName, string emailAddress, Subscriber editedSubscriber)

Les paramètres transmis à la méthode `UpdateModel` excluent les propriétés `PartitionKey` et `RowKey` de la liaison du modèle:

        var excludeProperties = new string[] { "PartitionKey", "RowKey" };
            

En effet, les propriétés `ListName` et `EmailAddress` utilisent `PartitionKey` et `RowKey` en tant que propriétés de stockage et l'utilisateur peut avoir modifié l'une de ces valeurs. Lorsqu'un classeur de modèles met à jour le modèle en définissant la propriété `ListName`, la propriété `PartitionKey` est automatiquement mise à jour. Si le modèle de liaison parvenait à mettre à jour la propriété `PartitionKey` avec la valeur d'origine de cette propriété après la mise à jour de la propriété `ListName`, cela remplacerait la nouvelle valeur définie par la propriété `ListName`. La propriété `EmailAddress` met automatiquement à jour la propriété `RowKey` de la même manière.

Après la mise à jour de l'objet de modèle `editedSubscriber`, le code détermine si la clé de partition ou de ligne a été modifiée. Si l'une des valeurs de clé a été modifiée, la ligne subscriber existante doit être supprimée, puis une nouvelle ligne insérée. Si seule la clé de ligne a été modifiée, la suppression et l'insertion peuvent être effectuées dans une transaction atomique par lots.

Notez que le code crée une entité à transmettre à l'opération `Delete`:

            // RowKey changed, do delete/insert in a batch.
            var batchOperation = new TableBatchOperation();
            batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
            batchOperation.Insert(editedSubscriber);
            await mailingListTable.ExecuteBatchAsync(batchOperation);

Les entités transmises vers les opérations dans un lot doivent être distinctes. Par exemple, vous ne pouvez pas créer une entité `Subscriber`, la transmettre à une opération `Delete`, puis modifier une valeur dans la même entité `Subscriber` et la transmettre à une opération `Insert`. Si vous avez procédé ainsi, l'état de l'entité après la modification de propriété doit prendre effet pour les opérations Delete et Insert.

Les opérations d'un lot doivent toutes être situées sur la même partition. Comme le fait de modifier le nom de la liste modifie la clé de partition, vous ne pouvez pas le faire dans une transaction.

### Ajout des vues Subscriber MVC

1.  Dans l'**Explorateur de solutions**, créez un nouveau dossier sous le dossier *Views* dans le projet MVC, et nommez-le *Subscriber*.

2.  Cliquez avec le bouton droit sur le nouveau dossier *Views\\Subscriber*, puis sélectionnez **Ajouter un élément existant**.

3.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez les cinq fichiers .cshtml dans le dossier *Views\\Subscriber*, puis cliquez sur **Ajouter**.

Dans le fichier *Edit.cshtml*, notez le champ masqué inclus pour la valeur `SubscriberGUID`, puisque ce champ n'est pas affiché et qu'il n'est donc pas automatiquement fourni dans un champ de formulaire

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.SubscriberGUID)
            @Html.HiddenFor(model => model.ETag)
                        

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5, puis cliquez sur **Subscribers**.

    ![Page d'index Subscriber vide][Page d'index Subscriber vide]

2.  Utilisez la fonction **Create** pour ajouter des listes de diffusion, puis essayez les fonctions **Edit** et **Delete** pour vérifier qu'elles fonctionnent correctement.

    ![Page d'index Subscribers avec des lignes][Page d'index Subscribers avec des lignes]

## <a name="message"></a>Création et test du contrôleur et des vues Message

L'interface utilisateur Web **Message** permet aux administrateurs de créer, modifier et afficher des informations sur les messages devant être envoyés vers les listes de diffusion.

### Ajout de la classe d'entité Message au dossier Models

1.  Dans le dossier *Models* du projet MVC, ajoutez le fichier *Message.cs* depuis le projet téléchargé.

La classe d'entité `Message` est utilisée pour les lignes dans la table `Message` contenant des informations sur un message devant être envoyé vers une liste. Ces lignes incluent des informations telles que la ligne d'objet, la liste vers laquelle envoyer le message et la date prévue pour l'envoi.

        public class Message : TableEntity
        {
            private DateTime? _scheduledDate;
            private long _messageRef;

            public Message()
            {
                this.MessageRef = DateTime.Now.Ticks;
                this.Status = "Pending";
            }

            [Required]
            [Display(Name = "Scheduled Date")]
            // DataType.Date shows Date only (not time) and allows easy hook-up of jQuery DatePicker
            [DataType(DataType.Date)]
            public DateTime? ScheduledDate 
            {
                get
                {
                    return _scheduledDate;
                }
                set
                {
                    _scheduledDate = value;
                    this.PartitionKey = value.Value.ToString("yyyy-MM-dd");
                }
            }
            
            public long MessageRef 
            {
                get
                {
                    return _messageRef;
                }
                set
                {
                    _messageRef = value;
                    this.RowKey = "message" + value.ToString();
                }
            }

            [Required]
            [Display(Name = "List Name")]
            public string ListName { get; set; }

            [Required]
            [Display(Name = "Subject Line")]
            public string SubjectLine { get; set; }

            // Pending, Queuing, Processing, Complete
            public string Status { get; set; }
        }
        

La classe `Message` définit un constructeur par défaut qui définit la propriété `MessageRef` sur une valeur unique pour le message. Comme cette valeur fait partie de la clé de ligne, le setter de la propriété `MessageRef` définit également automatiquement la propriété `RowKey`. Le setter de la propriété `MessageRef` concatène le terme « message » et la valeur `MessageRef` et place le résultat dans la propriété `RowKey`.

La valeur `MessageRef` est créée en obtenant la valeur `Ticks` de `DateTime.Now`. Ceci garantit par défaut que les messages affichés dans l'interface utilisateur seront également affichés dans l'ordre de leur création pour une date pour une date prévue (`ScheduledDate` est la clé de partition). Si vous utilisez un GUID pour rendre unique chaque ligne du message, l'ordre de récupération par défaut devient aléatoire.

Le constructeur par défaut définit également l'état par défaut des nouvelles lignes de `message` sur Pending.

Pour plus d'informations sur la structure de table `Message`, consultez le [premier didacticiel de cette série][le premier didacticiel de cette série].

### Ajout du contrôleur MVC Message

1.  Dans le dossier *Controllers* du projet MVC, ajoutez le fichier *MessageController.cs* à partir du projet téléchargé.

La plupart du code de ce contrôleur est semblable à celui du contrôleur `Subscriber`. Ce qui est nouveau ici, c'est que le code fonctionne avec les objets blob. Pour chaque message, le contenu du message électronique en HTML et en texte brut est téléchargé sous la forme de fichiers .htm et .txt et stocké dans des objets blob.

Les objets blob sont stockés dans des conteneurs blob. L'application Azure Email Service stocke tous ces objets blob dans un seul conteneur d'objets blob nommé "azuremailblobcontainer", et le code du constructeur de contrôleur est doté d'une référence à ce conteneur d'objets blob:

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");

La vue MVC fournit à chaque fichier qu'un utilisateur sélectionne pour le téléchargement un objet `HttpPostedFile` contenant des informations sur ce fichier. Lorsque l'utilisateur crée un message, l'objet `HttpPostedFile` permet d'enregistrer le fichier dans un objet blob. Lorsque l'utilisateur modifie un message, il peut choisir de télécharger un fichier de remplacement ou ne pas modifier l'objet blob.

Le contrôleur inclut une méthode appelée par les méthodes `HttpPost Create` et `HttpPost Edit` pour enregistrer un objet blob:

        private async Task SaveBlobAsync(string blobName, HttpPostedFileBase httpPostedFile)
        {
            // Retrieve reference to a blob.
            var blob = blobContainer.GetBlockBlobReference(blobName);
            // Create the blob or overwrite the existing blob by uploading a local file.
            using (var fileStream = httpPostedFile.InputStream)
            {
                await blob.UploadFromStreamAsync(fileStream);
            }
        }

La méthode `HttpPost Create` enregistre les deux objets blob, puis ajoute la ligne de table `Message`. Les objets blob sont nommés en concaténant la valeur `MessageRef` avec l'extension du nom de fichier « .htm » ou « .txt ».

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
        {
            if (file == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide an HTML file path");
            }
        
            if (txtFile == null)
            {
                ModelState.AddModelError(string.Empty, "Please provide a Text file path");
            }
        
            if (ModelState.IsValid)
            {
                await SaveBlobAsync(message.MessageRef + ".htm", file);
                await SaveBlobAsync(message.MessageRef + ".txt", txtFile);
        
                var insertOperation = TableOperation.Insert(message);
                await messageTable.ExecuteAsync(insertOperation);
        
                return RedirectToAction("Index");
            }
        
            var lists = await GetListNamesAsync();
            ViewBag.ListName = new SelectList(lists, "ListName", "Description");
            return View(message);
        }

La méthode `HttpGet Edit` valide l'état `Pending` du message récupéré, pour que l'utilisateur ne modifie pas un message une fois que le rôle de travail B a commencé à le traiter. Du code similaire figure dans la méthode `HttpPost Edit` et les méthodes `Delete` et `DeleteConfirmed`.

        if (message.Status != "Pending")
        {
            throw new Exception("Message can't be edited because it isn't in Pending status.");
        }

Dans la méthode `HttpPost Edit`, le code enregistre uniquement un nouvel objet blob si l'utilisateur a choisi de télécharger un nouveau fichier.

        if (httpFile == null)
        {
            // They didn't enter a path or navigate to a file, so don't update the file.
            excludePropLst.Add("HtmlPath");
        }
        else
        {
            // They DID enter a path or navigate to a file, assume it's changed.
            await SaveBlobAsync(editedMsg.MessageRef + ".htm", httpFile);
        }
        
        if (txtFile == null)
        {
            excludePropLst.Add("TextPath");
        }
        else
        {
            await SaveBlobAsync(editedMsg.MessageRef + ".txt", txtFile);
        }

Si la date planifiée est modifiée, la clé de partition est modifiée et il faut supprimer une ligne puis en insérer une autre. Cela ne peut pas être effectué durant une transaction, car cela affecte plusieurs partitions.

        var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
        await messageTable.ExecuteAsync(deleteOperation);
        var insertOperation = TableOperation.Insert(editedMsg);
        await messageTable.ExecuteAsync(insertOperation);

La méthode `HttpPost Delete` supprime les objets blob lorsqu'elle supprime la ligne dans la table:

        [HttpPost, ActionName("Delete")]
        public async Task<ActionResult> DeleteConfirmed(String partitionKey, string rowKey)
        {
            // Get the row again to make sure it's still in Pending status.
            var message = await FindRowAsync(partitionKey, rowKey);
            if (message.Status != "Pending")
            {
                throw new Exception("Message can't be deleted because it isn't in Pending status.");
            }
        
            await DeleteBlobAsync(message.MessageRef + ".htm");
            await DeleteBlobAsync(message.MessageRef + ".txt");
            var deleteOperation = TableOperation.Delete(message);
            messageTable.Execute(deleteOperation);
            return RedirectToAction("Index");
        }
        
        private async Task DeleteBlobAsync(string blobName)
        {
            var blob = blobContainer.GetBlockBlobReference(blobName);
            await blob.DeleteAsync();
        }

### Ajout des vues MVC Message

1.  Dans le projet MVC, créez un dossier sous le dossier *Views* et nommez-le `Message`.

2.  Dans le nouveau dossier *Views\\Message*, ajoutez les cinq fichiers *.cshtml* depuis le projet téléchargé.

La méthode `HttpPost Edit` requiert une clé de partition et de ligne, pour que le code du fichier *Edit.cshtml* les fournisse dans les champs masqués.

        @using (Html.BeginForm("Edit", "Message", FormMethod.Post, new { enctype = "multipart/form-data" }))
        {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            @Html.HiddenFor(model => model.ETag)
            <fieldset>
                <legend>Message</legend>
                @Html.HiddenFor(model => model.MessageRef)
                @Html.HiddenFor(model => model.PartitionKey)
                @Html.HiddenFor(model => model.RowKey)
                

Les champs masqués n'étaient pas requis dans le contrôleur `Subscriber`, car (a) les propriétés`ListName` et `EmailAddress` du modèle `Subscriber` mettent à jour les propriétés `PartitionKey` et `RowKey`, et (b) les propriétés `ListName` et `EmailAddress` sont incluses dans les aides `EditorFor` de la vue Edit. Lorsque le classeur de modèles MVC du modèle `Subscriber` met à jour la propriété `ListName`, la propriété `PartitionKey` est mise à jour automatiquement, puis, lorsque le classeur de modèles MVC met à jour la propriété `EmailAddress` du modèle `Subscriber`, la propriété `RowKey` est automatiquement mise à jour. Dans le modèle `Message`, les champs mappant vers les clés de partition et de ligne ne sont pas modifiables. Vous ne pouvez donc pas les configurer ainsi.

La propriété `MessageRef` contient aussi un champ masqué. Il contient la même valeur que la clé de partition, il est ici pour clarifier le code dans la méthode `HttpPost Edit`. Le fait d'inclure le champ masqué `MessageRef` permet au code de la méthode `HttpPost Edit` de faire référence à la valeur `MessageRef` par son nom, lors de la construction de noms de fichier pour les objets blob.

La vue **Index** de Message du fichier *Index.cshtml* est différente des autres vues **Index**, car les liens **Edit** et **Delete** sont uniquement affichés pour les messages dont l'état est `Pending`:

        <td>
            @if (item.Status == "Pending")
            {
                @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
            }
            @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
        </td>
                

Ceci permet d'empêcher l'utilisateur de modifier un message lorsque le rôle de travail A commence à le traiter.

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5, ou en cliquant sur **Messages**.

    ![Page d'index Empty Message vide][Page d'index Empty Message vide]

2.  Utilisez la fonction **Create** pour ajouter des listes de diffusion, puis essayez les fonctions **Edit** et **Delete** pour vérifier qu'elles fonctionnent correctement.

    ![Page d'index Subscribers avec des lignes][2]

## <a name="unsubscribe"></a>Création et test du contrôleur et des vues Unsubscribe

Ensuite, vous allez implémenter l'interface utilisateur pour le processus de désabonnement.

Ce didacticiel comporte uniquement des instructions pour le développement du contrôleur pour le processus de désabonnement et non pas pour le processus d'abonnement. Comme l'explique [le premier didacticiel][le premier didacticiel de cette série], l'interface utilisateur et la méthode de service du processus d'abonnement sont incluses dans le téléchargement mais ne sont pas présentes dans les instructions du didacticiel, car l'exemple d'application n'implémente pas la sécurité dans la méthode de service. À des fins de test, vous pouvez utiliser les pages d'administration **Subscriber** pour inscrire des adresses électroniques aux listes.

### Ajout du modèle de vue de désabonnement au dossier Models

1.  Dans le dossier `Models` du projet MVC, ajoutez le fichier `UnsubscribeVM.cs` à partir du projet téléchargé.

Le modèle de vue `UnsubscribeVM` permet de transmettre des données entre le contrôleur `Unsubscribe` et ses vues.

        public class UnsubscribeVM
        {
            public string EmailAddress { get; set; }
            public string ListName { get; set; }
            public string ListDescription { get; set; }
            public string SubscriberGUID { get; set; }
            public bool? Confirmed { get; set; }
        }

Les liens de désabonnement contiennent la valeur `SubscriberGUID`. Cette valeur permet d'obtenir l'adresse électronique, ainsi que le nom et la description de la liste, à partir de la table `MailingList`. La vue affiche l'adresse électronique et la description de la liste de laquelle se désinscrire, ainsi qu'un bouton **Confirm** sur lequel l'utilisateur doit cliquer pour terminer le processus de désabonnement.

### Ajout du contrôleur Unsubscribe

1.  Dans le dossier `Controllers` du projet MVC, ajoutez le fichier *UnsubscribeController.cs* à partir du projet téléchargé.

Ce contrôleur a une méthode `HttpGet Index` qui affiche la page de désabonnement d'origine, ainsi qu'une méthode `HttpPost Index` qui traite le bouton **Confirm** ou **Cancel**.

La méthode `HttpGet Index` utilise le GUID et le nom de la liste de la chaîne de requête pour obtenir la ligne de table `MailingList` de l'abonné. Puis les informations requises par la vue sont placées dans le modèle de vue et la page **Unsubscribe** s'affiche. La propriété `Confirmed` est définie sur null pour ordonner à la vue d'afficher la version d'origine de la page **Désabonnement**.

        public async Task<ActionResult> Index(string id, string listName)
        {
            if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
            {
                ViewBag.errorMessage = "Empty subscriber ID or list name.";
                return View("Error");
            }
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            if (subscriber == null)
            {
                ViewBag.Message = "You are already unsubscribed";
                return View("Message");
            }
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = id;
            unsubscribeVM.Confirmed = null;
            return View(unsubscribeVM);
        }

Remarque: SubscriberGUID n'est pas dans la clé de partition ou de ligne: les performances de cette requête diminuent lorsque la taille de partition (le nombre d'adresses électronique dans une liste de diffusion) augmente. Pour plus d'informations sur les alternatives pour améliorer la mise à l'échelle de cette requête, consultez [le premier didacticiel de cette série][le premier didacticiel de cette série].

La méthode `HttpPost Index` utilise de nouveau le GUID et le nom de la liste pour obtenir les informations sur les abonnés et remplit les propriétés du modèle de vue. Ensuite, si vous cliquez sur le bouton **Confirm**, la ligne subscriber de la table `MailingList` est supprimée. Si vous cliquez sur le bouton **Confirm**, il définit également la propriété `Confirm` sur `true` ; sinon, il définit la propriété `Confirm` sur `false`. La valeur de la propriété `Confirm` entraîne l'affichage de la version « confirmée » ou « annulée » de la page **Désabonnement**.

        [HttpPost] 
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Index(string subscriberGUID, string listName, string action)
        {
            TableRequestOptions reqOptions = new TableRequestOptions()
            {
                MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
            };
            string filter = TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
            var query = new TableQuery<Subscriber>().Where(filter);
            TableContinuationToken token = null;
            OperationContext ctx = new OperationContext() { ClientRequestID = "" };
            TableQuerySegment<Subscriber> currentSegment = null;
            currentSegment = await mailingListTable.ExecuteQuerySegmentedAsync(query, token, reqOptions, ctx);
            var subscriber = currentSegment.Results.ToList().Single();
        
            var unsubscribeVM = new UnsubscribeVM();
            unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
            var mailingList = await FindRowAsync(subscriber.ListName, "mailinglist");
            unsubscribeVM.ListDescription = mailingList.Description;
            unsubscribeVM.SubscriberGUID = subscriberGUID;
            unsubscribeVM.Confirmed = false;
        
            if (action == "Confirm")
            {
                unsubscribeVM.Confirmed = true;
                var deleteOperation = TableOperation.Delete(subscriber);
                mailingListTable.Execute(deleteOperation);
            }
        
            return View(unsubscribeVM);
        }

### Création des vues MVC

1.  Dans le dossier *Views* du projet MVC, créez un dossier et nommez-le *Unsubscribe*.

2.  Dans le nouveau dossier *Views\\Unsubscribe*, ajoutez le fichier *Index.cshtml* depuis le projet téléchargé.

Dans le fichier *Index.cshtml*, la ligne `Layout = null` indique que vous ne devez pas utiliser le fichier \_Layout.cshtml pour afficher cette page. La page **Unsubscibe** contient une interface utilisateur simple, sans en-tête ni pied de page, contrairement aux pages d'administration.

        @model MvcWebRole.Models.UnsubscribeVM
        
        @{
            ViewBag.Title = "Unsubscribe";
            Layout = null;
        }

Dans le corps de la page, la propriété `Confirmed` détermine ce qui sera affiché dans cette page: les boutons **Confirm** et **Cancel** si la propriété est null, le message unsubscribe-confirmed si la propriété est true, le message unsubscribe-canceled si la propriété est false.

        @using (Html.BeginForm()) {
            @Html.AntiForgeryToken()
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>Unsubscribe from Mailing List</legend>
                @Html.HiddenFor(model => model.SubscriberGUID)
                @Html.HiddenFor(model => model.EmailAddress)
                @Html.HiddenFor(model => model.ListName)
                @if (Model.Confirmed == null) {
                    <p>
                        Do you want to unsubscribe  @Html.DisplayFor(model => model.EmailAddress) from:  @Html.DisplayFor(model => model.ListDescription)?
                   </p>
                    <br />
                    <p>
                        <input type="submit" value="Confirm" name="action"/> 
                        &nbsp; &nbsp;
                        <input type="submit" value="Cancel" name="action"/>
                    </p>
                }
                @if (Model.Confirmed == false) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  will NOT be unsubscribed from: @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
                @if (Model.Confirmed == true) {
                    <p>
                        @Html.DisplayFor(model => model.EmailAddress)  has been unsubscribed from:  @Html.DisplayFor(model => model.ListDescription).
                    </p>
                }
            </fieldset>
        }

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5, puis cliquez sur **Abonnés**.

2.  Cliquez sur **Créer** et créez un sous-dossier pour chaque liste de diffusion créée durant les tests précédents.

    Laissez la fenêtre du navigateur ouverte sur les pages **Abonnés** et **Index**.

3.  Ouvrez l'**Explorateur de serveurs**, puis sélectionnez le nœud **Azure / Storage / (Development)**.

4.  Développez **Tables**, cliquez avec le bouton droit sur la table **MailingList**, puis cliquez sur **Afficher la table**.

5.  Double-cliquez sur la ligne d'abonné que vous avez ajouté.

6.  Dans la boîte de dialogue **Modifier l'entité**, sélectionnez et copiez la valeur `SubscriberGUID`.

    ![Azure Storage Explorer][Azure Storage Explorer]

7.  Revenez à la fenêtre de votre navigateur. Dans la barre d'adresse du navigateur, remplacez « Subscriber » dans l'URL par « unsubscribe?ID=[guidvalue]&listName=[listname] » où [guidvalue] est le GUID que vous avez copié à partir de l'explorateur de stockage Azure, et [listname] le nom de la liste de diffusion. Par exemple:

        http://127.0.0.1/unsubscribe?ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    La version de la page **Unsubscribe** demandant à l'utilisateur de confirmer son désabonnement est affichée:

    ![Unsubscribe page][Unsubscribe page]

8.  Cliquez sur **Confirm**: la confirmation du désabonnement de cette adresse électronique est affichée.

    ![Unsubscribe confirmed page][Unsubscribe confirmed page]

9.  Revenez à la page **Subscribers** **Index** pour vérifier que la ligne subscriber n'est plus là.

## <a name="nextsteps"></a>Étapes suivantes

Comme expliqué dans [le premier didacticiel de cette série][le premier didacticiel de cette série], ce didacticiel ne traite pas des composants du processus d'inscription, car la méthode du service de l'API web ASP.NET n'implémente pas de sécurité de secret partagé. Cependant, la restriction d'IP que vous avez configurée dans le second didacticiel protège également la méthode de service et vous pouvez ajouter la fonctionnalité d'abonnement en copiant les fichiers suivants à partir du projet téléchargé.

Pour la méthode de service de l'API Web ASP.NET:

-   Controllers\\SubscribeAPI.cs

Pour la page web affichée lorsqu'un abonné clique sur le lien **Confirm** dans le message électronique généré par la méthode de service:

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

Dans le [prochain didacticiel][prochain didacticiel] vous allez configurer et programmer un rôle de travail A, qui planifie l'envoi des courriers électroniques.

Pour obtenir des liens sur les ressources supplémentaires pour utiliser les tables, les files d'attente et les objets blob Azure Storage, consultez le [dernier didacticiel de cette série][dernier didacticiel de cette série].

<div><a href="/fr-fr/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/" class="site-arrowboxcta download-cta">Didacticiel 4</a></div>

  [le premier didacticiel de cette série]: /fr-fr/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Création de la solution Visual Studio]: #cloudproject
  [Mise à jour du package NuGet de la bibliothèque cliente de stockage]: #updatescl
  [Configuration des projets pour utiliser l'émulateur de stockage]: #configurestorage
  [Configuration du suivi et gestion des redémarrages]: #tracing
  [Ajout de code pour créer des tables, une file d'attente et un conteneur d'objets blob dans la méthode Application\_Start]: #createifnotexists
  [Création et test de la liste de diffusion]: #mailinglist
  [Création et test du contrôleur et des vues Subscriber]: #subscriber
  [Création et test du contrôleur et des vues Message]: #message
  [Création et test du contrôleur et des vues Unsubscribe]: #unsubscribe
  [Étapes suivantes]: #nextsteps
  [Menu New Project]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png
  [New Project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png
  [New Azure Cloud Project dialog box]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png
  [New Azure Cloud Project dialog box - renaming the web role]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png
  [Boîte de dialogue New Azure Cloud Project: ajouter un rôle de travail]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png
  [No authentication]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/noauth.png
  [solution complète]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [page d'accueil]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png
  [Émulateur de calcul dans la barre d'état système]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png
  [Manage NuGet Packages for Solution dans le menu]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png
  [Package Azure Storage dans la boîte de dialogue Manage NuGet Packages]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png
  [Sélectionner les deux projets dans la boîte de dialogue Select Projects]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png
  [Propriétés de rôle Web]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png
  [Clic droit, Propriétés]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png
  [Erreur Configuration de service et fichiers de définition incorrects]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png
  [second didacticiel]: /fr-fr/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Redémarrages d'instances de rôle pour cause de mise à jour du système d'exploitation]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [transfert à la demande]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433075.aspx
  [dbgview]: http://technet.microsoft.com/fr-fr/sysinternals/bb896647.aspx
  [TableEntity]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx
  [DynamicTableEntity]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx
  [Présentation approfondie des tables de bibliothèque du client de stockage Azure 2.0]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx
  [Présentation du modèle de données du service de table]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179338.aspx
  [Caractère % dans PartitionKey ou RowKey]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx
  [Utilisation de la prise en charge asynchrone .NET 4.5 pour éviter le blocage des appels]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async
  [TableOperation.Retrieve]: http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx
  [Comment tirer le meilleur parti des tables Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx
  [Tables Azure: utilisation des jetons de liaison]: http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously
  [Journalisation d'Azure Storage: utilisation de journaux pour suivre les requêtes de stockage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx
  [PhluffyFotos]: http://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31
  [Page d'index MailingList vide]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png
  [Page d'index MailingList avec des lignes]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png
  [Page d'index Subscriber vide]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png
  [Page d'index Subscribers avec des lignes]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png
  [Page d'index Empty Message vide]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png
  [Azure Storage Explorer]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png
  [Unsubscribe page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png
  [Unsubscribe confirmed page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png
  [prochain didacticiel]: /fr-fr/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
  [dernier didacticiel de cette série]: /fr-fr/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
