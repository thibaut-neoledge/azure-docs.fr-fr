<properties urlDisplayName="Using Offline Data" pageTitle="Utilisation des données hors connexion dans Mobile Services (Windows Store) | Centre de développement mobile" metaKeywords="" description="Découvrez comment utiliser Azure Mobile Services pour mettre en cache et synchroniser les données hors connexion dans votre application Windows Store" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Utilisation de la synchronisation des données hors connexion dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel explique comment ajouter une prise en charge hors connexion à une application Windows Universal Store à l'aide d'Azure Mobile Services. La prise en charge en mode hors connexion permet d'interagir avec une base de données locale lorsque l'application est hors connexion. Une fois l'application connectée avec la base de données principale, vous pouvez synchroniser vos modifications locales en utilisant la fonctionnalité hors connexion. 
</p>
<p>Si vous préférez regarder une vidéo, le clip sur la droite suit la même procédure que ce didacticiel.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">14:36:00</span></div>
</div>


Dans ce didacticiel, vous allez mettre à jour le projet d'application universelle développé dans le didacticiel [Prise en main de Mobile Services] pour prendre en charge les fonctionnalités hors connexion d'Azure Mobile Services. Ensuite, vous allez ajouter les données dans le cadre d'un scénario hors connexion déconnecté, synchroniser ces éléments dans la base de données en ligne, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.


>[WACOM.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données dans une application Windows Store à l'aide d'Azure. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services].
>
>Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10&nbsp;services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>. 
>
>L'ancien didacticiel Windows Phone 8 pour Visual Studio 2012 est toujours disponible ici, [Didacticiel Windows Phone 8 pour Visual Studio 2012].


Ce didacticiel vous familiarise avec ces étapes de base :

1. [Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion]
2. [Test de l'application dans un scénario hors connexion] 
3. [Mise à jour de l'application pour reconnecter votre service mobile]
4. [Test de l'application connectée au service mobile]

Ce didacticiel requiert les éléments suivants :

* Visual Studio 2013 s'exécutant sous Windows 8.1.
* Achèvement du didacticiel [Prise en main de Mobile Services].
* [Kit de développement logiciel (SDK) Azure Mobile Services version 1.3.0-beta2 (ou versions ultérieures)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store version 1.0.0-beta2 (ou versions ultérieures)][SQLite store nuget]
* SQLite pour Windows 8.1

>[AZURE.NOTE]Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Version d'évaluation gratuite d'Azure</a>. 

## <a name="enable-offline-app"></a>Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion

Les fonctionnalités hors connexion d'Azure Mobile Services vous permettent d'interagir avec une base de données locale lorsque vous êtes hors connexion avec votre service mobile. Pour pouvoir utiliser ces fonctionnalités dans votre application, vous initialisez un `MobileServiceClient.SyncContext` dans un magasin local. Ensuite, vous référencez votre table par le biais de l'interface `IMobileServiceSyncTable`. Dans ce didacticiel, nous utilisons SQLite pour le magasin local.

>[AZURE.NOTE]Vous pouvez ignorer cette section et simplement obtenir l'exemple de projet prenant déjà en charge le mode hors connexion dans le référentiel d'exemples de Github pour Mobile Services. L'exemple de projet prenant en charge le mode hors connexion se trouve ici, [Exemple de liste des tâches hors connexion].


1. Installez le runtime SQLite pour Windows 8.1 et Windows Phone 8.1. 

    * **Runtime Windows 8.1 :** Cliquez sur ce lien pour installer le runtine SQLite pour Windows 8.1, [SQLite pour Windows 8.1].
    * **Windows Phone 8,1 :** Cliquez sur ce lien pour installer le runtine SQLite pour Windows Phone 8.1, [SQLite pour Windows Phone 8.1].

    >[AZURE.NOTE] Si vous utilisez Internet Explorer et que vous cliquez sur le lien pour installer SQLite, vous serez peut-être invité à télécharger le fichier .vsix en tant que fichier .zip. Enregistrez le fichier à un emplacement sur votre disque dur avec l'extension .vsix plutôt que .zip. Ensuite, double-cliquez sur le fichier .vsix dans l'Explorateur Windows pour exécuter l'installation.

2. Dans Visual Studio, ouvrez le projet mené à bien dans le didacticiel [Prise en main de Mobile Services]. Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Références** pour le runtime Windows 8.1 et les projets de plateforme Windows Phone 8.1 et ajoutez une référence vers SQLite, qui est situé dans la section **Extensions**. 

    ![][1]
    </br>**Runtime Windows 8.1**

    ![][11]
    </br>**Windows Phone 8,1**

3. SQLite Runtime vous oblige à modifier l'architecture processeur du projet en cours de conception vers **x86**, **x64** ou **ARM**. **Tout processeur**  n'est pas pris en charge. Dans l'Explorateur de solutions de Visual Studio, cliquez sur la solution du haut, puis remplacez le menu déroulant de l'architecture processeur par l'un des paramètres pris en charge que vous souhaitez tester.

    ![][13]

4. Installez le package NuGet  **WindowsAzure.MobileServices.SQLiteStore** pour les projets de runtime Windows 8.1 et Windows Phone 8.1.

    * **Windows 8.1 :**Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet Windows 8.1, puis cliquez sur **Gérer les packages NuGet** pour exécuter le Gestionnaire de package NuGet. Recherchez **SQLiteStore** pour installer le package WindowsAzure.MobileServices.SQLiteStore package.
    * **Windows Phone 8,1 :**Cliquez avec le bouton droit sur le projet Windows Phone 8.1, puis cliquez sur **Gérer les packages NuGet** pour exécuter le Gestionnaire de package NuGet. Recherchez **SQLiteStore** pour installer le package WindowsAzure.MobileServices.SQLiteStore package.     

    >[WACOM.NOTE] Si l'installation crée une référence pointant vers une version antérieure de SQLite, vous pouvez simplement supprimer ce doublon. 

    ![][2]

5. Dans l'Explorateur de solutions de Visual Studio, dans le projet partagé, ouvrez le fichier MainPage.cs. Ajoutez les instructions using suivantes au début du fichier.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. Dans le fichier partagé (Mainpage.cs), remplacez la déclaration `todoTable` par une déclaration de type `IMobileServicesSyncTable` initialisée en appelant `MobileServicesClient.GetSyncTable()`.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. Dans le fichier partagé (MainPage.cs), mettez à jour le gestionnaire d'événements `OnNavigatedTo` de façon à ce qu'il initialise le contexte de synchronisation client avec un magasin SQLite. Le magasin SQLite est créé avec une table qui associe le schéma de la table de service mobile et il doit contenir la propriété système **Version** que vous allez ajouter lors de l'étape suivante.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. Dans l'Explorateur de solutions de Visual Studio, dans le projet partagé, développez **DataModel** et ouvrez TodoItem.cs. Ajoutez une instruction `using` pour l'espace de noms MobileServices. Cela est nécessaire pour résoudre l'attribut de version de la propriété " version " du système.

        using Microsoft.WindowsAzure.MobileServices;

      Puis mettez à jour la classe `TodoItem` de façon à ce qu'elle inclue la propriété système **Version** comme suit. Le schéma de table doit inclure la propriété système **Version**, comme indiqué ici, pour prendre en charge les fonctionnalités hors connexion.

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




9. Ensuite, mettez à jour l'interface utilisateur des projets Windows 8.1 et Windows Phone 8.1 pour inclure des boutons qui prendront en charge les opérations de synchronisation hors connexion entre la base de données hors connexion locale et la base de données du service mobile dans Azure. 

    * **Windows 8.1 :**Dans l'Explorateur de solutions de Visual Studio, sous le projet Windows 8.1, ouvrez le fichier MainPage.xaml. Recherchez le bouton nommé **ButtonRefresh**. Remplacez cet élément par le panneau d'empilement de boutons suivant. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8,1 :**Dans l'Explorateur de solutions de Visual Studio, sous le projet Windows Phone 8.1, ouvrez le fichier MainPage.xaml. Recherchez le bouton nommé **ButtonRefresh**. Remplacez cet élément par le panneau d'empilement de boutons suivant. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. Dans le fichier MainPage.cs partagé, ajoutez des gestionnaires d'événement Click pour les boutons **Push** et **Pull**. Puis enregistrez le fichier. 
        
    Notez que l'exception `MobileServicePushFailedException` peut survenir lors des opérations Push et Pull. Elle peut survenir pour une opération Pull, car celle-ci exécute en interne une opération Push pour s'assurer que toutes les tables et leurs relations sont synchronisées. Le didacticiel suivant, [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services], montre comment gérer ces exceptions relatives à la synchronisation.

    Pour prendre en charge la synchronisation des enregistrements supprimés par la synchronisation des données hors connexion, vous devez activer la [Suppression réversible](/fr-fr/documentation/articles/mobile-services-using-soft-delete/). Sinon, vous devez supprimer manuellement les enregistrements dans le magasin local ou appeler `IMobileServiceSyncTable::PurgeAsync()` pour vider le magasin local.

    Dans cet exemple, nous allons transmettre une requête à l'appel de méthode `PullAsync` pour prendre en charge la synchronisation des valeurs incrémentielles. Cela est utile si vous souhaitez éviter les dépenses entraînées par l'extraction de la totalité de la table pendant la synchronisation. Dans ce scénario, nous n'allons pas aborder les modifications apportées aux textes de la table Todoitems, nous allons simplement extraire les éléments terminés pour les noter comme tels dans notre liste des tâches.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // In this example scenario we are demonstrating incremental sync. We only want to 
                // sync the uncomplete todoitems. If another client updated the text or any other 
                // field after completing the item, it doesn't concern us. So we pass a query to the 
                // PullAsync() method to pull uncomplete todoitems instead of the entire table.
                await todoTable.PullAsync(todoTable.Where(todoItem => todoItem.Complete == false));

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
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

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. Générez la solution et vérifiez qu'aucune erreur de génération ne s'est produite dans un des projets.


## <a name="test-offline-app"></a>Test de l'application dans un scénario hors connexion

Dans cette section, vous allez interrompre la connexion de l'application avec le service mobile afin de simuler un scénario hors connexion. Ensuite, vous ajouterez quelques éléments de données qui seront conservés dans le magasin local.

Notez que dans cette section, l'application ne doit être connectée à aucun service mobile. Par conséquent, les boutons **Push** et **Pull** généreront des erreurs si vous les testez. Dans la section suivante, vous allez connecter cette application cliente au service mobile à nouveau pour tester les opérations **Push** et **Pull** afin de synchroniser le magasin avec la base de données du service mobile.


1. Dans l'Explorateur de solutions de Visual Studio, dans le projet partagé, ouvrez App.xaml.cs. Remplacez la valeur d'initialisation du **MobileServiceClient** par une adresse incorrecte en remplaçant " **azure-mobile.net** " par " **azure-mobile.xxx** " pour votre URL. Puis enregistrez le fichier.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. Dans Visual Studio, appuyez sur **F5** pour générer et exécuter l'application. Entrez quelques nouveaux éléments todo et cliquez sur **Enregistrer** pour chacun d'eux. Les nouveaux éléments todo se trouvent uniquement dans le magasin local s'ils peuvent être transmis par Push vers le service mobile. L'application cliente se comporte comme si elle était connectée au service mobile et prenait en charge toutes les opérations CRUD (Create, Read, Update et Delete, ou Créer, Lire, Mettre à jour et Supprimer).

    ![][4]

3. Fermez l'application et redémarrez-la pour vérifier que les nouveaux éléments que vous avez créés sont conservés dans le magasin local.

## <a name="update-online-app"></a>Mise à jour de l'application pour reconnecter votre service mobile

Dans cette section, vous allez reconnecter l'application au service mobile. Cette opération simule le passage de l'application d'un état hors connexion à un état connecté avec le service mobile.


1. Dans l'Explorateur de solutions de Visual Studio, dans le projet partagé, ouvrez App.xaml.cs. Restaurez l'adresse adéquate de la valeur d'initialisation du **MobileServiceClient** en remplaçant " **azure-mobile.xxx** " par " **azure-mobile.net** " pour votre URL. Puis enregistrez le fichier.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Test de l'application connectée au service mobile


Dans cette section, vous allez tester les opérations Push et Pull pour synchroniser le magasin local avec la base de données du service mobile. Vous pouvez tester le client Windows Store 8.1 ou Windows Phone 8.1. Les captures d'écran ci-dessous montrent le client Windows Store 8.1. 

1. Dans Visual Studio, appuyez sur **F5** pour régénérer et exécuter l'application. Notez que les données ont le même aspect que dans le scénario hors connexion, bien que l'application soit à présent connectée au service mobile. Cela s'explique par le fait que l'application fonctionne toujours avec la table `IMobileServiceSyncTable` pointant vers le magasin local.

    ![][4]

2. Connectez-vous au portail de gestion Microsoft Azure et examinez la base de données de votre service mobile. Si votre service utilise l'application dorsale de JavaScript pour les services mobiles, vous pouvez accéder aux données à partir de l'onglet **Données** du service mobile. Si vous utilisez le serveur principal .NET pour votre service mobile, vous pouvez cliquer sur le bouton **Gérer** pour votre base de données dans l'extension SQL Azure afin d'exécuter une requête sur la base de votre table.

    Notez que les données n'ont pas été synchronisées entre la base de données et le magasin local.

    ![][6]

3. Dans l'application, appuyez sur le bouton  **Push**. Ainsi, l'application appelle `MobileServiceClient.SyncContext.PushAsync`. Suite à l'opération Push, la base de données du service mobile reçoit les données à partir du magasin. Toutefois, le magasin local ne reçoit pas les éléments à partir de la base de données du service mobile.

    Une opération Push est exécutée à partir de `MobileServiceClient.SyncContext` plutôt que `IMobileServicesSyncTable` et transmet par Push les modifications à toutes les tables associées à ce contexte de synchronisation. Ceci permet de prendre en compte les scénarios dans lesquels des relations existent entre les tables.

    ![][7]

4. Dans l'application, activez la case à cocher située en regard de quelques éléments pour les marquer comme terminés dans le magasin local. 

    ![][8]

5. Cette fois, appuyez sur le bouton **Pull** dans l'application. L'application appelle `IMobileServiceSyncTable.PullAsync()` et `RefreshTodoItems`.  Notez que toutes les données de la base de données du service mobile ont été transmises par Pull vers le magasin local et affichées dans l'application. Toutefois, remarquez également que toutes les données dans le magasin local étaient toujours envoyées par Push vers la base de données du service mobile. La raison en est qu'une opération **Pull effectue toujours un Push d'abord**. Cela permet de vérifier que toutes les tables du magasin local, ainsi que leurs relations, restent synchronisées.
 
    ![][9]

    ![][10] 
  

##Résumé

Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l'interface `IMobileServiceSyncTable` et initialisé `MobileServiceClient.SyncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données SQLite.

Les opérations CRUD normales pour les services mobiles fonctionnent comme si l'application était toujours connectée, mais toutes les opérations ont lieu sur base du magasin local.

Lorsque nous avons voulu synchroniser le magasin local avec le serveur, nous avons utilisé les méthodes `IMobileServiceSyncTable.PullAsync` et `MobileServiceClient.SyncContext.PushAsync`.

*  Pour transmettre par Push les modifications au serveur, nous avons appelé `IMobileServiceSyncContext.PushAsync()`. Cette méthode est membre de `IMobileServicesSyncContext` à la place de la table de synchronisation, car elle envoie par Push les modifications sur toutes les tables :

    Seuls les enregistrements qui ont été modifiés d'une certaine façon en local (par le biais d'opérations CRUD) seront envoyés au serveur.
   
* Pour envoyer par Pull les données d'une table sur le serveur vers l'application, nous avons appelé `IMobileServiceSyncTable.PullAsync`.

    Une opération Pull commence toujours par émettre une opération Push. Cela permet de vérifier que toutes les tables du magasin local, ainsi que leurs relations, restent synchronisées.

    Les surcharges de `PullAsync()` permettent également de spécifier une requête pour prendre en charge la synchronisation des valeurs incrémentielles. Si aucune requête n'est transmise, `PullAsync()` extrait toutes les lignes de la table (ou de la requête) correspondante. Vous pouvez transmettre la requête pour filtrer uniquement les modifications avec lesquelles votre application doit se synchroniser.


## Étapes suivantes

* [Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]

<!-- Anchors. -->
[Mise à jour de l'application pour prendre en charge les fonctionnalités hors connexion]: #enable-offline-app
[Test de l'application dans un scénario hors connexion]: #test-offline-app
[Mise à jour de l'application pour reconnecter votre service mobile]: #update-online-app
[Test de l'application connectée au service mobile]: #test-online-app
[Étapes suivantes]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Gestion des conflits liés à la prise en charge hors connexion de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Exemple de liste des tâches hors connexion]: http://go.microsoft.com/fwlink/?LinkId=394777
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Mise en route]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/
[SQLite pour Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite pour Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Didacticiel Windows Phone 8 pour Visual Studio 2012]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[NuGet du Kit de développement logiciel (SDK) Mobile Services]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[NuGet du magasin SQLite]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
