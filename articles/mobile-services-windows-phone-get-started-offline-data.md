<properties 
	pageTitle="Utilisation des données hors connexion dans Mobile Services (Windows Phone) | Centre de développement mobile" 
	description="Découvrez comment utiliser Azure Mobile Services avec les données hors connexion de synchronisation dans votre application Windows Phone" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/10/2014" 
	ms.author="wesmc"/>

# Utilisation de la synchronisation des données hors connexion dans Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


Cette rubrique vous explique comment utiliser les fonctionnalités hors connexion d'Azure Mobile Services. Les fonctionnalités hors connexion d'Azure Mobile Services vous permettent d'interagir avec une base de données locale lorsque vous êtes hors connexion avec votre service mobile. Les fonctionnalités hors connexion vous permettent de synchroniser vos modifications locales avec le service mobile une fois que vous êtes à nouveau en ligne. 

Dans ce didacticiel, vous allez mettre à jour l'application à partir du didacticiel [Prise en main des données] pour pouvoir prendre en charge les fonctionnalités hors connexion d'Azure Mobile Services. Ensuite, vous allez ajouter les données dans le cadre d'un scénario hors connexion déconnecté, synchroniser ces éléments dans la base de données en ligne, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.


>[AZURE.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données dans une application Windows Phone à l'aide d'Azure. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord les didacticiels [Prise en main de Mobile Services] et [Prise en main des données]. 

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion]
2. [Test de l'application dans un scénario hors connexion]
3. [Mise à jour de l'application pour reconnecter votre service mobile]
4. [Test de l'application connectée au service mobile]

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2012
* [Kit de développement logiciel (SDK) Windows Phone 8]
* Achèvement du didacticiel [Prise en main des données].
* [Kit de développement logiciel (SDK) Azure Mobile Services version 1.3.0 (ou ultérieure)][Nuget - Kit de développement logiciel (SDK) Mobile Services]
* [Azure Mobile Services SQLite Store version 1.0.0 (ou version ultérieure)][Nuget - magasin SQLite]
* [SQLite pour Windows Phone 8]

>[AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>. 

## <a name="enable-offline-app"></a>Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion

Les fonctionnalités hors connexion d'Azure Mobile Services vous permettent d'interagir avec une base de données locale lorsque vous vous trouvez dans un scénario hors connexion avec votre version de Mobile Service. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez  `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `IMobileServiceSyncTable`.

Cette section utilise SQLite en tant que magasin local pour les fonctionnalités hors connexion.

>[AZURE.NOTE] Vous pouvez ignorer cette section et télécharger simplement une version du projet de prise en main disposant déjà d'une prise en charge hors connexion.  Pour télécharger un projet pour lequel la prise en charge hors connexion est activée, consultez la rubrique [Prise en main de l'exemple hors connexion pour Windows Phone].


1. Installez SQLite pour les projets Windows Phone 8. Vous pouvez l'installer à partir de ce lien, [SQLite pour Windows Phone 8].

    >[AZURE.NOTE] Si vous utilisez Internet Explorer et que vous cliquez sur le lien pour installer SQLite, vous serez peut-être invité à télécharger le fichier .vsix en tant que fichier .zip. Enregistrez le fichier à un emplacement sur votre disque dur avec l'extension .vsix plutôt que .zip. Ensuite, double-cliquez sur le fichier .vsix dans l'Explorateur Windows pour exécuter l'installation.

2. Dans Visual Studio, ouvrez le projet que vous avez terminé dans le didacticiel [Prise en main de Mobile Services] ou [Prise en main de données]. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références** sous le projet et ajoutez une référence à **SQLite pour Windows Phone** sous **Windows Phone**>**Extensions**. 

    ![][1]

3. Le runtime SQLite vous oblige à remplacer l'architecture de processeur du projet en cours de conception par **x86**, **x64** ou **ARM**. **L'option Tout processeur** n'est pas prise en charge. Remplacez l'architecture de processeur par l'un des paramètres pris en charge que vous voulez tester.

    ![][11]

4. Dans l'Explorateur de solutions pour Visual Studio, cliquez avec le bouton droit sur votre projet d'application cliente, puis cliquez sur **Gérer les packages Nuget** pour exécuter le gestionnaire de package NuGet. Recherchez **SQLiteStore** pour installer le package **WindowsAzure.MobileServices.SQLiteStore**.

    ![][2]

5. Dans l'Explorateur de solutions pour Visual Studio, ouvrez le fichier MainPage.xaml.cs. Ajoutez les instructions using suivantes au début du fichier.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Newtonsoft.Json.Linq;

6. Dans Mainpage.xaml.cs, remplacez la déclaration de `todoTable` par une déclaration de type `IMobileServicesSyncTable` qui est initialisée en appelant `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();

7. Dans MainPage.xaml.cs, mettez à jour la classe `TodoItem` de façon à ce qu'elle inclue la propriété système **Version** comme suit.

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }


8. Dans MainPage.xaml.cs, mettez à jour le gestionnaire d'événements `OnNavigatedTo` de façon à ce qu'il soit une méthode `async` et initialise le contexte de synchronisation client avec un magasin SQLite. Le magasin SQLite est créé avec une table qui associe le schéma de la table de service mobile mais il doit contenir la propriété système **Version** ajoutée à l'étape précédente.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync12.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store, new MobileServiceSyncHandler());
            }
            RefreshTodoItems();
        }

9. Dans l'Explorateur de solutions pour Visual Studio, ouvrez le fichier MainPage.xaml. Recherchez la définition pour le bouton **Actualiser**. Remplacez-la par la définition de panneau d'empilement suivante. 

    Ce code ajoute deux contrôles de bouton avec des gestionnaires d'événements Click pour les opérations **Push** et **Pull**. Les boutons se trouvent horizontalement à l'emplacement du bouton d'actualisation. Enregistrez le fichier.

        <StackPanel  Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2" HorizontalAlignment="Center">
          <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Width="160">Refresh</Button>
          <Button Name="ButtonPush" Click="ButtonPush_Click" Width="160">Push</Button>
          <Button Name="ButtonPull" Click="ButtonPull_Click" Width="160">Pull</Button>
        </StackPanel>

    Remplacez également le texte des blocs de texte comme illustré dans la capture d'écran suivante.

    ![][12]
        


10. Dans MainPage.xaml.cs, ajoutez les gestionnaires d'événements Click de bouton pour les boutons **Push** et **Pull**, puis enregistrez le fichier.

        private async void ButtonPull_Click(object sender, RoutedEventArgs e)
        {
            Exception pullException = null;
            try
            {
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
                RefreshTodoItems();
            }
            catch (Exception ex)
            {
                pullException = ex;
            }
            if (pullException != null) {
                MessageBox.Show("Pull failed: " + pullException.Message +
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Pull again when connected with your Mobile Serice.");
            }
        }
        private async void ButtonPush_Click(object sender, RoutedEventArgs e)
        {
            string errorString = null;
            try
            {
                await App.MobileService.SyncContext.PushAsync();
                RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count() + ", message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message;
            }
            if (errorString != null) {
                MessageBox.Show(errorString + 
                  "\n\nIf you are in an offline scenario, " + 
                  "try your Push again when connected with your Mobile Serice.");
            }
        }

11. N'exécutez pas encore l'application. Appuyez sur **F7** pour régénérer le projet. Vérifiez qu'aucune erreur de génération ne s'est produite.

## <a name="test-offline-app"></a>Test de l'application dans un scénario hors connexion

Dans cette section, vous allez interrompre la connexion de l'application avec le service mobile afin de simuler un scénario hors connexion. Ensuite, vous ajouterez quelques éléments de données qui seront conservés dans le magasin local.

Notez que dans cette section l'application ne doit être connectée à aucun service mobile. Par conséquent, les boutons **Push** et **Pull** généreront des erreurs si vous les testez. Dans la section suivante, vous reconnecterez cette application cliente au service mobile pour tester les opérations **Push** et **Pull** afin de synchroniser le magasin avec la base de données des services mobiles.


1. Dans l'Explorateur de solutions pour Visual Studio, ouvrez App.xaml.cs. Modifiez l'initialisation de **MobileServiceClient** en spécifiant une adresse non valide, en remplaçant " **azure-mobile.NET** " par " **azure-mobile.xxx** " pour l'URL. Puis enregistrez le fichier.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. Dans Visual Studio, appuyez sur **F5** pour générer et exécuter l'application. Entrez un nouvel élément todo et cliquez sur **Enregistrer**. Les nouveaux éléments todo se trouvent uniquement dans le magasin local s'ils peuvent être transmis par Push vers le service mobile. L'application cliente se comporte comme si elle était connectée au service mobile et supportait toutes les opérations CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

    ![][4]

3. Fermez l'application et redémarrez-la pour vérifier que les nouveaux éléments que vous avez créés sont conservés dans le magasin local.

## <a name="update-online-app"></a>Mise à jour de l'application pour reconnecter votre service mobile

Dans cette section, vous allez reconnecter l'application au service mobile. Cette opération simule le passage de l'application d'un état hors connexion à un état connecté avec le service mobile.


1. Dans l'Explorateur de solutions pour Visual Studio, ouvrez App.xaml.cs. Modifiez l'initialisation de **MobileServiceClient** en rétablissant l'adresse correcte, en remplaçant " **azure-mobile.xxx** " par " **azure-mobile.net** " pour l'URL. Puis enregistrez le fichier.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Test de l'application connectée au service mobile


Dans cette section, vous allez tester les opérations Push et Pull pour synchroniser le magasin local avec la base de données du service mobile.

1. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l'application. Notez que les données ont le même aspect que dans le scénario hors connexion, bien que l'application soit à présent connectée au service mobile. Cela s'explique par le fait que l'application fonctionne toujours avec la table `IMobileServiceSyncTable` pointant vers le magasin local.

    ![][4]

2.  Connectez-vous au portail de gestion Microsoft Azure et examinez la base de données de votre service mobile. Si votre service utilise le service principal JavaScript pour les services mobiles, vous pouvez accéder aux données à partir de l'onglet **Données** du service mobile. 

    Si vous utilisez le serveur principal .NET pour votre service mobile, dans Visual Studio, accédez à **Explorateur de serveurs** -> **Azure** -> **Bases de données SQL**. Cliquez avec le bouton droit sur votre base de données, puis sélectionnez **Ouvrir dans l'Explorateur d'objets SQL Server**.

    Notez que les données n'ont pas été synchronisées entre la base de données et le magasin local.

    ![][6]

3. Dans l'application, appuyez sur le bouton **Push**. L'application appelle alors  `MobileServiceClient.SyncContext.PushAsync`, puis  `RefreshTodoItems` pour actualiser l'application avec les éléments du magasin local. Suite à l'opération Push, la base de données du service mobile reçoit les données à partir du magasin. Toutefois, le magasin local ne reçoit pas les éléments à partir de la base de données du service mobile.

    Une opération push est exécutée à partir de `MobileServiceClient.SyncContext` plutôt que `IMobileServicesSyncTable` et transmet par push les modifications à toutes les tables associées à ce contexte de synchronisation. Ceci permet de prendre en compte les scénarios dans lesquels des relations existent entre les tables.

    ![][7]

4. Dans l'application, ajoutez quelques nouveaux éléments dans le magasin local.

    ![][8]

5. Cette fois, appuyez sur le bouton **Pull** dans l'application. L'application appelle uniquement `IMobileServiceSyncTable.PullAsync()` et `RefreshTodoItems`.  Notez que toutes les données de la base de données du service mobile ont été transmises par Pull vers le magasin local et affichées dans l'application. Toutefois, remarquez également que toutes les données dans le magasin local étaient toujours envoyées par Push vers la base de données du service mobile. La raison en est qu'une **opération Pull commence toujours par effectuer une opération Push**.
 
    Dans cet exemple, nous récupérons tous les enregistrements de la table `todoTable` distante, mais il est également possible de filtrer les enregistrements en transmettant une requête. Le premier paramètre de `PullAsync` est un ID de requête qui est utilisé pour la synchronisation incrémentielle, qui utilise l'horodatage `UpdatedAt` pour obtenir uniquement les enregistrements modifiés depuis la dernière synchronisation. L'ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Pour désactiver la synchronisation incrémentielle, transmettez `null` en tant qu'ID de la requête. Vous récupérez ainsi tous les enregistrements de chaque opération d'extraction, ce qui est assez inefficace.

    >[AZURE.NOTE] Pour prendre en charge la synchronisation des enregistrements supprimés par la synchronisation des données hors connexion, vous devez activer la [Suppression réversible]. Sinon, vous devez appeler `IMobileServiceSyncTable.PurgeAsync()` pour purger le magasin local.

 
    ![][9]

    ![][10] 
  

## Résumé

## Résumé

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## Étapes suivantes

* [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]

* [Utilisation de la suppression réversible dans Mobile Services][Suppression réversible]

<!-- Anchors. -->
[Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion]: #enable-offline-app
[Test de l'application dans un scénario hors connexion]: #test-offline-app
[Mise à jour de l'application pour reconnecter votre service mobile]: #update-online-app
[Test de l'application connectée au service mobile]: #test-online-app
[Étapes suivantes]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-phone-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-phone-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-phone-get-started-offline-data/vs-select-processor-architecture.png
[12]: ./media/mobile-services-windows-phone-get-started-offline-data/ui-screenshot.png

<!-- URLs. -->
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data/ 
[Prise en main de l'exemple hors connexion pour Windows Phone]: http://go.microsoft.com/fwlink/?LinkId=397952
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data/
[Prise en main de données]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data/
[SQLite pour Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Kit de développement logiciel (SDK) Windows Phone 8]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Suppression réversible]: /fr-fr/documentation/articles/mobile-services-using-soft-delete/

[Nuget - Kit de développement logiciel (SDK) Mobile Services]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[Nuget - magasin SQLite]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0


<!--HONumber=42-->
