<properties 
	pageTitle="Intégration du Kit de développement logiciel du module Couverture des applications Windows Universal" 
	description="Intégration du module Couverture d’Azure Mobile Engagement avec des applications Windows Universal"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="piyushjo" />

#Intégration du Kit de développement logiciel du module Couverture des applications Windows Universal

Vous devez suivre la procédure d'intégration décrite dans la rubrique [Intégration du Kit de développement logiciel Windows Universal Engagement](mobile-engagement-windows-store-integrate-engagement.md) avant de suivre ce guide.

##Intégration du Kit de développement logiciel (SDK) du module Couverture d’Engagement à votre projet Windows Universal

Vous n'avez rien à ajouter. Les références et les ressources de `EngagementReach` se trouvent déjà dans votre projet.

> [AZURE.TIP]Vous pouvez personnaliser les images situées dans le dossier `Resources` de votre projet, en particulier l'icône de marque (par défaut, il s'agit de l'icône d'Engagement). Sur les applications Universal, vous pouvez également déplacer le dossier `Resources` sur votre projet partagé pour partager son contenu entre les applications, mais vous devrez conserver le fichier `Resources\EngagementConfiguration.xml` à son emplacement par défaut, car il est dépendant de la plate-forme.

##Activer le service de notification Windows

Pour utiliser le **service de notification Windows** (WNS) dans votre fichier `Package.appxmanifest` sur `Application UI`, cliquez sur `All Image Assets` dans la zone de gauche. À droite de la zone dans `Notifications`, modifiez `toast capable` en remplaçant `(not set)` par `(Yes)`.

Vous devez, par ailleurs, synchroniser votre application avec votre compte Microsoft et la plateforme Engagement. Pour ce faire, vous devez créer un compte ou vous connecter au [Centre de développement Windows](https://dev.windows.com). Ensuite, créez une application et recherchez le SID et la clé secrète. Sur le serveur frontal Engagement, accédez au paramètre de votre application dans `native push`, puis collez vos informations d'identification. Ensuite, cliquez avec le bouton droit sur votre projet, puis sélectionnez `store` et `Associate App with the Store...`. Vous devez simplement sélectionner l'application créée avant de la synchroniser.

##Initialiser le SDK du module Couverture d'Engagement

Modifiez le `App.xaml.cs` :

-   Ajoutez à vos instructions `using` :

		using Microsoft.Azure.Engagement;

-   Insérez `EngagementReach.Instance.Init` juste après `EngagementAgent.Instance.Init` dans `OnLaunched` :

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

-   Si vous souhaitez activer Engagement Reach lorsque votre application est activée par une commande, une autre application ou un schéma personnalisé, remplacez la méthode `OnActivated` :

		protected override void OnActivated(IActivatedEventArgs args)
		{
		  EngagementAgent.Instance.Init(args);
		  EngagementReach.Instance.Init(args);
		}

	`EngagementReach.Instance.Init` est exécuté dans un thread dédié. Vous n'avez pas à le faire vous-même.

> [AZURE.TIP]Vous pouvez spécifier le nom du canal Push WNS de votre application dans le fichier `Resources\EngagementConfiguration.xml` de votre projet dans `<channelName></channelName>`. Par défaut, Engagement crée un nom basé sur l'ID de l'application. Il n'est pas nécessaire de spécifier le nom vous-même, sauf si vous prévoyez d'utiliser le canal Push en dehors d'Engagement.

##Intégration

Engagement permet d’implémenter les notifications et les annonces de Couverture de deux manières : l’intégration de superposition et l’intégration de vue web.

L’intégration de superposition ne nécessite pas l'écriture d'une grande quantité de code. Il vous suffit d'ajouter le mot-clé EngagementPageOverlay à vos pages et à vos fichiers .xaml et .cs. De plus, si vous personnalisez la vue par défaut d'Engagement, votre personnalisation sera partagée par toutes les pages avec mots-clés, et ne sera définie qu'une seule fois. Si vos pages doivent hériter d'un objet autre qu'EngagementPageOverlay, vous n'aurez d'autre choix que d'utiliser l'intégration de vue web.

L’intégration de vue web est plus compliquée à implémenter. Toutefois, si vos pages d’applications doivent hériter d’un objet autre que « Page », vous devrez intégrer la vue web et son comportement.

> [AZURE.TIP]Vous pouvez envisager d’ajouter un élément `<Grid></Grid>` au niveau racine pour encadrer l’ensemble du contenu des pages. Pour l'intégration de vue web, il vous suffit d'ajouter Webview en tant qu'enfant de cette grille. Si vous devez définir un composant Engagement à un autre endroit, vous devrez gérer la taille de l'affichage vous-même.

### Intégration de superposition

Engagement fournit une fonctionnalité de superposition pour l'affichage des notifications et des annonces.

Si vous voulez l’utiliser, n’utilisez pas l’intégration de vue web.

Dans le fichier .xaml, remplacez la référence EngagementPage par EngagementPageOverlay

-   Ajoutez une déclaration d'espace de noms :

			xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   Remplacez `engagement:EngagementPage` par `engagement:EngagementPageOverlay` :

**Avec EngagementPage :**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		
		    <!-- layout -->
		</engagement:EngagementPage>

**Avec EngagementPageOverlay :**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		
		    <!-- layout -->
		</engagement:EngagementPageOverlay>

> **Avec EngagementPageOverlay pour Windows 8.1 :**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		    <Grid>
		      <!-- layout -->
		    </Grid>
		</engagement:EngagementPageOverlay>

Dans le fichier .cs, ajoutez à votre page le mot-clé "EngagementPageOverlay" au lieu du mot-clé "EngagementPage", puis importez "Microsoft.Azure.Engagement.Overlay".

			using Microsoft.Azure.Engagement.Overlay;

-   Remplacez `EngagementPage` par `EngagementPageOverlay` :

**Avec EngagementPage :**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**Avec EngagementPageOverlay :**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

Cette page utilise le mécanisme de superposition d'Engagement. Il n'est donc pas nécessaire d'insérer une vue web.

La superposition Engagement utilise le premier élément « Grid » qu'elle trouve dans le fichier .xaml pour ajouter deux vues web à votre page. Si vous voulez connaître l'emplacement auquel seront définies les vues web, vous pouvez définir une grille nommée « EngagementGrid » de la manière suivante :

			<Grid x:Name="EngagementGrid"></Grid>

Vous pouvez personnaliser la superposition des notifications et des annonces directement dans les fichiers xaml et .cs :

-   `EngagementAnnouncement.html` : la conception HTML de la vue web de `Announcement`
-   `EngagementOverlayAnnouncement.xaml` : la conception XAML de `Announcement`
-   `EngagementOverlayAnnouncement.xaml.cs` : le code relatif à `EngagementOverlayAnnouncement.xaml`.
-   `EngagementNotification.html` : la conception HTML de la vue web de `Notification`
-   `EngagementOverlayNotification.xaml` : la conception XAML de `Notification`
-   `EngagementOverlayNotification.xaml.cs` : le code relatif à `EngagementOverlayNotification.xaml`.
-   `EngagementPageOverlay.cs` : le code d'affichage de la superposition des annonces et des notifications `Overlay`.

### Intégration de la vue web

Si vous voulez l’utiliser, n’utilisez pas l’intégration de superposition.

Pour afficher le contenu Engagement, vous devez intégrer les deux vues web XAML à chaque page et afficher une notification et une annonce. Ajoutez le code suivant au fichier XAML :

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **Pour l'intégration Windows 8.1 :**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPage>

Le fichier .cs associé doit ressembler à ce qui suit :

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			   /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			
			    Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			  }
			
			  #region to implement
			  /* Allow webview script to notify system */
			  private void scriptEvent(object sender, NotifyEventArgs e)
			  {
			  }
			
			  /* When page is left ensure to detach SizeChanged handler */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			    base.OnNavigatedFrom(e);
			  }
			
			  /* "width" is the current width of your application display */
			  double width = Window.Current.Bounds.Width;
			
			  /* "height" is the current height of your application display */
			  double height = Window.Current.Bounds.Height;
			
			  /// <summary>
			  /// Set your webview elements to the correct size
			  /// </summary>
			  /// <param name="width">The width of your current display</param>
			  /// <param name="height">The height of your current display</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			  /// </summary>
			  /// <param name="sender">Original event trigger</param>
			  /// <param name="e">Window Size Changed Event argument</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			  }
			  #endregion
			}

> Cette implémentation intègre le redimensionnement des vues web quand l'orientation de l'écran de l'appareil change.

##Gérer les Push de données (facultatif)

Si vous voulez que votre application puisse recevoir les Push de données du module Couverture, vous devez implémenter deux événements de la classe EngagementReach :

Dans App.xaml.cs dans « Public App(){} », ajoutez :

			EngagementReach.Instance.DataPushStringReceived += (body) =>
			{
			  Debug.WriteLine("String data push message received: " + body);
			  return true;
			};
			
			EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
			{
			  Debug.WriteLine("Base64 data push message received: " + encodedBody);
			  // Do something useful with decodedBody like updating an image view
			  return true;
			};

Vous pouvez voir que le rappel de chaque méthode renvoie un booléen. Engagement envoie un feedback à son serveur principal après la répartition des Push de données. Si le rappel renvoie la valeur false, le retour `exit` sera envoyé. Dans le cas contraire, il s'agira du retour `action`. Si aucun rappel n'est défini pour les événements, le retour `drop` sera envoyé à Engagement.

> [AZURE.WARNING]Engagement ne peut pas recevoir plusieurs feedbacks pour un Push de données. Si vous prévoyez de définir plusieurs gestionnaires pour un événement, sachez que le feedback correspondra au dernier envoyé. Dans ce cas, nous vous recommandons de toujours retourner la même valeur afin d'éviter un feedback prêtant à confusion sur le serveur frontal.

##Personnaliser l'interface utilisateur (facultatif)

### Première étape

Il vous est possible de personnaliser l'interface utilisateur du module Couverture.

Pour cela, vous devez créer une sous-classe de la classe `EngagementReachHandler`.

**Exemple de code :**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  internal class ExampleReachHandler : EngagementReachHandler
			  {
			   // Override EngagementReachHandler methods depending on your needs
			  }
			}

Ensuite, définissez le contenu du champ `EngagementReach.Instance.Handler` à l'aide de votre objet personnalisé dans la classe `App.xaml.cs` de la méthode `App()`.

**Exemple de code :**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE]Par défaut, Engagement utilise sa propre implémentation de `EngagementReachHandler`. Il n'est pas nécessaire de créer votre propre implémentation. Toutefois, si vous le faites, vous n'aurez pas à remplacer chaque méthode. Le comportement par défaut consiste à sélectionner l'objet de base Engagement.

### Vue web

Par défaut, le module Couverture utilise les ressources intégrées du DLL pour afficher les notifications et les pages.

Pour une personnalisation maximale, nous utilisons uniquement les vues web. Si vous voulez personnaliser les dispositions, remplacez directement les fichiers de ressources `EngagementAnnouncement.html` et `EngagementNotification.html`. Engagement a besoin de tous les codes de `<body></body>` pour être correctement exécuté. Toutefois, vous pouvez ajouter des balises en dehors de `engagement_webview_area`.

Vous pouvez décider d'utiliser vos propres ressources.

Vous pouvez remplacer les méthodes `EngagementReachHandler` de votre sous-classe pour indiquer à Engagement d'utiliser vos dispositions. Veillez, toutefois, à intégrer le mécanisme Engagement :

**Exemple de code :**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


Par défaut, AnnouncementHTML est `ms-appx-web:///Resources/EngagementAnnouncement.html`. Il correspond au fichier HTML qui conçoit le contenu du message Push (annonce texte, annonce web et annonce de sondage). AnnouncementName est `engagement_announcement_content`. Il s'agit du nom de la conception de vue web de la page XAML.

NotificationHTML est `ms-appx-web:///Resources/EngagementNotification.html`. Il s'agit du fichier HTML qui conçoit la notification d'un message Push. NotificationName est `engagement_notification_content`. Il s'agit du nom de la conception de vue web de la page XAML.

### Personnalisation

Vous pouvez personnaliser comme bon vous semble les vues web des notifications et des annonces, du moment que vous conservez l'objet Engagement. Veillez à décrire l'objet webview trois fois. La première fois dans le fichier .xaml, la deuxième fois dans la méthode « setwebview() » du fichier .cs, et la troisième fois dans le fichier .html.

-   Dans le fichier .xaml, vous décrivez le composant webview de disposition graphique.
-   Dans le fichier .cs, vous pouvez définir "setwebview()" où vous pourrez définir la dimension des deux vues web (notification, annonce). C'est très efficace quand l'application est redimensionnée.
-   Dans le fichier .html Engagement, nous décrivons le contenu de la vue web, sa conception et la position des éléments les uns par rapport aux autres.

### Lancer un message

Quand un utilisateur clique sur une notification système (un toast), Engagement lance l'application, charge le contenu des messages Push et affiche la page de la campagne correspondante.

Il y a un délai entre le lancement de l'application et l'affichage de la page (qui dépend de la vitesse de votre réseau).

Pour indiquer à l'utilisateur qu'un chargement est en cours, vous devez fournir une indication visuelle, telle qu'une barre ou un indicateur de progression. Engagement ne peut pas gérer cela lui-même. Toutefois, il fournit plusieurs gestionnaires à cet effet.

Pour implémenter le rappel, dans le fichier App.xaml.cs, dans "Public App(){}", ajoutez ce qui suit :

			/* The application has launched and the content is loading.
			 * You should display an indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
			
			/* The application has finished loading the content and the page
			 * is about to be displayed.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
			
			/* The content has been loaded, but an error has occurred.
			 * You can provide an information to the user.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Vous pouvez définir le rappel dans la méthode « Public App(){} » du fichier `App.xaml.cs`, de préférence avant l'appel `EngagementReach.Instance.Init()`.

> [AZURE.TIP]Chaque gestionnaire est appelé par le thread d'interface utilisateur. Vous n'avez pas de souci à vous faire quand vous utilisez un MessageBox ou autre objet d'interface utilisateur.

##Astuce concernant les schémas personnalisés

Il est possible d'utiliser des schémas personnalisés. Vous pouvez envoyer un autre type d'URI depuis le serveur frontal Engagement, destiné à être utilisé dans l'application Engagement. Les schémas par défaut comme `http, ftp, ...` sont gérés par Windows. Une fenêtre vous informera si aucune application par défaut n'est installée sur l'appareil. Les autres schémas, comme les schémas d'application, peuvent également être utilisés. De plus, vous pouvez utiliser un schéma personnalisé pour votre application.

Le moyen le plus simple de définir un schéma personnalisé dans votre application est d'ouvrir `Package.appxmanifest` et d'accéder au panneau `Declarations`. Sélectionnez `Protocol` dans la case de défilement Déclarations disponibles, puis ajoutez-le. Remplacez le contenu du champ `Name` par le nom du nouveau protocole.

Pour utiliser ce protocole, modifiez `App.xaml.cs` à l'aide de la méthode `OnActivated`, et n'oubliez pas d'initialiser Engagement ici également :

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion
 

<!---HONumber=August15_HO6-->