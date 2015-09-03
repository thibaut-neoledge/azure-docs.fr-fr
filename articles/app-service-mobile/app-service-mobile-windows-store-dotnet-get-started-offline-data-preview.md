<properties
	pageTitle="Activation de la synchronisation hors connexion pour votre application Azure Mobile App (Windows 8.1) | Microsoft Azure"
	description="Découvrez comment utiliser une application mobile Azure pour mettre en cache et synchroniser des données hors connexion dans votre application Windows Store"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="wesmc"/>

# Activation de la synchronisation hors connexion pour votre application Windows

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## Vue d'ensemble

Ce didacticiel explique comment prendre en charge le mode hors connexion dans une application Windows 8.1 Store ou Phone à l’aide d’un backend d’applications mobiles Azure. La synchronisation hors connexion permet aux utilisateurs finaux d'interagir avec une application mobile pour afficher, ajouter ou modifier des données, même lorsqu'il n'existe aucune connexion réseau. Les modifications sont stockées dans une base de données locale. Une fois l'appareil de nouveau en ligne, ces modifications sont synchronisées avec le backend distant.

Dans ce didacticiel, vous allez mettre à jour le projet d’application Windows 8.1 du didacticiel [Créer une application Windows] pour prendre en charge les fonctionnalités hors connexion d’Azure Mobile Apps.

Pour plus d’informations sur la fonctionnalité de synchronisation hors connexion, consultez la rubrique [Synchronisation des données hors connexion dans Azure Mobile Apps].

## Configuration requise

Ce didacticiel requiert les éléments suivants :

* exécution de Visual Studio 2013 sur Windows 8.1 ;
* finalisation de [Créer une application Windows][create a windows app].
* [Azure Mobile Services SQLite Store version 2.0.0-beta][sqlite store nuget]
* [SQLite pour Windows 8.1](http://www.sqlite.org/downloads)

>[AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.

## Examiner la configuration de votre projet de serveur (facultatif)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## Mettre à jour l’application cliente pour prendre en charge les fonctionnalités hors connexion

Les fonctionnalités hors connexion de l’application mobile Azure vous permettent d’interagir avec une base de données locale hors connexion. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez un `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `IMobileServiceSyncTable`. Dans ce didacticiel, nous utilisons SQLite pour le magasin local.

1. Installez le runtime SQLite pour Windows 8.1 et Windows Phone 8.1.

    * **Windows 8.1 Runtime :** installez [SQLite pour Windows 8.1].
    * **Windows Phone 8.1 :** installez [SQLite for Windows Phone 8.1].

    >[AZURE.NOTE]Si vous utilisez Internet Explorer et que vous cliquez sur le lien pour installer SQLite, vous serez peut-être invité à télécharger le fichier .vsix en tant que fichier .zip. Enregistrez le fichier à un emplacement sur votre disque dur avec l'extension .vsix plutôt que .zip. Ensuite, double-cliquez sur le fichier .vsix dans l'Explorateur Windows pour exécuter l'installation.

2. Dans Visual Studio, ouvrez le projet que vous avez finalisé dans le didacticiel [Créer une application Windows]. Installez le package NuGet **WindowsAzure.MobileServices.SQLiteStore** pour les projets Runtime Windows 8.1 et Windows Phone 8.1.

    Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution** pour exécuter le Gestionnaire de package NuGet. Sous l’onglet « En ligne », sélectionnez l’option « Inclure la version préliminaire » dans la liste déroulante située en haut. Recherchez **SQLiteStore** pour installer la version 2.0.0-beta de `WindowsAzure.MobileServices.SQLiteStore`.

    Ensuite, ajoutez la référence NuGet aux projets Windows Store 8.1 et Windows Phone 8.1.

    >[AZURE.NOTE]Si l’installation crée une référence supplémentaire à une autre version de SQLite que vous avez installée, vous aurez une erreur de compilation. Vous devez résoudre cette erreur en supprimant les doublons dans le nœud **Références** de vos projets.

3. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références** pour les projets de plateforme Runtime Windows 8.1 et Windows Phone 8.1, et vérifiez qu'il existe une référence à SQLite, qui doit se trouver dans la section **Extensions**.

    ![][1] </br>

    **Runtime Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite est une bibliothèque native et exige que vous choisissiez une architecture spécifique à la plateforme, comme **x86**, **x64** ou **ARM**. **Tout processeur** n'est pas pris en charge. Dans l'Explorateur de solutions, cliquez sur la solution en haut, puis modifiez la zone déroulante d'architecture de processeur en spécifiant l'un des paramètres pris en charge que vous souhaitez tester.

    ![][13]

5. Dans l'Explorateur de solutions, dans le projet partagé, ouvrez le fichier MainPage.cs. Supprimez les commentaires des instructions using suivantes situées en haut du fichier :

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. Dans MainPage.cs, commentez la ligne de code qui initialise `todoTable` en tant que `IMobileServiceTable`. Supprimez les commentaires de la ligne de code qui initialise `todoTable` en tant que `IMobileServiceSyncTable`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. Dans MainPage.cs, dans la zone marquée `Offline sync`, supprimez les commentaires des méthodes `InitLocalStoreAsync` et `SyncAsync`. La méthode `InitLocalStoreAsync` initialise le contexte de synchronisation du client avec un magasin SQLite. Dans Visual Studio, vous pouvez sélectionner toutes les lignes commentées et utiliser le raccourci clavier **CTRL**+**K**+**U** pour supprimer les commentaires.

	Notez que dans `SyncAsync` une opération push est exécutée à partir de `MobileServiceClient.SyncContext` plutôt que de `IMobileServicesSyncTable`. Il en est ainsi parce que le contexte suit les modifications apportées par le client à toutes les tables. Ceci permet de prendre en compte les scénarios dans lesquels des relations existent entre les tables. Pour plus d’informations, consultez la page [Synchronisation des données hors connexion dans Azure Mobile Apps].

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. Dans le gestionnaire d'événements `OnNavigatedTo`, supprimez le commentaire de l'appel vers `InitLocalStoreAsync` :

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. Supprimez les commentaires des 3 appels vers `SyncAsync` dans les méthodes `InsertTodoItem`, `UpdateCheckedTodoItem` et `ButtonRefresh_Click` :

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Ajoutez les gestionnaires d’exception à la méthode `SyncAsync` . Dans une situation hors connexion, une `MobileServicePushFailedException` sera levée avec `PushResult.Status == CancelledByNetworkError`.

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    Dans cet exemple `PullAsync`, nous récupérons tous les enregistrements du `todoTable` distant, mais nous aurions également pu filtrer les enregistrements en transmettant une requête. Le premier paramètre de `PullAsync` est un ID de requête utilisé pour la synchronisation incrémentielle, qui utilise l'horodatage `UpdatedAt` pour obtenir uniquement les enregistrements modifiés depuis la dernière synchronisation. L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application cliente. Pour refuser la synchronisation incrémentielle, passez `null` comme ID de requête. Vous récupérerez ainsi tous les enregistrements de chaque opération pull potentiellement inefficace.

    Notez que `MobileServicePushFailedException` peut survenir pour une opération push et une opération d'extraction. Elle peut se produire pour une extraction, car l'opération d'extraction exécute en interne une opération Push pour s'assurer que toutes les tables et toutes les relations sont cohérentes.

11. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l’application cliente. L'application se comportera comme avant la modification de la synchronisation hors connexion, car elle effectue une opération de synchronisation lors des opérations d'insertion, de mise à jour et d'actualisation. Elle remplira toutefois une base de données locale utilisable dans un scénario hors connexion. La base de données locale étant désormais remplie, dans la section suivante, nous allons générer un scénario hors connexion.

## <a name="update-sync"></a>Mise à jour du comportement de synchronisation de l’application cliente

Dans cette section, vous allez modifier l’application cliente pour simuler un scénario hors connexion en coupant votre connexion avec le backend d’applications Azure Mobile App. Lorsque vous ajouterez des éléments de données, votre gestionnaire d’exceptions signalera que l’application fonctionne en mode hors connexion avec `PushResult.Status == CancelledByNetworkError`. Les éléments ajoutés seront stockés dans le magasin local et ne seront synchronisés avec le backend d’applications mobiles que quand vous serez à nouveau en ligne et après l’exécution d’un push vers le backend d’applications mobiles Azure Mobile App.

1. Modifiez App.xaml.cs dans le projet partagé. Mettez en commentaire l’initialisation de **MobileServiceClient** et ajoutez les lignes suivantes qui utilisent une URL d’application mobile incorrecte :

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-service.azurewebsites.fail",
            "https://your-gateway.azurewebsites.fail",
            ""
        );

2. Appuyez sur **F5** pour générer et exécuter l'application. Notez que la synchronisation a échoué lors de l’actualisation au lancement de l’application.
3. Entrez quelques nouveaux éléments todo et cliquez sur **Enregistrer** pour chacun d'eux. L’exécution des push a échoué pour chacun d’eux avec un `PushResult.Status=CancelledByNetworkError`. Les nouveaux éléments todoitem n’existent que dans le magasin local tant qu’ils ne sont pas transmis au backend d’applications mobiles. 
 
	Vous pourriez supprimer la boîte de dialogue d’exception pour `PushResult.Status=CancelledByNetworkError`. l’application cliente se comporterait alors comme si elle était connectée au backend d’applications mobiles et prenait en charge toutes les opérations de création, de lecture, de mise à jour et de suppression (CRUD) de manière transparente.

4. Fermez l'application et redémarrez-la pour vérifier que les nouveaux élément que vous avez créés sont conservés dans le magasin local.

5. (Facultatif) À l’aide de Visual Studio, affichez votre table Azure SQL Database pour constater que les données dans la base de données principale n’ont pas changé.

   Dans Visual Studio, ouvrez l'**Explorateur de serveurs**. Accédez à votre base de données dans **Azure**->**Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**. Maintenant, vous pouvez accéder à votre table de base de données SQL et à son contenu.

6. (Facultatif) Utilisez un outil REST tel que Fiddler ou Postman pour interroger votre backend mobile, à l’aide d’une requête GET de la forme `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem`. 

## <a name="update-online-app"></a>Mise à jour de l’application pour la reconnecter à votre backend d’applications mobiles

Dans cette section, vous reconnectez l’application au backend d’applications mobiles. Cette opération simule le passage de l’application d’un état hors connexion à un état connecté au backend d’applications mobiles. Lors de la première exécution de l’application, le gestionnaire d’événements `OnNavigatedTo` appellera `InitLocalStoreAsync`. Cela appellera alors `SyncAsync` pour synchroniser votre magasin local avec la base de données du backend. L’application tentera alors une synchronisation au démarrage.

1. Ouvrez App.xaml.cs dans le projet partagé. Supprimez les commentaires de votre précédente initialisation de `MobileServiceClient` pour utiliser les URL correctes de l’application mobile et de la passerelle.

2. Appuyez sur **F5** pour régénérer et exécuter l'application. L’application synchronise vos modifications locales avec le backend d’applications Azure Mobile App par des opérations push et pull dès que le gestionnaire d’événements `OnNavigatedTo` s’exécute.

3. (Facultatif) Affichez les données mises à jour à l’aide de l’Explorateur d’objets SQL Server ou d’un outil REST tel que Fiddler. Notez que les données ont été synchronisées entre la base de données principale Azure Mobile App et le magasin local.

4. Dans l'application, cochez la case en regard de quelques éléments pour les exécuter dans le magasin local.

  `UpdateCheckedTodoItem` appelle `SyncAsync` pour synchroniser chaque élément avec le backend d’applications mobiles. `SyncAsync` appelle push et pull. Il convient toutefois de noter que **chaque fois que vous exécuterez un pull sur une table modifiée par le client, un push préalable sur le contexte de synchronisation du client sera toujours effectué automatiquement**. Cela a pour but de garantir que toutes les tables du magasin local, ainsi que les relations, restent cohérentes. Nous aurions donc pu supprimer l’appel à `PushAsync`, car il intervient automatiquement lors de l’exécution d’un pull. Ce comportement peut entraîner un push inattendu si vous n’en n’êtes pas conscient. Pour plus d’informations, consultez la page [Synchronisation des données hors connexion dans Azure Mobile Apps].


##Résumé

Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l'interface `IMobileServiceSyncTable` et initialisé `MobileServiceClient.SyncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données SQLite.

Les opérations CRUD normales pour les services mobiles fonctionnent comme si l'application était toujours connectée, mais toutes les opérations ont lieu sur base du magasin local.

Lorsque nous avons voulu synchroniser le magasin local avec le serveur, nous avons utilisé les méthodes `IMobileServiceSyncTable.PullAsync` et `MobileServiceClient.SyncContext.PushAsync`.

*  Pour transmettre par push les modifications au serveur, nous avons appelé `IMobileServiceSyncContext.PushAsync()`. Cette méthode est membre de `IMobileServicesSyncContext` à la place de la table de synchronisation parce qu’elle envoie par Push les modifications sur toutes les tables.

    Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur.

* Pour envoyer par pull les données d’une table sur le serveur vers l’application, nous avons appelé `IMobileServiceSyncTable.PullAsync`.

    Un pull exécute toujours un push de contexte de synchronisation préalable si le contexte de synchronisation du client comprend un suivi des modifications sur cette table. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes.

    Il existe également des surcharges de `PullAsync()` qui permettent de spécifier une requête afin de filtrer les données stockées sur le client. Si une requête n'est pas transmise, `PullAsync()` extrait toutes les lignes de la table (ou requête) correspondante. Vous pouvez transmettre la requête pour filtrer uniquement les modifications avec lesquelles votre application doit se synchroniser.

* Pour activer la synchronisation incrémentielle, transmettez un ID de requête à `PullAsync()`. L’ID de requête est utilisé pour stocker le dernier horodatage des résultats de la dernière opération d’extraction. L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Si la requête possède un paramètre, alors la même valeur de paramètre pourrait faire partie de l’ID de requête.

    Par exemple, si vous filtrez sur le nom d’utilisateur, votre ID de requête unique peut être :

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Si vous souhaitez désactiver la synchronisation incrémentielle, transmettez `null` en tant qu'ID de requête. Dans ce cas, tous les enregistrements seront extraits à chaque appel à `PullAsync`, ce qui est potentiellement inefficace.

* Votre application doit appeler périodiquement `IMobileServiceSyncTable.PurgeAsync()` pour purger le magasin local.


## Ressources supplémentaires

* [Synchronisation des données hors connexion dans Azure Mobile Apps]

* [Cloud Cover : synchronisation hors connexion dans Azure Mobile Services] \(remarque : le contexte de la vidéo est Mobile Services, mais la synchronisation hors connexion fonctionne de la même manière dans Azure Mobile Apps)

* [Azure Friday : applications prenant en charge le mode hors connexion dans Azure Mobile Services]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[Synchronisation des données hors connexion dans Azure Mobile Apps]: ../app-service-mobile-offline-data-sync-preview.md
[create a windows app]: ../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
[Créer une application Windows]: ../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
[SQLite pour Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[sqlite for windows phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/2.0.0-beta
 
[Cloud Cover : synchronisation hors connexion dans Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday : applications prenant en charge le mode hors connexion dans Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!----HONumber=August15_HO8-->