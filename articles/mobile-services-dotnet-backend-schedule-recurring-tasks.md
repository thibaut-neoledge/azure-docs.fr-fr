<properties 
	pageTitle="Planification des tâches du backend avec le planificateur - Mobile Services" 
	description="Azure Mobile Services Scheduler permet de planifier des tâches pour votre application mobile." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	writer="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="2/26/2015" 
	ms.author="glenga"/>

# Planification des travaux récurrents dans Mobile Services 

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [(Any | Javascript)](mobile-services-schedule-recurring-tasks.md)
 
Cette rubrique explique comment utiliser la fonctionnalité de planification de travaux dans le portail de gestion pour définir le code de script serveur exécuté selon une planification que vous définissez. Dans ce cas, le script effectue régulièrement une vérification auprès d'un service distant, Twitter en l'occurrence, et stocke les résultats dans une nouvelle table. D'autres tâches périodiques peuvent être planifiées, notamment les tâches suivantes :

+ Archivage des enregistrements de données anciens ou en double.
+ Demande et stockage de données externes, par exemple, des tweets, des entrées RSS et des informations d'emplacement.
+ Traitement ou redimensionnement des images stockées.

Ce didacticiel explique comment utiliser la planification des travaux pour créer un travail planifié qui demande des données de tweets à Twitter et stocke les tweets dans une nouvelle table Updates. La procédure comporte les étapes suivantes :

1. [Inscription pour l'accès à Twitter et stockage des informations d'identification]
2. [Téléchargement et installation de la bibliothèque LINQ to Twitter]
3. [Création de la table Updates]
4. [Création d'une tâche planifiée]
5. [Test local de la tâche planifiée]
6. [Publication du service et inscription de la tâche]

>[AZURE.NOTE]Ce didacticiel utilise la bibliothèque tierce LINQ to Twitter pour simplifier l'accès d'OAuth 2.0 aux API Twitter v1.1 . Vous devez télécharger et installer le package NuGet LINQ to Twitter pour suivre ce didacticiel. Pour plus d'informations, consultez la page [Projet CodePlex LINQ to Twitter].

##<a name="get-oauth-credentials"></a>Inscription pour l'accès aux API de Twitter v1.1 et stockage des informations d'identification

[AZURE.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

<ol start="7">
<li><p>Dans l'Explorateur de solutions Visual&#160;Studio, ouvrez le fichier web.config correspondant au projet de service mobile, localisez les paramètres d'application <strong>MS_TwitterConsumerKey</strong> et <strong>MS_TwitterConsumerSecret</strong> et remplacez les valeurs de ces clés par des valeurs de clé de consommateur et de secret de consommateur Twitter que vous définissez dans le portail.</p></li>

<li><p>Dans la même section, ajoutez les nouveaux paramètres d'application suivants, remplacez les espaces réservés par les valeurs du jeton d'accès et du secret de jeton d'accès Twitter que vous définissez comme paramètres d'application dans le portail&#160;:</p>

<pre><code>&lt;add key="TWITTER_ACCESS_TOKEN" value="**votre_jeton_accès**" />
&lt;add key="TWITTER_ACCESS_TOKEN_SECRET" value="**votre_secret_jeton_accès**" /></code></pre>

<p>Le service mobile utilise ces paramètres stockés lorsqu'il s'exécute sur l'ordinateur local, ce qui permet de tester la tâche planifiée avant de la publier. Si le service mobile est exécuté dans Azure, il utilise à la place les valeurs définies dans le portail et ignore les paramètres du projet.  </p></li>
</ol>

##<a name="install-linq2twitter"></a>Téléchargement et installation du LINQ dans la bibliothèque Twitter

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez `linq2twitter`, cliquez sur **Installer** au niveau du package **linqtotwitter**, puis lisez et acceptez le contrat de licence.

  	![][1]

  	Cela ajoute la bibliothèque LINQ to Twitter à votre projet de service mobile.

Ensuite, vous devez créer une nouvelle table pour y stocker les tweets.

##<a name="create-table"></a>Création de la table Updates

1. Dans l'explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur le dossier DataObjects, développez **Ajouter**, cliquez sur **Classe**, tapez `Updates` comme **Nom**, puis cliquez sur **Ajouter**.

	Ainsi, un nouveau fichier de projet est créé pour la classe Updates.

2. Cliquez avec le bouton droit sur le nœud **Références**, cliquez sur **Ajouter une référence...**, sélectionnez **Infrastructure** sous **Assemblys**, cochez **System.ComponentModel.DataAnnotations**, puis cliquez sur **OK**.

	![][7]

	Une nouvelle référence d'assembly est alors créée.

2. Dans cette nouvelle classe, ajoutez les instructions **using** suivantes :
 
		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. Remplacez la définition de la classe **Updates** par le code suivant :

		public class Updates 
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. Développez le dossier Modèles, ouvrez le fichier de contexte du modèle de données (nommé <em>nom_service</em>Context.cs) et ajoutez la propriété suivante qui renvoie un **DbSet** typé :

		public DbSet<Updates> Updates { get; set; }

	La table Updates, qui est créée dans la base de données lors du premier accès à DbSet, est utilisée par le service pour stocker les données des tweets.

	>[AZURE.NOTE]Lors de l'utilisation de l'initialiseur de base de données par défaut, Entity Framework supprime et recrée la base de données lorsqu'il détecte une modification du modèle de données dans la définition du modèle Code First. Pour modifier ce modèle de données et conserver les données existantes dans la base de données, vous devez utiliser les migrations Code First. L'initialiseur par défaut ne peut pas être utilisé avec une base de données SQL dans Azure. Pour plus d'informations, consultez l'article [Utilisation des migrations Code First pour mettre à jour le modèle de données](mobile-services-dotnet-backend-use-code-first-migrations.md).

Vous pouvez ensuite créer la tâche planifiée qui accède à Twitter et stocke les données des tweets dans la nouvelle table Updates.

##<a name="add-job"></a>Création d'une tâche planifiée  

1. Développez le dossier ScheduledJobs et ouvrez le fichier de projet SampleJob.cs.

	Cette classe, qui hérite de **ScheduledJob**, représente une tâche pouvant être planifiée, dans le portail de gestion Azure, pour être exécutée selon une planification fixe ou à la demande.

2. Remplacez le contenu de SampleJob.cs par le code suivant :
 
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
		    // A simple scheduled job which can be invoked manually by submitting an HTTP
		    // POST request to the path "/jobs/sample".
		    public class SampleJob : ScheduledJob
		    {
		        private todolistContext context;
		        private string accessToken;
		        private string accessTokenSecret;
		
		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);
		
		            // Create a new context with the supplied schema name.
		            context = new todolistContext();
		        }
		
		        public async override Task ExecuteAsync()
		        {            
		            // Try to get the stored Twitter access token from app settings.  
		            if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
		            Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
		            {
		                Services.Log.Error("Could not retrieve Twitter access credentials.");
		            }
		
		            // Create a new authorizer to access Twitter v1.1 APIs
		            // using single-user OAUth 2.0 credentials.
		            MvcAuthorizer auth = new MvcAuthorizer();
		            SingleUserInMemoryCredentialStore store = 
		                new SingleUserInMemoryCredentialStore()
		            {
		                ConsumerKey = Services.Settings.TwitterConsumerKey,
		                ConsumerSecret = Services.Settings.TwitterConsumerSecret,
		                OAuthToken = accessToken,
		                OAuthTokenSecret = accessTokenSecret
		            };
		
		            // Set the credentials for the authorizer.
		            auth.CredentialStore = store;
		
		            // Create a new LINQ to Twitter context.
		            TwitterContext twitter = new TwitterContext(auth);
		
		            // Get the ID of the most recent stored tweet.
		            long lastTweetId = 0;
		            if (context.Updates.Count() > 0)
		            {
		                lastTweetId = (from u in context.Updates
		                               orderby u.TweetId descending
		                               select u).Take(1).SingleOrDefault()
		                                            .TweetId;
		            }
		
		            // Execute a search that returns a filtered result.
		            var response = await (from s in twitter.Search
		                                  where s.Type == SearchType.Search
		                                  && s.Query == "%23mobileservices"
		                                  && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
		                                  && s.ResultType == ResultType.Recent
		                                  select s).SingleOrDefaultAsync();
		
		            // Remove retweets and replies and log the number of tweets.
		            var filteredTweets = response.Statuses
		                .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
		            Services.Log.Info("Fetched " + filteredTweets.Count()
		                + " new tweets from Twitter.");
		
		            // Store new tweets in the Updates table.
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

	Dans le code ci-dessus, vous devez remplacer les chaînes _todolistService_ et _todolistContext_ par l'espace de noms et le DbContext du projet téléchargé, qui sont <em>nom&#95;service&#95;mobile</em>Service et <em>nom&#95;service&#95;mobile</em>Context, respectivement.
   	
	Dans le code ci-dessus, la méthode de remplacement **ExecuteAsync** appelle l'API de requêtes Twitter en utilisant les informations d'identification stockées pour demander les tweets récents contenant la balise de hachage `#mobileservices`. Les tweets en double et les réponses sont supprimés des résultats avant que ces derniers ne soient stockés dans la table.

##<a name="run-job-locally"></a>Test local de la tâche planifiée

Les tâches planifiées peuvent être testées localement avant d'être publiées sur Azure et enregistrées dans le portail.

1. Dans Visual Studio, avec le projet de service mobile défini comme projet de démarrage, appuyez sur F5.

	Cela démarre le projet de service mobile et affiche une nouvelle fenêtre de navigateur avec la page de bienvenue.

2. Cliquez sur **Essayer**, puis cliquez sur **POST jobs/{jobName}**.

	![][8]
 
4. Cliquez sur **Essayer**, tapez `Sample` comme valeur de paramètre **{jobName}**, puis cliquez sur **Envoyer**.

	![][9]

	Cette commande crée une nouvelle demande POST au point de terminaison de l'exemple de tâche. La méthode **ExecuteAsync** est lancée dans le service local. Vous pouvez définir un point de rupture dans cette méthode pour déboguer le code.

3. Dans l'Explorateur de serveurs, développez **Connexions de données**, **MSTableConnectionString** et **tables** ; cliquez avec le bouton droit sur **Mises à jours**, puis cliquez sur **Afficher les données de la table**.

	Les nouveaux tweets sont entrés sous la forme de lignes dans la table des données.

##<a name="register-job"></a>Publication du service et enregistrement de la nouvelle tâche 

La tâche doit être enregistrée dans l'onglet **Planificateur** afin que Mobile Services puisse l'exécuter dans la planification que vous définissez.

3. Publiez à nouveau le projet de service mobile dans Azure. 

4. Dans le [portail de gestion Azure], cliquez sur Mobile Services, puis cliquez sur votre application.

2. Cliquez sur l'onglet **Scheduler**, puis sur **+Créer**.

    >[AZURE.NOTE]Quand vous exécutez votre service mobile en mode <em>Gratuit</em>, vous ne pouvez exécuter qu'une seule tâche planifiée à la fois. Aux niveaux payants, vous pouvez exécuter jusqu'à dix travaux planifiés à la fois.

3. Dans la boîte de dialogue du planificateur, entrez _Sample_ comme **Nom du travail**, définissez l'intervalle et les unités de planification, puis cliquez sur le bouton de vérification.
   
   	![][4]

   	Cette commande crée une tâche nommée **Sample**.

4. Cliquez sur la tâche que vous venez de créer, puis cliquez sur l'onglet **Exécuter une fois** pour tester le script.

   	Ainsi, la tâche est exécutée, tout en restant désactivée dans le planificateur. Depuis cette page, vous pouvez activer la tâche et modifier sa planification à n'importe quel moment.

	>[AZURE.NOTE]Une demande POST peut encore être utilisée pour démarrer la tâche planifiée. Néanmoins, l'autorisation prend implicitement la valeur utilisateur, ce qui signifie que la requête doit inclure la clé d'application dans l'en-tête.

4. (Facultatif) Dans le [portail de gestion Azure], cliquez sur manage pour la base de données associée à votre service mobile.

    ![][6]

5. Dans le portail de gestion, exécutez une requête pour afficher les modifications apportées par l'application. Votre requête sera similaire à la requête suivante, mais utilisera votre nom de service mobile comme nom de schéma au lieu de `todolist`.

        SELECT * FROM [todolist].[Updates]

Félicitations, vous avez créé un nouveau travail planifié dans votre service mobile. Celui-ci sera exécuté conformément à la planification jusqu'à ce que vous la désactiviez ou la modifiiez.

<!-- Anchors. -->
[Inscription pour l'accès à Twitter et stockage des informations d'identification]: #get-oauth-credentials
[Téléchargement et installation de la bibliothèque LINQ to Twitter]: #install-linq2twitter
[Création de la table Updates]: #create-table
[Création d'une tâche planifiée]: #add-job
[Test local de la tâche planifiée]: #run-job-locally
[Publication du service et inscription de la tâche]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[portail de gestion Azure]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[Projet CodePlex LINQ to Twitter]: http://linqtotwitter.codeplex.com/
<!--HONumber=54-->