<properties 
	pageTitle="Utilisation de Notification Hubs pour diffuser les dernières nouvelles (Windows Universal)" 
	description="Utilisez Azure Notification Hubs avec des balises dans l’inscription pour envoyer les dernières nouvelles à une application Windows universelle." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>


<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/27/2015" 
	ms.author="wesmc"/>

# Utilisation de Notification Hubs pour diffuser les dernières nouvelles


[AZURE.INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]


##Vue d'ensemble

Cette rubrique montre comment utiliser Azure Notification Hubs pour diffuser des notifications relatives aux dernières nouvelles vers une application Windows Store ou Windows Phone 8.1 (non-Silverlight). Si vous ciblez Windows Phone 8.1 Silverlight, consultez la version [Windows Phone](notification-hubs-ios-send-breaking-news.md). Lorsque vous aurez terminé, vous pourrez vous inscrire aux catégories de dernières nouvelles qui vous intéressent et recevoir uniquement des notifications Push pour ces catégories. Ce scénario est un modèle courant pour de nombreuses applications pour lesquelles des notifications doivent être envoyées à des groupes d’utilisateurs qui ont manifesté antérieurement leur intérêt : lecteur RSS, applications pour fans de musique, etc.

Les scénarios de diffusion sont activés en incluant une ou plusieurs _balises_ lors de la création d'une inscription dans le Notification Hub. Lorsque des notifications sont envoyées à une balise, tous les appareils pour lesquels cette balise est inscrite reçoivent la notification. Les balises étant de simples chaînes, il n’est pas nécessaire de les mettre en service à l’avance. Pour plus d'informations sur les balises, consultez la page [Recommandations relatives à Notification Hubs].

##Composants requis

Cette rubrique s'appuie sur l'application que vous avez créée dans [Prise en main de Notification Hubs][get-started]. Avant de commencer ce didacticiel, vous devez suivre celui intitulé [Prise en main de Notification Hubs][get-started].

##Ajout d’une sélection de catégories à l’application

La première étape consiste à ajouter des éléments de l’interface utilisateur à votre page principale existante qui permettent à l’utilisateur de sélectionner des catégories auxquelles s’inscrire. Les catégories sélectionnées par un utilisateur sont stockées sur l'appareil. Lorsque l'application démarre, une inscription d'appareil est créée dans votre Notification Hub avec les catégories sélectionnées sous forme de balises.

1. Ouvrez le fichier projet MainPage.xaml, puis copiez le code suivant dans l'élément **Grid** :
			
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

3. Ajoutez le code suivant dans la nouvelle classe **Notifications** :

		private NotificationHub hub;

        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }

        public async Task StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            await SubscribeToCategories(categories);
        }

		public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
            return categories != null ? categories.Split(','): new string[0];
        }

        public async Task SubscribeToCategories(IEnumerable<string> categories = null)
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            if (categories == null)
            {
                IEnumerable<string> categories = RetrieveCategories();
            }

            await hub.RegisterNativeAsync(channel.Uri, categories);
        }

    Cette classe utilise le stockage local pour stocker les catégories de nouvelles que cet appareil doit recevoir. Elle comporte également des méthodes pour s’inscrire à ces catégories.


4. Dans le fichier projet App.xaml.cs, ajoutez la propriété suivante à la classe **App** :

		public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");

	Cette propriété sert à créer l'instance **Notifications** et à y accéder.

	Dans le code ci-dessus, remplacez les espaces réservés `<hub name>` et `<connection string with listen access>` par le nom du concentrateur de notification et la chaîne de connexion pour *DefaultListenSharedAccessSignature* obtenue précédemment.

	> [AZURE.NOTE]Les informations d’identification distribuées avec une application cliente n’étant généralement pas sécurisées, vous ne devez distribuer que la clé d’accès d’écoute avec votre application cliente. L'accès d'écoute permet à votre application de s'inscrire à des notifications, mais les inscriptions existantes ne peuvent pas être modifiées et les notifications ne peuvent pas être envoyées. La clé d’accès complet est utilisée dans un service de serveur principal sécurisé pour l’envoi de notifications et la modification d’inscriptions existantes.

5. Dans MainPage.xaml.cs, ajoutez la ligne suivante :

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

            await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

            var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories));
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
	
	Cette méthode crée une liste de catégories et utilise la classe **Notifications** pour stocker la liste dans le stockage local et inscrire les balises correspondantes auprès du Notification Hub. Lorsque des catégories sont modifiées, l'inscription est à nouveau créée avec les nouvelles catégories.

Votre application peut désormais stocker un ensemble de catégories dans le stockage local sur l’appareil et s’inscrire auprès du concentrateur de notification lorsque l’utilisateur modifie la sélection des catégories.

##Inscription à des notifications

Les étapes suivantes permettent l’inscription auprès du concentrateur de notification au démarrage en utilisant les catégories qui ont été stockées dans le stockage local.

> [AZURE.NOTE]L'URI de canal affecté par le Service de notifications Push Windows (WNS) pouvant changer à n'importe quel moment, vous devez vous inscrire fréquemment aux notifications afin d'éviter les défaillances de notification. Cet exemple s’inscrit aux notifications chaque fois que l’application démarre. Pour les applications exécutées fréquemment, plus d’une fois par jour, vous pouvez probablement ignorer l’inscription afin de préserver la bande passante si moins d’un jour s’est écoulé depuis l’inscription précédente.

1. Ouvrez le fichier App.xaml.cs et ajoutez le modificateur **async** à la méthode **OnLaunched**.

2. Dans la méthode **OnLaunched**, localisez et remplacez l'appel existant à la méthode **InitNotificationsAsync** par la ligne de code suivante :

		await notifications.SubscribeToCategories();

	Cette opération garantit que chaque fois que l'application démarre, elle récupère les catégories du stockage local et demande une inscription pour ces catégories. La méthode **InitNotificationsAsync** a été créée dans le cadre du didacticiel [Prise en main de Notification Hubs] mais n'est pas nécessaire dans cette rubrique.

3. Dans le fichier projet MainPage.xaml.cs, ajoutez le code suivant à la méthode *OnNavigatedTo* :

		var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;

	Cette opération met la page principale à jour selon le statut des catégories enregistrées précédemment.

L'application est désormais terminée et peut stocker un ensemble de catégories dans le stockage local de l'appareil utilisé pour s'inscrire auprès du Notification Hub lorsque l'utilisateur modifie la sélection des catégories. Ensuite, nous allons définir un serveur principal qui peut envoyer des notifications de catégorie à cette application.

##Envoi de notifications à partir de votre serveur principal

[AZURE.INCLUDE [notification-hubs-back-end](../../includes/notification-hubs-back-end.md)]

##Exécution de l’application et génération de notifications

1. Dans Visual Studio, appuyez sur la touche F5 pour compiler et démarrer l’application.

	![][1]

	Notez que l’interface utilisateur de l’application fournit un ensemble de bascules qui vous permet de choisir les catégories auxquelles vous abonner.

2. Activez une ou plusieurs bascules de catégories, puis cliquez sur **S'abonner**.

	L'application convertit les catégories sélectionnées en balises et demande une nouvelle inscription de l'appareil pour les balises sélectionnées depuis le Notification Hub. Les catégories inscrites sont renvoyées et affichées dans une boîte de dialogue.

	![][19]

4. Envoyez une nouvelle notification depuis le serveur principal de l’une des manières suivantes :

	+ **Application console :** démarrez l'application console.

	+ **Java/PHP :** exécutez votre application/script.

	Les notifications pour les catégories sélectionnées apparaissent comme notifications toast.

	![][14]

##Étapes suivantes

Dans ce didacticiel, nous avons appris à diffuser les dernières nouvelles par catégorie. Envisagez de suivre un des didacticiels suivants qui soulignent d’autres scénarios avancés Notification Hubs :

+ [Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]

	Apprenez à développer l’application relative aux dernières nouvelles pour permettre l’envoi de notifications localisées.

+ [Notification des utilisateurs avec Notification Hubs]

	Apprenez à transmettre des notifications à des utilisateurs authentifiés spécifiques. Il s’agit d’une solution appropriée pour l’envoi de notifications uniquement vers des utilisateurs spécifiques.


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
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Utilisation de Notification Hubs pour diffuser les dernières nouvelles localisées]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notification des utilisateurs avec Notification Hubs]: /manage/services/notification-hubs/notify-users
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Recommandations relatives à Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591





 

<!---HONumber=July15_HO3-->