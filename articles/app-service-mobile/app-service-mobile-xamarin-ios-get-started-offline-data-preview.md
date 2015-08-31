<properties
    pageTitle="Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Xamarin iOS)"
    description="Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Xamarin iOS"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="08/11/2015"
    ms.author="wesmc"/>

# Activer la synchronisation hors connexion pour votre application mobile Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## Vue d’ensemble

Ce didacticiel présente la fonctionnalité de synchronisation hors connexion d’Azure Mobile Apps pour Xamarin.iOS. La synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu’il n’existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l’appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

Dans ce didacticiel, vous allez mettre à jour le projet d’application Xamarin.iOS du didacticiel [Création d’une application Xamarin iOS] pour prendre en charge les fonctionnalités hors connexion d’Azure Mobile Apps.

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## Configuration requise

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013
* [Extension Xamarin] ** Visual Studio ou ** [Xamarin Studio] sur OS X
* Suivi du didacticiel [Création d’une application Xamarin iOS]. Le présent didacticiel utilise l’application terminée abordée dans ce didacticiel.

##<a name="review"></a>Examiner la configuration de votre projet de serveur (facultatif)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## Examiner le code de synchronisation client

Le projet client Xamarin que vous avez téléchargé quand vous avez suivi le didacticiel [Création d’une application Xamarin iOS] contient déjà du code prenant en charge la synchronisation hors connexion à l’aide d’une base de données SQLite locale. Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel. Pour obtenir une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps].

* Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. La base de données du magasin local est initialisée quand `QSTodoListViewController.ViewDidLoad()` exécute `QSTodoService.InitializeStoreAsync()`. Cette opération crée une base de données SQLite locale à l’aide de la classe `MobileServiceSQLiteStore` fournie par le Kit de développement logiciel (SDK) client Azure Mobile App. 

	La méthode `DefineTable` crée une table dans le magasin local qui correspond aux champs dans le type fourni, `ToDoItem` ici. Ce type n’a pas besoin d’inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker uniquement un sous-ensemble de colonnes.

		// QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }


* Le membre `todoTable` de `QSTodoService` est du type `IMobileServiceSyncTable` au lieu d’`IMobileServiceTable`. Ainsi, toutes les opérations de table CRUD (Create, Read, Update et Delete) sont redirigées vers la base de données du magasin local.
 
	Vous décidez à quel moment ces modifications sont envoyées au backend d’application Azure Mobile App en appelant `IMobileServiceSyncContext.PushAsync()` à l’aide du contexte de synchronisation pour la connexion cliente. Le contexte de synchronisation permet de conserver les relations entre les tables grâce au suivi et à l’envoi des modifications dans toutes les tables qu’une application cliente modifie quand `PushAsync` est appelé.

	Le code fourni appelle `QSTodoService.SyncAsync()` pour effectuer une synchronisation chaque fois que la liste des éléments de tâche est actualisée ou qu’un élément de tâche est ajouté ou terminé. Ainsi, la synchronisation a lieu dès qu’une modification locale a exécuté un envoi sur le contexte de synchronisation et une extraction sur la table de synchronisation. Toutefois, si une extraction est exécutée sur une table qui comporte des mises à jour locales en attente faisant l’objet d’un suivi en fonction du contexte, cette opération déclenche automatiquement un envoi sur le contexte au préalable. Donc, dans pareil cas (actualisation, ajout et achèvement des éléments), vous pouvez omettre l’appel `PushAsync` explicite, car il est redondant.

    Dans le code fourni, tous les enregistrements de la table `TodoItem` distante sont interrogés, mais il est également possible de filtrer les enregistrements en transmettant un ID de requête et une requête à `PushAsync`. Pour plus d’informations, consultez la section *Synchronisation incrémentielle* dans [Synchronisation des données hors connexion dans Azure Mobile Apps].

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
	-->	
	// QSTodoService.cs

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


## Exécuter l’application cliente

Exécutez l’application cliente au moins une fois pour remplir la base de données du magasin local. Dans la section suivante, vous allez simuler un scénario hors connexion et modifier les données du magasin local pendant que l’application est hors connexion.


## Mettre à jour le comportement de synchronisation de l’application cliente

Dans cette section, vous allez modifier le projet client pour simuler un scénario hors connexion à l’aide d’une URL d’application non valide pour votre backend. Quand vous ajoutez ou modifiez des éléments de données, ces modifications sont conservées dans le magasin local, mais ne sont synchronisées avec le magasin de données principal qu’une fois la connexion rétablie.

1. Au début de `QSTodoService.cs`, modifiez l’initialisation des paramètres `applicationURL` et `gatewayURL` pour qu’ils pointent vers une URL non valide :

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; 
        const string gatewayURL = @"https://your-gateway.azurewebsites.xxx";


2. Ajoutez un autre `catch` pour la classe `Exception` dans `QSTodoService.SyncAsync`, destiné à écrire le message d’exception dans la console.

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
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
        }

3. Générez et exécutez l’application cliente. Ajouter de nouveaux éléments de tâche et notez l’exception consignée dans la console pour chaque tentative de synchronisation avec le backend d’application Mobile App. Ces nouveaux éléments se trouvent uniquement dans le magasin local jusqu’à ce qu’ils puissent être transmis par push vers le backend mobile. L’application cliente se comporte comme si elle est connectée au backend et prend en charge toutes les opérations de création, lecture, mise à jour et suppression.

4. Fermez l’application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.

5. (Facultatif) À l’aide de Visual Studio, affichez votre table Azure SQL Database pour constater que les données dans la base de données principale n’ont pas changé.

   Dans Visual Studio, ouvrez l’**Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table SQL Database et à son contenu.

6. (Facultatif) Utilisez un outil REST tel que Fiddler ou Postman pour interroger votre backend mobile, à l’aide d’une requête GET de la forme `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`. 

## Mettre à jour l’application cliente pour reconnecter votre backend mobile

Dans cette section, vous allez reconnecter l’application au backend mobile, ce qui simule le retour de l’application à un état en ligne. Quand vous effectuez le mouvement d’actualisation, les données sont synchronisées avec votre backend mobile.

1. Ouvrez `QSTodoService.cs`. Corrigez les paramètres `applicationURL` et `gatewayURL` pour les faire pointer vers les URL correctes.

2. Régénérez et exécutez l’application cliente. Après son lancement, l’application tente une synchronisation avec le backend d’application Azure Mobile App. Vérifiez qu’aucune exception n’est consignée dans la console de débogage.

3. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données ont été synchronisées entre la base de données principale Azure Mobile App et le magasin local.

    Notez que les données ont été synchronisées entre la base de données et le magasin local et qu’elles contiennent les éléments que vous avez ajoutés pendant que votre application était déconnectée.

## Ressources supplémentaires

* [Synchronisation des données hors connexion dans Azure Mobile Apps]

* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services] (remarque : le contexte de la vidéo est Mobile Services, mais la synchronisation hors connexion fonctionne de la même manière dans Azure Mobile Apps)

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Création d’une application Xamarin iOS]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Synchronisation des données hors connexion dans Azure Mobile Apps]: ../app-service-mobile-offline-data-sync-preview.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Extension Xamarin]: http://xamarin.com/visual-studio
 
[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=August15_HO8-->