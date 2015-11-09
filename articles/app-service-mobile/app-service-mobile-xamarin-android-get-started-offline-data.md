<properties
    pageTitle="Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Xamarin Android)"
    description="Découvrez comment utiliser App Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Xamarin Android"
    documentationCenter="xamarin"
    authors="wesmc7777"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
	ms.date="08/22/2015"
    ms.author="wesmc"/>

# Activation de la synchronisation hors connexion pour votre application mobile Xamarin Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Vue d'ensemble

Ce didacticiel présente la fonctionnalité de synchronisation hors connexion des applications mobiles Azure pour Xamarin.Android. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le service distant.

Dans ce didacticiel vous allez mettre à jour le projet client du didacticiel [Création d’une application Xamarin Android] pour prendre en charge les fonctionnalités hors connexion d’Azure Mobile Apps. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## Configuration requise

* Visual Studio 2013
* [Extension Xamarin] Visual Studio **ou** [Xamarin Studio]
* Réalisation du didacticiel [Création d’une application Xamarin Android]. Le présent didacticiel utilise l’application terminée abordée dans ce didacticiel.

## Examiner le code de synchronisation client

Le projet client Xamarin que vous avez téléchargé pendant l’exécution du didacticiel [Création d’une application Xamarin Android] contient déjà du code prenant en charge la synchronisation hors connexion à l’aide d’une base de données SQLite locale. Voici un bref aperçu de ce qui est déjà inclus dans le code du didacticiel. Pour obtenir une vue d’ensemble conceptuelle de la fonctionnalité, consultez [Synchronisation des données hors connexion dans Azure Mobile Apps].

* Avant de pouvoir effectuer des opérations de table, le magasin local doit être initialisé. La base de données du magasin local est initialisée quand `ToDoActivity.OnCreate()` exécute `ToDoActivity.InitLocalStoreAsync()`. Cette opération crée une base de données SQLite locale à l’aide de la classe `MobileServiceSQLiteStore` fournie par le Kit de développement logiciel (SDK) client Azure Mobile Apps. 
 
	La méthode `DefineTable` crée une table dans le magasin local qui correspond aux champs dans le type fourni, `ToDoItem` ici. Ce type n’a pas besoin d’inclure toutes les colonnes qui se trouvent dans la base de données distante. Il est possible de stocker uniquement un sous-ensemble de colonnes. // ToDoActivity.cs

        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync. For more details, see http://go.microsoft.com/fwlink/?LinkId=521416
            await client.SyncContext.InitializeAsync(store);
        }


* Le membre `toDoTable` de `ToDoActivity` est du type `IMobileServiceSyncTable` au lieu d’`IMobileServiceTable`. Ainsi, toutes les opérations de table CRUD (Create, Read, Update et Delete) sont redirigées vers la base de données du magasin local.
 
	Vous décidez à quel moment ces modifications sont envoyées au serveur principal Azure Mobile App en appelant `IMobileServiceSyncContext.PushAsync()` à l’aide du contexte de synchronisation pour la connexion cliente. Le contexte de synchronisation permet de conserver les relations entre les tables grâce au suivi et à l’envoi des modifications dans toutes les tables qu’une application cliente modifie quand `PushAsync` est appelé.

	Le code fourni appelle `ToDoActivity.SyncAsync()` pour effectuer une synchronisation chaque fois que la liste des éléments de tâche est actualisée ou qu’un élément de tâche est ajouté ou terminé. Ainsi, la synchronisation a lieu dès qu’une modification locale a exécuté un envoi sur le contexte de synchronisation et une extraction sur la table de synchronisation. Toutefois, si une extraction est exécutée sur une table qui comporte des mises à jour locales en attente faisant l’objet d’un suivi en fonction du contexte, cette opération déclenche automatiquement un envoi sur le contexte au préalable. Donc, dans ce cas (actualisation, ajout et achèvement des éléments), vous pouvez omettre l’appel `PushAsync` explicite. car il est redondant.

    Dans le code fourni, tous les enregistrements de la table `TodoItem` distante sont interrogés, mais il est également possible de filtrer les enregistrements en transmettant un ID de requête et une requête à `PushAsync`. Pour plus d’informations, consultez la section *Synchronisation incrémentielle* dans [Synchronisation des données hors connexion dans Azure Mobile Apps].

	<!-- Need updated conflict handling info : `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
-->	// ToDoActivity.cs

        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


## Exécuter l’application cliente

Exécutez l’application cliente au moins une fois pour remplir la base de données du magasin local. Dans la section suivante, vous allez simuler un scénario hors connexion et modifier les données du magasin local pendant que l’application est hors connexion.

## Mettre à jour le comportement de synchronisation de l’application cliente

Dans cette section, vous allez modifier l’application cliente pour simuler un scénario hors connexion à l’aide d’une URL d’application non valide pour votre backend. Quand vous ajoutez ou modifiez des éléments de données, ces modifications sont conservées dans le magasin local, mais ne sont synchronisées avec le magasin de données principal qu’une fois la connexion rétablie.

1. Au début de `ToDoActivity.cs`, modifiez l’initialisation des paramètres `applicationURL` et `gatewayURL` pour qu’ils pointent vers une URL non valide :

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; 
        const string gatewayURL = @"https://your-gateway.azurewebsites.xxx";


2. Mettez à jour `ToDoActivity.SyncAsync` afin que les `MobileServicePushFailedException` soient interceptées et simplement ignorées en supposant que vous êtes hors connexion.

        private async Task SyncAsync()
        {
			try {
	            await client.SyncContext.PushAsync();
	            await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
			} catch (Java.Net.MalformedURLException) {
				CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
			} catch (MobileServicePushFailedException)
            {
                // Not reporting this exception. Assuming the app is offline for now
            }
            catch (Exception e) {
				CreateAndShowDialog (e, "Error");
			}
        }


3. Générez et exécutez l'application. Si une boîte de dialogue d’exception signale une exception de résolution de nom, fermez-la. Ajoutez quelques nouveaux éléments todo et notez que l’application se comporte comme si elle était connectée, car `MobileServicePushFailedException` est géré sans afficher la boîte de dialogue.

4. Les nouveaux éléments que vous ajoutez se trouvent uniquement dans le magasin local jusqu’à ce qu’ils puissent être transmis par push vers le backend mobile. Fermez l'application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.

5. (Facultatif) À l’aide de Visual Studio, affichez votre table Azure SQL Database pour constater que les données dans la base de données principale n’ont pas changé.

   Dans Visual Studio, ouvrez l'**Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu.

6. (Facultatif) Utilisez un outil REST tel que Fiddler ou Postman pour interroger votre serveur principal mobile, à l’aide d’une requête GET de la forme `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`. 


## Mettre à jour l’application cliente pour reconnecter votre backend mobile

Dans cette section, vous allez reconnecter l'application au backend mobile, ce qui simule le retour de l'application à un état en ligne. Quand vous effectuez le mouvement d'actualisation, les données sont synchronisées avec votre backend mobile.

1. Ouvrez `ToDoActivity.cs`. Corrigez les paramètres `applicationURL` et `gatewayURL` pour les faire pointer vers les URL correctes.

2. Régénérez et exécutez l'application. Après son lancement, l’application tente une synchronisation avec le backend Azure Mobile App. Vérifiez qu’aucune boîte de dialogue d’exception n’est créée.

3. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données ont été synchronisées entre la base de données principale Azure Mobile App et le magasin local.

    Notez que les données ont été synchronisées entre la base de données et le magasin local et qu’elles contiennent les éléments que vous avez ajouté pendant une situation hors connexion.

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
[Création d’une application Xamarin Android]: ../app-service-mobile-xamarin-android-get-started.md
[Synchronisation des données hors connexion dans Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Extension Xamarin]: http://xamarin.com/visual-studio

[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri

<!---HONumber=Nov15_HO1-->