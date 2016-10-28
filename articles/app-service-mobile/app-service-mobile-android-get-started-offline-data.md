<properties
	pageTitle="Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Android)"
	description="Découvrez comment utiliser Service Mobile App pour mettre en cache et synchroniser des données hors connexion dans votre application Android"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# Activation de la synchronisation hors connexion pour votre application mobile Android

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## Vue d'ensemble

Ce didacticiel traite de la fonctionnalité de synchronisation hors connexion d’Azure Mobile Apps pour Android. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le backend distant.

Si vous n’avez aucune expérience d’Azure Mobile Apps, vous devez commencer par suivre le didacticiel [Création d’une application Android]. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter les packages d’extension d’accès aux données à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## Mettre à jour l’application pour prendre en charge la synchronisation hors connexion

Avec la synchronisation hors connexion, vous disposez d’un accès en lecture et en écriture à partir d’une *table de synchronisation* (à l’aide de l’interface *IMobileServiceSyncTable*), qui fait partie d’une base de données **SQLite** sur votre appareil.

Pour envoyer et extraire des modifications entre l’appareil et Azure Mobile Services, vous utilisez un *contexte de synchronisation* (*MobileServiceClient.SyncContext*), que vous initialisez avec la base de données locale utilisée pour stocker des données localement.

1. Dans `TodoActivity.java`, commentez la définition existante de `mToDoTable` et supprimez les marques de commentaires de la version de table de synchronisation :

	    private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. Dans la méthode `onCreate`, commentez l’initialisation existante de `mToDoTable` et supprimez les marques de commentaires de cette définition :

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. Dans `refreshItemsFromTable` commentez la définition de `results` et supprimez les marque de commentaires de cette définition :

		// Offline Sync
		final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Commentez la définition de `refreshItemsFromMobileServiceTable`.

5. Supprimez les marques de commentaires de la définition de `refreshItemsFromMobileServiceTableSyncTable` :

	    private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
	        //sync the data
	        sync().get();
	        Query query = QueryOperations.field("complete").
	                eq(val(false));
	        return mToDoTable.read(query).get();
	    }

6. Supprimez les marques de commentaires de la définition de `sync` :

	    private AsyncTask<Void, Void, Void> sync() {
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
	                    MobileServiceSyncContext syncContext = mClient.getSyncContext();
	                    syncContext.push().get();
	                    mToDoTable.pull(null).get();
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	        return runAsyncTask(task);
	    }

## Test de l'application

Dans cette section, vous testez le comportement de l’application avec le Wi-Fi activé, puis vous désactivez le Wi-Fi pour créer un scénario hors connexion.

Lorsque vous ajoutez des éléments de données, ils sont stockés dans le magasin local SQLite, mais ils ne sont synchronisés avec le service mobile que si vous appuyez sur le bouton **Actualiser**. D’autres applications peuvent avoir des besoins différents concernant la fréquence de synchronisation des données, mais à des fins de démonstration, l’utilisateur doit en faire explicitement la requête dans le cadre de ce didacticiel.

Lorsque vous appuyez sur ce bouton, une nouvelle tâche démarre en arrière-plan, et toutes les modifications apportées sont d’abord envoyées dans le magasin local, via le contexte de synchronisation. Ensuite, toutes les données modifiées sont extraites d’Azure vers la table locale.

### Test hors connexion

1. Mettez l’appareil ou le simulateur en *Mode avion*. Cela crée un scénario hors connexion.

2. Ajoutez des *tâches*, ou marquez-en certaines comme terminées. Quittez l’appareil ou le simulateur (ou forcez la fermeture de l’application) et redémarrez. Vérifiez que vos modifications persistent sur l’appareil, car elles sont stockées dans le magasin SQLite local.

3. Affichez le contenu de la table *TodoItem* d’Azure avec un outil SQL, par exemple *SQL Server Management Studio* ou un client REST, comme *Fiddler* ou *Postman*. Vérifiez que les nouveaux éléments n’ont _pas_ été synchronisés avec le serveur

   	+ Pour un backend Node.js, accédez au [portail Azure](https://portal.azure.com/), puis dans votre backend d’application mobile, cliquez sur **Tables faciles** > **TodoItem** pour afficher le contenu de la table `TodoItem`.
   	+ Pour un backend .NET, affichez le contenu de la table avec un outil SQL, par exemple *SQL Server Management Studio* ou un client REST, comme *Fiddler* ou *Postman*.

4. Activez le Wi-Fi sur le simulateur ou l’appareil. Appuyez ensuite sur le bouton **Actualiser**.

5. Affichez à nouveau les données TodoItem sur le portail Azure. Les nouveaux éléments TodoItems et ceux modifiés doivent maintenant s'afficher.

## Ressources supplémentaires

* [Synchronisation des données hors connexion dans Azure Mobile Apps]

* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services] (remarque : le contexte de la vidéo est Mobile Services, mais la synchronisation hors connexion fonctionne de la même manière dans Azure Mobile Apps)


<!-- URLs. -->

[Synchronisation des données hors connexion dans Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

[Création d’une application Android]: ../app-service-mobile-android-get-started.md

[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0727_2016-->