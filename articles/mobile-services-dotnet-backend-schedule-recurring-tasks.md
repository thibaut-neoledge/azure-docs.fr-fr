<properties pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="" solutions="" writer="" manager="" editor="" />

Planification de tâches récurrentes dans Mobile Services
========================================================

[.NET backend](/fr-fr/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ ".NET backend") | [JavaScript backend](/fr-fr/documentation/articles/mobile-services-schedule-recurring-tasks/ "JavaScript backend")

Cette rubrique explique comment utiliser la fonctionnalité du planificateur de tâches dans le portail de gestion pour définir le code du script serveur qui est exécuté sur la base d'un calendrier défini par vous. Dans ce cas, le script effectue régulièrement une vérification auprès d'un service distant, Twitter en l'occurrence, et stocke les résultats dans une nouvelle table. D'autres tâches périodiques peuvent être planifiées, notamment les tâches suivantes :

-   Archivage des enregistrements de données anciens ou en double.
-   Demande et stockage de données externes, par exemple, des tweets, des entrées RSS et des informations d'emplacement.
-   Traitement ou redimensionnement des images stockées.

Ce didacticiel explique comment utiliser le planificateur de tâches pour créer une tâche planifiée qui demande des données de tweets à Twitter et stocke les tweets dans une nouvelle table Updates. La procédure comporte les étapes suivantes :

-   [Inscription pour l'accès à Twitter et stockage des informations d'identification](#get-oauth-credentials)
-   [Téléchargement et installation du LINQ dans la bibliothèque Twitter](#install-linq2twitter)
-   [Création de la nouvelle table Updates](#create-table)
-   [Création d'une nouvelle tâche planifiée](#add-job)
-   [Test local de la tâche planifiée](#run-job-locally)
-   [Publication du service et inscription de la tâche](#register-job)

> [WACOM.NOTE]Ce didacticiel utilise la bibliothèque tierce LINQ to Twitter pour simplifier l'accès d'OAuth 2.0 aux API Twitter v1.1. Vous devez télécharger et installer le package NuGet LINQ to Twitter pour suivre ce didacticiel. Pour plus d'informations, consultez la page [Projet CodePlex LINQ to Twitter](http://linqtotwitter.codeplex.com/).

Inscription pour l'accès aux API de Twitter v1.1 et stockage des informations d'identification
----------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

1.  Dans l'Explorateur de solutions Visual Studio, ouvrez le fichier web.config correspondant au projet de service mobile, localisez les paramètres d'application **MS\_TwitterConsumerKey** et **MS\_TwitterConsumerSecret** et remplacez les valeurs de ces clés par des valeurs de clé de consommateur et de secret de consommateur Twitter que vous définissez dans le portail.

2.  Dans la même section, ajoutez les nouveaux paramètres d'application suivants, remplacez les espaces réservés par les valeurs du jeton d'accès et du secret de jeton d'accès Twitter que vous définissez comme paramètres d'application dans le portail :

    ``` {}
    <add key="TWITTER_ACCESS_TOKEN" value="**votre_jeton_accès**" />
    <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**votre_secret_jeton_accès**" />
    ```

    Le service mobile utilise ces paramètres stockés lorsqu'il s'exécute sur l'ordinateur local, ce qui permet de tester la tâche planifiée avant de la publier. Si le service mobile est exécuté dans Azure, il utilise à la place les valeurs définies dans le portail et ignore les paramètres du projet.

Téléchargement et installation de la bibliothèque LINQ to Twitter
-----------------------------------------------------------------

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2.  Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez `linq2twitter`, cliquez sur **Installer** au niveau du package **linqtotwitter**, puis acceptez le contrat de licence.

	![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png) 

	Cela ajoute la bibliothèque LINQ to Twitter à votre projet de service mobile.

Ensuite, vous devez créer une nouvelle table pour y stocker les tweets.

Création de la nouvelle table Updates
-------------------------------------

1.  Dans l'Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le dossier DataObjects, développez **Ajouter**, cliquez sur **Classe**, tapez `Updates` comme **Nom**, puis cliquez sur **Ajouter**.

    Ainsi, un nouveau fichier de projet est créé pour la classe Updates.

2.  Dans cette nouvelle classe, ajoutez les instructions **using** suivantes :

         using Microsoft.WindowsAzure.Mobile.Service;
         using System.ComponentModel.DataAnnotations;

3.  Remplacez la définition de la classe **Updates** par le code suivant :

         public class Updates 
         {
             [Key]
             public int UpdateId { get; set; }
             public long TweetId { get; set; }
             public string Text { get; set; }
             public string Author { get; set; }
             public DateTime Date { get; set; }

    }

4.  Développez le dossier Modèles, ouvrez le fichier de contexte du modèle de données (nommé *service\_name*Context.cs) et ajoutez la propriété suivante qui renvoie un **DbSet** typé :

         public DbSet<Updates> Updates { get; set; }

    La table Updates, qui est créée dans la base de données lors du premier accès à DbSet, est utilisée par le service pour stocker les données des tweets.

    > [WACOM.NOTE] Lors de l'utilisation de l'initialiseur de base de données par défaut, Entity Framework supprime et recrée la base de données lorsqu'il détecte une modification du modèle de données dans la définition du modèle Code First. Pour modifier ce modèle de données et conserver les données existantes dans la base de données, vous devez utiliser les migrations Code First. L'initialiseur par défaut ne peut pas être utilisé avec une base de données SQL dans Azure. Pour plus d'informations, consultez l'article [Utilisation des migrations Code First pour mettre à jour le modèle de données](/fr-fr/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations).

Vous pouvez ensuite créer la tâche planifiée qui accède à Twitter et stocke les données des tweets dans la nouvelle table Updates.

Création d'une nouvelle tâche planifiée
---------------------------------------

1.  Développez le dossier ScheduledJobs et ouvrez le fichier de projet SampleJob.cs.

    Cette classe, qui hérite de **ScheduledJob**, représente une tâche pouvant être planifiée, dans le portail de gestion Azure, pour être exécutée selon une planification fixe ou à la demande.

2.  Remplacez le contenu de SampleJob.cs par le code suivant :

         using System;
         using System.Linq;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Web.Http;
         using Microsoft.WindowsAzure.Mobile.Service;
         using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
         using LinqToTwitter;
         using todolistService.Models;
         using todolistService.DataObjects;
            
         namespace todolistService
         {
             // Une simple tâche planifiée qui peut être appelée manuellement en envoyant une requête
             // HTTP POST au chemin "/jobs/sample".
             public class SampleJob : ScheduledJob
             {
                 private todolistContext context;
                 private string accessToken;
                 private string accessTokenSecret;
            
                 protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
                 {
                     base.Initialize(scheduledJobDescriptor, cancellationToken);
            
                     // Créer un nouveau contexte avec le nom de schéma fourni.
                     context = new todolistContext(Services.Settings.Name);
                 }
            
                 public async override Task ExecuteAsync()
                 {            
                     // Essayer d'obtenir le jeton d'accès Twitter stocké à partir des paramètres de l'application.  
                     if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
                     Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
                     {
                         Services.Log.Error("Impossible de récupérer les informations d'identification Twitter.");
                     }
            
                     // Créer un nouvel agent d'autorisation pour accéder aux API Twitter v1.1
                     // à l'aide des informations d'authentification mono-utilisateur OAUth 2.0.
                     MvcAuthorizer auth = new MvcAuthorizer();
                     SingleUserInMemoryCredentialStore store = 
                         new SingleUserInMemoryCredentialStore()
                     {
                         ConsumerKey = Services.Settings.TwitterConsumerKey,
                         ConsumerSecret = Services.Settings.TwitterConsumerSecret,
                         OAuthToken = accessToken,
                         OAuthTokenSecret = accessTokenSecret
                     };
            
                     // Définir les informations d'identification de l'agent d'autorisation.
                     auth.CredentialStore = store;
            
                     // Créer un nouveau contexte LINQ to Twitter.
                     TwitterContext twitter = new TwitterContext(auth);
            
                     // Obtenir l'identificateur du tweet stocké le plus récent.
                     long lastTweetId = 0;
                     if (context.Updates.Count() > 0)
                     {
                         lastTweetId = (from u in context.Updates
                                        orderby u.TweetId descending
                                        select u).Take(1).SingleOrDefault()
                                                     .TweetId;
                     }
            
                     // Exécuter une recherche qui renvoie un résultat filtré.
                     var response = await (from s in twitter.Search
                                           where s.Type == SearchType.Search
                                           && s.Query == "%23mobileservices"
                                           && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
                                           && s.ResultType == ResultType.Recent
                                           select s).SingleOrDefaultAsync();
            
                     // Supprimer les retweets et réponses et consigner le nombre de tweets.
                     var filteredTweets = response.Statuses
                         .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
                     Services.Log.Info("Fetched " + filteredTweets.Count()
                         + " new tweets from Twitter.");
            
                     // Stocker les nouveaux tweets dans la table Updates.
                     foreach (Status tweet in filteredTweets)
                     {
                         Updates newTweet =
                             new Updates
                             {
                                 TweetId = Convert.ToInt64(tweet.StatusID),
                                 Text = tweet.Text,
                                 Author = tweet.User.Name,
                                 Date = tweet.CreatedAt
                             };
            
                         context.Updates.Add(newTweet);
                     }
            
                     await context.SaveChangesAsync();
                 }
                 protected override void Dispose(bool disposing)
                 {
                     base.Dispose(disposing);
                     if (disposing)
                     {
                         context.Dispose();
                     }
                 }
             }
         }

    Dans le code ci-dessus, vous devez remplacer les chaînes *todolistService* et *todolistContext* par l'espace de noms et le DbContext du projet téléchargé, qui sont *mobile\_service\_name*Service et *mobile\_service\_name*Context, respectivement.

    Dans le code ci-dessus, la méthode de remplacement **ExecuteAsync** appelle l'API de requêtes Twitter en utilisant les informations d'identification stockées pour demander les tweets récents contenant la balise de hachage `#mobileservices`. Les tweets en double et les réponses sont supprimés des résultats avant que ces derniers ne soient stockés dans la table.

Test local de la tâche planifiée
--------------------------------

Les tâches planifiées peuvent être testées localement avant d'être publiées sur Azure et enregistrées dans le portail.

1.  Dans Visual Studio, avec le projet de service mobile défini comme projet de démarrage, appuyez sur F5.

    Cela démarre le projet de service mobile et affiche une nouvelle fenêtre de navigateur avec la page de bienvenue.

2.  Copiez l'URL de service mobile depuis la fenêtre de navigateur ouverte, modifiez le chemin `/tables/samplejob` sur l'URL, puis exécutez une nouvelle requête POST vers cette URL à l'aide d'un utilitaire HTTP, tel que Fiddler.

    La méthode *\*ExecuteAsync\** est lancée sur l'ordinateur local.

3.  Dans l'Explorateur de serveurs, développez **Connexions de données**, **MSTableConnectionString** et **tables** ; cliquez avec le bouton droit sur **Mises à jours**, puis cliquez sur **Afficher les données de la table**.

    Les nouveaux tweets sont entrés sous la forme de lignes dans la table des données.

Publication du service et enregistrement de la nouvelle tâche
-------------------------------------------------------------

La tâche doit être enregistrée dans l'onglet **Planificateur** afin que Mobile Services puisse l'exécuter dans la planification que vous définissez.

1.  Publiez à nouveau le projet de service mobile dans Azure.

2.  Dans le [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur Mobile Services, puis cliquez sur votre application.

    ![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png)

3.  Cliquez sur l'onglet **Planificateur**, puis sur **+Créer**.

	![][3]

    > [WACOM.NOTE]Lorsque vous exécutez votre service mobile au niveau *Gratuit*, vous ne pouvez exécuter qu'une seule tâche planifiée à la fois. Aux niveaux payants, vous pouvez exécuter jusqu'à dix tâches planifiées à la fois.

4.  Dans la boîte de dialogue du planificateur, entrez *SampleJob* pour le **Nom de la tâche**, définissez l'intervalle et les unités de planification, puis cliquez sur le bouton de vérification.

	![][4]

	Une nouvelle tâche nommée **SampleJob** est créée. 

5.  Cliquez sur la tâche que vous venez de créer, puis cliquez sur l'onglet **Exécuter une fois** pour tester le script.

	![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png) 

	Ainsi, la tâche est exécutée, tout en restant désactivée dans le planificateur. Depuis cette page, vous pouvez activer la tâche et modifier sa planification à n'importe quel moment.

    >[WACOM.NOTE]Une requêtes POST peut encore être utilisée pour démarrer la tâche planifiée. Néanmoins, l'autorisation prend implicitement la valeur utilisateur, ce qui signifie que la requête doit inclure la clé d'application dans l'en-tête.

1.  (Facultatif) Dans le [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur manage pour la base de données associée à votre service mobile.

    ![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png)

2.  Dans le portail de gestion, exécutez une requête pour afficher les modifications apportées par l'application. Votre requête sera similaire à la requête suivante, mais utilisera votre nom de service mobile comme nom de schéma au lieu de `todolist`.

         SELECT * FROM [todolist].[Updates]

Félicitations, vous avez créé une nouvelle tâche planifiée dans votre service mobile. Cette tâche sera exécutée conformément à la planification jusqu'à ce que vous la désactiviez ou la modifiiez.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png

<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /fr-fr/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/fr-fr/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ to Twitter CodePlex project]: http://linqtotwitter.codeplex.com/