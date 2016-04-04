<properties
	pageTitle="Utilisation des données hors connexion dans Mobile Services (Xamarin iOS) | Microsoft Azure"
	description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application"
	documentationCenter="xamarin"
	authors="lindydonna"
	editor="wesmc"
	manager="dwrede"
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="donnam"/>

# Utilisation de la synchronisation des données hors connexion dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Activation de la synchronisation hors connexion pour votre application mobile Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started-offline-data.md).

Cette rubrique décrit les fonctionnalités de synchronisation hors connexion d'Azure Mobile Services dans l'application de démarrage rapide todo list. La synchronisation hors connexion vous permet de créer facilement des applications qui sont utilisables même lorsque l'utilisateur final n'a pas accès au réseau.

La synchronisation hors connexion possède plusieurs utilisations potentielles :

* Améliorer la réactivité de l'application en mettant en cache les données de serveur localement sur l'appareil
* Rendre les applications résistantes à une connectivité réseau intermittente
* Permettre aux utilisateurs finaux de créer et de modifier des données même en l'absence d'accès au réseau, prenant ainsi en charge des scénarios avec une connectivité faible ou nulle
* Synchroniser des données sur plusieurs appareils et détecter des conflits lorsque le même enregistrement est modifié par deux appareils

>[AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10&nbsp;services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.
>
> Si vous n'avez aucune expérience de Mobile Services, commencez par suivre entièrement le didacticiel [Prise en main de Mobile Services].

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Examiner le code de synchronisation de Mobile Services]
2. [Mettre à jour le comportement de synchronisation de l'application]
3. [Mettre à jour l'application pour reconnecter votre service mobile]

Ce didacticiel requiert les éléments suivants :

* Visual Studio avec l'[extension Xamarin] **ou** [Xamarin Studio] sur OS X
* XCode 4.5 et iOS 6.0 (ou versions ultérieures)
* Exécution du didacticiel [Prise en main de Mobile Services]

## <a name="review-offline"></a>Examiner le code de synchronisation de Mobile Services

La synchronisation hors connexion d'Azure Mobile Services permet aux utilisateurs d'interagir avec une base de données locale lorsque le réseau n'est pas accessible. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l’interface `IMobileServiceSyncTable`. Cette section décrit le code associé à la synchronisation hors connexion dans `QSTodoService.cs`.

1. Dans Visual Studio, ouvrez le projet mené à bien dans le didacticiel [Prise en main de Mobile Services]. Ouvrez le fichier `QSTodoService.cs`.

2. Notez que le type du membre `todoTable` est `IMobileServiceSyncTable`. La synchronisation hors connexion utilise cette interface de table de synchronisation à la place de `IMobileServiceTable`. Lorsqu'une table de synchronisation est utilisée, toutes les opérations vont vers le magasin local et sont uniquement synchronisées avec le service distant à l'aide d'opérations push et pull explicites.

    La méthode `GetSyncTable()` permet d’obtenir une référence à une table de synchronisation. Pour supprimer la fonctionnalité de synchronisation hors connexion, vous utiliseriez plutôt `GetTable()`.

3. Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. Cette opération est effectuée dans la méthode `InitializeStoreAsync` :

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Ceci crée un magasin local à l'aide de la classe `MobileServiceSQLiteStore`, qui est fournie dans le Kit de développement logiciel (SDK) Mobile Services. Vous pouvez également fournir une autre implémentation de magasin local en mettant en œuvre `IMobileServiceLocalStore`.

    La méthode `DefineTable` crée une table dans le magasin local qui correspond aux champs dans le type fourni, `ToDoItem` ici. Ce type n'a pas besoin d'inclure toutes les colonnes qui se trouvent dans la base de données distante, il est possible de stocker uniquement un sous-ensemble de colonnes.

    Cette surcharge de `InitializeAsync` utilise le gestionnaire de conflits par défaut qui échoue en cas de conflit. Pour obtenir un gestionnaire de conflits personnalisé, consultez le didacticiel [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services].

4. La méthode `SyncAsync` déclenche l'opération de synchronisation réelle :

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

    >[AZURE.NOTE] Pour supprimer des enregistrements du magasin local du périphérique lorsqu'ils ont été supprimés dans la base de données de votre service mobile, vous devez activer la [Suppression réversible]. Sinon, votre application doit appeler périodiquement `IMobileServiceSyncTable.PurgeAsync()` pour vider le magasin local.

    Notez que `MobileServicePushFailedException` peut survenir pour une opération push et une opération d'extraction. Le didacticiel suivant, [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services], montre comment gérer ces exceptions relatives à la synchronisation.

5. Dans la classe `QSTodoService`, la méthode `SyncAsync()` est appelée après les opérations qui modifient les données, `InsertTodoItemAsync()` et `CompleteItemAsync`. Elle est également appelée à partir de `RefreshDataAsync()`, pour que l’utilisateur obtienne les données les plus récentes chaque fois qu’il effectue le mouvement d’actualisation. L’application exécute également une synchronisation au démarrage, puisque `QSTodoListViewController.ViewDidLoad()` appelle `RefreshDataAsync()`.

    Étant donné que `SyncAsync()` est appelée chaque fois que les données sont modifiées, cette application suppose que l’utilisateur est en ligne chaque fois qu’il modifie des données. Dans la section suivante, nous mettrons à jour l'application pour que les utilisateurs puissent effectuer des modifications même lorsqu'ils ne sont pas connectés.

## <a name="update-sync"></a>Mettre à jour le comportement de synchronisation de l'application

Dans cette section, vous allez modifier l'application pour qu'elle ne se synchronise ni lors de son démarrage, ni lors des opérations d'insertion et de mise à jour, mais uniquement lors d'une actualisation. Ensuite, vous allez rompre la connexion de l'application avec le service mobile pour simuler un scénario hors connexion. Lorsque vous ajoutez des éléments data, ils seront stockés dans le magasin local, sans être immédiatement synchronisés vers le service mobile.

1. Ouvrez `QSTodoService.cs`. Commentez les appels à `SyncAsync()` dans les méthodes suivantes :

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshDataAsync`

    Désormais, `RefreshAsync()` chargera uniquement les données à partir du magasin local, mais ne se connectera pas au backend de l'application.

2. Dans `QSTodoService.cs`, commentez les définitions des membres `applicationURL` et `applicationKey`. Ajoutez les lignes suivantes, qui font référence à une URL de service mobile incorrecte :

        const string applicationURL = @"https://your-mobile-service.azure-mobile.xxx/";
        const string applicationKey = @"AppKey";

3. Pour vous assurer que les données sont synchronisées lors de la demande d'actualisation, modifiez la méthode `QSTodoListViewController.RefreshAsync()`. Ajoutez un appel à `SyncAsync()` avant l’appel à `RefreshDataAsync()` :

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Générez et exécutez l'application. Ajoutez de nouveaux éléments todo. Ces nouveaux éléments todo se trouvent uniquement dans le magasin local s'ils peuvent être transmis par Push vers le service mobile. L'application cliente se comporte comme si elle était connectée au service mobile et supportait toutes les opérations CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

5. Fermez l'application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.

## <a name="update-online-app"></a>Mettre à jour l'application pour reconnecter votre service mobile

Dans cette section, vous allez reconnecter l'application au service mobile. Cette opération simule le passage de l'application d'un état hors connexion à un état connecté avec le service mobile. Lorsque vous effectuez une actualisation, les données seront synchronisées avec votre service mobile.

1. Ouvrez `QSTodoService.cs`. Supprimez l'URL de service mobile incorrecte et rajoutez la clé d'application et l'URL adéquates.

2. Régénérez et exécutez l'application. Notez que les données ont le même aspect que dans le scénario hors connexion, bien que l'application soit à présent connectée au service mobile. Cela s’explique par le fait que l’application utilise toujours la `IMobileServiceSyncTable` pointant vers le magasin local.

3. Connectez-vous au [portail Azure Classic] et examinez la base de données de votre service mobile. Si votre service utilise le backend JavaScript, vous pouvez accéder aux données à partir de l'onglet **Données** du service mobile.

    Si vous utilisez le backend .NET pour votre service mobile, dans Visual Studio, accédez à **Explorateur de serveurs** -> **Azure** -> **Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**.

    Notez que les données n’ont *pas* été synchronisées entre la base de données et le magasin local.

4. Dans l'application, effectuez le geste d'actualisation en tirant la liste des éléments vers le bas. Ainsi, l'application appelle `RefreshDataAsync()`, qui à son tour appelle `SyncAsync()`. Les opérations push et pull sont effectuées, tout d'abord pour envoyer les éléments du magasin local vers le service mobile, puis pour récupérer les nouvelles données à partir du service.

5. Contrôlez la base de données de votre service mobile pour confirmer que les modifications ont été synchronisées.

##Résumé

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Étapes suivantes

* [Utilisation du client Xamarin Component pour Azure Mobile Services]

<!-- Anchors. -->
[Examiner le code de synchronisation de Mobile Services]: #review-offline
[Mettre à jour le comportement de synchronisation de l'application]: #update-sync
[Mettre à jour l'application pour reconnecter votre service mobile]: #update-online-app

<!-- Images -->

<!-- URLs. -->
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: mobile-services-xamarin-ios-handling-conflicts-offline-data.md
[Prise en main de Mobile Services]: mobile-services-ios-get-started.md
[Utilisation du client Xamarin Component pour Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Suppression réversible]: mobile-services-using-soft-delete.md

[Xamarin Studio]: http://xamarin.com/download
[extension Xamarin]: http://xamarin.com/visual-studio
[portail Azure Classic]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0323_2016-->