<properties
    pageTitle="Se connecter à Azure Storage dans votre application Xamarin.Forms"
    description="Ajouter des images à l’application mobile Xamarin.Forms todo list en vous connectant au stockage d’objets blob Azure"
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="donnam"/>

#Se connecter à Azure Storage dans votre application Xamarin.Forms

## Vue d'ensemble

Les SDK client et serveur Azure Mobile Apps prennent en charge la synchronisation hors connexion des données structurées avec des opérations CRUD sur le point de terminaison /tables. En règle générale, ces données sont stockées dans une base de données ou un magasin similaire qui ne peuvent pas stocker efficacement d’importants volumes de données binaires. De même, certaines applications stockent leurs données associées à un autre emplacement (par exemple, un stockage d’objets blob ou des partages de fichiers) ; il est dans ce cas utile de pouvoir créer des associations entre les enregistrements du point de terminaison /tables et les autres données.

Cette rubrique vous montre comment ajouter la prise en charge d’images au démarrage rapide d’une liste des tâches Mobile Apps. Vous devez commencer par suivre le didacticiel [Créer une application Xamarin.Forms].

Dans ce didacticiel, vous allez créer un compte de stockage et ajouter une chaîne de connexion à votre backend Mobile App. Vous ajouterez ensuite à votre projet de serveur un nouvel héritier du nouveau type Mobile Apps `StorageController<T>`.

>[AZURE.TIP] Un [exemple de compagnon](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/) est disponible pour ce didacticiel, que vous pouvez déployer dans votre propre compte Azure.

## Composants requis

* Effectuez le didacticiel [Créer une application Xamarin.Forms] qui répertorie les autres composants requis. Cet article utilise l’application exécutée à partir de ce didacticiel.

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service](https://tryappservice.azure.com/?appServiceName=mobile). Là, vous pouvez créer immédiatement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ni aucun engagement.

## Créez un compte de stockage.

1. Créez un compte de stockage en suivant le didacticiel [Créer un compte Azure Storage].

2. Dans le portail Azure, accédez à votre nouveau compte de stockage et cliquez sur l’icône **Clés**. Copiez la **chaîne de connexion principale**.

3. Accédez à votre backend d’application mobile Sous **Tous les paramètres** -> **Paramètres de l’application** -> **Chaînes de connexion**, créez une clé nommée `MS_AzureStorageAccountConnectionString` et utilisez la valeur que vous avez copiée à partir de votre compte de stockage. Choisissez le type de clé **Personnalisé**.

## Ajoutez un contrôleur de stockage au serveur.

Vous devez ajouter un nouveau contrôleur à votre projet de serveur, qui pourra répondre aux demandes de jeton SAS pour Azure Storage, ainsi que retourner une liste de fichiers qui correspondent à un enregistrement :

- [Ajoutez un contrôleur de stockage à votre projet de serveur.](#add-controller-code)
- [Routes inscrites par le contrôleur de stockage](#routes-registered)
- [Communication client et serveur](#client-communication)

###<a name="add-controller-code"></a>Ajoutez un contrôleur de stockage à votre projet de serveur.

1. Dans Visual Studio, ouvrez votre projet de serveur .NET. Ajoutez le package NuGet [Microsoft.Azure.Mobile.Server.Files]. Veillez à sélectionner **Inclure la version préliminaire**.

2. Dans Visual Studio, ouvrez votre projet de serveur .NET. Cliquez avec le bouton droit sur le dossier **Contrôleurs** et sélectionnez **Ajouter** -> **Contrôleur** -> **Contrôleur Web API 2 - Vide**. Nommez le contrôleur `TodoItemStorageController`.

3. Ajoutez les instructions using suivantes :

        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;

4. Définissez la classe de base sur `StorageController` :
    
        public class TodoItemStorageController : StorageController<TodoItem>

5. Ajoutez les méthodes suivantes à la classe :

        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);

            return Request.CreateResponse(token);
        }

        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);

            return Request.CreateResponse(files);
        }

        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }

6. Mettez à jour la configuration de l'API Web pour configurer le routage des attributs. Dans **Startup.MobileApp.cs**, ajoutez la ligne suivante à la méthode `ConfigureMobileApp()`, après la définition de la variable `config` :

        config.MapHttpAttributeRoutes();

7. Publiez votre projet de serveur sur votre backend d’application mobile.

###<a name="routes-registered"></a>Routes inscrites par le contrôleur de stockage

La nouvelle classe `TodoItemStorageController` expose deux sous-ressources sous l’enregistrement qu’elle gère :

- StorageToken

    + HTTP POST : crée un jeton de stockage
    
        `/tables/TodoItem/{id}/MobileServiceFiles`
    
- MobileServiceFiles

    + HTTP GET : récupère une liste de fichiers associés à l’enregistrement
    
        `/tables/TodoItem/{id}/MobileServiceFiles`

    + HTTP DELETE : supprime le fichier spécifié dans l’identificateur de ressources de fichier
    
        `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

###<a name="client-communication"></a>Communication client et serveur

Notez que la classe `TodoItemStorageController` ne dispose *pas* d’une route précise permettant de transférer ou télécharger un blob. Pour effectuer ces opérations, en effet, un client mobile interagit *directement* avec Blob Storage après avoir obtenu un jeton de signature d’accès partagé (SAS) pour pouvoir accéder en toute sécurité à un blob ou conteneur donné. Il s’agit d’un aspect de conception architecturale important, car dans d’autres circonstances, l’accès au stockage serait limité par l’extensibilité et la disponibilité du backend mobile. Mais grâce à une connexion directe à Azure Storage, le client mobile peut tirer parti de certaines de ses fonctionnalités, telles que le partitionnement automatique et la distribution géographique.

Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Cela vous permet d’octroyer à un client des autorisations d’accès limité à des objets de votre compte de stockage pendant une période donnée et avec un ensemble défini d’autorisations, sans partager les clés d’accès de votre compte. Pour plus d’informations, consultez [Présentation des signatures d’accès partagé].

Le schéma ci-dessous illustre les interactions entre le client et le serveur. Avant de télécharger un fichier, le client demande un jeton SAP au service. Le service utilise la chaîne de connexion du stockage pour générer un nouveau jeton SAS, qu’il renvoie ensuite au client. Le jeton SAS a une durée de vie limitée et limite les autorisations à un fichier ou conteneur particulier. Le client mobile utilise ensuite cette signature d’accès partagé et le SDK du client Azure Storage pour télécharger le fichier dans le stockage d’objets blob.

![Demande d’un jeton SAS](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## Mettre à jour votre application cliente pour ajouter la prise en charge d’images

Ouvrez le projet de démarrage rapide Xamarin.Forms dans Visual Studio ou Xamarin Studio. Vous allez installer les packages NuGet et mettre à jour le projet de bibliothèque portable, ainsi que les projets client iOS, Android et Windows :

- [Ajout de packages NuGet](#add-nuget)
- [Ajouter l’interface IPlatform](#add-iplatform)
- [Ajouter la classe FileHelper](#add-filehelper)
- [Ajouter un gestionnaire de synchronisation de fichiers](#file-sync-handler)
- [Mettre à jour TodoItemManager](#update-todoitemmanager)
- [Ajouter une vue de détails](#add-details-view)
- [Mettre à jour la vue principale](#update-main-view)
- [Mettre à jour le projet Android](#update-android), [le projet iOS](#update-ios) et [le projet Windows](#update-windows)

>[AZURE.NOTE] Ce didacticiel contient uniquement des instructions pour les plateformes Android, iOS et Windows Store ; il ne concerne pas Windows Phone.

###<a name="add-nuget"></a>Ajout de packages NuGet

Cliquez avec le bouton droit sur la solution et sélectionnez **Gérer les packages NuGet pour la solution**. Ajoutez les packages NuGet suivants à **tous** les projets dans la solution. Veillez à cocher la case **Inclure la version préliminaire**.

  - [Microsoft.Azure.Mobile.Client.Files]

  - [Microsoft.Azure.Mobile.Client.SQLiteStore]

  - [PCLStorage]

Pour plus de commodité, cet exemple utilise la bibliothèque [PCLStorage], bien qu’elle ne soit pas requise par le Kit de développement logiciel (SDK) du client Azure Mobile Apps.

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

###<a name="add-iplatform"></a>Ajouter l’interface IPlatform

Créez une interface `IPlatform` dans le projet de bibliothèque portable principal. Cette interface suit le modèle [Xamarin.Forms DependencyService] pour charger la classe adaptée à la plateforme au moment de l’exécution. Vous ajouterez ultérieurement des implémentations spécifiques à votre plateforme dans chaque projet client.

1. Ajoutez les instructions using suivantes :

        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;

2. Remplacez l’implémentation par le code suivant :

        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();

            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);

            Task<string> TakePhotoAsync(object context);

            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

###<a name="add-filehelper"></a>Ajouter la classe FileHelper

1. Créez une classe `FileHelper` dans le projet de bibliothèque portable principal. Ajoutez les instructions using suivantes :

        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;

2. Ajoutez la définition de classe :

        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;

                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);

                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);

                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);

                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }

                return targetPath;
            }

            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();

                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);

                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }

                return Path.Combine(recordFilesPath, fileName);
            }

            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);

                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

###<a name="file-sync-handler"></a>Ajouter un gestionnaire de synchronisation de fichiers

Créez une classe `TodoItemFileSyncHandler` dans le projet de bibliothèque portable principal. Cette classe contient des rappels en provenance du SDK Azure afin de notifier votre code en cas d’ajout ou de suppression d’un fichier.

Le Kit de développement logiciel (SDK) du client Azure Mobile ne stocke pas réellement les données de fichier : il appelle l’implémentation de `IFileSyncHandler` qui détermine ensuite si et de quelle manière les fichiers sont stockés sur l’appareil local.

1. Ajoutez les instructions using suivantes :

        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;

2. Remplacez la définition de la classe par le code suivant :

        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;

            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }

            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }

            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

###<a name="update-todoitemmanager"></a>Mettre à jour TodoItemManager

1. Dans **TodoItemManager.cs**, supprimez les marques de commentaire de la ligne `#define OFFLINE_SYNC_ENABLED`.

2. Dans **TodoItemManager.cs**, ajoutez les instructions using suivantes :

        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;

3. Dans le constructeur de `TodoItemManager`, ajoutez le code suivant après l’appel à `DefineTable()` :

        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);

4. Dans le constructeur, remplacez l’appel à `InitializeAsync` par le code suivant. Des rappels seront ainsi générés en cas de modification des enregistrements dans le magasin local. La fonctionnalité de synchronisation de fichiers utilise ces rappels pour déclencher votre gestionnaire de synchronisation de fichiers.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

5. Dans `SyncAsync()`, ajoutez le code suivant après l’appel à `PushAsync()` :

        await this.todoTable.PushFileChangesAsync();

6. Ajoutez les méthodes suivantes à `TodoItemManager` :

        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();

            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }

        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }

        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }

        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

###<a name="add-details-view"></a>Ajouter une vue de détails

Dans cette section, vous allez ajouter une nouvelle vue de détails pour un élément todo. La vue est créée lorsque l’utilisateur sélectionne un élément todo et permet d’ajouter de nouvelles images à un élément.

1. Ajoutez une nouvelle classe **TodoItemImage** au projet de bibliothèque portable avec l’implémentation suivante :

        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;

            public MobileServiceFile File { get; private set; }

            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }

            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }

            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;

                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }

            public event PropertyChangedEventHandler PropertyChanged;

            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }

2. Modifiez le fichier **App.cs**. Remplacez l’initialisation de `MainPage` par le code suivant :
    
        MainPage = new NavigationPage(new TodoList());

3. Dans **App.cs**, ajoutez la propriété suivante :

        public static object UIContext { get; set; }

4. Cliquez avec le bouton droit sur le projet de bibliothèque portable et sélectionnez **Ajouter** -> **Nouvel élément** -> **Multiplateforme** -> **Page Forms Xaml**. Nommez la vue `TodoItemDetailsView`.

5. Ouvrez **TodoItemDetailsView.xaml** et remplacez le corps de la ContentPage par le code suivant :

          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>

6. Modifiez le fichier **TodoItemDetailsView.xaml.cs** et ajoutez les instructions using suivantes :

        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;

7. Remplacez l’implémentation de `TodoItemDetailsView` par le code suivant :

        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;

            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }

            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;

                this.TodoItem = todoItem;
                this.manager = manager;

                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }

            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();

                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }

            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);

                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);

                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

###<a name="update-main-view"></a>Mettre à jour la vue principale 

Mettez à jour la vue principale pour ouvrir la vue de détails lorsqu’un élément todo est sélectionné.

Dans le fichier **TodoList.xaml.cs**, remplacez l’implémentation de `OnSelected` par le code suivant :

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

###<a name="update-android"></a>Mettre à jour le projet Android

Ajoutez au projet Android le code spécifique à la plateforme, y compris le code permettant de télécharger un fichier et d’utiliser l’appareil photo pour capturer une nouvelle image.

Ce code utilise le modèle [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) de Xamarin.Forms pour charger la classe adaptée à la plateforme au moment de l’exécution.

1. Ajoutez le composant **Xamarin.Mobile** au projet Android.

2. Ajoutez une nouvelle classe `DroidPlatform` avec l’implémentation suivante. Remplacez « YourNamespace » par l’espace de noms principal de votre projet.

        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());

                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }

                    return null;
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modifiez le fichier **MainActivity.cs**. Dans `OnCreate`, ajoutez le code suivant avant l’appel à `LoadApplication()` :

        App.UIContext = this;

###<a name="update-ios"></a>Mettre à jour le projet iOS

Ajoutez au projet iOS le code spécifique à la plateforme.

1. Ajoutez le composant **Xamarin.Mobile** au projet iOS.

2. Ajoutez une nouvelle classe `TouchPlatform` avec l’implémentation suivante. Remplacez « YourNamespace » par l’espace de noms principal de votre projet.

        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;

        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }

                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");

                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }

                    return Task.FromResult(filesPath);
                }
            }
        }

3. Modifiez le fichier **AppDelegate.cs** et supprimez les marques de commentaires de l’appel à `SQLitePCL.CurrentPlatform.Init()`.

###<a name="update-windows"></a>Mettre à jour le projet Windows

1. Installez l’extension de Visual Studio [SQLite pour Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919). Pour plus d’informations, consultez le didacticiel [Activation de la synchronisation hors connexion pour votre application Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md).

2. Modifiez le fichier **Package.appxmanifest** et vérifiez la fonctionnalité **Webcam**.

3. Ajoutez une nouvelle classe `WindowsStorePlatform` avec l’implémentation suivante. Remplacez « YourNamespace » par l’espace de noms principal de votre projet.

        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;

        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }

                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }

                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";

                    var result = await storageFolder.TryGetItemAsync(filePath);

                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }

                    return result.Name; // later operations will use relative paths
                }

                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;

                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

##Résumé

Dans cet article, vous avez appris à utiliser la prise en charge de nouveaux fichiers dans les SDK client et serveur Azure Mobile pour garantir leur compatibilité avec Azure Storage.

- Créer un compte de stockage et ajouter une chaîne de connexion à votre backend Mobile App. Seul le backend possède la clé d’accès à Azure Storage : le client mobile doit demander un jeton de signature d’accès partagé (SAS) chaque fois qu’il doit accéder à Azure Storage. Pour en savoir plus sur les jetons SAS dans Azure Storage, consultez [Présentation des signatures d’accès partagé].

- Créez un contrôleur qui sous-classe `StorageController` afin de gérer les demandes de jeton SAS et d’obtenir les fichiers associés à un enregistrement. Par défaut, les fichiers sont associés à un enregistrement en utilisant l’ID d’enregistrement dans le nom du conteneur ; le comportement peut être personnalisé en spécifiant une implémentation de `IContainerNameResolver`. La stratégie de jeton SAS peut également être personnalisée.

- Le SDK du client Azure Mobile ne stocke pas réellement de données de fichier. Le Kit de développement logiciel (SDK) client appelle en fait votre gestionnaire `IFileSyncHandler`, qui décide ensuite de la manière dont les fichiers sont stockés (le cas échéant) sur l’appareil local. Le gestionnaire de synchronisation est enregistré comme suit :

        client.InitializeFileSync(new MyFileSyncHandler(), store);

      + `IFileSyncHandler.GetDataSource` est appelé lorsque le Kit de développement logiciel (SDK) du client Azure Mobile a besoin des données de fichier (par exemple, dans le cadre du processus de chargement). Vous êtes ainsi en mesure de gérer la manière dont les fichiers sont stockés sur le périphérique local, le cas échéant, et de retourner ces informations si nécessaire.

      + `IFileSyncHandler.ProcessFileSynchronizationAction` est appelé dans le cadre du flux de synchronisation des fichiers. Une référence de fichier et une valeur d’énumération FileSynchronizationAction sont fournies afin de vous aider à déterminer la manière dont votre application doit gérer cet événement (par exemple, télécharger automatiquement un fichier lors de sa création ou de sa mise à jour, ou supprimer un fichier du périphérique local lorsque ce fichier est supprimé sur le serveur).

- Un `MobileServiceFile` peut être utilisé en mode en ligne ou hors connexion, en utilisant respectivement `IMobileServiceTable` ou `IMobileServiceSyncTable`. Dans le scénario hors connexion, le chargement se produit lorsque l’application appelle `PushFileChangesAsync`. Cette action lance le traitement de la file d’attente des opérations hors connexion ; pour chaque opération de fichier, le Kit de développement logiciel (SDK) du client Azure Mobile appelle la méthode `GetDataSource` sur l’instance `IFileSyncHandler` pour récupérer le contenu du fichier en vue du chargement.

- Pour récupérer les fichiers d’un élément, appelez la méthode `GetFilesAsync` sur l’instance `IMobileServiceTable<T>` ou IMobileServiceSyncTable<T>. Cette méthode retourne une liste des fichiers associés à l’élément de données fourni. Remarque : il s’agit d’une opération *locale* qui renvoie les fichiers en fonction de l’état de l’objet lors de sa dernière synchronisation. Pour obtenir une liste à jour des fichiers du serveur, vous devez d’abord lancer une opération de synchronisation.

        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);

- La fonctionnalité de synchronisation de fichiers utilise les notifications de modification d’enregistrements dans le magasin local afin de récupérer les enregistrements que le client a reçus dans le cadre d’une opération d’envoi ou d’extraction. Pour cela, il suffit d’activer les notifications locales et serveur pour le contexte de synchronisation à l’aide du paramètre `StoreTrackingOptions`.

        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);

      + Il existe également d’autres options de suivi de magasin, telles que des notifications locales uniquement ou serveur uniquement. Vous pouvez ajouter votre propre rappel personnalisé à l’aide de la propriété `EventManager` de `IMobileServiceClient` :

            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);

- Il est possible d’ajouter ou supprimer des fichiers dans un enregistrement en modifiant directement le stockage d’objets blob, car l’association s’effectue via une convention de dénomination. Toutefois, dans ce cas, vous devez toujours **mettre à jour l’horodatage de l’enregistrement lors de la modification des blobs associés**. Le SDK du client Azure Mobile met constamment à jour un enregistrement lors de l’ajout ou de la suppression d’un fichier,

    car certains clients mobiles possèdent déjà l’enregistrement dans le stockage local. Lorsque ces clients effectuent une extraction incrémentielle, cet enregistrement ne sera pas renvoyé et le client n’enverra pas de requête pour les nouveaux fichiers associés. Pour éviter ce problème, il est recommandé de mettre à jour l’horodatage de l’enregistrement lorsque vous apportez au stockage d’objets blob des modifications qui n’utilisent pas le SDK du client Azure Mobile.

- Le projet client utilise le modèle [Xamarin.Forms DependencyService] pour charger la classe adaptée à la plateforme au moment de l’exécution. Dans cet exemple, nous avons défini une interface `IPlatform` avec des implémentations dans des projets propres à chaque plateforme.

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Créer une application Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Présentation des signatures d’accès partagé]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Créer un compte Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account

<!---HONumber=AcomDC_0824_2016-->