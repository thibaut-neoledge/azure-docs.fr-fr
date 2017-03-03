---
title: "Utilisation du stockage d’objets blob à partir de Xamarin | Microsoft Docs"
description: "La bibliothèque cliente de stockage Azure pour Xamarin permet aux développeurs d’utiliser un code base C# partagé pour créer des applications iOS, Android et Windows Store avec leurs interfaces utilisateur natives. Ce didacticiel montre comment utiliser Xamarin pour créer une application qui utilise le stockage d’objets blob Azure."
services: storage
documentationcenter: xamarin
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: 44cb845d-cf78-4942-95b8-952da4f9a2c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 5e531c6c23c510936fe7e4e844db2ec3afb170dc
ms.openlocfilehash: 32d79cf050968738764c2239cbd79c73734ee855
ms.lasthandoff: 01/30/2017


---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Utilisation du stockage d’objets blob à partir de Xamarin
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Vue d'ensemble
Xamarin permet aux développeurs d’utiliser un code base C# partagé pour créer des applications iOS, Android et Windows Store avec leurs interfaces utilisateur natives. Ce didacticiel vous montre comment utiliser le stockage d’objets blob Azure avec une application Xamarin. Si vous souhaitez en savoir plus sur le stockage Azure, avant de vous plonger dans le code, consultez [Introduction à Microsoft Azure Storage](storage-introduction.md).

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Création d’une application Xamarin
Pour la prise en main, nous allons créer une application qui cible Windows, iOS et Android. Cette application créera simplement un conteneur et chargera un objet blob dans ce conteneur. Nous utiliserons Visual Studio sous Windows pour la prise en main, mais les mêmes méthodes peuvent être appliquées lors de la création d’une application à l’aide de Xamarin Studio sous Mac OS.

Procédez comme suit pour créer votre application :

1. Si vous ne l’avez pas encore fait, téléchargez et installez [Xamarin pour Visual Studio](https://www.xamarin.com/download).
2. Ouvrez Visual Studio et créez une application vide (native portable) : **Fichier > Nouveau > Projet > Multiplateforme > Application vide (native portable)**.
3. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre solution, puis sélectionnez **Gérer les packages NuGet pour la solution**. Recherchez **WindowsAzure.Storage** et installez la dernière version disponible sur tous les projets dans votre solution.
4. Créez et exécutez votre projet.

Vous devez maintenant avoir une application qui vous permet de cliquer sur un bouton et d’incrémenter un compteur.

## <a name="create-container-and-upload-blob"></a>Créer un conteneur et télécharger un objet blob
Ensuite, sous votre projet `(Portable)`, ajoutez du code à `MyClass.cs`. Ce code crée un conteneur et charge dans celui-ci un objet blob. `MyClass.cs` doit se présenter comme suit :

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

Veillez à remplacer « nom_de_votre_compte_ici » et « clé_de_votre_compte_ici » par le nom du compte et la clé de compte. 

Vos projets iOS, Android et Windows Phone ont tous des références à votre projet Portable. Vous pouvez donc écrire la totalité de votre code partagé au même endroit et l’utiliser dans tous vos projets. Pour cela, ajoutez la ligne de code suivante à chaque projet : `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>Exécution de l'application
Vous pouvez maintenant exécuter cette application dans un émulateur Android ou Windows Phone. Vous pouvez également exécuter cette application dans un émulateur iOS, mais cette opération nécessite un ordinateur Mac. Pour des instructions spécifiques sur la façon de procéder, lisez la documentation sur la [connexion de Visual Studio à un Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Une fois que vous exécutez votre application, elle crée le conteneur `mycontainer` dans votre compte de stockage. Il doit contenir l’objet blob, `myblob`, qui présente le texte, `Hello, world!`. Vous pouvez le vérifier à l’aide de [l’Explorateur de stockage Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Étapes suivantes
Dans cette prise en main, vous avez appris à créer une application multiplateforme dans Xamarin qui utilise le stockage Azure. Cette prise en main est spécifiquement axée sur un seul scénario dans le stockage Blob. Toutefois, vous pouvez en faire beaucoup plus, non seulement le Stockage Blob, mais également avec Table, Fichier et Stockage File d’attente. Pour en savoir plus, voir les articles suivants :

* [Prise en main du stockage d’objets blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
* [Prise en main d’Azure Table Storage à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
* [Prise en main d’Azure Queue Storage à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
* [Prise en main du stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]


