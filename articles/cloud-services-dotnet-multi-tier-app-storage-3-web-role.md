<properties linkid="develop-net-tutorials-multi-tier-web-site-3-web-role" urlDisplayName="Step 3: Web Role" pageTitle="ASP.NET Multi-tier Web Application with Azure - Step 3: Web role" metaKeywords="Azure tutorial, Email Service application, ASP.NET MVC web role, MVC controllers, Web API controller, Cloud Service project" description="The third tutorial in a series that teaches how to create and deploy the Email Service app in an Azure cloud service." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Building the web role for the Azure Email Service application - 3 of 5." authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

Développement du rôle Web pour l'application Azure Email Service : 3 sur 5.
===========================================================================

Voici le troisième didacticiel de notre série sur le développement et le déploiement de l'exemple d'application Azure Email Service. Pour plus d'informations sur l'application et notre série de didacticiels, consultez [le premier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   Création d'une solution contenant un projet de service cloud avec un rôle Web et un rôle de travail.
-   Utilisation des tables Azure, des objets blob et des files d'attentes dans les contrôleurs et les vues MVC 4
-   Gestion des conflits d'accès concurrentiels lors de l'utilisation des tables Azure
-   Configuration d'un rôle ou d'un projet Web pour utiliser votre compte de stockage Azure

Création de solutionCréation d'une solution Visual Studio
---------------------------------------------------------

Commencez par créer une solution Visual Studio avec un projet pour votre application Web frontale et un projet pour l'un des rôles de travail Azure principaux. Vous ajouterez le second rôle de travail ultérieurement.

Si vous voulez exécuter l'interface utilisateur Web dans un site Web Azure au lieu d'un service cloud Azure, consultez la section [Autre architecture](#alternativearchitecture) plus tard dans ce didacticiel lire une autre version de ces instructions.

### Création d'un projet de service cloud avec un rôle Web et un rôle de travail

1.  Démarrez Visual Studio avec des privilèges élevés.

    > [WACOM.NOTE] Pour Visual Studio 2013, vous n'avez pas besoin d'utiliser des privilèges élevés, car les nouveaux projets utilisent l'émulateur express par défaut.

2.  Dans le menu **File**, sélectionnez **New Project**.

    ![Menu New Project](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-file-new-project.png)

3.  Développez **C\#** puis sélectionnez **Cloud** sous **Installed Templates**, puis sélectionnez **Azure Cloud Service**.

4.  Nommez l'application **AzureEmailService** puis cliquez sur **OK**.

    ![Boîte de dialogue Nouveau projet](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-project.png)

5.  Dans la boîte de dialogue **New Azure Cloud Service**, sélectionnez **ASP.NET Web Role** puis cliquez sur la flèche pointant vers la droite.

    > [WACOM.NOTE] Le code téléchargé que vous utilisez pour ce didacticiel est MVC 4, mais vous ne pouvez pas créer un rôle Web MVC 4 dans Visual Studio 2013 en utilisant les instructions écrites pour Visual Studio 2012. Pour Visual Studio 2013, procédez ainsi : (1) Ignorez les étapes pour la création du rôle Web et suivez celles pour le rôle de travail. (2) Une fois le rôle de travail créé, cliquez avec le bouton droit sur la solution dans l'**Explorateur de solutions**, puis cliquez sur **Add** -- **New Project**. Dans le volet gauche de la boîte de dialogue **Add New Project**, développez **Web** puis sélectionnez **Visual Studio 2012**. (3) Sélectionnez **ASP.NET MVC 4 Web Application**, nommez le projet **MvcWebRole**, puis cliquez sur **OK**. (4) Dans la boîte de dialogue **New ASP.NET Project**, sélectionnez le modèle **Internet Application**. (5) Cliquez avec le bouton droit sur **Roles** sous **AzureEmailService** dans l'**Explorateur de solutions**, puis cliquez sur **Add** - **Web Role Project in Solution**. (6) Dans la boîte de dialogue **Associate with Role Project**, sélectionnez le projet **MvcWebRole**, puis cliquez sur **OK**.

    ![Boîte de dialogue New Azure Cloud Project](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog.png)

6.  Dans la colonne de droite, passez le pointeur de la souris sur **MvcWebRole1**, puis cliquez sur l'icône en forme de crayon et renommez le rôle Web.

7.  Nommez-le MvcWebRole , puis appuyez sur Entrée.

    ![Boîte de dialogue New Azure Cloud Project : renommer le rôle Web](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-dialog-rename.png)

8.  Suivez la même procédure pour ajouter un **Worker Role**, nommez-le WorkerRoleA, puis cliquez sur **OK**.

    ![Boîte de dialogue New Azure Cloud Project : ajouter un rôle de travail](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-cloud-service-add-worker-a.png)

9.  Dans la boîte de dialogue **New ASP.NET Project**, sélectionnez le modèle **Internet Application**.

10. Dans la liste déroulante **View Engine**, vérifiez que **Razor** est sélectionné, puis cliquez sur **OK**.

    ![Boîte de dialogue Nouveau projet](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-new-mvc4-project.png)

### Définition de l'en-tête, du menu et du pied de page

Dans cette section, vous allez mettre à jour les en-têtes, pieds de page et éléments de menu affichés sur chaque page de l'interface Web d'administration. L'application aura trois ensembles de pages Web d'administration : une pour les listes de diffusion, une pour les abonnés aux listes de diffusion et une pour les messages.

1.  Dans l'**Explorateur de solutions**, développez le dossier Views\\Shared et ouvrez le fichier \_Layout.cshtml.

    ![\_Layout.cshtml dans l'Explorateur de solutions](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-opening-layout-cshtml.png)

2.  Dans l'élément **&lt;title\>**, remplacez "My ASP.NET MVC Application" par "Azure Email Service".

3.  Dans l'élément **&lt;p\>** avec la classe "site-title", remplacez "your logo here" par "Azure Email Service", puis "Home" par "MailingList".

    ![titre et en-tête dans \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-title-and-logo-in-layout.png)

4.  Supprimez la section du menu :

    ![menu dans \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-menu-in-layout.png)

5.  Insérez une nouvelle section de menu à la place de l'ancienne :

         <ul id="menu">
             <li>@Html.ActionLink("Mailing Lists", "Index", "MailingList")</li>
             <li>@Html.ActionLink("Messages", "Index", "Message")</li>
             <li>@Html.ActionLink("Subscribers", "Index", "Subscriber")</li>
         </ul>

6.  Dans l'élément **&lt;footer\>**, remplacez "My ASP.NET MVC Application" par "Azure Email Service".

    ![pied de page dans \_Layout.cshtml](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-footer-in-layout.png)

### Exécution locale de l'application

1.  Appuyez sur Ctrl+F5 pour exécuter l'application.

    La page d’accueil de l’application apparaît dans le navigateur par défaut.

    ![page d'accueil](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-home-page-before-adding-controllers.png)

    L'application s'exécute dans l'émulateur de calcul Azure. Vous pouvez afficher l'icône d'émulateur de calcul dans la barre d'état système Windows :

    ![Émulateur de calcul dans la barre d'état système](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-compute-emulator-icon.png)

Configuration du suiviConfiguration du suivi
--------------------------------------------

Pour activer le suivi des données à enregistrer, ouvrez le fichier *WebRole.cs* et ajoutez la méthode `ConfigureDiagnostics` suivante. Ajoutez le code appelant la nouvelle méthode dans la méthode `OnStart`.

> [WACOM.NOTE] Pour Visual Studio 2013, au lieu de suivre la procédure suivante pour modifier manuellement le code dans *WebRole.cs*, cliquez avec le bouton droit dans le projet MvcWebRole, cliquez sur **Ajouter un élément existant**, puis ajoutez le fichier *WebRole.cs* à partir du projet téléchargé.

    private void ConfigureDiagnostics()
    {
        DiagnosticMonitorConfiguration config = DiagnosticMonitor.GetDefaultInitialConfiguration();
        config.Logs.BufferQuotaInMB = 500;
        config.Logs.ScheduledTransferLogLevelFilter = LogLevel.Verbose;
        config.Logs.ScheduledTransferPeriod = TimeSpan.FromMinutes(1d);

        DiagnosticMonitor.Start(
            "Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString",
            config);
    }

    public override bool OnStart()
    {
        ConfigureDiagnostics();
        return base.OnStart();
    }

La méthode `ConfigureDiagnostics` est détaillée dans [le deuxième didacticiel](/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/).

RedémarrageAjout de code pour gérer les redémarrages de façon efficace
----------------------------------------------------------------------

Les applications Azure Cloud Service sont redémarrées environ deux fois par mois pour des mises à jour du système d'exploitation (pour plus d'informations sur ces mises à jour, consultez le billet [Redémarrages d'instances de rôle pour cause de mise à jour du système d'exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)). Lorsqu'une application Web va être arrêtée, un événement `OnStop` est généré. Le rôle Web réutilisable créé par Visual Studio ne remplace pas la méthode `OnStop`, l'application aura uniquement quelques secondes pour terminer le traitement des requêtes HTTP avant son arrêt. Vous pouvez ajouter du code pour remplacer la méthode `OnStop` pour assurer une gestion appropriée des arrêts.

Pour gérer les arrêts et les redémarrages, ouvrez le fichier *WebRole.cs* et ajoutez le remplacement de méthode `OnStop` suivant.

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

Ce code requiert une instruction `using` supplémentaire :

      using System.Diagnostics;

La méthode `OnStop` a jusqu'à 5 minutes pour se terminer avant l'arrêt de l'application. Pour pouvez ajouter un appel de veille pour 5 minutes à la méthode `OnStop` afin de laisser à votre application la durée maximum pour traiter les demandes en cours, mais si l'application est correctement mise à l'échelle, elle doit pouvoir traiter les demandes restantes en moins de 5 minutes. Nous vous conseillons de procéder à l'arrêt dès que possible, pour que l'application puisse redémarrer aussi rapidement que possible et continuer le traitement des demandes.

Lorsqu'un rôle est placé en hors connexion par Azure, le programme d'équilibrage de la charge arrête l'envoi des demandes vers l'instance de rôle, après quoi la méthode `OnStop` est appelée. Si vous n'avez pas d'autre instance pour votre rôle, aucune demande ne sera traitée jusqu'au redémarrage de votre rôle (ce qui peut prendre quelques minutes). C'est pourquoi le contrat de niveau de service Azure nécessite que vous ayez au moins deux instances de chaque rôle pour profiter de la garantie de disponibilité.

Dans le code affiché pour la méthode `OnStop`, un compteur de performances ASP.NET est créé pour `Requests Current`. La valeur du compteur `Requests Current` contient le nombre actuel de demandes, notamment celles en file d'attente, en cours d'exécution ou en attente d'écriture vers le client. La valeur `Requests Current` est vérifiée chaque seconde : lorsqu'elle atteint zéro, la méthode `OnStop` est renvoyée. Une fois la méthode `OnStop` renvoyée, le rôle s'arrête.

Les données de suivi ne sont pas enregistrées lorsqu'elles sont appelées depuis la méthode `OnStop` sans effectuer de [transfert à la demande](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433075.aspx). Vous pouvez afficher les informations de suivi `OnStop` en temps réel avec l'utilitaire [dbgview](http://technet.microsoft.com/en-us/sysinternals/bb896647.aspx) à partir d'une connexion Bureau à distance.

Mise à jour de la bibliothèque du client de stockageMise à jour du package NuGet de la bibliothèque du client de stockage
-------------------------------------------------------------------------------------------------------------------------

> [WACOM.NOTE] Cette étape peut être facultative. Si le package NuGet de Azure Storage n'est pas affiché dans la liste Mises à jour, la version installée est à jour.

L'infrastructure d'API utilisée avec les tables, les files d'attente et les objets blob Azure Storage est la bibliothèque du client de stockage (SCL, Storage Client Library). Cette API est incluse dans un package NuGet dans le modèle de projet de service cloud. Cependant, au moment de la rédaction de ce didacticiel, les modèles de projet incluent la version 1.7 de SCL et non la version 2.0. C'est pourquoi, avant de rédiger le code, vous devez mettre à jour le package NuGet.

1.  Dans le menu **Outils** de Visual Studio, passez la souris sur **Library Package Manager**, puis cliquez sur **Manage NuGet Packages for Solution**.

    ![Manage NuGet Packages for Solution dans le menu](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-manage-nuget-for-solution.png)

2.  Dans le volet gauche de la boîte de dialogue **Manage NuGet Packages**, sélectionnez **Mises à jour**, puis faites défiler jusqu'au package **Azure Storage** et cliquez sur **Mettre à jour**.

    ![Package Azure Storage dans la boîte de dialogue Manage NuGet Packages](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-update-storage-nuget-pkg.png)

3.  Dans la boîte de dialogue **Select Projects**, vérifiez que les deux projets sont sélectionnés, puis cliquez sur **OK**.

    ![Sélectionner les deux projets dans la boîte de dialogue Select Projects](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-nuget-select-projects.png)

4.  Acceptez les termes du contrat de licence pour terminer l'installation du package, puis fermez la boîte de dialogue **Manage NuGet Packages**.

5.  Dans le projet WorkerRoleA dans *WorkerRole.cs*, si l'instruction `using` est présente, supprimez-la, car elle n'est plus nécessaire :

         using Microsoft.WindowsAzure.StorageClient;

La version 1.7 du SCL inclut un fournisseur LINQ qui simplifie le codage pour les requêtes de tables. ?Au moment de la rédaction de ce didacticiel, le TSL 2.0 n'a pas encore de fournisseur LINQ. Si vous voulez utiliser LINQ, vous devez avoir accès au fournisseur LINQ SCL 1.7 dans l'espace de noms [Microsoft.WindowsAzure.Storage.Table.DataServices](http://msdn.microsoft.com/fr-fr/library/microsoft.windowsazure.storage.table.dataservices.aspx). Le TSL 2.0 est conçu pour améliorer les performances, et le fournisseur LINQ 1.7 ne bénéficie pas de toutes ces améliorations. L'exemple d'application utilise le TSL 2.0, il n'utilise donc pas LINQ pour les requêtes. Pour plus d'informations sur SCL et TSL 2.0, consultez les ressources à la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

> [WACOM.NOTE] SCL 2.1 permet la prise en charge LINQ, mais ce didacticiel n'utilise pas LINQ pour les requêtes de table de stockage. Le SCL actuel prend également en charge la programmation asynchrone, mais ce didacticiel ne présente pas le code async. Pour plus d'informations sur la programmation asynchrone et pour obtenir un exemple de code qui l'utilise avec le SCL Azure, consultez le chapitre du livre électronique suivant et le projet téléchargeable qui l'accompagne : [Utilisation de la prise en charge asynchrone .NET 4.5 pour éviter le blocage des appels](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

Ajout d'une référence SCL 1.7Ajout d'une référence à un assembly SCL 1.7
------------------------------------------------------------------------

> [WACOM.NOTE] Vous pouvez ignorer cette section si vous utilisez SCL 2.1 ou une version ultérieure (Visual Studio 2013 avec le dernier Kit de développement logiciel (SDK)).

La version 2.0 de la bibliothèque du client de stockage (SCL) 2.0 n'est pas dotée de tous les éléments nécessaires pour établir des diagnostics. Vous devez donc ajouter une référence pointant vers un assembly 1.7.

1.  Cliquez avec le bouton droit sur le projet MvcWebRole, puis sélectionnez **Ajouter une référence**.

2.  Cliquez sur le bouton **Parcourir...** en bas de la boîte de dialogue.

3.  Accédez au dossier suivant :

         C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK012-10\ref

4.  Sélectionnez *Microsoft.WindowsAzure.StorageClient.dll*, puis cliquez sur **Ajouter**.

5.  Dans la boîte de dialogue **Gestionnaire de références**, cliquez sur **OK**.

6.  Répétez le processus pour le projet WorkerRoleA.

App\_Start CodeAjout de code pour créer des tables, une file d'attente et un conteneur d'objets blob dans la méthode Application\_Start
---------------------------------------------------------------------------------------------------------------------------------------

L'application Web utilisera les tables `MailingList` et `Message`, la file d'attente `azuremailsubscribequeue`, ainsi que le conteneur d'objets blob `azuremailblobcontainer`. Vous pouvez créer ces éléments manuellement en utilisant un outil tel que l'Explorateur de stockage Azure, mais cela vous obligerait à répéter cette opération à chaque démarrage de l'application avec un nouveau compte de stockage. Dans cette section, vous allez ajouter un code qui s'exécute au démarrage de l'application, contrôle la présence des tables, files d'attente et conteneurs d'objets blob, et crée les éléments manquants.

Vous pouvez ajouter ce code de démarrage unique à la méthode `OnStart` dans le fichier *WebRole.cs* ou dans le fichier *Global.asax*. Pour ce didacticiel, vous allez initialiser Azure Storage dans le fichier *Global.asax* car il fonctionne avec les sites Web Azure ainsi qu'avec les rôles Web de Azure Cloud Services.

1.  Dans l'**Explorateur de solutions**, développez *Global.asax*, puis ouvrez *Global.asax.cs*.

2.  Ajoutez une nouvelle méthode `CreateTablesQueuesBlobContainers` après la méthode `Application_Start`, puis appelez la nouvelle méthode à partir de la méthode `Application_Start`, comme indiqué dans l'exemple suivant :

         protected void Application_Start()
         {
             AreaRegistration.RegisterAllAreas();
             WebApiConfig.Register(GlobalConfiguration.Configuration);
             FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
             RouteConfig.RegisterRoutes(RouteTable.Routes);
             BundleConfig.RegisterBundles(BundleTable.Bundles);
             AuthConfig.RegisterAuth();
             // Vérifie que toutes les tables, files d'attente et conteneurs d'objets blob utilisés dans cette application
             // existent, puis crée les éléments manquants.
             CreateTablesQueuesBlobContainers();
         }

         private static void CreateTablesQueuesBlobContainers()
         {
             var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
             // Si ce code est exécuté dans un site Web Azure (et non pas dans un service cloud), utilisez le fichier Web.config :
             //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);
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

3.  Cliquez avec le bouton droit sur la ligne sinueuse bleue sous `RoleEnvironment`, sélectionnez **Resolve**, puis **using Microsoft.WindowsAzure.ServiceRuntime**.

    ![rightClick](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-4.png)

4.  Cliquez avec le bouton droit sur la ligne sinueuse bleue sous `CloudStorageAccount`, sélectionnez **Resolve**, puis **using Microsoft.WindowsAzure.Storage**.

5.  Vous pouvez également ajouter manuellement les instructions using suivantes :

         using Microsoft.WindowsAzure.ServiceRuntime;
         using Microsoft.WindowsAzure.Storage;

6.  Développez l'application, qui enregistre le fichier et vérifie qu'il n'y a pas d'erreur de compilation.

Dans les sections suivantes, vous allez développer les composants de l'application Web, puis vous pourrez les tester avec le stockage de développement ou votre compte de stockage sans avoir à créer d'abord des tables, des files d'attente ou un conteneur d'objets blob manuellement.

Liste de diffusionCréation et test du contrôleur et des vues de la liste de diffusion
-------------------------------------------------------------------------------------

L'interface utilisateur Web **Liste de diffusion** permet aux administrateurs de créer, modifier et afficher les listes de diffusion, telles que « Annonces du département d'histoire de l'université Contoso » et « Offres d'emploi Fabrikam Engineering ».

### Ajout de la classe d'entité MailingList au dossier Models

La classe d'entité `MailingList` est utilisée pour les lignes de la table `MailingList` contenant des informations sur la liste, comme ses descriptions et l'adresse électronique « De » pour les courriers électroniques envoyés à la liste.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier `Models` du projet MVC, puis sélectionnez **Ajouter un élément existant**.

    ![Ajouter un élément existant au dossier Models](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-models.png)

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *MailingList.cs* dans le dossier `Models`, puis cliquez sur **Ajouter**.

3.  Ouvrez *MailingList.cs* puis examinez le code.

         public class MailingList : TableEntity
         {
             public MailingList()
             {
                 this.RowKey = "mailinglist";
             }

             [Required]
             [RegularExpression(@"[\w]+",
              ErrorMessage = @"Seuls les caractères alphanumériques et les traits de soulignement(_) sont autorisés.")]
             [Display(Name = "Nom de la liste")]
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
             [Display(Name = "Adresse électronique 'De'")]
             public string FromEmailAddress { get; set; }

             public string Description { get; set; }
         }

    Pour les opérations de table de l'API TSL 2.0 Azure Storage, vous devez utiliser des classes d'entité dérivées de [TableEntity](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storage.table.tableentity.aspx). Cette classe définit les champs `PartitionKey`, `RowKey`, `TimeStamp`, et `ETag`. Les propriétés `TimeStamp` et `ETag` sont utilisées par le système. L'utilisation de la propriété `ETag` pour la gestion des conflits d'accès concurrentiels est abordée plus tard dans ce didacticiel.

    Il existe également une classe [DynamicTableEntity](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storage.table.dynamictableentity.aspx), que vous pouvez utiliser lorsque vous voulez utiliser les lignes de table en tant que collections Dictionnaire de paires clé-valeur au lieu d'utiliser des classes de modèle prédéfinies. Pour plus d'informations, consultez le billet [Présentation approfondie des tables de bibliothèque du client de stockage Azure 2.0](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/06/windows-azure-storage-client-library-2-0-tables-deep-dive.aspx).

    La clé de partition de table `mailinglist` est le nom de la liste. Dans cette classe d'entité, vous pouvez accéder à la valeur de clé de partition en utilisant la propriété `PartitionKey` (définie dans la classe `TableEntity`) ou la propriété `ListName` (définie dans la classe `MailingList`). La propriété `ListName` utilise `PartitionKey` en tant que variable de sauvegarde. Le fait de définir la propriété `ListName` vous permet d'utiliser un nom de variable plus explicite dans le code et facilite la programmation de l'interface utilisateur Web, car les attributs de mise en forme et de validation DataAnnotations peuvent être ajoutés à la propriété `ListName`, mais ils ne peuvent pas être ajoutés directement dans la propriété `PartitionKey`.

    L'attribut `RegularExpression` de la propriété `ListName` oblige MVC à valider les entrées utilisateur pour vérifier que la valeur du nom de la liste entrée contient uniquement des caractères alphanumérique ou des traits de soulignement. Cette restriction a été implémentée pour que les noms de liste restent simples et faciles à utiliser dans les chaînes de requête des URL.

    **Remarque :** si vous voulez que la mise en forme des noms de liste soit moins restrictive, vous pouvez autoriser d'autres caractères et noms de liste URL-encode lorsqu'ils sont utilisés dans des chaînes de requête. Cependant, certains caractères sont interdits dans les clés de partition ou de lignes de tables Azure, et vous devrez exclure au moins ces caractères. Pour plus d'informations sur les caractères interdits ou posant problème dans les champs de clé de partition ou de ligne, consultez l'article [Présentation du modèle de données du service de table](http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179338.aspx) et le billet [Caractère % dans PartitionKey ou RowKey](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/05/28/partitionkey-or-rowkey-containing-the-percent-character-causes-some-windows-azure-tables-apis-to-fail.aspx).

    La classe `MailingList` définit un constructeur par défaut qui définit `RowKey` sur la chaîne « mailinglist » codée en dur, car toutes les lignes MailingList de cette table contiennent cette valeur en tant que clé de ligne. Pour plus d'informations sur la structure de table, consultez le [premier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/). Vous pouvez choisir n'importe quelle valeur constante dans ce cas, tant qu'elle reste toujours différente d'une valeur d'adresse électronique, qui est la clé de ligne des lignes subscriber de cette table.

    Le nom de la liste et l'adresse électronique « De » doivent toujours être entrées lors de la création d'une nouvelle entité `MailingList`, pour qu'ils possèdent les attributs `Required`.

    Les attributs `Display` indiquent la légende par défaut à utiliser pour un champ dans l'interface utilisateur MVC.

### Ajout au contrôleur MVC MailingList

1.  Dans l'**Explorateur de solution**, cliquez avec le bouton droit sur le dossier Controllers du projet MVC, puis sélectionnez **Ajouter un élément existant**.

    ![Ajouter un élément existant au dossier Controllers](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-controllers.png)

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *MailingListController.cs* dans le dossier `Controllers`, puis cliquez sur **Ajouter**.

3.  Ouvrez *MailingListController.cs* et examinez le code.

    Le constructeur par défaut crée un objet `CloudTable` à utiliser avec la table `mailinglist`.

         public class MailingListController : Controller
         {
             private CloudTable mailingListTable;

             public MailingListController()
             {
                 var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 // Si ce code est exécuté dans un site Web Azure (et non pas dans un service cloud), utilisez le fichier Web.config :
                 //    var storageAccount = Microsoft.WindowsAzure.Storage.CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 var tableClient = storageAccount.CreateCloudTableClient();
                 mailingListTable = tableClient.GetTableReference("mailinglist");
             }

    Le code permet d'obtenir les informations d'identification de votre compte Azure Storage à partir du fichier de paramètres du projet de service cloud pour établir une connexion avec le compte de stockage. La configuration de ces paramètres est abordée plus tard dans ce didacticiel, avant le test du contrôleur. Si vous voulez exécuter le projet MVC dans un site Web Azure, vous pouvez obtenir les chaînes de connexion à partir du fichier Web.config.

    La méthode suivante, `FindRow`, est appelée lorsque le contrôleur doit rechercher une entrée spécifique de la liste de diffusion dans la table `MailingList` (par exemple, lorsque vous voulez modifier une entrée de cette liste). Le code récupère une seule entité `MailingList` en utilisant la clé de partition et les valeurs de clé de ligne qui lui sont transmises. Les lignes modifiées par ce contrôleur sont celles dont la clé de ligne est « MailingList ». Vous auriez donc pu coder « MailingList » en dur, mais le fait de spécifier une clé de partition et une clé de ligne est un modèle utilisé pour les méthodes `FindRow` dans tous les contrôleurs.

         private MailingList FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<MailingList>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var mailingList = retrievedResult.Result as MailingList;
             if (mailingList == null)
             {
                 throw new Exception("Aucune liste de diffusion trouvée pour : " + partitionKey);
             }

             return mailingList;
         }

    Il est intéressant de comparer la méthode `FindRow` du contrôleur `MailingList`, qui renvoie une ligne MailingList, à la méthode `FindRow` du contrôleur `Subscriber`, qui renvoie une ligne subscriber de la même table `mailinglist`.

         private Subscriber FindRow(string partitionKey, string rowKey)
         {
             var retrieveOperation = TableOperation.Retrieve<Subscriber>(partitionKey, rowKey);
             var retrievedResult = mailingListTable.Execute(retrieveOperation);
             var subscriber = retrievedResult.Result as Subscriber;
             if (subscriber == null)
             {
                 throw new Exception("Aucun abonné trouvé pour : " + partitionKey + ", " + rowKey);
             }
             return subscriber;
         }

    La seule différence entre ces deux requêtes est le type de modèle transmis à la méthode [TableOperation.Retrieve](http://msdn.microsoft.com/fr-fr/library/windowsazure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx). Le type de modèle indique le schéma (les propriétés) d'une ou plusieurs lignes que la requête doit renvoyer. Une seule table peut avoir différents schémas dans différentes lignes. Généralement, le type de modèle utilisé pour la lecture d'une ligne est le même que celui utilisé pour sa création.

    La page **Index** affiche toutes les lignes MailingList. La requête de la méthode `Index` renvoie donc toutes les entités `MailingList` dont la clé de ligne est « mailinglist » (les autres lignes de la table ont une adresse de messagerie comme clé de ligne, et contiennent des informations sur les abonnés).

                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();

    La méthode `Index` entoure cette requête avec du code conçu pour gérer les conditions de délai d'expiration.

         public ActionResult Index()
         {
             TableRequestOptions reqOptions = new TableRequestOptions()
             {
                 MaximumExecutionTime = TimeSpan.FromSeconds(1.5),
                 RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3)
             };
             List<MailingList> lists;
             try
             {
                 var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist"));
                 lists = mailingListTable.ExecuteQuery(query, reqOptions).ToList();
             }
             catch (StorageException se)
             {
                 ViewBag.errorMessage = "Erreur de délai d'expiration, veuillez réessayer. ";
                 Trace.TraceError(se.Message);
                 return View("Error");
             }

             return View(lists);
         }

    Si vous n'indiquez pas de paramètres de délai d'expiration, l'API effectue trois tentatives en augmentant progressivement les limites du délai de déconnexion. Dans le cas d'une interface Web où un utilisateur attend l'affichage d'une page, ce délai serait trop long. C'est pourquoi ce code indique des nouvelles tentatives linéaires (pour que la limite de délai n'augmente pas à chaque fois) ainsi qu'une limite de délai raisonnable pour l'utilisateur.

    Lorsque l'utilisateur clique sur le bouton **Créer** de la page **Créer**, le module de liaison de modèle MVC crée une entité `MailingList` à partir des entrées saisies dans la vue, puis la méthode `HttpPost Create` ajoute l'entité à la table.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(MailingList mailingList)
         {
             if (ModelState.IsValid)
             {
                 var insertOperation = TableOperation.Insert(mailingList);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             return View(mailingList);
         }

Pour la page **Modifier**, la méthode `HttpGet Edit` effectue une recherche dans la ligne, puis la méthode `HttpPost` met à jour la ligne.

     [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit(string partitionKey, string rowKey, MailingList editedMailingList)
        {
            if (ModelState.IsValid)
            {
                var mailingList = new MailingList();
                UpdateModel(mailingList);
                try
                {
                    var replaceOperation = TableOperation.Replace(mailingList);
                    mailingListTable.Execute(replaceOperation);
                    return RedirectToAction("Index");
                }
                catch (StorageException ex)
                {
                    if (ex.RequestInformation.HttpStatusCode == 412)
                    {
                        // Concurrency error
                        var currentMailingList = FindRow(partitionKey, rowKey);
                        if (currentMailingList.FromEmailAddress != editedMailingList.FromEmailAddress)
                        {
                            ModelState.AddModelError("FromEmailAddress", "Current value: " + currentMailingList.FromEmailAddress);
                        }
                        if (currentMailingList.Description != editedMailingList.Description)
                        {
                            ModelState.AddModelError("Description", "Current value: " + currentMailingList.Description);
                        }
                        ModelState.AddModelError(string.Empty, "L'enregistrement que vous essayez de modifier "
                            + "a été modifié par un autre utilisateur après que vous ayez obtenu la valeur d'origine. L'"
                            + "opération de modification a été annulée et les valeurs actuelles de la base de données "
                            + "ont été affichées. Si vous voulez toujours modifier cet enregistrement, cliquez de nouveau sur "
                            + "le bouton Enregistrer. Sinon, cliquez sur le lien hypertexte Revenir à la liste.");
                         ModelState.SetModelValue("ETag", new ValueProviderResult(currentMailingList.ETag, currentMailingList.ETag, null));
                    }
                    else
                    {
                        throw;
                    }
                }
            }
            return View(editedMailingList);
        }

    Le bloc try-catch gère les erreurs d'accès concurrentiels. Une exception d'accès concurrentiel est générée si un utilisateur sélectionne une liste de diffusion pour la modifier, et pendant que la page **Modifier** est affichée dans le navigateur, un autre utilisateur modifie la même liste de diffusion. Lorsque cela se produit, le code affiche un message d'avertissement et indique les champs modifiés par l'autre utilisateur.  L'API TSL utilise le champ « ETag » pour vérifier les conflits d'accès concurrentiels. Lorsqu'une ligne de table est mise à jour, la valeur « ETag » est modifiée.  Lorsque vous obtenez une ligne pour la modifier, vous enregistrez la valeur « ETag », puis, lorsque vous procédez à une mise à jour ou à une suppression, vous transmettez cette valeur enregistrée (la vue « Modifier » a un champ caché pour la valeur ETag). Si l'opération de mise à jour détecte une différence entre la valeur « ETag » de l'enregistrement que vous mettez à jour et la valeur « ETag » transmise, une exception d'accès concurrentiel est générée. Si vous ne vous souciez pas des conflits d'accès concurrentiels, vous pouvez définir la valeur du champ ETag en entrant un astérisque (*) dans l'entité transmise à l'opération de mise à jour : tout conflit sera alors ignoré. 

    Remarque : l'erreur HTTP 412 n'est pas propre aux erreurs d'accès concurrentiels. L'API SCL peut la générer pour d'autres types d'erreurs.

    Pour la page **Supprimer**, la méthode « HttpGet Delete » recherche la ligne pour afficher son contenu, puis la méthode « HttpPost » supprime la ligne « MailingList » ainsi que toutes les lignes « Subscriber » qui y sont associées dans la table « MailingList ».

        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(string partitionKey)
        {
            // Supprime toutes les lignes de cette liste de diffusion, c'est à dire 
            // les lignes Subscriber ainsi que les lignes MailingList.
            // C'est pourquoi vous n'avez pas besoin d'indiquer une clé de ligne.
            var query = new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey));
            var listRows = mailingListTable.ExecuteQuery(query).ToList();
            var batchOperation = new TableBatchOperation();
            int itemsInBatch = 0;
            foreach (MailingList listRow in listRows)
            {
                batchOperation.Delete(listRow);
                itemsInBatch++;
                if (itemsInBatch == 100)
                {
                    mailingListTable.ExecuteBatch(batchOperation);
                    itemsInBatch = 0;
                    batchOperation = new TableBatchOperation();
                }
            }
            if (itemsInBatch > 0)
            {
                mailingListTable.ExecuteBatch(batchOperation);
            }
            return RedirectToAction("Index");
        }

    Si vous devez supprimer un grand nombre d'abonnés, le code supprime les enregistrements par lots. Le coût de transaction de la suppression d'une ligne est identique à celui de la suppression de 100 lignes dans un lot. Le nombre maximum d'opérations que vous pouvez effectuer dans un lot est 100. 

    Même si la boucle traite les lignes « MailingList » et « Subscriber », elle les lit toutes dans la classe d'entité « MailingList », car les seuls champs requis pour l'opération « Delete » sont les champs « PartitionKey », « RowKey », et « ETag ».

### Ajout des vues MVC MailingList

1.  Dans l'**Explorateur de solution**, créez un nouveau dossier sous le dossier *Views* dans le projet MVC, puis nommez-le *MailingList*.

2.  Cliquez avec le bouton droit sur le nouveau dossier *Views\\MailingList* puis sélectionnez **Ajouter un élément existant**.

    ![Ajouter un élément existant au dossier Views](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-add-existing-item-to-views.png)

3.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez les quatre fichiers .cshtml du dossier *Views\\MailingList*, puis cliquez sur **Ajouter**.

4.  Ouvrez le fichier *Edit.cshtml* et examinez le code.

         @model MvcWebRole.Models.MailingList
                 @{
             ViewBag.Title = "Edit Mailing List";
         }
                 <h2>Edit Mailing List</h2>
               @using (Html.BeginForm()) {
               @Html.AntiForgeryToken()
               @Html.ValidationSummary(true)
               @Html.HiddenFor(model => model.ETag)
               <fieldset>
                 <legend>MailingList</legend>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DisplayFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Description)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Description)
                     @Html.ValidationMessageFor(model => model.Description)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.FromEmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.FromEmailAddress)
                     @Html.ValidationMessageFor(model => model.FromEmailAddress)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>       
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Ce code est propre aux vues MVC. Notez le champ masqué inclus pour conserver la valeur `ETag` utilisée pour la gestion des conflits d'accès concurrentiels. Notez également que le champ `ListName` a une application auxiliaire `DisplayFor` au lieu d'une application auxiliaire `EditorFor`. Nous n'avons pas activé la page **Modifier** pour modifier le nom de la liste, car cela requiert un code trop complexe dans le contrôleur : la méthode `HttpPost Edit` aurait dû supprimer la ligne MailingList existante et toutes les lignes subscriber associées, avant de toutes les réinsérer avec la nouvelle valeur de clé. Dans une application de production, il est possible qu'une telle complexité vaille la peine. Comme nous le verrons plus tard, le contrôleur `Subscriber` permet de modifier le nom de la liste, mais cela n'affecte qu'une ligne à la fois.

    Les codes *Create.cshtml* et *Delete.cshtml* sont semblables à *Edit.cshtml*.

5.  Ouvrez *Index.cshtml* et examinez le code.

         @model IEnumerable<MvcWebRole.Models.MailingList>
         @{
             ViewBag.Title = "Mailing Lists";
         }
         <h2>Mailing Lists</h2>
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Description)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.FromEmailAddress)
                 </th>
                 <th></th>
             </tr>
         @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.ListName)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Description)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.FromEmailAddress)
                 </td>
                 <td>
                     @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey }) |
                     @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey=item.RowKey  })
                 </td>
             </tr>
         }
         </table>

    Ce code est également propre aux vues MVC. Les liens hypertexte **Modifier** et **Supprimer** spécifient des paramètres de chaîne de requête de clés de partition et de ligne pour identifier une ligne spécifique. Dans le cas des entités `MailingList`, seule la clé de partition est vraiment nécessaire, car la clé de ligne est toujours « MailingList », mais les deux types de clés sont conservés pour que le code de vue MVC soit cohérent pour l'ensemble des contrôleurs et des vues.

### Définition de MailingList comme contrôleur par défaut

1.  Ouvrez *Route.config.cs* dans le dossier *App\_Start*.

2.  Dans la ligne spécifiant la valeur par défaut, remplacez le contrôleur par défaut « Home » par « MailingList ».

          routes.MapRoute(
              name: "Default",
              url: "{controller}/{action}/{id}",
              defaults: new { controller = "MailingList", action = "Index", id = UrlParameter.Optional }

Configuration de stockageConfiguration du rôle Web utiliser votre compte Azure Storage de test
----------------------------------------------------------------------------------------------

Vous allez entrer les paramètres pour votre compte de stockage de test, que vous utiliserez pour exécuter le projet en local. Vous devez ajouter chaque nouveau paramètre sur le cloud et en local, mais vous pouvez modifier la valeur sur le cloud ultérieurement. Vous ajouterez les mêmes paramètres pour le rôle de travail A ultérieurement.

Si vous voulez exécuter l'interface utilisateur Web dans un site Web Azure au lieu d'un service cloud Azure, consultez la section [Autre architecture](#alternativearchitecture) plus tard dans ce didacticiel lire une autre version de ces instructions.

1.  Dans l'**Explorateur de solution**, cliquez avec le bouton droit sur **MvcWebRole** sous **Rôles** dans le projet cloud **AzureEmailService**, puis sélectionnez **Propriétés**.

    ![Propriétés de rôle Web](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mvcwebrole-properties-menu.png)

2.  Assurez-vous que l'option **Toutes les configurations** est sélectionnée dans la liste déroulante **Configuration du service**.

3.  Sélectionnez l'onglet **Paramètres**, puis cliquez sur **Ajouter un paramètre**.

4.  Entrez « StorageConnectionString » dans la colonne **Nom**.

5.  Sélectionnez **Chaîne de connexion** dans la liste déroulante **Type**.

6.  Cliquez sur le bouton **...** situé à l'extrême droite de la ligne pour ouvrir la boîte de dialogue **Storage Account Connection String**.

    ![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-elip.png)

7.  Dans cette boîte de dialogue, cliquez sur la case d'option **Your subscription**, puis sur le lien **Download Publish Settings**.

    > [WACOM.NOTE] Avec le dernier Kit de développement logiciel (SDK) vous n'avez pas besoin de télécharger quoi que ce soit : vous sélectionnez simplement les comptes de stockage disponibles dans une liste déroulante.

    **Remarque :** si vous avez configuré les paramètres de stockage pour le deuxième didacticiel et que vous suivez ce didacticiel sur la même machine, vous n'avez pas besoin de télécharger ces paramètres de nouveau. Cliquez simplement sur **Your subscription** puis sélectionnez l'**abonnement** et le **nom du compte** requis.

    ![Clic droit, Propriétés](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-enter.png)

    Lorsque vous cliquez sur le lien **Download Publish Settings**, Visual Studio démarre une nouvelle instance de votre navigateur par défaut avec URL de la page de téléchargement des paramètres de publication du portail Azure. Si vous n'êtes pas connecté au portail, vous êtes invité à vous connecter. Une fois connecté, votre navigateur vous invite à enregistrer les paramètres de publication. Notez l'emplacement où vous enregistrez les paramètres.

    ![paramètres de publication](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-3.png)

8.  Dans la boîte de dialogue **Create Storage Connection String**, cliquez sur **Import**, puis accédez au fichier de paramètres de publication que vous avez enregistré à l'étape précédente.

9.  Sélectionnez l'abonnement et le compte de stockage que vous souhaitez utiliser, puis cliquez sur **OK**.

    ![sélectionner le compte de stockage](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-5.png)

10. En suivant la même procédure que celle utilisée pour la chaîne de connexion `StorageConnectionString`, définissez la chaîne de connexion `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    Il n'est pas nécessaire de télécharger de nouveau le fichier de paramètres de publication. Lorsque vous cliquez sur les points de suspension correspondant à la chaîne de connexion `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`, vous pouvez constater que la boîte de dialogue **Create Storage Connection String** a mémorisé vos informations d'abonnement. Lorsque vous cliquez sur la case d'option **Your subscription**, vous n'avez plus qu'à sélectionner l'**Abonnement** et le **Nom du compte** que vous avez sélectionnés précédemment, puis à cliquer sur **OK**.

11. Suivez la même procédure que celle utilisée pour les deux chaînes de connexion du rôle MvcWebRole pour définir les chaînes de connexion pour le rôle WorkerRoleA.

Lorsque vous ajoutez un nouveau paramètre avec le bouton **Add Settings**, il est ajouté au code XML dans le fichier *ServiceDefinition.csdf* et dans chacun des deux fichiers de configuration *.cscfg*. Le code XML suivant est ajouté par Visual Studio au fichier *ServiceDefinition.csdf*.

      <ConfigurationSettings>
        <Setting name="StorageConnectionString" />
      </ConfigurationSettings>

Le code XML suivant est ajouté à chaque fichier de configuration *.cscfg*.

       <Setting name="StorageConnectionString"
       value="DefaultEndpointsProtocol=https;
       AccountName=azuremailstorage;
       AccountKey=[la clé de votre compte]" />

Vous pouvez ajouter manuellement des paramètres au fichier *ServiceDefinition.csdf* ainsi qu'aux deux fichiers de configuration *.cscfg*, mais l'utilisation de l'éditeur de propriétés procure les avantages suivants pour les chaînes de connexion :

-   Vous n'avez qu'à ajouter le nouveau paramètre à un seul endroit, puis le code XML correspondant est ajouté aux trois fichiers.
-   Le code XML adéquat est généré pour les trois fichiers de paramètres. Le fichier *ServiceDefinition.csdf* définit les paramètres de chaque fichier de configuration *.cscfg*. Si le fichier *ServiceDefinition.csdf* et les deux fichiers de configuration *.cscfg* ne sont pas cohérents, Visual Studio affiche le message d'erreur suivant : « Le modèle de service actuel n'est pas synchronisé. Veuillez vérifier que la configuration du service et les fichiers de définition sont corrects. »

    ![Erreur Configuration de service et fichiers de définition incorrects](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-er1.png)

Si vous obtenez cette erreur, l'éditeur de propriétés ne fonctionnera pas tant que le problème de cohérence ne sera pas résolu.

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5.

    ![Page d'index MailingList vide](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-empty-index-page.png)

2.  Utilisez la fonction **Créer** pour ajouter des listes de diffusion, puis essayez les fonctions **Modifier** et **Supprimer** pour vérifier qu'elles fonctionnent correctement.

    ![Page d'index MailingList avec des lignes](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-mailing-list-index-page.png)

SubscriberCréation et test du contrôleur et des vues d'abonnés
--------------------------------------------------------------

L'interface utilisateur Web **Abonnés** permet aux administrateurs d'ajouter des abonnés à une liste de diffusion, mais aussi de modifier, afficher et supprimer les abonnés existants.

### Ajout de la classe d'entité Subscriber au dossier Models

La classe d'entité `Subscriber` est utilisée pour les lignes de la table `MailingList` qui contiennent des informations sur les abonnés à une liste. Ces lignes contiennent des informations telles que l'adresse électronique de chaque abonné et un champ indiquant si chaque adresse a été vérifiée.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Models* du projet MVC, puis sélectionnez **Ajouter un élément existant**.

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *Subscriber.cs* dans le dossier *Models*, puis cliquez sur **Ajouter**.

3.  Ouvrez *Subscriber.cs* et examinez le code.

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
                 [Display(Name = "Adresse électronique")]
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
            
                 public bool
         Verified { get; set; }
             }

    Comme la classe d'entité `MailingList`, la classe d'entité `Subscriber` permet de lire et d'écrire des lignes dans la table `mailinglist`. Les lignes `Subscriber` utilisent l'adresse électronique au lieu de la constante « mailinglist » comme clé de ligne. Pour plus d'informations sur la structure de table, consultez le [premier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/). C'est pourquoi une propriété `EmailAddress` est définie. Elle utilise la propriété `RowKey` en tant que champ de sauvegarde, tout comme `ListName` utilise `PartitionKey` de la même manière. Comme nous l'avons vu précédemment, ceci vous permet de placer des attributs de mise en forme et de validation DataAnnotations dans les propriétés.

    La valeur `SubscriberGUID` est générée lors de la création d'une entité `Subscriber`. Elle est utilisée dans les liens d'abonnement et de désabonnement, pour garantir que seules les personnes autorisées peuvent s'abonner ou se désabonner en utilisant leurs adresses électroniques. Lors de la création initiale d'une ligne pour un nouvel abonné, la valeur `Verified ` est `false`. La valeur `Verified` passe à `true` uniquement lorsque le nouvel abonné clique sur le lien **Confirmer** dans le courrier électronique de bienvenue. Si un message est envoyé à la liste alors que la valeur `Verified` d'un abonné est `false`, aucun message n'est envoyé à cet abonné.

    La propriété `Verified` de l'entité `Subscriber` est définie sur Nullable. Lorsque vous indiquez qu'une requête doit renvoyer des entités `Subscriber`, il est possible que certaines lignes récupérées n'aient pas une propriété `Verified`. C'est pourquoi l'entité `Subscriber` définit sa propriété `Verified` comme Nullable, pour qu'elle puisse refléter plus précisément le contenu d'une ligne si la requête renvoie des lignes de table sans propriété *Verified*. Vous êtes peut-être habitué à utiliser des tables SQL Server, dans lesquelles chaque ligne a le même schéma. Dans une table Azure Storage, chaque ligne est une collection de propriétés différente. Par exemple, dans l'exemple d'application Azure Email Service, les lignes dont la clé est « MailingList » n'ont pas de propriété `Verified`. Si une requête renvoie une ligne de table sans propriété `Verified`, lors de l'instanciation de la classe d'entité `Subscriber`, la propriété `Verified` de l'objet d'entité sera null. Si la propriété n'était pas nullable, vous obtiendriez la même valeur `false` pour les lignes dont la propriété `Verified` est définie sur `false` et pour les lignes sans propriété `Verified`. C'est pourquoi nous vous conseillons d'utiliser les tables Azure pour définir chaque propriété d'une classe d'entité sur nullable afin de préciser la lecture des lignes créés en utilisant différentes classes d'entité ou différentes versions de la classe d'entité actuelle.

### Ajout du contrôleur MVC Subscriber

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Controllers* dans le projet MVC, puis sélectionnez **Ajouter un élément existant**.

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *SubscriberController.cs* dans le dossier *Controllers*, puis cliquez sur **Ajouter**. Vérifiez que vous avez bien sélectionné *Subscriber.cs* et non pas *Subscribe.cs* : nous ajouterons *Subscribe.cs* ultérieurement.

3.  Ouvrez *SubscriberController.cs* et examinez le code.

    La plupart du code de ce contrôleur est semblable à celui du contrôleur `MailingList`. Même le nom de la table est le même, car les informations sur les abonnés sont conservées dans la table `MailingList`. Après la méthode `FindRow`, vous trouverez la méthode `GetListNames`. Cette méthode permet d'obtenir les données pour une liste déroulante sur les pages **Créer** et **Modifier**, dans laquelle vous pouvez sélectionner la liste de diffusion sur laquelle s'inscrire et l'adresse électronique d'abonnement.

         private List<MailingList> GetListNames()
         {
             var query = (new TableQuery<MailingList>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.Equal, "mailinglist")));
             var lists = mailingListTable.ExecuteQuery(query).ToList();
             return lists;
         }

    Il s'agit de la même requête que celle du contrôleur `MailingList`. Les lignes de cette liste déroulante doivent contenir des informations sur les listes de diffusion : sélectionnez donc uniquement celles dont la clé est « mailinglist ».

    Les lignes de la méthode de récupération des données pour la page **Index** doivent contenir des informations sur les abonnées : sélectionnez donc les lignes dont la clé n'est pas « MailingList ».

         public ActionResult Index()
         {
             var query = (new TableQuery<Subscriber>().Where(TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.NotEqual, "mailinglist")));
             var subscribers = mailingListTable.ExecuteQuery(query).ToList();
             return View(subscribers);
         }

    Notez que la requête indique que les données seront lues dans les objets `Subscriber` (en indiquant `<Subscriber>`) mais que les données seront lues depuis la table `mailinglist`.

    **Remarque :** le nombre d'abonnés peut augmenter jusqu'à devenir trop volumineux pour pouvoir être géré de cette manière, avec une seule requête. Dans une prochaine version de ce didacticiel, nous espérons pouvoir implémenter des fonctionnalités de pagination et expliquer comment gérer les jetons de liaison. Vous devez gérer des jetons de liaison lorsque vous exécutez des requêtes renvoyant plus de 1 000 lignes : Azure renvoie 1 000 lignes et un jeton de liaison permettant d'exécuter une autre requête qui démarre là où la première s'arrête. L'Explorateur de stockage Azure ne gère pas les jetons de liaison. C'est pourquoi ses requêtes ne renvoient pas plus de 1 000 lignes. Pour plus d'informations sur les grands ensembles de résultats et les jetons de liaison, consultez les billets [Comment tirer le meilleur parti des tables Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/11/06/how-to-get-most-out-of-windows-azure-tables.aspx) et [Tables Azure : utilisation des jetons de liaison](http://blog.smarx.com/posts/windows-azure-tables-expect-continuation-tokens-seriously).

    Configurez les données pour la liste déroulante dans la méthode `HttpGet Create` et définissez les valeurs par défaut dans la méthode `HttpPost` avant d'enregistrer la nouvelle entité.

         public ActionResult Create()
         {
             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             var model = new Subscriber() { Verified = false };
             return View(model);
         }

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Subscriber subscriber)
         {
             if (ModelState.IsValid)
             {
                 subscriber.SubscriberGUID = Guid.NewGuid().ToString();
                 if (subscriber.Verified.HasValue == false)
                 {
                     subscriber.Verified = false;
                 }

                 var insertOperation = TableOperation.Insert(subscriber);
                 mailingListTable.Execute(insertOperation);
                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", subscriber.ListName);

             return View(subscriber);
         }

    La page `HttpPost Edit` est plus complexe que celle du contrôleur `MailingList`, car la page `Subscriber` vous permet de modifier le nom de la liste ou l'adresse électronique, qui sont tous deux des champs clés. Si l'utilisateur change l'un de ces champs, vous devez supprimer l'enregistrement existant et en ajouter un nouveau au lieu de mettre à jour l'enregistrement existant. Le code suivant concerne la partie de la méthode edit qui gère les différentes procédures pour des modifications de clés par rapport à des non-clés :

             if (ModelState.IsValid)
             {
                 try
                 {
                     UpdateModel(editedSubscriber, string.Empty, null, excludeProperties);
                     if (editedSubscriber.PartitionKey == partitionKey && editedSubscriber.RowKey == rowKey)
                     {
                         //Les clés ne sont pas modifiées -- Mise à jour de la ligne
                         var replaceOperation = TableOperation.Replace(editedSubscriber);
                         mailingListTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // Les clés sont modifiées -- Suppression de l'ancien enregistrement, puis insertion du nouveau
                         if (editedSubscriber.PartitionKey != partitionKey)
                         {
                             // PartitionKey modifiée, impossible de supprimer ou insérer dans un lot.
                             var deleteOperation = TableOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             mailingListTable.Execute(deleteOperation);
                             var insertOperation = TableOperation.Insert(editedSubscriber);
                             mailingListTable.Execute(insertOperation);
                         }
                         else
                         {
                             // RowKey modifiée, supprimer ou insérer dans un lot.
                             var batchOperation = new TableBatchOperation();
                             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
                             batchOperation.Insert(editedSubscriber);
                             mailingListTable.ExecuteBatch(batchOperation);
                         }
                     }
                     return RedirectToAction("Index");

    Les paramètres que le module de liaison de modèle MVC transmet à la méthode `Edit` incluent le nom de liste et les valeurs d'adresses électroniques d'origine (dans les paramètres `partitionKey` et `rowKey`) ainsi que les valeurs entrées par l'utilisateur (dans les paramètres `listName` et `emailAddress`) :

         public ActionResult Edit(string partitionKey, string rowKey, string listName, string emailAddress)

    Les paramètres transmis à la méthode `UpdateModel` excluent les propriétés `PartitionKey` et `RowKey` de la liaison de modèle :

             var excludeProperties = new string[] { "PartitionKey", "RowKey" };

    Ceci est justifié par le fait que les propriétés `ListName` et `EmailAddress` utilisent `PartitionKey` et `RowKey` en tant que propriétés de sauvegarde, et que l'utilisateur a peut-être modifié l'une de ces valeurs. Lorsqu'un module de liaison de modèle met à jour le modèle en définissant la propriété `ListName`, la propriété `PartitionKey` est automatiquement mise à jour. Si le modèle de liaison parvenait à mettre à jour la propriété `PartitionKey` avec la valeur d'origine de cette propriété après la mise à jour de la propriété `ListName`, cela remplacerait la nouvelle valeur définie par la propriété `ListName`. La propriété `EmailAddress` met automatiquement à jour la propriété `RowKey` de la même manière.

    Après la mise à jour de l'objet de modèle `editedSubscriber`, le code détermine si la clé de partition ou de ligne a été modifiée. Si l'une des valeurs de clé a été modifiée, la ligne subscriber existante doit être supprimée, puis une nouvelle ligne insérée. Si seule la clé de ligne a été modifiée, la suppression et l'insertion peuvent être effectuées dans une transaction atomique par lots.

    Notez que le code crée une entité à transmettre à l'opération `Delete` :

             // RowKey modifiée, supprimer ou insérer dans un lot.
             var batchOperation = new TableBatchOperation();
             batchOperation.Delete(new Subscriber { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedSubscriber.ETag });
             batchOperation.Insert(editedSubscriber);
             mailingListTable.ExecuteBatch(batchOperation);

    Les entités transmises vers les opérations dans un lot doivent être distinctes. Par exemple, vous ne pouvez pas créer une entité `Subscriber`, la transmettre à une opération `Delete`, puis modifier une valeur dans la même entité `Subscriber` et la transmettre à une opération `Insert`. Si vous avez procédé ainsi, l'état de l'entité après la modification de propriété doit prendre effet pour les opérations Delete et Insert.

    **Remarque :** les opérations d'un lot doivent toutes être sur la même partition. Comme le fait de modifier le nom de la liste modifie la clé de partition, vous ne pouvez pas le faire dans une transaction.

### Ajout des vues Subscriber MVC

1.  Dans l'**Explorateur de solutions**, créez un nouveau dossier sous le dossier *Views* dans le projet MVC, et nommez-le *Subscriber*.

2.  Cliquez avec le bouton droit sur le nouveau dossier *Views\\Subscriber*, puis sélectionnez **Ajouter un élément existant**.

3.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez les cinq fichiers .cshtml dans le dossier *Views\\Subscriber*, puis cliquez sur **Ajouter**.

4.  Ouvrez le fichier *Edit.cshtml* et examinez le code.

         @model MvcWebRole.Models.Subscriber
            
         @{
             ViewBag.Title = "Edit Subscriber";
         }
            
         <h2>Edit Subscriber</h2>
            
         @using (Html.BeginForm()) {
             @Html.AntiForgeryToken()
             @Html.ValidationSummary(true)
              @Html.HiddenFor(model => model.SubscriberGUID)
              @Html.HiddenFor(model => model.ETag)
              <fieldset>
                 <legend>Subscriber</legend>
                 <div class="display-label">
                      @Html.DisplayNameFor(model => model.ListName)
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.EmailAddress)
                     @Html.ValidationMessageFor(model => model.EmailAddress)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.Verified)
                 </div>
                 <div class="display-field">
                     @Html.EditorFor(model => model.Verified)
                 </div>
                 <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    Ce code est identique à celui que vous avez vu précédemment pour la vue **Modifier** `MailingList`. La valeur `SubscriberGUID` n'est pas affichée, donc la valeur n'est pas fournie automatiquement dans un champ de formulaire pour la méthode du contrôleur `HttpPost`. C'est pourquoi un champ masqué est inclus pour conserver cette valeur.

    Les autres vues contiennent du code semblable à celui que vous avez vu pour le contrôleur `MailingList`.

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5, puis cliquez sur **Subscribers**.

    ![Page d'index Subscriber vide](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-empty-index-page.png)

2.  Utilisez la fonction **Créer** pour ajouter des listes de diffusion, puis essayez les fonctions **Modifier** et **Supprimer** pour vérifier qu'elles fonctionnent correctement.

    ![Page d'index Subscribers avec des lignes](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-subscribers-index-page.png)

MessageCréation et test du contrôleur et des vues de message
------------------------------------------------------------

L'interface utilisateur Web **Message** permet aux administrateurs de créer, modifier et afficher des informations sur les messages devant être envoyés vers les listes de diffusion.

### Ajout de la classe d'entité Message au dossier Models

La classe d'entité `Message` est utilisée pour les lignes dans la table `Message` contenant des informations sur un message devant être envoyé vers une liste. Ces lignes incluent des informations telles que la ligne d'objet, la liste vers laquelle envoyer le message et la date prévue pour l'envoi.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier *Models* du projet MVC, puis sélectionnez **Ajouter un élément existant**.

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *Message.cs* dans le dossier Models, puis cliquez sur **Ajouter**.

3.  Ouvrez *Message.cs* et examinez le code.

         public class Message : TableEntity
         {
             private DateTime
         _scheduledDate;
             private long _messageRef;

             public Message()
             {
                 this.MessageRef = DateTime.Now.Ticks;
                 this.Status = "Pending";
             }

             [Required]
             [Display(Name = "Date prévue")]
             // DataType.Date indique uniquement la date (pas l'heure) et permet de raccorder facilement jQuery DatePicker
             [DataType(DataType.Date)]
             public DateTime
         ScheduledDate
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
             [Display(Name = "Nom de la liste")]
             public string ListName { get; set; }

             [Required]
             [Display(Name = "Ligne d'objet")]
             public string SubjectLine { get; set; }

             // Pending, Queuing, Processing, Complete
             public string Status { get; set; }
         }

    La classe `Message` définit un constructeur par défaut qui définit la propriété `MessageRef` sur une valeur unique pour le message. Comme cette valeur fait partie de la clé de ligne, le setter de la propriété `MessageRef` définit également automatiquement la propriété `RowKey`. Le setter de la propriété `MessageRef` concatène le terme « message » et la valeur `MessageRef` et place le résultat dans la propriété `RowKey`.

    La valeur `MessageRef` est créé en obtenant la valeur `Ticks` à partir de `DateTime.Now`. Ceci garantit par défaut que les messages affichés dans l'interface utilisateur seront également affichés dans l'ordre de leur création pour une date pour une date prévue (`ScheduledDate` est la clé de partition). Si vous utilisez un GUID pour rendre unique chaque ligne du message, l'ordre de récupération par défaut devient aléatoire.

    Le constructeur par défaut définit également l'état par défaut des nouvelles lignes de `message` sur Pending.

    Pour plus d'informations sur la structure de table `Message`, consultez le [premier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

### Ajout du contrôleur MVC Message

1.  Dans l'**Explorateur de solution**, cliquez avec le bouton droit sur le dossier Controllers du projet MVC, puis sélectionnez **Ajouter un élément existant**.

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *MessageController.cs* dans le dossier *Controllers*, puis cliquez sur **Ajouter**.

3.  Ouvrez *MessageController.cs* et examinez le code.

    La plupart du code de ce contrôleur est semblable à celui du contrôleur `Subscriber`. Ce qui est nouveau ici, c'est que le code fonctionne avec les objets blob. Pour chaque message, le contenu du message électronique en HTML et en texte brut est téléchargé sous la forme de fichiers .htm et .txt et stocké dans des objets blob.

    Les objets blob sont stockés dans des conteneurs blob. L'application Azure Email Service stocke tous ces objets blob dans un seul conteneur d'objets blob nommé "azuremailblobcontainer", et le code du constructeur de contrôleur est doté d'une référence à ce conteneur d'objets blob :

         public class MessageController : Controller
         {
             private TableServiceContext serviceContext;
             private static CloudBlobContainer blobContainer;

               public MessageController()
             {
                 var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
                 // Si ce code est exécuté dans un site Web Azure (et non pas dans un service cloud), utilisez le fichier Web.config :
                 //    var storageAccount = CloudStorageAccount.Parse(ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

                 // Obtient un objet de contexte pour fonctionner avec des tables et une référence vers le conteneur d'objets blob.
                 var tableClient = storageAccount.CreateCloudTableClient();
             serviceContext = tableClient.GetTableServiceContext();
                 var blobClient = storageAccount.CreateCloudBlobClient();
                 blobContainer = blobClient.GetContainerReference("azuremailblobcontainer");
             }

    La vue MVC fournit à chaque fichier qu'un utilisateur sélectionne pour le téléchargement un objet `HttpPostedFile` contenant des informations sur ce fichier. Lorsque l'utilisateur crée un message, l'objet `HttpPostedFile` permet d'enregistrer le fichier dans un objet blob. Lorsque l'utilisateur modifie un message, il peut choisir de télécharger un fichier de remplacement ou ne pas modifier l'objet blob.

    Le contrôleur inclut une méthode appelée par les méthodes `HttpPost Create` et `HttpPost Edit` pour enregistrer un objet blob :

         private void SaveBlob(string blobName, HttpPostedFileBase httpPostedFile)
         {
             // Récupère une référence vers un objet blob. 
             CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);
             // Crée un objet blob ou remplace l'objet blob existant en téléchargeant un fichier local.
             using (var fileStream = httpPostedFile.InputStream)
             {
                 blob.UploadFromStream(fileStream);
             }
         }

    La méthode `HttpPost Create` enregistre les deux objets blob, puis ajoute la ligne de table `Message`. Les objets blob sont nommés en concaténant la valeur `MessageRef` avec l'extension du nom de fichier « .htm » ou « .txt ».

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Create(Message message, HttpPostedFileBase file, HttpPostedFileBase txtFile)
         {
             if (file == null)
             {
                 ModelState.AddModelError(string.Empty, "Veuillez fournir un chemin d'accès au fichier HTML");
             }

             if (txtFile == null)
             {
                 ModelState.AddModelError(string.Empty, "Veuillez fournir un chemin d'accès au fichier texte");
             }

             if (ModelState.IsValid)
             {
                 SaveBlob(message.MessageRef + ".htm", file);
                 SaveBlob(message.MessageRef + ".txt", txtFile);

                 var insertOperation = TableOperation.Insert(message);
                 messageTable.Execute(insertOperation);

                 return RedirectToAction("Index");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description");
             return View(message);
         }

    La méthode `HttpGet Edit` valide l'état `Pending` du message récupéré, pour que l'utilisateur ne modifie pas un message une fois que le rôle de travail B a commencé à le traiter. Les méthodes `HttpPost Edit`, `Delete` et `DeleteConfirmed` contiennent un code semblable.

         public ActionResult Edit(string partitionKey, string rowKey)
         {
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Désolé... Nous ne pouvons pas modifier le message, car il n'est pas à l'état Pending.");
             }

             var lists = GetListNames();
             ViewBag.ListName = new SelectList(lists, "ListName", "Description", message.ListName);
             return View(message);
         }

    Dans la méthode `HttpPost Edit`, le code enregistre uniquement un nouvel objet blob si l'utilisateur a choisi de télécharger un nouveau fichier. Le code suivant ne contient pas la partie concernant la gestion des conflits d'accès concurrentiels, qui est identique à celle présentée pour le contrôleur `MailingList`.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Edit(string partitionKey, string rowKey, Message editedMsg,
             DateTime scheduledDate, HttpPostedFileBase httpFile, HttpPostedFileBase txtFile)
         {
             if (ModelState.IsValid)
             {
                 var excludePropLst = new List<string>();
                 excludePropLst.Add("PartitionKey");
                 excludePropLst.Add("RowKey");

                 if (httpFile == null)
                 {
                     // Il n'y a pas de chemin d'accès ou de tentative d'accès à un fichier, il n'est donc pas nécessaire de mettre à jour le fichier.
                     excludePropLst.Add("HtmlPath");
                 }
                 else
                 {
                     // Il y a un chemin d'accès ou une tentative d'accès à un fichier, il est certainement modifié.
                     SaveBlob(editedMsg.MessageRef + ".htm", httpFile);
                 }

                 if (txtFile == null)
                 {
                     excludePropLst.Add("TextPath");
                 }
                 else
                 {
                     SaveBlob(editedMsg.MessageRef + ".txt", txtFile);
                 }

                 string[] excludeProperties = excludePropLst.ToArray();

                 try
                 {
                     UpdateModel(editedMsg, string.Empty, null, excludeProperties);
                     if (editedMsg.PartitionKey == partitionKey)
                     {
                         // Les clés ne sont pas modifiées -- mise à jour de la ligne.
                         var replaceOperation = TableOperation.Replace(editedMsg);
                         messageTable.Execute(replaceOperation);
                     }
                     else
                     {
                         // La clé de partition est modifiée -- suppression et insertion de ligne.
                         // (Une date est planifiée pour la modification de la clé de partition ;
                         // la clé de ligne a une MessageRef qui n'est pas modifiée.)
                         var deleteOperation = TableOperation.Delete(new Message { PartitionKey = partitionKey, RowKey = rowKey, ETag = editedMsg.ETag });
                         messageTable.Execute(deleteOperation);
                         var insertOperation = TableOperation.Insert(editedMsg);
                         messageTable.Execute(insertOperation);
                     }
                     return RedirectToAction("Index");
                 }

    Si la date planifiée est modifiée, la clé de partition est modifiée et il faut supprimer une ligne puis en insérer une autre. Cela ne peut pas être effectué durant une transaction, car cela affecte plusieurs partitions.

    La méthode `HttpPost Delete` supprimer les objets blob lorsqu'elle supprime la ligne dans la table :

         [HttpPost, ActionName("Delete")]
         public ActionResult DeleteConfirmed(String partitionKey, string rowKey)
         {
             // Récupérez de nouveau la ligne pour vérifier que son état est toujours Pending.
             var message = FindRow(partitionKey, rowKey);
             if (message.Status != "Pending")
             {
                 throw new Exception("Désolé... Nous ne pouvons pas supprimer le message, car son état n'est pas Pending.");
             }

             DeleteBlob(message.MessageRef + ".htm");
             DeleteBlob(message.MessageRef + ".txt");
             var deleteOperation = TableOperation.Delete(message);
             messageTable.Execute(deleteOperation);
             return RedirectToAction("Index");
         }

         private void DeleteBlob(string blobName)
         {
             var blob = blobContainer.GetBlockBlobReference(blobName);
             blob.Delete();
         }

### Ajout des vues MVC Message

1.  Dans l'**Explorateur de solutions**, créez un dossier sous le dossier *Views* du projet MVC, puis nommez-le `Message`.

2.  Cliquez avec le bouton droit sur le dossier *Views\\Message*, puis sélectionnez **Ajouter un élément existant**.

3.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez les cinq fichiers .cshtml du dossier *Views\\Message*, puis cliquez sur **Ajouter**.

4.  Ouvrez le fichier *Edit.cshtml* et examinez le code.

         @model MvcWebRole.Models.Message
            
         @{
             ViewBag.Title = "Edit Message";
         }
            
         <h2>Edit Message</h2>
            
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
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ListName, "MailingList")
                 </div>
                 <div class="editor-field">
                     @Html.DropDownList("ListName", String.Empty)
                     @Html.ValidationMessageFor(model => model.ListName)
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.SubjectLine)
                     @Html.ValidationMessageFor(model => model.SubjectLine)
                 </div>
                 <div class="editor-label">
                     HTML Path: laissez ce champ vide pour conserver le fichier HTML actuel.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="file" />
                 </div>
                 <div class="editor-label">
                     Text Path: laissez ce champ vide pour conserver le fichier texte actuel.
                 </div>
                 <div class="editor-field">
                     <input type="file" name="TxtFile" />
                 </div>
                 <div class="editor-label">
                     @Html.LabelFor(model => model.ScheduledDate)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.ScheduledDate)
                     @Html.ValidationMessageFor(model => model.ScheduledDate)
                 </div>
                 <div class="display-label">
                     @Html.DisplayNameFor(model => model.Status)
                 </div>
                 <div class="editor-field">
                     @Html.EditorFor(model => model.Status)
                 </div>
                <p>
                     <input type="submit" value="Save" />
                 </p>
             </fieldset>
         }
            
         <div>
             @Html.ActionLink("Back to List", "Index")
         </div>
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    La méthode `HttpPost Edit`requiert une clé de partition et de ligne, pour que le code les fournisse dans les champs masqués. Les champs masqués sont facultatifs dans le contrôleur `Subscriber`, car : (a) les propriétés `ListName` et `EmailAddress` du modèle `Subscriber` mettent à jour les propriétés `PartitionKey` et `RowKey`, (b) les propriétés `ListName` et `EmailAddress` sont incluses dans les aides `EditorFor` de la vue Modifier. Lorsque que le module de liaison de modèle MVC du modèle `Subscriber` met à jour la propriété `ListName`, la propriété `PartitionKey` est mise à jour automatiquement, puis, lorsque le module de liaison de modèle MVC met à jour la propriété `EmailAddress` du modèle `Subscriber`, la propriété `RowKey` est mise à jour automatiquement. Dans le modèle `Message`, les champs mappant vers les clés de partition et de ligne ne sont pas modifiables. Vous ne pouvez donc pas les configurer ainsi.

    La propriété `MessageRef` contient aussi un champ masqué. Il contient la même valeur que la clé de partition, il est ici pour clarifier le code dans la méthode `HttpPost Edit`. Le fait d'inclure le champ masqué `MessageRef` permet au code de la méthode `HttpPost Edit` de faire référence à la valeur `MessageRef` par son nom, lors de la construction de noms de fichier pour les objets blob.

5.  Ouvrez le fichier *Index.cshtml* et examinez le code.

         @model IEnumerable<MvcWebRole.Models.Message>
            
         @{
             ViewBag.Title = "Messages";
         }
            
         <h2>Messages</h2>
            
         <p>
             @Html.ActionLink("Create New", "Create")
         </p>
         <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.ListName)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.SubjectLine)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.ScheduledDate)
                 </th>
                 <th>
                     @Html.DisplayNameFor(model => model.Status)
                 </th>
                 <th></th>
             </tr>
             @foreach (var item in Model)
             {
                 <tr>
                     <td>
                         @Html.DisplayFor(modelItem => item.ListName)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.SubjectLine)
                     </td>
                     <td>
                         @Html.DisplayFor(modelItem => item.ScheduledDate)
                     </td>
                     <td>
                         @item.Status
                     </td>
                     <td>
                         @if (item.Status == "Pending")
                         {
                             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
                             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
                         }
                         @Html.ActionLink("Details", "Details", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })
                     </td>
                 </tr>
             }
            
         </table>

    ?À la différence des autres vues **Index**, les liens **Modifier** et **Supprimer** sont uniquement affichés pour les messages dont le l'état est `Pending` :

         @if (item.Status == "Pending")
         {
             @Html.ActionLink("Edit", "Edit", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey })  @: | 
             @Html.ActionLink("Delete", "Delete", new { PartitionKey = item.PartitionKey, RowKey = item.RowKey }) @: |
         }

    Ceci permet d'empêcher l'utilisateur de modifier un message lorsque le rôle de travail A commence à le traiter.

    Les autres vues contiennent du code semblable à celui de la vue **Modifier** ou aux autres vues présentées pour les autres contrôleurs.

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5, ou en cliquant sur **Messages**.

    ![Page d'index Empty Message vide](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-empty-index-page.png)

2.  Utilisez la fonction **Créer** pour ajouter des listes de diffusion, puis essayez les fonctions **Modifier** et **Supprimer** pour vérifier qu'elles fonctionnent correctement.

    ![Page d'index Subscribers avec des lignes](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-message-index-page.png)

DésabonnementCréation et test du contrôleur et des vues de désabonnement
------------------------------------------------------------------------

Ensuite, vous allez implémenter l'interface utilisateur pour le processus de désabonnement.

**Remarque :** ce didacticiel développe uniquement le contrôleur pour le processus de désabonnement et non pas pour le processus d'abonnement. Comme l'explique [le premier didacticiel](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/), l'interface utilisateur et la méthode de service du processus d'abonnement ont été laissées de côté jusqu'à l'implémentation de la sécurité adéquate dans la méthode de service. En attendant, vous pouvez utiliser les pages d'administration **Subscriber** pour inscrire des adresses électroniques aux listes.

### Ajout du modèle de vue de désabonnement au dossier Models

Le modèle de vue `UnsubscribeVM` permet de transmettre des données entre le contrôleur `Unsubscribe` et ses vues.

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier `Models` du projet MVC, puis sélectionnez **Ajouter un élément existant**.

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier `UnsubscribeVM.cs` dans le dossier Models, puis cliquez sur **Ajouter**.

3.  Ouvrez `UnsubscribeVM.cs` et examinez le code.

         public class UnsubscribeVM
         {
             public string EmailAddress { get; set; }
             public string ListName { get; set; }
             public string ListDescription { get; set; }
             public string SubscriberGUID { get; set; }
             public bool
         Confirmed { get; set; }
         }

    Les liens de désabonnement contiennent `SubscriberGUID`. Cette valeur permet d'obtenir l'adresse électronique, ainsi que le nom et la description de la liste, à partir de la table `MailingList`. La vue affiche l'adresse électronique et la description de la liste de laquelle se désinscrire, ainsi qu'un bouton **Confirmer** sur lequel l'utilisateur doit cliquer pour terminer le processus de désabonnement.

### Ajout du contrôleur Unsubscribe

1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier `Controllers` dans le projet MVC, puis sélectionnez **Ajouter un élément existant**.

2.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *UnsubscribeController.cs* dans le dossier *Controllers*, puis cliquez sur **Ajouter**.

3.  Ouvrez *UnsubscribeController.cs* et examinez le code.

    La méthode `HttpGet Index` de ce contrôleur affiche la page de désabonnement d'origine, et la méthode `HttpPost Index` traite le bouton **Confirmer** ou **Annuler**.

    La méthode `HttpGet Index` utilise le GUID et le nom de la liste de la chaîne de requête pour obtenir la ligne de table `MailingList` de l'abonné. Puis les informations requises par la vue sont placées dans le modèle de vue et affiche la page **Désabonnement**. La propriété `Confirmed` est définie sur null pour ordonner à la vue d'afficher la version d'origine de la page **Désabonnement**.

          public ActionResult Index(string id, string listName)
          {
              if (string.IsNullOrEmpty(id) == true || string.IsNullOrEmpty(listName))
              {
                  ViewBag.errorMessage = "Empty subscriber ID or list name.";
                  return View("Error");
              }
              string filter = TableQuery.CombineFilters(
                  TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                  TableOperators.And,
                  TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, id));
              var query = new TableQuery<Subscriber>().Where(filter);
              var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();
              if (subscriber == null)
              {
                  ViewBag.Message = "Vous êtes déjà désabonné";
                  return View("Message");
              }
              var unsubscribeVM = new UnsubscribeVM();
              unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
              unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
              unsubscribeVM.SubscriberGUID = id;
              unsubscribeVM.Confirmed = null;
              return View(unsubscribeVM);
          }

    Remarque : SubscriberGUID n'est pas dans la clé de partition ou de ligne : les performances de cette requête diminuent lorsque la taille de partition (le nombre d'adresses électronique dans une liste de diffusion) augmente. Pour plus d'informations sur les alternatives pour améliorer la mise à l'échelle de cette requête, consultez [le premier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/).

    La méthode `HttpPost Index` utilise de nouveau le GUID et le nom de la liste pour obtenir les informations sur les abonnés et remplit les propriétés du modèle de vue. Ensuite, si vous cliquez sur le bouton **Confirmer**, la ligne subscriber de la table `MailingList` est supprimée. Si vous cliquez sur le bouton **Confirmer**, la propriété `Confirm` est également définie sur `true`. Sinon, elle est définie sur `false`. La valeur de la propriété `Confirm` entraîne l'affichage de la version « confirmée » ou « annulée » de la page **Désabonnement**.

         [HttpPost]
         [ValidateAntiForgeryToken]
         public ActionResult Index(string subscriberGUID, string listName, string action)
         {
             string filter = TableQuery.CombineFilters(
                 TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, listName),
                 TableOperators.And,
                 TableQuery.GenerateFilterCondition("SubscriberGUID", QueryComparisons.Equal, subscriberGUID));
             var query = new TableQuery<Subscriber>().Where(filter);
             var subscriber = mailingListTable.ExecuteQuery(query).ToList().Single();

             var unsubscribeVM = new UnsubscribeVM();
             unsubscribeVM.EmailAddress = MaskEmail(subscriber.EmailAddress);
             unsubscribeVM.ListDescription = FindRow(subscriber.ListName, "mailinglist").Description;
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

1.  Dans l'**Explorateur de solutions**, créez un nouveau dossier sous le dossier *Views* du projet MVC, et nommez-le *Unsubscribe*.

2.  Cliquez avec le bouton droit sur le nouveau dossier *Views\\Unsubscribe*, puis sélectionnez **Ajouter un élément existant**.

3.  Accédez au dossier où vous avez téléchargé l'exemple d'application, sélectionnez le fichier *Index.cshtml* dans le dossier *Views\\Unsubscribe*, puis cliquez sur **Ajouter**.

4.  Ouvrez le fichier *Index.cshtml* et examinez le code.

         @model MvcWebRole.Models.UnsubscribeVM
            
         @{
             ViewBag.Title = "Unsubscribe";
             Layout = null;
         }
            
         <h2>Service d'abonnement à la liste de diffusion</h2>
            
         @using (Html.BeginForm()) {
             @Html.AntiForgeryToken()
             @Html.ValidationSummary(true)
             <fieldset>
                 <legend>Se désabonner de la liste de diffusion</legend>
                 @Html.HiddenFor(model => model.SubscriberGUID)
                 @Html.HiddenFor(model => model.EmailAddress)
                 @Html.HiddenFor(model => model.ListName)
                 @if (Model.Confirmed == null) {
                     <p>
                         Voulez-vous vous désabonner  @Html.DisplayFor(model => model.EmailAddress) de :  @Html.DisplayFor(model => model.ListDescription)

                    </p>
                     <br />
                     <p>
                         <input type="submit" value="Confirm" name="action"/> 
                            
                         <input type="submit" value="Cancel" name="action"/>
                     </p>
                 }
                 @if (Model.Confirmed == false) {
                     <p>
                         @Html.DisplayFor(model => model.EmailAddress)  ne SERA PAS désabonné de : @Html.DisplayFor(model => model.ListDescription).
                     </p>
                 }
                 @if (Model.Confirmed == true) {
                     <p>
                         @Html.DisplayFor(model => model.EmailAddress)  est désabonné de :  @Html.DisplayFor(model => model.ListDescription).
                     </p>
                 }
             </fieldset>
         }
            
         @section Scripts {
             @Scripts.Render("~/bundles/jqueryval")
         }

    La ligne `Layout = null` indique que vous ne devez pas utiliser le fichier \_Layout.cshtml pour afficher cette page. La page **Désabonnement** contient une interface utilisateur très simple, sans en-tête ou pied de page, contrairement aux pages d'administration.

    Dans le corps de la page, la propriété `Confirmed` détermine ce qui sera affiché dans cette page : Les boutons **Confirmer** et **Annuler** si la propriété est null, le message unsubscribe-confirmed si la propriété est true, le message unsubscribe-canceled si la propriété est false.

### Test de l'application

1.  Exécutez le projet en appuyant sur CTRL+F5, puis cliquez sur **Abonnés**.

2.  Cliquez sur **Créer** et créez un sous-dossier pour chaque liste de diffusion créée durant les tests précédents.

    Laissez la fenêtre du navigateur ouverte sur les pages **Abonnés** et **Index**.

3.  Ouvrez l'Explorateur Azure Storage, puis sélectionnez votre compte de stockage test.

4.  Cliquez sur **Tables** sous **Type de stockage**, sélectionnez la table **MailingList**, puis cliquez sur **Requête**.

5.  Double-cliquez sur la ligne d'abonné que vous avez ajouté.

    ![Explorateur de stockage Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-unsubscribe.png)

6.  Dans la boîte de dialogue **Modifier l'entité**, sélectionnez et copiez la valeur `SubscriberGUID`.

    ![Explorateur de stockage Azure](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-ase-edit-entity-unsubscribe.png)

7.  Revenez à la fenêtre de votre navigateur. Dans la barre d'adresse du navigateur, remplacez « Subscriber » dans l'URL par « unsubscribe?ID=[guidvalue]&listName=[listname] » où [guidvalue] est le GUID que vous avez copié à partir de l'explorateur de stockage Azure, et [listname] le nom de la liste de diffusion. Par exemple :

         http://127.0.0.1/unsubscribe
         ID=b7860242-7c2f-48fb-9d27-d18908ddc9aa&listName=contoso1

    La version de la page **Désabonnement** demandant à l'utilisateur de confirmer son désabonnement est affichée :

    ![Page Désabonnement](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-query-page.png)

8.  Cliquez sur **Confirmer** : la confirmation du désabonnement de cette adresse électronique est affichée.

    ![Page de désabonnement confirmé](./media/cloud-services-dotnet-multi-tier-app-storage-1-web-role/mtas-unsubscribe-confirmation-page.png)

9.  Revenez à la page d'**index** **Subscribers** pour vérifier que la ligne subscriber n'est plus là.

Autre architecture(Facultatif) Développement d'une autre alternative
--------------------------------------------------------------------

Modifiez les instructions comme suit pour développer une autre architecture, consistant à exécuter l'interface utilisateur Web dans un site Web Azure au lieu d'un rôle Web de service cloud Azure.

-   Lorsque vous créez la solution, créez d'abord le projet **ASP.NET MVC 4 Web Application**, puis ajoutez à la solution un projet **Azure Cloud Service** avec un rôle de travail.

-   Stockez la chaîne de connexion Azure Storage dans le fichier Web.config au lieu du fichier de configuration de service cloud. Ceci fonctionne uniquement avec les sites Web Azure. Si vous essayez d'utiliser le fichier Web.config pour la chaîne de connexion de stockage dans un rôle Web de service cloud Azure, vous obtiendrez une erreur HTTP 500.

Ajoutez une nouvelle chaîne de connexion nommée `StorageConnectionString` au fichier *Web.config*, comme indiqué dans l'exemple ci-dessous :

           <connectionStrings>
              <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-MvcWebRole-20121010185535;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-MvcWebRole-20121010185535.mdf" providerName="System.Data.SqlClient" />
              <add name="StorageConnectionString" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[primarykey]" />
           </connectionStrings>

Récupérez les valeurs de la chaîne de connexion à partir du [portail de gestion Azure](http://manage.windowsazure.com) : sélectionnez l'onglet **Stockage** et votre compte de stockage, puis cliquez sur **Gérer les clés** en bas de la page.

-   Si `RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString")` est affiché dans le code, remplacez-le par `ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString`.

Étapes suivantesÉtapes suivantes
--------------------------------

Comme expliqué dans [le premier didacticiel de la série](/en-us/develop/net/tutorials/multi-tier-web-site/1-overview/), nous ne verrons pas comment développer le processus d'abonnement en détail dans ce didacticiel tant que nous n'aurons pas implémenté un secret partagé pour sécuriser la méthode de service de l'API Web ASP.NET. Cependant, la restriction d'IP protège également la méthode de service et vous pouvez ajouter la fonctionnalité d'abonnement en copiant les fichiers suivant à partir du projet téléchargé.

Pour la méthode de service de l'API Web ASP.NET :

-   Controllers\\SubscribeAPI.cs

Pour la page Web affichée lorsqu'un abonné clique sur le lien **Confirmer** dans le message électronique généré par la méthode de service :

-   Models\\SubscribeVM.cs
-   Controllers\\SubscribeController.cs
-   Views\\Subscribe\\Index.cshtml

Dans le [prochain didacticiel](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/) vous allez configurer et programmer un rôle de travail A, qui planifie l'envoi des courriers électroniques.

Pour obtenir des liens sur les ressources supplémentaires pour travailler avec les tables, les files d'attente et les objets blob Azure Storage, consultez la fin du [dernier didacticiel de cette série](/en-us/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/).

[Didacticiel 4](/en-us/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/)

