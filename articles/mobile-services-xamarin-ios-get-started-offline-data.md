<properties urlDisplayName="Using Offline Data" pageTitle="Utilisation de données hors connexion dans les Mobile Services (Xamarin iOS) | Centre de développement mobile" metaKeywords="" description="Découvrez comment utiliser Azure Mobile Services pour mettre en cache et synchroniser les données hors connexion dans votre application Xamarin iOS" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Utilisation de la synchronisation des données hors connexion dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

Cette rubrique vous présente l'utilisation des fonctionnalités hors connexion d'Azure Mobile Services. Ces fonctionnalités vous permettent d'interagir avec une base de données locale lorsque vous vous trouvez dans un scénario hors connexion avec votre service mobile. Les fonctionnalités hors connexion vous permettent de synchroniser vos modifications locales avec le service mobile une fois que vous êtes à nouveau en ligne. 

Dans ce didacticiel, vous allez mettre à jour l'application à partir du didacticiel [Prise en main de Mobile Services] ou [Prise en main de données] pour pouvoir prendre en charge les fonctionnalités hors connexion d'Azure Mobile Services. Ensuite, vous allez ajouter les données dans le cadre d'un scénario hors connexion déconnecté, synchroniser ces éléments dans la base de données en ligne, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

>[WACOM.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données dans une application Windows Store à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services].
>
>Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10&nbsp;services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>. 

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion]
2. [Test de l'application connectée au service mobile]

Ce didacticiel requiert les éléments suivants :

* XCode 4.5 et iOS 6.0 (ou versions ultérieures) 
* Visual Studio avec l'[extension Xamarin] **ou** [Xamarin Studio] sur OS X
* Didacticiel [Prise en main de Mobile Services] ou [Prise en main des données].
* [Kit de développement logiciel (SDK) Azure Mobile Services version 1.3.0-beta2 (ou versions ultérieures)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store version 1.0.0-beta2 (ou versions ultérieures)][SQLite store nuget]

>[WACOM.NOTE] Les instructions qui suivent partent du principe que vous utilisez Visual Studio 2012 ou version ultérieure avec l'extension Xamarin. Si vous utilisez Xamarin Studio sur OS X, utilisez la prise en charge du Gestionnaire de package NuGet intégrée.

## <a name="enable-offline-app"></a>Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion

La synchronisation hors connexion d'Azure Mobile Services permet aux utilisateurs d'interagir avec une base de données locale lorsque le réseau n'est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `IMobileServiceSyncTable`.

Un projet avec l'état terminé après ce didacticiel est disponible [ici](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).

1. Dans Visual Studio, ouvrez le projet que vous avez créé dans le didacticiel [Prise en main de Mobile Services] ou [Prise en main de données]. Dans l'Explorateur de solutions, supprimez la référence au **Kit de développement logiciel (SDK) Azure Mobile Services** dans **Composants**.

2. Installez le package préliminaire de Mobile Services SQLiteStore à l'aide de la commande suivante dans la Console du gestionnaire de package : 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Cette commande installe également toutes les dépendances requises.
    
3. Dans le nœud des références, supprimez les références à `System.IO`, `System.Runtime` et `System.Threading.Tasks`.

### Modification du fichier QSTodoService.cs 

Modifiez la classe `QSTodoService` afin de permettre l'utilisation des fonctionnalités hors connexion de Mobile Services avec un magasin local SQLite.

1. Ajoutez les instructions using suivantes au début du fichier.

		using Microsoft.WindowsAzure.MobileServices; 
		using Microsoft.WindowsAzure.MobileServices.Sync; 
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2. Changez le type de membre `todoTable` de `IMobileServiceTable` en `IMobileServicesSyncTable`

		IMobileServiceSyncTable<ToDoItem> todoTable; 

3. Dans le constructeur de `QSTodoService`, modifiez l'initialiseur de `todoTable` :

        todoTable = client.GetSyncTable <ToDoItem> ();

4. Dans le constructeur de `QSTodoService`, ajoutez un appel à `SQLitePCL.CurrentPlatform.Init()` comme deuxième ligne de code :

		QSTodoService ()
		{
			CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

			// Initialize the Mobile Service client with your URL and key
			client = new MobileServiceClient (applicationURL, applicationKey, this);

			// Create an MSTable instance to allow us to work with the TodoItem table
			todoTable = client.GetSyncTable <ToDoItem> ();
		}
 
5. Dans la classe `QSTodoService`, définissez une nouvelle méthode `InitializeAsync` :
 
		public async Task InitializeStoreAsync()
		{
		    string path = "syncstore.db";
		    var store = new MobileServiceSQLiteStore(path);
		    store.DefineTable<ToDoItem>();
		    await client.SyncContext.InitializeAsync(store);
		}

6. Dans la classe `QSTodoService`, définissez une nouvelle méthode `SyncAsync` :
 
		public async Task SyncAsync()
		{
		    try
		    {
		        await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    }
		    catch (MobileServiceInvalidOperationException e)
		    {
		        Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
		    }
		}

### Modification de ToDoItem.cs 

1. Ajoutez l'instruction using : 

        using Microsoft.WindowsAzure.MobileServices; 


2. Ajoutez les membres suivants à la classe `ToDoItem` :
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

### Modification de QSTodoListViewController.cs 

Modifiez `QSTodoListViewController` afin d'appeler la nouvelle méthode `SyncAsync` lorsque l'utilisateur effectue le geste d'actualisation.
 
1. Ajoutez un appel à `InitializeStoreAsync` dans `ViewDidLoad()`, après l'initialisation de `todoService` :

		public override async void ViewDidLoad ()
		{
		    base.ViewDidLoad ();
		
		    todoService = QSTodoService.DefaultService;
			await todoService.InitializeStoreAsync();
			
			...    // the rest of the code in the method is unchanged
		}

2. Modifiez la méthode `AddRefreshControl` pour appeler `SyncAsync` avant l'appel à `RefreshAsync` :


		RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
			await RefreshAsync();
		}; 

<!-- 
DM: commenting this out because this tutorial doesn't show OC conflict handling
### Edit ToDoItem.cs 

Modify the strongly-type data class to add a version field

1. In the top of the file, add the using statement: 

        using Microsoft.WindowsAzure.MobileServices; 

2. Add the following members to the class `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

-->

## <a name="test-online-app"></a>Test de l'application 

Dans cette section, vous allez tester la méthode `SyncAsync` qui synchronise le magasin local avec la base de données du service mobile.

1. Dans Visual Studio, appuyez sur le bouton **Exécuter** pour générer le projet et démarrer l'application dans l'émulateur iPhone (configuration par défaut pour ce projet).

2. Notez que la liste des éléments dans l'application est vide. Suite aux modifications de code appliquées dans la section précédente, l'application ne lit plus les éléments à partir du service mobile, mais à partir du magasin local. 

3. Ajout d'éléments à la liste ToDo

    ![][1]


4. Connectez-vous au portail de gestion Microsoft Azure et examinez la base de données de votre service mobile. Si votre service utilise l'application dorsale de JavaScript pour les services mobiles, vous pouvez accéder aux données à partir de l'onglet **Données** du service mobile. Si vous utilisez l'application dorsale .NET pour votre service mobile, vous pouvez cliquer sur le bouton **Gérer** pour votre base de données dans l'extension SQL Azure afin d'exécuter une requête sur base de votre table.

    Notez que les données n'ont pas été synchronisées entre la base de données et le magasin local.

5. Dans l'application, effectuez le geste d'actualisation en tirant la liste des éléments vers le bas. L'application appelle `MobileServiceClient.SyncContext.PushAsync` et `IMobileServiceSyncTable.PullAsync(), puis `RefreshTodoItems` pour actualiser l'application avec les éléments du magasin local. 

    Suite à l'opération Push, la base de données du service mobile reçoit les données à partir du magasin. Une opération Push est exécutée à partir de `MobileServiceClient.SyncContext` plutôt que `IMobileServicesSyncTable` et transmet par Push les modifications à toutes les tables associées à ce contexte de synchronisation. Ceci permet de prendre en compte les scénarios dans lesquels des relations existent entre les tables.
    
    À l'inverse, l'opération Pull récupère les enregistrements uniquement de la table spécifiée. S'il reste des opérations en attente pour cette table dans le contexte de synchronisation, une opération `PushAsync` est appelée de façon implicite par le Kit de développement logiciel (SDK) Mobile Services.
        
    ![][3] 



    ![][2]


  

##Résumé

Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l'interface `IMobileServiceSyncTable` et initialisé `MobileServiceClient.SyncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données SQLite.

Les opérations CRUD normales pour les services mobiles fonctionnent comme si l'application était toujours connectée, mais toutes les opérations ont lieu sur base du magasin local.

Lorsque nous avons voulu synchroniser le magasin local avec le serveur, nous avons utilisé les méthodes `IMobileServiceSyncTable.PullAsync` et `MobileServiceClient.SyncContext.PushAsync`.

*  Pour transmettre par Push les modifications au serveur, nous avons appelé `IMobileServiceSyncContext.PushAsync()`. Cette méthode est membre de `IMobileServicesSyncContext` à la place de la table de synchronisation parce qu'elle envoie par Push les modifications sur toutes les tables :

    Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur.
   
* Pour envoyer par Pull les données d'une table sur le serveur vers l'application, nous avons appelé `IMobileServiceSyncTable.PullAsync`.

    Une opération Pull émet toujours d'abord une opération Push.  

    Cet exemple utilise une surcharge de **PullAsync()** qui permet de spécifier une clé de requête et une requête. La clé de requête est utilisée pour la synchronisation incrémentielle. Le kit de développement logiciel (SDK) Mobile Services suit la dernière modification après chaque opération d'extraction réussie. Dans l'opération suivante, seuls les nouveaux enregistrements seront récupérés. Si aucune clé de requête n'est spécifiée, une synchronisation complète sera effectuée pour la table de synchronisation.

## Étapes suivantes

Vous pouvez télécharger la version complète de ce didacticiel dans notre [référentiel d'exemples GitHub](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS).


<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Utilisation du client Xamarin Component pour Azure Mobile Services]

<!-- Anchors. -->
[Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion]: #enable-offline-app
[Test de l'application connectée au service mobile]: #test-online-app
[Étapes suivantes]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
[2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png



<!-- URLs. -->
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: /fr-fr/documentation/articles/mobile-services-xamarin-ios-handling-conflicts-offline-data/ 
[Prise en main des données]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
[Utilisation du client Xamarin Component pour Azure Mobile Services]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Kit de développement logiciel (SDK) Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
[Xamarin Studio]: http://xamarin.com/download
[Extension Xamarin]: http://xamarin.com/visual-studio
[Complément NuGet pour Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
