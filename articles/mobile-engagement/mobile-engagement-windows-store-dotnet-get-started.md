<properties
	pageTitle="Prise en main d’Azure Mobile Engagement pour les applications universelles Windows"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications universelles Windows."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/30/2015"
	ms.author="piyushjo"/>

# Prise en main d’Azure Mobile Engagement pour les applications universelles Windows

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Cette rubrique explique comment utiliser Azure Mobile Engagement pour analyser l'utilisation de votre application et envoyer des notifications Push à des segments d'utilisateurs d'une application universelle Windows. Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. Vous allez créer une application universelle Windows vide destinée à collecter des données de base d’applications et à recevoir des notifications Push à l'aide du service de notification Windows. Quand vous aurez terminé ce didacticiel, vous serez en mesure de diffuser des notifications Push vers tous les appareils, ainsi que de cibler des utilisateurs spécifiques en fonction des propriétés de leurs appareils. Veillez à bien suivre le didacticiel qui suit pour savoir comment utiliser Mobile Engagement dans le but de cibler des utilisateurs et des groupes d'appareils spécifiques.

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ [Kit de développement logiciel (SDK) Mobile Engagement Windows Universal]

> [AZURE.IMPORTANT]Vous devez suivre ce didacticiel pour avoir accès à tous les autres didacticiels Mobile Engagement pour applications universelles Windows. Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-FR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d’évaluation gratuite d’Azure</a>.

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application universelle Windows

1. Connectez-vous au portail Azure et cliquez sur **NOUVEAU** en bas de l’écran.

2. Cliquez sur **App Services**, sur **Mobile Engagement**, puis sur **Créer**.

  	![][7]

3. Dans la fenêtre contextuelle qui s'affiche, saisissez les informations suivantes :

    ![][8]

	 - **Nom de l’application** : saisissez ici le nom de votre application. Tous les caractères sont autorisés.
	 - **Plateforme** : sélectionnez la plateforme cible (**Windows Universal**) de l’application (si l’application cible plusieurs plateformes, répétez ce didacticiel pour chacune des plateformes).
	 - **Nom de la ressource d’application** : nom utilisé dans les API et les URL pour faire référence à l’application. Vous devez utiliser uniquement des caractères d'URL conventionnels. Le nom généré automatiquement vous fournit un point de départ solide. Il est également recommandé d’ajouter le nom de la plateforme pour éviter tout conflit de noms, car ce nom doit être unique.
	 - **Emplacement** : sélectionnez le centre de données qui hébergera l’application (et surtout sa collection).
	 - **Collection** : si vous avez déjà créé une application, sélectionnez une collection créée au préalable. Sinon, sélectionnez **Nouvelle collection**.
	 - **Nom de collection** : correspond à votre groupe d'applications. Cela permet également de garantir que toutes vos applications font partie d'un groupe qui autorise les calculs agrégés de statistiques. Vous devez utiliser ici le nom de votre société ou service le cas échéant.

	> [AZURE.TIP]Si votre application universelle va cibler les plateformes Windows et Windows Phone, vous devez toujours créer deux applications Mobile Engagement, une par plateforme prise en charge. Cette étape garantit que vous êtes en mesure de créer une segmentation correcte de l’audience et d’envoyer des notifications ciblées de manière appropriée pour chaque plateforme.

4. Sélectionnez l’application que vous venez de créer sous l’onglet **Applications**.

5. Ensuite, cliquez sur **Informations de connexion** pour afficher les paramètres de **Connexion** que vous voulez ajouter à l’intégration de votre Kit de développement logiciel (SDK) dans votre application mobile.

    ![][10]

6. Copiez la **chaîne de connexion** : cela vous sert à identifier cette application dans votre code d’application et à vous connecter avec Mobile Engagement à partir de votre application universelle.

    ![][11]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications Push. La documentation complète concernant l’intégration se trouve dans la Documentation du Kit de développement logiciel (SDK) Mobile Engagement Windows Universal.

Nous allons créer une application basique à l'aide de Visual Studio pour la démonstration de l'intégration.

###Création d’un nouveau projet d'application universelle Windows

Vous pouvez ignorer cette étape si vous disposez déjà d’une application et si vous maîtrisez bien le développement universel Windows.

1. Démarrez Visual Studio, puis, dans la page **Accueil**, sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez **Applications du Windows Store** -> **Applications universelles** -> **Application vide (applications universelles)**. Entrez le **Nom** et le **Nom de la solution**, puis cliquez sur **OK**.

    ![][13]

Vous avez maintenant créé un nouveau projet d’application universelle Windows dans lequel nous allons intégrer le Kit de développement logiciel Azure Mobile Engagement.

###Connectez votre application au serveur principal Mobile Engagement

1. Installez le package nuget du[Kit de développement logiciel Mobile Engagement Windows Universal] dans votre projet. Si vous ciblez les plateformes Windows et Windows Phone, vous devrez effectuer cette opération pour les deux projets. Le même package Nuget placera les fichiers binaires spécifiques de plateforme adéquats dans chaque projet.

2. Ouvrez **Package.appxmanifest** et ajoutez le code suivant s’il n’est pas ajouté automatiquement :

		Internet (Client)

	![][20]

3. Collez maintenant la chaîne de connexion, que vous avez copiée précédemment pour votre application Mobile Engagement, dans le fichier `Resources\EngagementConfiguration.xml`, entre les balises `<connectionString>` et `</connectionString>` :

	![][22]

	>[AZURE.TIP]Si votre application va cibler les plateformes Windows et Windows Phone, vous devez toujours créer deux applications Mobile Engagement, une par plateforme prise en charge. Cette étape garantit que vous êtes en mesure de créer une segmentation correcte de l’audience et d’envoyer des notifications ciblées de manière appropriée pour chaque plateforme.

4. Dans le fichier `App.xaml.cs` :

	a. Ajoutez l'instruction `using` :

			using Microsoft.Azure.Engagement;

	b. Initialisez le Kit de développement logiciel (SDK) dans la méthode **OnLaunched** :

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Insérez le code suivant dans la méthode **OnActivated** et ajoutez la méthode si elle n’y est pas déjà :

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Activer la surveillance en temps réel

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement. Dans ce but, nous allons sous-classer notre **MainPage** avec la **EngagementPage** fournie par le Kit de développement logiciel (SDK) Mobile Engagement.

1. 	Ajoutez l'instruction `using` :

		   using Microsoft.Azure.Engagement;

2. Remplacez la superclasse de **MainPage**, qui se trouve avant **PhoneApplicationPage**, avec **EngagementPage** :

	![][23]

3. Dans votre fichier `MainPage.xml` :

	a. Ajoutez une déclaration d'espace de noms :

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. Remplacez l’élément **Page** dans le nom de balise XML par **engagement:EngagementPage**.
	
> [AZURE.IMPORTANT]Si votre page remplace la méthode `OnNavigatedTo`, veillez à appeler `base.OnNavigatedTo(e)`. Sinon, l'activité ne sera pas signalée (la `EngagementPage` appelle `StartActivity` à l'intérieur de sa méthode `OnNavigatedTo`). Ceci est particulièrement important dans un projet Windows Phone, où le modèle par défaut dispose d’une méthode `OnNavigatedTo`.

###Vérifiez que votre application est connectée avec la surveillance en temps réel

Cette section montre comment vérifier que votre application est connectée au serveur principal Mobile Engagement et ce, grâce à la fonctionnalité de surveillance en temps réel de Mobile Engagement.

1. Accédez au portail Mobile Engagement.

	Dans votre portail Azure, vérifiez que vous êtes bien dans l’application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engager** en bas de l’écran :

	![][26]

2. Vous serez redirigé vers la page **Paramètres** du portail Mobile Engagement de votre application. Depuis cette page, cliquez sur l’onglet **Surveiller** comme indiqué ci-dessous. ![][30]

3. Le moniteur est prêt à afficher tous les appareils, en temps réel, qui démarrent votre application

4. Revenez dans Visual Studio, lancez votre application dans l'émulateur ou dans un appareil connecté.

5. Si cela a fonctionné, vous devriez voir une session en temps réel ! ![][33]

**Félicitations !** Vous avez terminé la première étape de ce didacticiel ; vous disposez d'une application qui se connecte au système principal Mobile Engagement, qui envoie déjà des données.

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie in-app

Mobile Engagement vous permet d’interagir et d’atteindre vos utilisateurs à l’aide de notifications Push et de la messagerie dans l’application, dans le cadre d’une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettent de configurer votre application pour la réception.

###Activer la réception des notifications Push des Services de notifications Push Windows pour votre application

1. Dans le fichier `Package.appxmanifest`, sous l’onglet **Application**, sous **Notifications**, sélectionnez **Oui** pour **Compatible toast** :

	![][35]

###Initialiser le SDK du module Couverture

1. Dans `App.xaml.cs`, appelez **EngagementReach.Instance.Init() ;** dans la fonction **OnLaunched** juste après l’initialisation de l’agent :

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. Dans `App.xaml.cs`, appelez **EngagementReach.Instance.Init(e);** dans la fonction **OnActivated** juste après initialisation de l’agent :

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Vous êtes prêt à envoyer un toast. Nous allons maintenant vérifier que vous avez correctement effectué l’intégration de base.

###Accorder l’accès à Mobile Engagement pour l’envoi de notifications Push

1. Vous devrez associer votre application à une application Windows Store pour obtenir votre **ID de sécurité (SID) de package** et votre **clé secrète** (clé secrète client). Vous pouvez créer une application à partir du [Centre de développement Windows Store]. Veillez ensuite à utiliser **Associer l’application au Store** dans Visual Studio.

2. Accédez aux **Paramètres** du portail Mobile Engagement, puis cliquez sur la section **Push natif** située sur la gauche.

3. Cliquez sur le bouton **Modifier** pour entrer l’**identificateur de sécurité (SID) du package** et votre **clé secrète**, comme indiqué ci-dessous :

	![][36]

##<a id="send"></a>Envoi d’une notification vers votre application

Nous allons à présent créer une campagne simple qui envoie une notification Push à notre application.

1. Accédez à l'onglet **REACH** de votre portail Mobile Engagement.

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne de notification Push. ![][37]

3. Configurez le premier champ de votre campagne en suivant les étapes ci-dessous : ![][38]

	a. Nommez votre campagne.

	b. Sélectionnez l’**Heure de remise** sur *N’importe quand* pour permettre à l’application de recevoir une notification, qu’elle soit démarrée ou non.

	c. Dans le texte de notification, tapez le **Titre** qui apparaîtra en gras dans la notification Push.

	d. Tapez ensuite votre message.

4. Faites défiler l’écran vers le bas et, dans la section **Contenu**, sélectionnez **Notification uniquement**. ![][39]

5. Vous avez terminé de définir la campagne de base la plus simple possible. Maintenant, reprenez le difficilement, puis cliquez sur le bouton **Créer** pour enregistrer votre campagne.

6. Dernière opération : cliquez sur **Activer** pour activer votre campagne et envoyer des notifications Push. ![][41]

Vous devez maintenant voir une notification toast à partir de votre campagne sur votre périphérique (l'application doit être fermée pour voir cette notification toast). Si l’application était en cours d’exécution, assurez-vous alors qu’elle est fermée depuis quelques minutes avant l’activation de la campagne pour pouvoir recevoir des notifications toast. Si vous souhaitez intégrer la notification intra-application afin que la notification s'affiche dans l'application lorsqu'elle est ouverte, consultez [Applications universelles Windows - intégration de superposition].

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[Kit de développement logiciel Mobile Engagement Windows Universal]: http://go.microsoft.com/?linkid=9864592
[Kit de développement logiciel (SDK) Mobile Engagement Windows Universal]: http://go.microsoft.com/?linkid=9864592
[Centre de développement Windows Store]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Applications universelles Windows - intégration de superposition]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->