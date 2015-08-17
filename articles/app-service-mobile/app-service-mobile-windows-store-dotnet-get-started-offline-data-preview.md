<properties
	pageTitle="Utilisation de données hors connexion dans une application mobile Azure (Windows Store) | Centre de développement mobile"
	description="Découvrez comment utiliser une application mobile Azure pour mettre en cache et synchroniser des données hors connexion dans votre application Windows Store"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Utilisation de la synchronisation des données hors connexion dans des applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


Ce didacticiel explique comment prendre en charge le mode hors connexion dans une application universelle Windows Store à l’aide d’un backend d’applications mobiles Azure. La prise en charge du mode hors connexion vous permet d’interagir avec une base de données locale lorsque votre application est hors connexion. Une fois que votre application est en ligne avec la base de données principale, vous synchronisez vos modifications locales à l'aide des fonctionnalités hors connexion.



Dans ce didacticiel vous allez mettre à jour le projet d’application universelle du didacticiel [Créer une application Windows] pour prendre en charge les fonctionnalités hors connexion des applications mobiles Azure. Ensuite, vous allez ajouter les données dans le cadre d'un scénario hors connexion déconnecté, synchroniser ces éléments dans la base de données en ligne, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Mettre à jour l'application pour prendre en charge les fonctionnalités hors connexion]
2. [Mettre à jour le comportement de synchronisation de l'application]
3. [Mise à jour de l’application pour la reconnecter à votre backend d’applications mobiles]

Ce didacticiel requiert les éléments suivants :

* exécution de Visual Studio 2013 sur Windows 8.1 ;
* finalisation de [Créer une application Windows][create a windows app].
* [Kit de développement logiciel (SDK) Azure Mobile Services version 2.0.0 (ou version ultérieure)][azure mobile app sdk nuget]
* [Azure Mobile Services SQLite Store version 1.0.2 (ou version ultérieure)][sqlite store nuget]
* [SQLite pour Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.

##<a name="review"></a>Examiner la configuration de votre projet de serveur (facultatif)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## <a name="enable-offline-app"></a>Mettre à jour l'application pour prendre en charge les fonctionnalités hors connexion

Les fonctionnalités hors connexion de l’application mobile Azure vous permettent d’interagir avec une base de données locale lorsque votre service mobile est hors connexion. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez un `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `IMobileServiceSyncTable`. Dans ce didacticiel, nous utilisons SQLite pour le magasin local.

1. Installez le runtime SQLite pour Windows 8.1 et Windows Phone 8.1.

    * **Windows 8.1 Runtime :** installez [SQLite pour Windows 8.1].
    * **Windows Phone 8.1 :** installez [SQLite for Windows Phone 8.1].

    >[AZURE.NOTE]Si vous utilisez Internet Explorer et que vous cliquez sur le lien pour installer SQLite, vous serez peut-être invité à télécharger le fichier .vsix en tant que fichier .zip. Enregistrez le fichier à un emplacement sur votre disque dur avec l'extension .vsix plutôt que .zip. Ensuite, double-cliquez sur le fichier .vsix dans l'Explorateur Windows pour exécuter l'installation.

2. Dans Visual Studio, ouvrez le projet que vous avez finalisé dans le didacticiel [Créer une application Windows]. Installez le package NuGet **WindowsAzure.MobileServices.SQLiteStore** pour les projets Runtime Windows 8.1 et Windows Phone 8.1.

    * **Windows 8.1 :** dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet Windows 8.1, puis cliquez sur **Gérer les packages NuGet** pour exécuter le Gestionnaire de package NuGet. Recherchez **SQLiteStore** pour installer le package `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1 :** cliquez avec le bouton droit sur le projet Windows Phone 8.1, puis cliquez sur **Gérer les packages Nuget** pour exécuter le Gestionnaire de packages NuGet. Recherchez **SQLiteStore** pour installer le package `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Si l'installation crée une référence à une version antérieure de SQLite, supprimez simplement cette référence en double.

3. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références** pour les projets de plateforme Runtime Windows 8.1 et Windows Phone 8.1, et vérifiez qu'il existe une référence à SQLite, qui doit se trouver dans la section **Extensions**.

    ![][1] </br>

    **Runtime Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite Runtime vous oblige à modifier l'architecture processeur du projet en cours de conception vers **x86**, **x64** ou **ARM**. **Tout processeur** n'est pas pris en charge. Dans l'Explorateur de solutions, cliquez sur la solution en haut, puis modifiez la zone déroulante d'architecture de processeur en spécifiant l'un des paramètres pris en charge que vous souhaitez tester.

    ![][13]

5. Dans l'Explorateur de solutions, dans le projet partagé, ouvrez le fichier MainPage.cs. Supprimez les commentaires des instructions using suivantes situées en haut du fichier :

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. Dans MainPage.cs, commentez la définition de `todoTable` et supprimez le commentaire de la ligne suivante, qui appelle `MobileServicesClient.GetSyncTable()` :

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. Dans MainPage.cs, dans la zone marquée `Offline sync`, supprimez les commentaires des méthodes `InitLocalStoreAsync` et `SyncAsync`. La méthode `InitLocalStoreAsync` initialise le contexte de synchronisation du client avec un magasin SQLite.

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

10. Ajoutez les gestionnaires d'exception à la méthode `SyncAsync` :

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
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
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

    Dans cet exemple, nous récupérons tous les enregistrements du `todoTable` distant, mais nous aurions également pu filtrer les enregistrements en transmettant une requête. Le premier paramètre de `PullAsync` est un ID de requête utilisé pour la synchronisation incrémentielle, qui utilise l'horodatage `UpdatedAt` pour obtenir uniquement les enregistrements modifiés depuis la dernière synchronisation. L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Pour refuser la synchronisation incrémentielle, passez `null` comme ID de requête. Vous récupérerez ainsi tous les enregistrements de chaque opération pull potentiellement inefficace.

    Notez que `MobileServicePushFailedException` peut survenir pour une opération push et une opération d'extraction. Elle peut se produire pour une extraction, car l'opération d'extraction exécute en interne une opération Push pour s'assurer que toutes les tables et toutes les relations sont cohérentes.

11. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l'application. L'application se comportera comme avant la modification de la synchronisation hors connexion, car elle effectue une opération de synchronisation lors des opérations d'insertion, de mise à jour et d'actualisation.

## <a name="update-sync"></a>Mettre à jour le comportement de synchronisation de l'application

Dans cette section, vous allez modifier l'application pour qu'elle n'effectue pas de synchronisation lors des opérations d'insertion et de mise à jour, mais uniquement lorsque vous activez le bouton **Actualiser**. Ensuite, vous allez interrompre la connexion de l’application au backend d’applications mobiles pour simuler un scénario hors connexion. Si vous ajoutez des éléments de données, ils sont stockés dans le magasin local, mais pas synchronisés avec le backend d’applications mobiles.

1. Ouvrez App.xaml.cs dans le projet partagé. Modifiez les méthodes `InsertTodoItem` et `UpdateCheckedTodoItem` pour commenter les appels vers `SyncAsync`.

2. Modifiez App.xaml.cs dans le projet partagé. Mettez en commentaire l’initialisation de **MobileServiceClient** et ajoutez les lignes suivantes qui utilisent une URL d’application mobile incorrecte :

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. Dans `InitLocalStoreAsync()`, commentez l'appel vers `SyncAsync()`, de sorte que l'application effectue une synchronisation au démarrage.

4. Appuyez sur **F5** pour générer et exécuter l'application. Entrez quelques nouveaux éléments todo et cliquez sur **Enregistrer** pour chacun d'eux. Les nouveaux éléments todoitem n’existent que dans le magasin local tant qu’ils ne sont pas transmis au backend d’applications mobiles. L’application cliente se comporte comme si elle était connectée au backend d’applications mobiles et prenait en charge toutes les opérations de création, de lecture, de mise à jour et de suppression (CRUD).

5. Fermez l'application et redémarrez-la pour vérifier que les nouveaux éléments que vous avez créés sont conservés dans le magasin local.

## <a name="update-online-app"></a>Mise à jour de l’application pour la reconnecter à votre backend d’applications mobiles

Dans cette section, vous reconnectez l’application au back d’applications mobiles. Cette opération simule le passage de l’application d’un état hors connexion à un état connecté au backend d’applications mobiles. Lorsque vous cliquez sur le bouton Actualiser, les données sont synchronisées avec votre backend d’applications mobiles.

1. Ouvrez App.xaml.cs dans le projet partagé. Annulez la mise en commentaire de votre précédente initialisation de `MobileServiceClient` pour rajouter l’URL du backend d’applications mobiles, l’URL de la passerelle et la clé de l’application.

2. Appuyez sur **F5** pour régénérer et exécuter l'application. Notez que les données présentent le même aspect qu’en mode hors connexion, même si l’application est maintenant connectée au backend d’applications mobiles. Cela s'explique par le fait que l'application fonctionne toujours avec la table `IMobileServiceSyncTable` pointant vers le magasin local.

3. Connectez-vous au portail Azure et examinez la base de données de votre backend d’applications mobiles.

    Dans Visual Studio, accédez à **Explorateur de serveurs** -> **Azure** -> **Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l’Explorateur d’objets SQL Server**.

    Notez que les données n'ont pas été synchronisées entre la base de données et le magasin local.

4. Dans l'application, cliquez sur le bouton **Actualiser**. L'application appelle `PushAsync` et `PullAsync`. Cette opération push envoie les éléments de magasin local au service mobile, puis récupère les nouvelles données du service mobile.

    Une opération push est exécutée à partir de `MobileServiceClient.SyncContext` au lieu de `IMobileServicesSyncTable` et transmet par push les modifications à toutes les tables associées à ce contexte de synchronisation. Ceci permet de prendre en compte les scénarios dans lesquels des relations existent entre les tables.

5. Dans l'application, cochez la case en regard de quelques éléments pour les exécuter dans le magasin local.

6. Appuyez de nouveau sur le bouton **Actualiser**, ce qui déclenche l'appel de `SyncAsync`. `SyncAsync` appelle les opérations push et pull, mais dans ce cas nous aurions pu supprimer l'appel vers `PushAsync`. La raison en est qu'une opération **Pull effectue toujours un Push d'abord**. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes.


##Résumé

Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l'interface `IMobileServiceSyncTable` et initialisé `MobileServiceClient.SyncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données SQLite.

Les opérations CRUD normales pour les services mobiles fonctionnent comme si l'application était toujours connectée, mais toutes les opérations ont lieu sur base du magasin local.

Lorsque nous avons voulu synchroniser le magasin local avec le serveur, nous avons utilisé les méthodes `IMobileServiceSyncTable.PullAsync` et `MobileServiceClient.SyncContext.PushAsync`.

*  Pour transmettre par push les modifications au serveur, nous avons appelé `IMobileServiceSyncContext.PushAsync()`. Cette méthode est membre de `IMobileServicesSyncContext` à la place de la table de synchronisation parce qu’elle envoie par Push les modifications sur toutes les tables.

    Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CUD) seront envoyés au serveur.

* Pour envoyer par pull les données d’une table sur le serveur vers l’application, nous avons appelé `IMobileServiceSyncTable.PullAsync`.

    Une opération Pull émet toujours d'abord une opération Push. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes.

    Il existe également des surcharges de `PullAsync()` qui permettent de spécifier une requête afin de filtrer les données stockées sur le client. Si une requête n'est pas transmise, `PullAsync()` extrait toutes les lignes de la table (ou requête) correspondante. Vous pouvez transmettre la requête pour filtrer uniquement les modifications avec lesquelles votre application doit se synchroniser.

* Pour activer la synchronisation incrémentielle, transmettez un ID de requête à `PullAsync()`. L’ID de requête est utilisé pour stocker le dernier horodatage des résultats de la dernière opération d’extraction. L’ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Si la requête possède un paramètre, alors la même valeur de paramètre doit faire partie de l’ID de requête.

    Par exemple, si vous filtrez selon userid, il doit faire partie de l’ID de la requête :

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Si vous souhaitez désactiver la synchronisation incrémentielle, transmettez `null` en tant qu'ID de requête. Dans ce cas, tous les enregistrements seront extraits à chaque appel à `PullAsync`, ce qui est potentiellement inefficace.

* Votre application doit appeler périodiquement `IMobileServiceSyncTable.PurgeAsync()` pour purger le magasin local.


<!-- Anchors. -->
[Mettre à jour l'application pour prendre en charge les fonctionnalités hors connexion]: #enable-offline-app
[Mettre à jour le comportement de synchronisation de l'application]: #update-sync
[Mise à jour de l’application pour la reconnecter à votre backend d’applications mobiles]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[create a windows app]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Créer une application Windows]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite pour Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[sqlite for windows phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=August15_HO6-->