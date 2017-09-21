---
title: "Utiliser Azure Notification Hubs pour diffuser les dernières nouvelles (plateforme Windows universelle)"
description: "Utilisez Azure Notification Hubs avec des balises dans l’inscription pour envoyer les dernières nouvelles à une application de plateforme Windows universelle."
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: d510e7e665adec9607aeee80802c466b363d5d5b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/08/2017

---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Utiliser Notification Hubs pour diffuser les dernières nouvelles
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Vue d'ensemble
Cette rubrique montre comment utiliser Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles vers une application Windows Store ou Windows Phone 8.1 (non-Silverlight). Si vous ciblez Windows Phone 8.1 Silverlight, reportez-vous à la version [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md). 

À l’issue de ce processus, vous pouvez vous inscrire aux catégories de dernières nouvelles qui vous intéressent pour recevoir des notifications push dans ces catégories uniquement. Ce scénario est commun à bon nombre d’applications (par exemple, aux lecteurs RSS ou aux applications qui s’adressent aux fans de musique) pour lesquelles des notifications doivent être envoyées à des groupes d’utilisateurs qui ont signalé antérieurement un intérêt. 

Vous pouvez rendre possible les scénarios de diffusion en incluant une ou plusieurs *balises* au moment de créer une inscription dans le hub de notification. Quand des notifications sont envoyées à une balise, tous les appareils qui se sont inscrits à cette balise reçoivent la notification. Comme les balises consistent en de simples chaînes, il n’est pas nécessaire de les configurer à l’avance. Pour plus d’informations sur les balises, consultez [Routage et expressions de balise Notification Hubs](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> Les projets Windows Store et Windows Phone versions 8.1 et antérieures ne sont pas pris en charge dans Visual Studio 2017. Pour en savoir plus, consultez [Plateforme cible et compatibilité dans Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs). 

## <a name="prerequisites"></a>Composants requis
Cette rubrique s’appuie sur l’application que vous avez créée dans [Prise en main de Notification Hubs][get-started]. Avant de commencer, vous devez parcourir entièrement [Prise en main de Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Ajouter une sélection de catégories à l’application
La première étape consiste à ajouter des éléments d’interface utilisateur à votre page principale existante pour permettre aux utilisateurs de sélectionner les catégories auxquelles s’inscrire. Les catégories sélectionnées sont stockées sur l’appareil. Quand l’application démarre, une inscription d’appareil est créée dans votre hub de notification, avec les catégories sélectionnées se présentant sous forme de balises.

1. Ouvrez le fichier projet MainPage.xaml, puis copiez le code suivant dans l’élément **Grid** :
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition/>
                <ColumnDefinition/>
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
            <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
        </Grid>

2. Cliquez avec le bouton droit sur le projet **Partagé**, ajoutez une nouvelle classe nommée **Notifications**, ajoutez le modificateur **public** à la définition de la classe, puis ajoutez les instructions **using** suivantes au nouveau fichier de code :
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;

3. Copiez le code suivant dans la nouvelle classe **Notifications** :
   
        private NotificationHub hub;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            return await SubscribeToCategories(categories);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }
   
        public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            if (categories == null)
            {
                categories = RetrieveCategories();
            }
   
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";
   
            return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                    categories);
        }
   
    Cette classe utilise le stockage local pour stocker les catégories de nouvelles que cet appareil doit recevoir. Au lieu d’appeler la méthode *RegisterNativeAsync*, appelez *RegisterTemplateAsync* pour vous inscrire aux catégories en utilisant une inscription de modèle. 
   
    Sachant que vous pouvez être amené à inscrire plus d’un modèle (par exemple, un pour les notifications toast et un pour les vignettes), indiquez aussi un nom de modèle (par exemple, « simpleWNSTemplateExample »). Vous devez nommer les modèles pour pouvoir les mettre à jour ou les supprimer.
   
    >[!NOTE]
    >Si un appareil inscrit plusieurs modèles avec la même balise, un message entrant qui cible cette balise provoque l’envoi de plusieurs notifications à l’appareil (un pour chaque modèle). Ce comportement s’avère utile quand un même message logique doit générer plusieurs notifications visuelles (par exemple, en affichant un badge et un toast dans une application Windows Store).
   
    Pour plus d’informations, consultez [Modèles](notification-hubs-templates-cross-platform-push-messages.md).

4. Dans le fichier projet App.xaml.cs, ajoutez la propriété suivante à la classe **App** :
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
    Cette propriété sert à créer une instance **Notifications** et à y accéder.
   
    Dans le code, remplacez les espaces réservés `<hub name>` et `<connection string with listen access>` par le nom de votre hub de notification et la chaîne de connexion de *DefaultListenSharedAccessSignature*, que vous avez obtenue précédemment.
   
   > [!NOTE]
   > Les informations d’identification distribuées avec une application cliente n’étant généralement pas sécurisées, ne distribuez que la clé d’accès d’*écoute* avec votre application cliente. Grâce à l’accès d’écoute, votre application peut s’inscrire à des notifications, mais les inscriptions existantes ne peuvent pas être modifiées et les notifications ne peuvent pas être envoyées. La clé d’accès complet est utilisée dans un service backend sécurisé pour l’envoi de notifications et la modification d’inscriptions existantes.
   > 
   > 
5. Dans le fichier projet MainPage.xaml.cs, ajoutez la ligne suivante :
   
        using Windows.UI.Popups;

6. Dans le fichier projet MainPage.xaml.cs, ajoutez la méthode suivante :
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
            var categories = new HashSet<string>();
            if (WorldToggle.IsOn) categories.Add("World");
            if (PoliticsToggle.IsOn) categories.Add("Politics");
            if (BusinessToggle.IsOn) categories.Add("Business");
            if (TechnologyToggle.IsOn) categories.Add("Technology");
            if (ScienceToggle.IsOn) categories.Add("Science");
            if (SportsToggle.IsOn) categories.Add("Sports");
   
            var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
   
    Cette méthode crée une liste de catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local. Elle inscrit aussi les balises correspondantes auprès de votre hub de notification. Quand les catégories sont modifiées, l’inscription est recréée avec les nouvelles catégories.

Votre application peut alors stocker un ensemble de catégories dans le stockage local de l’appareil. L’application s’inscrit auprès du hub de notification chaque fois que des utilisateurs modifient la sélection de catégories.

## <a name="register-for-notifications"></a>Inscription à des notifications
Dans cette section, vous allez vous inscrire auprès du hub de notification au démarrage en utilisant les catégories que vous avez stockées dans le stockage local.

> [!NOTE]
> L’URI de canal affecté par le service de notification Windows (WNS) pouvant changer à tout moment, vous devez vous inscrire fréquemment aux notifications pour éviter des échecs de notifications. Cet exemple s’inscrit aux notifications chaque fois que l’application démarre. Pour les applications que vous exécutez fréquemment (plus d’une fois par jour), vous pouvez probablement ignorer l’inscription de façon à préserver la bande passante si moins d’un jour s’est écoulé depuis l’inscription précédente.
> 
> 

1. Pour utiliser la classe `notifications` pour vous abonner en fonction des catégories, ouvrez le fichier App.xaml.cs et mettez à jour la méthode **InitNotificationsAsync**.
   
        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
   
        var result = await notifications.SubscribeToCategories();
   
    Ce processus garantit que lorsque l’application démarre, elle récupère les catégories dans le stockage local et demande une inscription pour ces catégories. Vous avez créé la méthode **InitNotificationsAsync** dans le didacticiel [Prise en main de Notification Hubs][get-started].

2. Dans le fichier projet MainPage.xaml.cs, ajoutez le code suivant à la méthode *OnNavigatedTo* :
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldToggle.IsOn = true;
            if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
            if (categories.Contains("Business")) BusinessToggle.IsOn = true;
            if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
            if (categories.Contains("Science")) ScienceToggle.IsOn = true;
            if (categories.Contains("Sports")) SportsToggle.IsOn = true;
        }
   
    Ce code met à jour la page principale en fonction de l’état des catégories enregistrées précédemment.

L’application est maintenant terminée. Elle peut stocker un ensemble de catégories dans le stockage local de l’appareil utilisé pour s’inscrire auprès du hub de notification quand les utilisateurs modifient la sélection de catégories. Dans la section suivante, vous allez définir un backend capable d’envoyer des notifications de catégorie à cette application.

## <a name="send-tagged-notifications"></a>Envoyer des notifications avec balises
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>Exécuter l’application et générer des notifications
1. Dans Visual Studio, sélectionnez **F5** pour compiler et démarrer l’application.  
    L’interface utilisateur de l’application fournit un ensemble de bascules qui vous permet de choisir les catégories auxquelles vous abonner. 
   
    ![Application de diffusion des dernières nouvelles][1]

2. Activez une ou plusieurs bascules de catégories, puis cliquez sur **S’abonner**.
   
    L'application convertit les catégories sélectionnées en balises et demande une nouvelle inscription de l'appareil pour les balises sélectionnées depuis le Notification Hub. Les catégories inscrites sont retournées et affichées dans une boîte de dialogue.
   
    ![Bascules de catégories et bouton S’abonner][19]

3. Envoyez une nouvelle notification à partir du backend de l’une des façons suivantes :

   * **Application console** : démarrez l’application console.
   * **Java/PHP** : exécutez votre application ou script.
     
     Les notifications pour les catégories sélectionnées apparaissent comme notifications toast.
     
     ![Notifications toast][14]

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à diffuser les dernières nouvelles par catégorie. Envisagez d’effectuer le didacticiel suivant, qui présente un autre scénario Notification Hubs avancé :

* [Utilisation de Notification Hubs pour envoyer les dernières nouvelles localisées] Ce didacticiel explique comment développer l’application de diffusion des dernières nouvelles de façon à envoyer des notifications localisées.

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /azure/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification
[Utilisation de Notification Hubs pour envoyer les dernières nouvelles localisées]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notify users with Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591

