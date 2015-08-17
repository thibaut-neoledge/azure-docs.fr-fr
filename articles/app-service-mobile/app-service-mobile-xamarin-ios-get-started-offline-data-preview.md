<properties
    pageTitle="Activation de la synchronisation hors connexion pour votre application mobile (Xamarin iOS)"
    description="Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Xamarin iOS"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="07/01/2015"
    ms.author="donnam"/>

# Activation de la synchronisation hors connexion pour votre application mobile Xamarin iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]

Ce didacticiel traite de la fonctionnalité de synchronisation hors connexion de Mobile Apps pour iOS. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

La synchronisation hors connexion possède plusieurs utilisations potentielles :

* Améliorer la réactivité de l'application en mettant en cache les données de serveur localement sur l'appareil
* Rendre les applications résistantes à une connectivité réseau intermittente
* Permettre aux utilisateurs finaux de créer et de modifier des données même en l'absence d'accès au réseau, prenant ainsi en charge des scénarios avec une connectivité faible ou nulle
* Synchroniser des données sur plusieurs appareils et détecter des conflits lorsque le même enregistrement est modifié par deux appareils

Si vous n’avez aucune expérience de Mobile Apps, commencez par suivre le didacticiel [Création d’une application Xamarin iOS].

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013
* [Extension Xamarin] ** Visual Studio ou ** [Xamarin Studio] sur OS X

##<a name="review"></a>Examiner la configuration de votre projet de serveur (facultatif)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## Examen du code de synchronisation Mobile App

La synchronisation hors connexion Mobile App permet aux utilisateurs d'interagir avec une base de données locale quand le réseau n'est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l’interface `IMobileServiceSyncTable`. Cette section décrit le code associé à la synchronisation hors connexion dans `QSTodoService.cs`.

1. Dans Visual Studio, ouvrez le projet que vous avez terminé dans le didacticiel [Prise en main de Mobile Apps]. Ouvrez le fichier `QSTodoService.cs`.

2. Notez que le type du membre `todoTable` est `IMobileServiceSyncTable`. La synchronisation hors connexion utilise cette interface de table de synchronisation à la place de `IMobileServiceTable`. Lorsqu'une table de synchronisation est utilisée, toutes les opérations vont vers le magasin local et sont uniquement synchronisées avec le service distant à l'aide d'opérations push et pull explicites.

    La méthode `GetSyncTable()` permet d’obtenir une référence à une table de synchronisation. Pour supprimer la fonctionnalité de synchronisation hors connexion, vous utiliseriez plutôt `GetTable()`.

3. Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. Cette opération est effectuée dans la méthode `InitializeStoreAsync` :

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Ceci crée un magasin local à l’aide de la classe `MobileServiceSQLiteStore`, qui est fournie dans le Kit de développement logiciel (SDK) Mobile App. Vous pouvez également fournir une implémentation de magasin local différente en implémentant `IMobileServiceLocalStore`.

    La méthode `DefineTable` crée une table dans le magasin local qui correspond aux champs dans le type fourni, `ToDoItem` ici. Ce type n'a pas besoin d'inclure toutes les colonnes qui se trouvent dans la base de données distante, il est possible de stocker uniquement un sous-ensemble de colonnes.

<!--     This overload of `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
 -->
4. La méthode `SyncAsync` déclenche l’opération de synchronisation réelle :

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    Tout d’abord, un appel à `IMobileServiceSyncContext.PushAsync()` est effectué. Cette méthode est membre de `IMobileServicesSyncContext` à la place de la table de synchronisation parce qu’elle envoie par Push les modifications sur toutes les tables. Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur.

    Ensuite, la méthode appelle `IMobileServiceSyncTable.PullAsync()` pour extraire les données d’une table sur le serveur vers l’application. Notez que s'il existe des modifications en attente dans le contexte de synchronisation, une opération pull commence toujours par émettre une opération push. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes. Ici, nous avons appelé l'opération push explicitement.

    Dans cet exemple, nous récupérons tous les enregistrements de la table `TodoItem` distante, mais il est également possible de filtrer les enregistrements en transmettant une requête. Le premier paramètre passé à `PullAsync()` est un ID de requête qui est utilisé pour la synchronisation incrémentielle. Il utilise l’horodatage `UpdatedAt` pour obtenir uniquement les enregistrements modifiés depuis la dernière synchronisation. L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Pour refuser la synchronisation incrémentielle, passez `null` comme ID de requête. Vous récupérerez ainsi tous les enregistrements de chaque opération pull potentiellement inefficace.

<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `IMobileServiceSyncTable.PurgeAsync()` to purge the local store.

    Note that the `MobileServicePushFailedException` can occur for both a push and a pull operation. The next tutorial, [Handling conflicts with offline support for Mobile Services], shows how to handle these sync related exceptions.
-->

5. Dans la classe `QSTodoService`, la méthode `SyncAsync()` est appelée après les opérations qui modifient les données, `InsertTodoItemAsync()` et `CompleteItemAsync`. Elle est également appelée à partir de `RefreshDataAsync()`, pour que l’utilisateur obtienne les données les plus récentes chaque fois qu’il effectue le mouvement d’actualisation. L’application exécute également une synchronisation au démarrage, puisque `QSTodoListViewController.ViewDidLoad()` appelle `RefreshDataAsync()`.

    Étant donné que `SyncAsync()` est appelée chaque fois que les données sont modifiées, cette application suppose que l’utilisateur est en ligne chaque fois qu’il modifie des données. Dans la section suivante, nous mettrons à jour l'application pour que les utilisateurs puissent effectuer des modifications même lorsqu'ils ne sont pas connectés.

## Mise à jour du comportement de synchronisation de l’application

Dans cette section, vous allez modifier l'application pour qu'elle ne se synchronise ni lors de son démarrage, ni lors des opérations d'insertion et de mise à jour, mais uniquement lors d'une actualisation. Ensuite, vous allez rompre la connexion de l'application avec le backend mobile pour simuler un scénario hors connexion. Quand vous ajoutez des éléments de données, ils sont stockés dans le magasin local, mais ils ne sont pas synchronisés immédiatement vers le magasin de données du backend mobile.

1. Ouvrez `QSTodoService.cs`. Commentez les appels à `SyncAsync()` dans les méthodes suivantes :

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    Désormais, `RefreshAsync()` chargera uniquement les données à partir du magasin local, mais ne se connectera pas au serveur principal de l’application.

2. Dans `QSTodoService.cs`, modifiez la définition de `applicationURL` pour pointer vers un URI d’application mobile non valide :

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. Pour vous assurer que les données sont synchronisées lorsque le mouvement d’actualisation est effectué, modifiez la méthode `QSTodoListViewController.RefreshAsync()`. Ajoutez un appel à `SyncAsync()` avant l’appel à `RefreshDataAsync()` :

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Générez et exécutez l'application. Ajoutez de nouveaux éléments todo. Ces nouveaux éléments se trouvent uniquement dans le magasin local jusqu'à ce qu'ils puissent être transmis par push vers le backend mobile. L'application cliente se comporte comme si elle est connectée au backend et prend en charge toutes les opérations de création, lecture, mise à jour et suppression.

5. Fermez l'application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.

## Mise à jour de l'application pour reconnecter votre backend mobile

Dans cette section, vous allez reconnecter l'application au backend mobile, ce qui simule le retour de l'application à un état en ligne. Quand vous effectuez le mouvement d'actualisation, les données sont synchronisées avec votre backend mobile.

1. Ouvrez `QSTodoService.cs`. Supprimez l'URL d'application mobile non valide et rajoutez la clé d'application et l'URL appropriées.

2. Régénérez et exécutez l'application. Notez que les données n'ont pas changé, même si l'application est à présent connectée au backend mobile. Cela s’explique par le fait que l’application utilise toujours la `IMobileServiceSyncTable` pointant vers le magasin local.

3. Connectez-vous à la base de données SQL de votre backend pour afficher les données qui ont été stockées. Dans Visual Studio, accédez à **Explorateur de serveurs** -> **Azure** -> **Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**.

    Notez que les données n’ont *pas* été synchronisées entre la base de données et le magasin local.

4. Dans l'application, effectuez le geste d'actualisation en tirant la liste des éléments vers le bas. Ainsi, l’application appelle `RefreshDataAsync()`, qui appelle `SyncAsync()` à son tour. Les opérations push et pull sont effectuées, tout d'abord pour envoyer les éléments du magasin local vers le backend mobile, puis pour récupérer les nouvelles données à partir du backend.

5. Actualisez la vue de base de données et confirmez que les modifications ont été synchronisées.

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Création d’une application Xamarin iOS]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Extension Xamarin]: http://xamarin.com/visual-studio
 

<!---HONumber=August15_HO6-->