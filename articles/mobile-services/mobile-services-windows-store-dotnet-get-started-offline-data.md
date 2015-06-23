<properties 
	pageTitle="Utilisation des données hors connexion dans Mobile Services (Windows Store) | Centre de développement mobile" 
	description="Learn how to use Azure Mobile Services to cache and sync offline data in your Windows Store application" 
	documentationCenter="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="donnam"/>

# Utilisation de la synchronisation des données hors connexion dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel explique comment ajouter la prise en charge des données hors connexion dans une application Windows Store universelle à l'aide d'Azure Mobile Services. La prise en charge en mode hors connexion permet d'interagir avec une base de données locale lorsque l'application est hors connexion. Une fois l'application connectée avec la base de données principale, vous pouvez synchroniser vos modifications locales en utilisant la fonctionnalité hors connexion. 
</p>
<p>Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">14:36:00</span></div>
</div>


Dans ce didacticiel, vous allez mettre à jour le projet d'application universelle développé dans le didacticiel [Prise en main de Mobile Services] pour prendre en charge les fonctionnalités hors connexion d'Azure Mobile Services. Ensuite, vous allez ajouter les données dans le cadre d'un scénario hors connexion déconnecté, synchroniser ces éléments dans la base de données en ligne, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.


>[AZURE.NOTE]Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données dans une application Windows Store à l'aide d'Azure. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services].
>
>Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10&nbsp;services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.
>
>L'ancien didacticiel Windows Phone 8 pour Visual Studio 2012 est toujours disponible ici, [Didacticiel Windows Phone 8 pour Visual Studio 2012].


Ce didacticiel vous familiarise avec ces étapes de base :

1. [Mettre à jour l'application pour prendre en charge les fonctionnalités hors connexion]
2. [Mettre à jour le comportement de synchronisation de l'application] 
3. [Mettre à jour l'application pour reconnecter votre service mobile]

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013 s'exécutant sous Windows 8.1.
* Achèvement du didacticiel [Prise en main de Mobile Services].
* [Kit de développement logiciel (SDK) Azure Mobile Services version 1.3.0 (ou version ultérieure)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store version 1.0.0 (ou version ultérieure)][SQLite store nuget]
* [SQLite pour Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>.

## <a name="enable-offline-app"></a>Mettre à jour l'application pour prendre en charge les fonctionnalités hors connexion

Les fonctionnalités hors connexion d'Azure Mobile Services vous permettent d'interagir avec une base de données locale lorsque vous vous trouvez dans un scénario hors connexion avec votre version de Mobile Service. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez un `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `IMobileServiceSyncTable`. Dans ce didacticiel, nous utilisons SQLite pour le magasin local.

>[AZURE.NOTE]Vous pouvez ignorer cette section et simplement obtenir l'exemple de projet prenant déjà en charge le mode hors connexion dans le référentiel d'exemples de Github pour Mobile Services. L'exemple de projet prenant en charge le mode hors connexion se trouve ici, [Exemple de liste des tâches hors connexion].

1. Installez le runtime SQLite pour Windows 8.1 et Windows Phone 8.1. 

    * **Windows 8.1 Runtime :** installez [SQLite pour Windows 8.1].
    * **Windows Phone 8.1 :** installez [SQLite for Windows Phone 8.1].

    >[AZURE.NOTE]Si vous utilisez Internet Explorer et que vous cliquez sur le lien pour installer SQLite, vous serez peut-être invité à télécharger le fichier .vsix en tant que fichier .zip. Enregistrez le fichier à un emplacement sur votre disque dur avec l'extension .vsix plutôt que .zip. Ensuite, double-cliquez sur le fichier .vsix dans l'Explorateur Windows pour exécuter l'installation.

2. Dans Visual Studio, ouvrez le projet mené à bien dans le didacticiel [Prise en main de Mobile Services]. Installez le package NuGet **WindowsAzure.MobileServices.SQLiteStore** pour les projets Runtime Windows 8.1 et Windows Phone 8.1.

    * **Windows 8.1 :** dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet Windows 8.1, puis cliquez sur **Gérer les packages NuGet** pour exécuter le Gestionnaire de package NuGet. Recherchez **SQLiteStore** pour installer le package `WindowsAzure.MobileServices.SQLiteStore`.
    * **Windows Phone 8.1 :** cliquez avec le bouton droit sur le projet Windows Phone 8.1, puis cliquez sur **Gérer les packages Nuget** pour exécuter le Gestionnaire de packages NuGet. Recherchez **SQLiteStore** pour installer le package `WindowsAzure.MobileServices.SQLiteStore`.

    >[AZURE.NOTE]Si l'installation crée une référence à une version antérieure de SQLite, supprimez simplement cette référence en double.

    ![][2]

2. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références** pour les projets de plateforme Runtime Windows 8.1 et Windows Phone 8.1, et vérifiez qu'il existe une référence à SQLite, qui doit se trouver dans la section **Extensions**.

    ![][1] </br>

    **Runtime Windows 8.1**

    ![][11] </br>

    **Windows Phone 8.1**

3. SQLite Runtime vous oblige à modifier l'architecture processeur du projet en cours de conception vers **x86**, **x64** ou **ARM**. **Tout processeur** n'est pas pris en charge. Dans l'Explorateur de solutions, cliquez sur la solution en haut, puis modifiez la zone déroulante d'architecture de processeur en spécifiant l'un des paramètres pris en charge que vous souhaitez tester.

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

    >[AZURE.NOTE]*Pour supprimer des enregistrements du magasin local du périphérique lorsqu'ils ont été supprimés dans la base de données de votre service mobile, vous devez activer la [Suppression réversible]. Sinon, votre application doit appeler périodiquement `IMobileServiceSyncTable.PurgeAsync()` pour vider le magasin local.

    Notez que le `MobileServicePushFailedException` peut survenir pour une opération push et une opération d'extraction. Elle peut se produire pour une extraction, car l'opération d'extraction exécute en interne une opération Push pour s'assurer que toutes les tables et toutes les relations sont cohérentes. Le didacticiel suivant, [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services], montre comment gérer ces exceptions relatives à la synchronisation.

11. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l'application. L'application se comportera comme avant la modification de la synchronisation hors connexion, car elle effectue une opération de synchronisation lors des opérations d'insertion, de mise à jour et d'actualisation.

## <a name="update-sync"></a>Mettre à jour le comportement de synchronisation de l'application

Dans cette section, vous allez modifier l'application pour qu'elle n'effectue pas de synchronisation lors des opérations d'insertion et de mise à jour, mais uniquement lorsque vous activez le bouton **Actualiser**. Ensuite, vous allez rompre la connexion de l'application avec le service mobile pour simuler un scénario hors connexion. Lorsque vous ajoutez des éléments de données, ils seront peut-être stockés dans le magasin local, mais pas synchronisées avec le service mobile.

1. Ouvrez MainPage.cs dans le projet partagé. Modifiez les méthodes `InsertTodoItem` et `UpdateCheckedTodoItem` pour commenter les appels vers `SyncAsync`.

2. Modifiez App.xaml.cs dans le projet partagé. Commentez l'initialisation de **MobileServiceClient** et ajoutez les lignes suivantes, qui utilisent une URL de service mobile incorrecte :

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. Dans `InitLocalStoreAsync()`, commentez l'appel vers `SyncAsync()`, de sorte que l'application effectue une synchronisation au démarrage.

4. Appuyez sur **F5** pour générer et exécuter l'application. Entrez quelques nouveaux éléments todo et cliquez sur **Enregistrer** pour chacun d'eux. Les nouveaux éléments todo se trouvent uniquement dans le magasin local s'ils peuvent être transmis par Push vers le service mobile. L'application cliente se comporte comme si elle était connectée au service mobile et supportait toutes les opérations CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

5. Fermez l'application et redémarrez-la pour vérifier que les nouveaux éléments que vous avez créés sont conservés dans le magasin local.

## <a name="update-online-app"></a>Mettre à jour l'application pour reconnecter votre service mobile

Dans cette section, vous allez reconnecter l'application au service mobile. Cette opération simule le passage de l'application d'un état hors connexion à un état connecté avec le service mobile. Lorsque vous appuyez sur le bouton Actualiser, les données seront synchronisées avec votre service mobile.

1. Ouvrez App.xaml.cs dans le projet partagé. Supprimez les commentaires de votre précédente initialisation de `MobileServiceClient` pour rajouter la clé d'application et l'URL du service mobile adéquates.

2. Appuyez sur **F5** pour régénérer et exécuter l'application. Notez que les données ont le même aspect que dans le scénario hors connexion, bien que l'application soit à présent connectée au service mobile. Cela s'explique par le fait que l'application fonctionne toujours avec la table `IMobileServiceSyncTable` pointant vers le magasin local.

3. Connectez-vous au portail de gestion Microsoft Azure et examinez la base de données de votre service mobile. Si votre service utilise le backend JavaScript pour les services mobiles, vous pouvez accéder aux données à partir de l'onglet **Données** du service mobile.

    Si vous utilisez le backend .NET pour votre service mobile, dans Visual Studio, accédez à **Explorateur de serveurs** -> **Azure** -> **Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l'Explorateur d'objets SQL Server**.

    Notez que les données n'ont pas été synchronisées entre la base de données et le magasin local.

    ![][6]

4. Dans l'application, cliquez sur le bouton **Actualiser**. L'application appelle `PushAsync` et `PullAsync`. Cette opération push envoie les éléments de magasin local au service mobile, puis récupère les nouvelles données du service mobile.

    Une opération push est exécutée à partir de `MobileServiceClient.SyncContext` au lieu de `IMobileServicesSyncTable` et transmet par push les modifications à toutes les tables associées à ce contexte de synchronisation. Ceci permet de prendre en compte les scénarios dans lesquels des relations existent entre les tables.

    ![][7]

5. Dans l'application, cochez la case en regard de quelques éléments pour les exécuter dans le magasin local.

    ![][8]

6. Appuyez de nouveau sur le bouton **Actualiser**, ce qui déclenche l'appel de `SyncAsync`. `SyncAsync` appelle les opérations push et pull, mais dans ce cas nous aurions pu supprimer l'appel vers `PushAsync`. La raison en est qu'une opération **Pull effectue toujours un Push d'abord**. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes.

    ![][10]
  

##Résumé

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Étapes suivantes

* [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]

* [Utilisation de la suppression réversible dans Mobile Services][Soft Delete]

<!-- Anchors. -->
[Mettre à jour l'application pour prendre en charge les fonctionnalités hors connexion]: #enable-offline-app
[Mettre à jour le comportement de synchronisation de l'application]: #update-sync
[Mettre à jour l'application pour reconnecter votre service mobile]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: mobile-services-windows-store-dotnet-handling-conflicts-offline-data.md
[Exemple de liste des tâches hors connexion]: http://go.microsoft.com/fwlink/?LinkId=394777
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Getting Started]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Prise en main de Mobile Services]: ../mobile-services-windows-store-get-started.md
[SQLite pour Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Didacticiel Windows Phone 8 pour Visual Studio 2012]: mobile-services-windows-phone-get-started-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[Suppression réversible]: mobile-services-using-soft-delete.md


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0

<!--HONumber=54--> 