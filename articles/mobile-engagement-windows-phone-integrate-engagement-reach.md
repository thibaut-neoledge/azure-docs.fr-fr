<properties 
	pageTitle="Intégration du Kit de développement logiciel (SDK) Azure Mobile Engagement sur Windows Phone" 
	description="Intégration de l'outil Couverture d'Engagement sur Windows Phone"				
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

# Comment intégrer le Kit de développement de l'outil Couverture Engagement à votre projet Windows Phone

Vous devez suivre la procédure d'intégration décrite dans le document [Intégration d'Engagement sur Windows Phone](../mobile-engagement-windows-phone-integrate-engagement/) avant de suivre ce guide.

## Intégration du SDK de l'outil Couverture d'Engagement dans votre projet Windows Phone

Vous n'avez rien à ajouter. Les références et ressources  `EngagementReach` figurent déjà dans votre projet.

> [AZURE.TIP]  Vous pouvez personnaliser les images situées dans le dossier  `Resources` de votre projet, notamment l'icône de marque (la valeur par défaut est l'icône d'Engagement).

## Ajout de fonctionnalités

Le Kit de développement de l'outil Couverture d'Engagement a besoin de fonctionnalités supplémentaires.

Ouvrez votre fichier  `WMAppManifest.xml` et assurez-vous que les fonctionnalités suivantes sont indiquées dans le volet  `Capabilities` :

-   `ID_CAP_PUSH_NOTIFICATION`
-   `ID_CAP_WEBBROWSERCOMPONENT`

## Activation du service de notifications Push Microsoft

Pour utiliser le **service de notifications Push Microsoft** (appelé MPNS), votre fichier  `WMAppManifest.xml` doit contenir une balise `<App />` avec un attribut  `Publisher` défini sur le nom de votre projet.

## Initialisation du SDK de l'outil Couverture d'Engagement

### Configuration d'Engagement

La configuration d'Engagement est centralisée dans le fichier  `Resources\EngagementConfiguration.xml` de votre projet.

Modifiez ce fichier pour spécifier la configuration de couverture :

-   *Facultatif*, indiquez si l'installation du service (MPNS) natif est activée ou non entre les balises `<enableNativePush>` et `</enableNativePush>` (`true` par défaut).
-   *Facultatif*, indiquez le nom du canal de transmission Push entre les balises`<channelName>` et `</channelName>`. Indiquez celui actuellement utilisé par votre application ou laissez-le vide.

Si vous souhaitez plutôt le spécifier lors de l'exécution, vous pouvez appeler la méthode suivante avant l'initialisation de l'agent Engagement :

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			engagementConfiguration.Reach.EnableNativePush = true;                  /* [Optional] whether the native push (MPNS) is activated or not. */
			engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   /* [Optional] Provide the same channel name that your application may currently use. */
			
			/* Initialize Engagement agent with above configuration. */
			EngagementAgent.Instance.Init(engagementConfiguration);

> [AZURE.TIP] Vous pouvez spécifier le nom du canal de transmission MPNS de votre application. Par défaut, Engagement crée un nom basé sur l'AppId. Vous n'avez pas besoin de spécifier le nom vous-même, sauf si vous prévoyez d'utiliser le canal de transmission Push en dehors d'Engagement.

### Initialisation d'Engagement

Modifiez  `App.xaml.cs` :

-   Ajoutez à vos instructions `using` :

			using Microsoft.Azure.Engagement;

-   Insérez  `EngagementReach.Instance.Init` juste après  `EngagementAgent.Instance.Init` dans  `Application_Launching` :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   EngagementAgent.Instance.Init();
			   EngagementReach.Instance.Init();
			}

-   Insérez  `EngagementReach.Instance.OnActivated` dans la méthode  `Application_Activated` :

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			   EngagementReach.Instance.OnActivated(e);
			}

> [AZURE.IMPORTANT]  `EngagementReach.Instance.Init` s'exécute dans un thread dédié. Vous n'avez pas à le faire vous-même.

Soumission
----------

Microsoft impose certaines règles lors de l'utilisation de notifications Push.

Selon la documentation sur les [politiques d'applications] Microsoft, section 2.9 :

> 2.9.1) L'application doit tout d'abord décrire les notifications envoyées et **obtenir l'autorisation expresse de l'utilisateur**. Elle **doit également proposer un moyen de désactiver la réception de notifications Push**. Toutes les notifications fournies par le biais des services MPNS doivent être cohérentes avec la description fournie à l'utilisateur et doit respecter toutes les [politiques d'application][stratégies de contenu], et [exigences supplémentaires pour les types d'application spécifiques] applicables.

**En bref**: vous devez demander à l'utilisateur s'il accepte de recevoir des notifications Push. Ensuite, ajoutez un moyen de désactiver les notifications Push dans vos paramètres.

L'objet EngagementReach fournit deux méthodes pour gérer les autorisations de réception :  `EnableNativePush()` et  `DisableNativePush()`. Par exemple, vous pouvez créer une option dans les paramètres avec un bouton bascule pour désactiver ou activer le service MPNS.

Vous pouvez également décider de désactiver MPNS via la configuration Engagement \<windows-phone-sdk-reach-configuration\>.

> 2.9.2) L'application et son utilisation du service MPNS ne doivent pas utiliser une capacité réseau ou une bande passante MNPS excessive, ni surcharger de notifications un appareil Windows Phone ou autre service ou appareil Microsoft, comme déterminé par Microsoft à sa discrétion, ni endommager ou interférer avec les réseaux ou serveurs Microsoft ou tout serveur ou serveurs tiers connectés au service MNPS.

**En bref**: vous ne devez pas utiliser trop de notifications Push. Engagement gèrera cela pour vous.

> 2.9.3) Le service MNPS ne peut pas être utilisé pour envoyer des notifications stratégiques ou pouvant toucher à la vie des utilisateurs, y compris, mais sans s'y limiter, les notifications critiques liées à une affection ou un dispositif médical. MICROSOFT NE GARANTIT AUCUNEMENT LA NON-INTERRUPTION, L'ABSENCE D'ERREUR OU L'EXÉCUTION EN TEMPS RÉEL DE L'UTILISATION DU SERVICE MPNS OU DE LA LIVRAISON DES NOTIFICATIONS MPNS.

**En bref**: n'utilisez pas le service MPNS pour envoyer des informations critiques. Engagement utilise MPNS, cette règle s'applique donc aussi pour les campagnes créées dans le serveur frontal Engagement.

**Nous ne pouvons pas garantir que votre application soit validée si vous ne respectez pas ces recommandations.**

## Gestion des Push de données (facultatif)

Si vous souhaitez que votre application puisse recevoir des Push de données Couverture, vous devez implémenter deux événements de la classe EngagementReach :

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

Vous pouvez voir que le rappel de chaque méthode renvoie une valeur booléenne. Engagement envoie un retour à son serveur principal après la distribution du Push de données. Si le rappel renvoie la valeur false, le retour  `exit` sera envoyé. Dans le cas contraire, il s'agira du retour  `action`. Si aucun rappel n'est défini pour les événements, le retour  `drop` sera envoyé à Engagement.

> [AZURE.WARNING] Engagement n'est pas en mesure de recevoir des retours multiples pour un Push de données. Si vous envisagez de définir plusieurs gestionnaires pour un événement, sachez que les retours correspondent au dernier gestionnaire envoyé. Dans ce cas, nous vous recommandons de toujours renvoyer la même valeur pour éviter la confusion avec les retours du serveur frontal.

## Personnalisation de l'interface utilisateur (facultatif)

### Première étape

Vous pouvez personnaliser l'interface utilisateur de l'outil Couverture.

Pour ce faire, vous devez créer une sous-classe de la classe  `EngagementReachHandler`.

**Exemple de code :**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			   internal class ExampleReachHandler : EngagementReachHandler
			   {
			      // Override EngagementReachHandler methods depending on your needs
			   }
			}

Ensuite, définissez le contenu du champ  `EngagementReach.Instance.Handler` avec votre objet personnalisé dans votre classe  `App.xaml.cs` au sein de la méthode  `Application_Launching`.

**Exemple de code :**

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			   // your app initialization 
			   EngagementReach.Instance.Handler = new ExampleReachHandler();
			   // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] Par défaut, Engagement utilise sa propre implémentation de  `EngagementReachHandler`. Vous n'êtes pas obligé de créer la vôtre, et si vous le faites, vous n'êtes pas obligé de remplacer toutes les méthodes. Le comportement par défaut consiste à sélectionner l'objet de base d'Engagement.

### Mises en forme

Par défaut, l'outil Couverture utilisera les ressources intégrées de la DLL pour afficher les pages et les notifications.

Toutefois, vous pouvez décider d'utiliser vos propres ressources pour refléter votre marque dans ces composants.

Vous pouvez remplacer les méthodes  `EngagementReachHandler` dans votre sous-classe pour indiquer à Engagement d'utiliser vos mises en forme :

**Exemple de code :**

			// In your subclass of EngagementReachHandler
			
			public override string GetTextViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetWebViewAnnouncementUri()
			{
			   // return the path of your own xaml
			}
			
			public override string GetPollUri()
			{
			   // return the path of your own xaml
			}
			
			public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
			{
			   // return a new instance of your own notification
			}

> [AZURE.TIP] La méthode  `CreateNotification` peut renvoyer un résultat null. La notification ne s'affichera pas et la couverture campagne est abandonnée.

Pour simplifier l'implémentation de votre mise en forme, nous fournissons également notre propre xaml qui peut servir de base pour votre code. Il se trouve dans l'archive du SDK Engagement (/src/reach/).

> [AZURE.WARNING] Les sources que nous proposons sont absolument identiques à celles que nous utilisons. Par conséquent, si vous souhaitez les modifier directement, n'oubliez pas de modifier l'espace de noms et le nom.

### Position des notifications

Par défaut, une notification interne à l'application s'affiche dans la partie inférieure gauche de l'application. Vous pouvez modifier ce comportement en remplaçant la méthode  `GetNotificationPosition` de l'objet  `EngagementReachHandler`.

			// In your subclass of EngagementReachHandler
			
			public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
			{
			   // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
			}

Actuellement, vous pouvez choisir entre les positions  `BOTTOM` (par défaut) et  `TOP`.

### Message de lancement

Lorsqu'un utilisateur clique sur une notification du système (un toast), Engagement lance l'application, charge le contenu des messages envoyés et affiche la page de la campagne correspondante.

Il existe un délai entre le lancement de l'application et l'affichage de la page (selon la vitesse de votre réseau).

Pour indiquer à l'utilisateur que le chargement est en cours, vous devez fournir une information visuelle, comme une barre ou un indicateur de progression. Engagement ne peut pas gérer cela, mais propose quelques gestionnaires.

Pour implémenter le rappel, procédez comme suit :

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

Vous pouvez définir le rappel dans la méthode  `Application_Launching` de votre fichier  `App.xaml.cs`, de préférence avant l'appel  `EngagementReach.Instance.Init()`.

> [AZURE.TIP] Chaque gestionnaire est appelé par le thread d'interface utilisateur. Vous n'avez pas à vous soucier du moment où utiliser une boîte de message ou un élément relatif à l'interface utilisateur.


[politiques d'applications]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[stratégies de contenu]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[exigences supplémentaires pour les types d'application spécifiques]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

<!--HONumber=47-->
