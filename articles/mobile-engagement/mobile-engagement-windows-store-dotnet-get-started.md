<properties
	pageTitle="Prise en main d’Azure Mobile Engagement pour les applications universelles Windows"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications push pour les applications universelles Windows."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Prise en main d’Azure Mobile Engagement pour les applications universelles Windows

[AZURE.INCLUDE [Sélecteur de didacticiel Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique explique comment utiliser Azure Mobile Engagement pour analyser l'utilisation de votre application et envoyer des notifications push à des segments d'utilisateurs d'une application universelle Windows. Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. Vous allez créer une application universelle Windows vide destinée à collecter des données de base d’applications et à recevoir des notifications push à l'aide du service de notification Windows.

Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ Package NuGet [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-store-dotnet-get-started).

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application universelle Windows

[AZURE.INCLUDE [Création d’application Mobile Engagement dans le portail](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. La documentation complète d’intégration peut être consultée dans [Intégration du Kit de développement logiciel (SDK) universel Mobile Engagement Windows](../mobile-engagement-windows-store-sdk-overview/).

Nous allons créer une application basique à l'aide de Visual Studio pour la démonstration de l'intégration.

###Création d’un nouveau projet d'application universelle Windows

Les étapes suivantes supposent l’utilisation de Visual Studio 2015, même si elles sont similaires dans les versions antérieures de Visual Studio.

1. Démarrez Visual Studio, puis, dans l’écran **Accueil**, sélectionnez **Nouveau projet**.

2. Dans le menu contextuel, sélectionnez **Windows 8** -> **Universal** -> **Application vide (Universal Windows 8.1)**. Entrez le **Nom** et le **Nom de la solution** de l’application, puis cliquez sur **OK**.

    ![][1]

Vous avez maintenant créé un nouveau projet d’application universelle Windows dans lequel nous allons intégrer le Kit de développement logiciel Azure Mobile Engagement.

###Connectez votre application au serveur principal Mobile Engagement

1. Installez le package nuget [MicrosoftAzure.MobileEngagement] dans votre projet. Si vous ciblez les plateformes Windows et Windows Phone, vous devrez effectuer cette opération pour les deux projets. Pour Windows 8.x et Windows Phone 8.1, le même package Nuget place les fichiers binaires spécifiques de plateforme adéquats dans chaque projet.

2. Ouvrez **Package.appxmanifest** et assurez-vous que la fonctionnalité suivante y est ajoutée :

		Internet (Client)

	![][2]

3. Collez maintenant la chaîne de connexion, que vous avez copiée précédemment pour votre application Mobile Engagement, dans le fichier `Resources\EngagementConfiguration.xml`, entre les balises `<connectionString>` et `</connectionString>` :

	![][3]

	>[AZURE.TIP] Si votre application va cibler les plateformes Windows et Windows Phone, vous devez toujours créer deux applications Mobile Engagement, une par plateforme prise en charge. Cette étape garantit que vous êtes en mesure de créer une segmentation correcte de l’audience et d’envoyer des notifications ciblées de manière appropriée pour chaque plateforme.

4. Dans le fichier `App.xaml.cs` :

	a. Ajoutez l'instruction `using` :

			using Microsoft.Azure.Engagement;

	b. Ajoutez une méthode dédiée pour l’initialisation et la configuration d’Engagement :

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

			 //... rest of the code
           }

    c. Initialisez le kit de développement logiciel (SDK) dans la méthode **OnLaunched** :

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  InitEngagement(e);

			  //... rest of the code
			}

	c. Insérez le code suivant dans la méthode **OnActivated** et ajoutez la méthode si elle n’est pas encore présente :

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  InitEngagement(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Activation de l’analyse en temps réel

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

1. 	Dans le fichier **MainPage.xaml.cs**, ajoutez l’instruction `using` suivante :

		using Microsoft.Azure.Engagement.Overlay;

2. Remplacez la classe de base de **MainPage** **Page** par **EngagementPageOverlay** :

		class MainPage : EngagementPageOverlay

3. Dans le fichier `MainPage.xaml` :

	a. Ajoutez une déclaration d'espace de noms :

		xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

	b. Remplacez **Page** dans le nom de balise XML par **engagement:EngagementPageOverlay**.
	
> [AZURE.IMPORTANT] Si votre page remplace la méthode `OnNavigatedTo`, veillez à appeler `base.OnNavigatedTo(e)`. Sinon, l'activité ne sera pas signalée (la `EngagementPage` appelle `StartActivity` à l'intérieur de sa méthode `OnNavigatedTo`). Ceci est particulièrement important dans un projet Windows Phone où le modèle par défaut dispose d’une méthode `OnNavigatedTo`.

##<a id="monitor"></a>Connexion d’application avec l’analyse en temps réel

[AZURE.INCLUDE [Connexion d’application avec l’analyse en temps réel](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie in-app

Mobile Engagement vous permet d’interagir et d’atteindre vos utilisateurs à l’aide de notifications push et de la messagerie dans l’application, dans le cadre d’une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettent de configurer votre application pour la réception.

###Activer la réception des notifications push des Services de notifications push Windows pour votre application

1. Dans le fichier `Package.appxmanifest`, sous l’onglet **Application**, sous **Notifications**, pour **Compatible toast**, sélectionnez **Oui**.

	![][5]

###Initialiser le SDK du module Couverture

Dans `App.xaml.cs`, appelez **EngagementReach.Instance.Init(e);** dans la fonction **InitEngagement**, juste après initialisation de l’agent :

        private void InitEngagement(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Vous êtes prêt à envoyer un toast. Nous allons maintenant vérifier que vous avez correctement effectué l’intégration de base.

###Accorder l’accès à Mobile Engagement pour l’envoi de notifications push

1. Ouvrez le [Centre de développement Windows Store] dans votre navigateur web, connectez-vous et créez un compte si nécessaire.
2. Cliquez sur **Tableau de bord** en haut à droite, puis cliquez sur **Créer une application** dans le menu du panneau gauche. 

	![][9]

2. Créez votre application en réservant son nom.

	![][10]

3. Une fois l’application créée, accédez à **Services -> Notifications Push** dans le menu de gauche.

	![][11]

4. Dans la section Notifications Push, cliquez sur le lien du **site Services Microsoft Live**.

	![][12]

5. Vous accédez à la section des informations d’identification Push. Assurez-vous que vous êtes dans la section **Paramètres de l’application** et copiez le **SID du package** et votre **clé secrète client**

	![][13]

6. Accédez aux **Paramètres** du portail Mobile Engagement, puis cliquez sur la section **Push natif** à gauche. Cliquez ensuite sur le bouton **Modifier** pour entrer l’**identificateur de sécurité (SID) du package** et votre **clé secrète**, comme indiqué ci-dessous :

	![][6]

8. Enfin, assurez-vous que vous avez associé votre application Visual Studio à cette application que vous avez créée dans l’App Store. Pour ce faire, vous devez cliquer sur **Associer l’application au Store** à partir de Visual Studio.

	![][7]

##<a id="send"></a>Envoi d’une notification vers votre application

[AZURE.INCLUDE [Création d’une campagne push Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Si l’application était en cours d’exécution, une notification dans l’application s’affiche. Par contre, si l’application était fermée, une notification toast apparaît. Si vous voyez une notification dans l’application, mais pas qu’il ne s’agit pas d’une notification toast, et que vous exécutez l’application en mode débogage dans Visual Studio, accédez à **Événements de cycle de vie -> Interrompre** dans la barre d’outils pour vous assurer que l’application est effectivement suspendue. Si vous avez simplement cliqué sur le bouton Accueil pendant le débogage de l’application dans Visual Studio, l’application n’est pas systématiquement interrompue et la notification dans l’application ne fait pas place à une notification toast.

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centre de développement Windows Store]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png

<!---HONumber=AcomDC_0330_2016-->