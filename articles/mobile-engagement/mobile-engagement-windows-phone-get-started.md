<properties
	pageTitle="Prise en main d'Azure Mobile Engagement pour les applications Windows Phone Silverlight"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications push pour les applications Windows Phone Silverlight."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="piyushjo" />

# Prise en main d'Azure Mobile Engagement pour les applications Windows Phone Silverlight

[AZURE.INCLUDE [Sélecteur de didacticiel Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique vous explique comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications push à des utilisateurs segmentés d'une application Windows Phone Silverlight. Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. Il vous apprend à créer une application Windows Phone Silverlight vide qui collecte des données de base et reçoit des notifications push au moyen du service de notifications push Microsoft (MPNS).

> [AZURE.NOTE] Si vous ciblez Windows Phone 8.1 (non-Silverlight), consultez le [didacticiel Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md).

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ Package NuGet [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application Windows Phone

[AZURE.INCLUDE [Création d’application Mobile Engagement dans le portail](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. La documentation complète d’intégration peut être consultée dans [Intégration du Kit de développement logiciel (SDK) Windows Phone pour Azure Mobile Engagement.](mobile-engagement-windows-phone-sdk-overview.md)

Nous allons créer une application basique à l'aide de Visual Studio pour la démonstration de l'intégration.

###Création d'un nouveau projet Windows Phone Silverlight

Les étapes suivantes supposent l’utilisation de Visual Studio 2015, même si elles sont similaires dans les versions antérieures de Visual Studio.

1. Démarrez Visual Studio, puis, dans l’écran **Accueil**, sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez **Windows 8** -> **Windows Phone** -> **Application vide (Silverlight Windows Phone)**. Entrez le **Nom** et le **Nom de la solution** de l’application, puis cliquez sur **OK**.

    ![][1]

3. Vous pouvez choisir de cibler **Windows Phone 8.0** ou **Windows Phone 8.1**.

Vous avez maintenant créé une nouvelle application Windows Phone Silverlight dans laquelle nous allons intégrer le Kit de développement logiciel Azure Mobile Engagement.

###Connexion de votre application au serveur principal Mobile Engagement

1. Installez le package NuGet [MicrosoftAzure.MobileEngagement] dans votre projet.

2. Ouvrez `WMAppManifest.xml` (dans le dossier Propriétés) et assurez-vous que les éléments suivants sont déclarés (dans le cas contraire, ajoutez-les) dans la balise `<Capabilities />`  :

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

    ![][2]

3. Collez maintenant la chaîne de connexion que vous avez copiée précédemment pour votre application Mobile Engagement dans le fichier `Resources\EngagementConfiguration.xml`, entre les balises `<connectionString>` et `</connectionString>` :

    ![][3]

4. Dans le fichier `App.xaml.cs` :

	a. Ajoutez l'instruction `using` :

			using Microsoft.Azure.Engagement;

	b. Initialisez le Kit de développement logiciel (SDK) dans la méthode `Application_Launching` :

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. Insérez les instructions suivantes dans le code `Application_Activated` :

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>Activation de l’analyse en temps réel

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

1. Dans le fichier MainPage.xaml.cs, ajoutez l’instruction `using` :

    	using Microsoft.Azure.Engagement;

2. Remplacez la classe de base de **MainPage**, qui se trouve devant **PhoneApplicationPage**, par **EngagementPage**.

    	class MainPage : EngagementPage 
	
3. Dans votre fichier `MainPage.xml` :

	a. Ajoutez une déclaration d'espace de noms :

	   	 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Remplacez `phone:PhoneApplicationPage` dans le nom de la balise XML par `engagement:EngagementPage`.

##<a id="monitor"></a>Connexion d’application avec l’analyse en temps réel

[AZURE.INCLUDE [Connexion d’application avec l’analyse en temps réel](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activation des notifications push et de la messagerie in-app

Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs à l'aide de notifications push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettent de configurer votre application pour la réception.

###Permettre à votre application de recevoir les notifications push MPNS

Ajoutez de nouvelles capacités à votre fichier `WMAppManifest.xml` :

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###Initialiser le SDK du module Couverture

1. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.Init();` dans la fonction **Application\_Launching** juste après l’initialisation de l’agent :

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.OnActivated(e);` dans la fonction **Application\_Activated**, juste après l’initialisation de l’agent :

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Vous êtes prêt. Nous allons maintenant vérifier que vous avez correctement effectué cette intégration de base.

##<a id="send"></a>Envoi d’une notification vers votre application

[AZURE.INCLUDE [Création d’une campagne push Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Vous devez maintenant voir une notification sur votre appareil, s’affichant en tant que notification d’application si l’application est ouverte, ou sinon en tant que notification toast comme suit :

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!---HONumber=AcomDC_0504_2016-->