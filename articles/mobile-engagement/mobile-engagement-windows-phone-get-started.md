<properties
	pageTitle="Prise en main d'Azure Mobile Engagement pour les applications Windows Phone Silverlight"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Windows Phone Silverlight."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/30/2015"
	ms.author="piyushjo"/>

# Prise en main d'Azure Mobile Engagement pour les applications Windows Phone Silverlight

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Cette rubrique vous explique comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d'une application Windows Phone Silverlight. Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. Il vous apprend à créer une application Windows Phone Silverlight vide qui collecte des données de base et reçoit des notifications Push au moyen du service de notifications Push Microsoft (MPNS). Quand vous aurez terminé ce didacticiel, vous serez en mesure de diffuser des notifications Push vers tous les appareils, ainsi que de cibler des utilisateurs spécifiques en fonction des propriétés de leurs appareils (utilisation de MPNS). Veillez à bien suivre le didacticiel qui suit pour savoir comment utiliser Mobile Engagement dans le but de cibler des utilisateurs et des groupes d'appareils spécifiques.

> [AZURE.NOTE]Si vous ciblez Windows Phone 8.1 (non-Silverlight), consultez le [didacticiel Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ Le Kit de développement logiciel [SDK Windows Phone Mobile Engagement]

> [AZURE.IMPORTANT]Pour accéder aux autres didacticiels Mobile Engagement relatifs aux applications Windows Phone Silverlight et terminer notamment celui-ci, vous devez disposer d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application Windows Silverlight

1. Connectez-vous au portail Azure et cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **App Services**, sur **Mobile Engagement**, puis sur **Créer**.

    ![][7]

3. Dans la fenêtre contextuelle qui s'affiche, saisissez les informations suivantes :

    ![][8]

  - **Nom de l'application** : saisissez ici le nom de votre application. Tous les caractères sont autorisés.
  - **Plateforme** : sélectionnez la plateforme cible (**Windows Phone Silverlight**) de l'application (si l'application cible plusieurs plateformes, répétez ce didacticiel pour chacune des plateformes).
 - **Nom de la ressource d’application** : nom utilisé dans les API et les URL pour faire référence à l’application. Vous devez utiliser uniquement des caractères d'URL conventionnels. Le nom généré automatiquement vous fournit un point de départ solide. Il est également recommandé d'ajouter le nom de la plateforme pour éviter tout conflit de noms, puisque ce nom doit être unique.
 - **Emplacement** : sélectionnez le centre de données qui hébergera l'application (et surtout sa collection).
 - **Collection** : si vous avez déjà créé une application, sélectionnez une collection créée au préalable. Sinon, sélectionnez **Nouvelle collection**.
 - **Nom de collection** : correspond à votre groupe d'applications. Cela permet également de garantir que toutes vos applications font partie d'un groupe qui autorise les calculs agrégés de statistiques. Vous devez utiliser ici le nom de votre société ou service le cas échéant.

4. Sélectionnez l’application que vous venez de créer sous l’onglet **Applications**.

5. Ensuite, cliquez sur **Informations de connexion** pour afficher les paramètres de connexion que vous voulez ajouter à l'intégration de votre Kit de développement logiciel (SDK) dans votre application mobile.

    ![][10]

6. Copiez la **chaîne de connexion** : cela vous sert à identifier cette application dans votre code d'application et à vous connecter avec Engagement Mobile à partir de votre application téléphonique.

    ![][11]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications Push. La documentation complète d'intégration peut être consultée dans la documentation du [Kit de développement logiciel (SDK)] Windows Phone Mobile Engagement.

Nous allons créer une application basique à l'aide de Visual Studio pour la démonstration de l'intégration.

###Création d'un nouveau projet Windows Phone Silverlight

1. Démarrez Visual Studio, puis, dans la page **Accueil**, sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez **Applications du Windows Store** -> **Applications Windows Phone** -> **Application vide (Silverlight Windows Phone)**. Renseignez les applications `Name` et `Solution name`, puis cliquez sur **OK**.

    ![][13]

3. Vous pouvez choisir de cibler **Windows Phone 8.0** ou **Windows Phone 8.1**.

Vous avez maintenant créé une nouvelle application Windows Phone Silverlight dans laquelle nous allons intégrer le Kit de développement logiciel Azure Mobile Engagement.

###Connexion de votre application au serveur principal Mobile Engagement

1. Installez le package nuget du [Kit de développement logiciel Mobile Engagement Windows Phone] dans votre projet.

2. Ouvrez `WMAppManifest.xml` (dans le dossier Propriétés) et assurez-vous que les éléments suivants sont déclarés (ajoutez-les dans le cas contraire) dans la balise `<Capabilities />`  :

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][20]

3. Collez maintenant la chaîne de connexion, que vous avez copiée précédemment pour votre application Mobile Engagement, dans le fichier `Resources\EngagementConfiguration.xml`, entre les balises `<connectionString>` et `</connectionString>` :

    ![][21]

4. Dans le fichier `App.xaml.cs` :

	a. Ajoutez l'instruction `using` :

			using Microsoft.Azure.Engagement;

	b. Initialisez le Kit de développement logiciel (SDK) dans la méthode `Application_Launching` :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Insérez les instructions suivantes dans `Application_Activated` :

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Activer la surveillance en temps réel

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement. Dans ce but, nous allons sous-classer notre `MainPage` avec la `EngagementPage` fournie par le Kit de développement logiciel (SDK) Mobile Engagement.

1. Ajoutez l'instruction `using` :

       à l'aide de Microsoft.Azure.Engagement ;

2. Remplacez la superclasse de **MainPage**, qui se trouve **PhoneApplicationPage**, avec **EngagementPage**, comme indiqué ci-dessous :

	![][22]

3. Dans votre fichier `MainPage.xml` :

	a. Ajoutez une déclaration d'espace de noms :

	   	 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Remplacez `phone:PhoneApplicationPage` par `engagement:EngagementPage` dans le nom de la balise xml.

###Vérifiez que votre application est connectée avec la surveillance en temps réel

Cette section montre comment vérifier que votre application est connectée au serveur principal Mobile Engagement à l'aide de la fonctionnalité de surveillance en temps réel de Mobile Engagement.

1. Accédez au portail Mobile Engagement.

	Dans votre portail Azure, vérifiez que vous êtes bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engagement** en bas de l'écran :

    ![][26]

2. Vous serez redirigé vers la page **Paramètres** du portail Mobile Engagement de votre application. Depuis cette page, cliquez sur l'onglet **Surveiller** comme indiqué ci-dessous. ![][30]

3. Le moniteur est prêt à afficher tous les appareils, en temps réel, qui démarrent votre application

4. Revenez dans Visual Studio, lancez votre application dans l'émulateur ou dans un appareil connecté.

5. Si cela a fonctionné, vous devriez voir une session ! ![][33]

**Félicitations !** Vous avez terminé la première étape de ce didacticiel ; vous disposez d'une application qui se connecte au système principal Mobile Engagement, qui envoie déjà des données.

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l'application

Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs à l'aide de notifications Push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettent de configurer votre application pour la réception.

###Permettre à votre application de recevoir les notifications Push MPNS

Ajoutez de nouvelles capacités à votre fichier `WMAppManifest.xml` :

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

   ![][34]

###Initialiser le SDK du module Couverture

1. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.Init();` dans la fonction **Application\_Launching** située immédiatement après l'initialisation de l'agent :

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.OnActivated(e);` dans la fonction **Application\_Activated** située immédiatement après l'initialisation de l'agent :

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Vous êtes prêt. Nous allons maintenant vérifier que vous avez correctement effectué cette intégration de base.

##<a id="send"></a>Envoi d’une notification vers votre application

Nous allons à présent créer une campagne simple qui envoie une notification Push à notre application.

1. Accédez à l'onglet **REACH** de votre portail Mobile Engagement.

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push. ![][35]

3. Configurez le premier champ de votre campagne en suivant les étapes ci-dessous : ![][36]

	1. Attribuez le nom de votre choix à votre campagne.
	2. Définissez l'**Heure de remise** sur *N'importe quand* pour permettre à l'application de recevoir une notification, qu'elle soit démarrée ou non.
	3. Dans le texte de notification, saisissez le Titre qui apparaîtra en gras dans la notification Push.
	4. Tapez ensuite votre message.

4. Faites défiler l'écran vers le bas et, dans la section **Contenu**, sélectionnez **Notification uniquement**. ![][37]

5. Vous avez terminé de définir la campagne de base la plus simple possible. Maintenant, reprenez le difficilement, puis cliquez sur le bouton **Créer** pour enregistrer votre campagne.

6. Dernière opération : cliquez sur **Activer** pour activer votre campagne et envoyer des notifications Push. ![][39]

7. Vous devriez à présent voir la notification suivante sur votre appareil : **Félicitations !** : ![][40]

<!-- URLs. -->
[Kit de développement logiciel (SDK) Mobile Engagement Windows Phone] : http://go.microsoft.com/?linkid=9874664[Mobile documentation du kit de développement logiciel Engagement Windows Phone]:... /Mobile-engagement-Windows-Phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!---HONumber=August15_HO8-->