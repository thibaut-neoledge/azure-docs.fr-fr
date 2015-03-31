<properties 
	pageTitle="Prise en main d'Azure Mobile Engagement sur Windows Phone" 
	description="Découvrez comment utiliser Azure Mobile Engagement à l'aide des analyses et des notifications Push sur Windows Phone."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="C#" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Prise en main de Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Universal">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

Cette rubrique vous explique comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d'une application Windows Phone. 
Ce didacticiel présente un scénario de diffusion simple utilisant Mobile Engagement. Il vous apprend à créer une application Windows Phone vide qui collecte des données de base et reçoit des notifications Push au moyen du service de notifications Push Microsoft (MPNS). Une fois la création terminée, vous pouvez diffuser des notifications Push sur tous les appareils ou cibler des utilisateurs spécifiques en fonction des propriétés de leurs périphériques. Suivez scrupuleusement le didacticiel suivant pour apprendre à utiliser Mobile Engagement pour accéder à des utilisateurs et groupes d'appareils spécifiques.


Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ Le [Kit de développement logiciel (SDK) Windows Phone Mobile Engagement]

> [AZURE.IMPORTANT] Pour accéder aux autres didacticiels Mobile Engagement relatifs aux applications Windows Phone et terminer notamment celui-ci, vous devez disposer d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite Azure</a>.

## <a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **Services d'application**, sur **Mobile Engagement**, puis sur **Créer**.

   	![][7]

3. Dans le menu contextuel qui apparaît, saisissez les informations suivantes :
 
   	![][8]

	1. **Nom de l'application** : vous pouvez saisir le nom de votre application. N'hésitez pas à utiliser tout type de caractère.
	2. **Plateforme** : Sélectionnez la plateforme cible pour cette application (si votre application cible plusieurs plateformes, répétez cette opération pour chaque plateforme).
	3. **Nom de la ressource d'application** : Ceci est le nom via lequel cette application sera accessible à l'aide d'API et d'URL. Nous vous recommandons de n'utiliser que des caractères d'URL classiques : le nom qui sera généré automatiquement devrait vous fournir une base solide. Nous vous conseillons également d'ajouter le nom de la plateforme pour éviter tout conflit de nom, car ce nom doit être unique
	4. **Emplacement** : Sélectionnez le centre de données dans lequel cette application (et surtout sa collection : voir ci-dessous) sera hébergée.
	5. **Collection** : Si vous avez déjà créé une application, sélectionnez une collection précédemment créée, sinon sélectionnez Nouvelle collection.
	6. **Nom de la collection** : Il représente votre groupe d'applications. Il garantit également que toutes vos applications se trouvent dans un groupe qui autorisera les calculs agrégés. Nous vous recommandons vivement d'utiliser le nom de votre entreprise ou de votre département.


	Cliquez ensuite sur le bouton de vérification pour terminer la création de votre application.

4. Sélectionnez/cliquez maintenant sur l'application que vous venez de créer dans l'onglet **Application**.
 
   	![][9]

5. Cliquez ensuite sur **Informations de connexion** pour afficher les paramètres de connexion à appliquer à l'intégration de votre Kit de développement logiciel SDK.
 
   	![][10]

6. Notez ensuite la **Chaîne de connexion** dont vous aurez besoin pour identifier cette application à partir de votre code d'application.

   	![][11]

	>[AZURE.TIP] Vous pouvez utiliser l'icône " copie ", située sur la droite de la chaîne de connexion, pour la copier dans le presse-papiers à titre de référence.

## <a id="connecting-app"></a>Connexion de votre application au serveur principal de Mobile Engagement

Ce didacticiel présente une " intégration de base " qui constitue la configuration minimale requise pour collecter des données et envoyer une notification Push. La documentation complète d'intégration peut être consultée dans la [documentation du Kit de développement logiciel (SDK) Windows Phone Mobile Engagement].

Nous créerons une application de base à l'aide de Visual Studio pour expliquer cette intégration.

### Création d'un nouveau projet Windows Phone

Vous pouvez ignorer cette étape si vous disposez déjà d'une application et que vous connaissez bien le développement de Windows Phone.

1. Lancez Visual Studio, et dans l'écran d'accueil, sélectionnez **Nouveau projet...**.

   	![][12]

2. Dans le menu contextuel, (1) sélectionnez  `Windows Phone`, puis(2)  `Application Windows Phone`. (3) Indiquez également le `Name` de l'application et (4)le `Solution name`, puis cliquez sur **OK**.

   	![][13]

Votre projet est désormais créé via l'application de démonstration à laquelle nous intégrerons Mobile Engagement.

### Incluez la bibliothèque SDK dans votre projet

Téléchargez et intégrez la bibliothèque SDK

1. Téléchargez le [Kit de développement logiciel (SDK) Windows Phone Mobile Engagement].
2. Décompressez le fichier .tar.gz dans un dossier de votre ordinateur.
3. Rendez-vous sur `PROJET`, puis sur `Manage NuGet Packages...`
4. Dans le menu contextuel, cliquez sur Paramètres
5. Appuyez ensuite sur le bouton `+` pour créer une nouvelle source
	![][17]

6. Cliquez sur le `...` bouton d'en bas pour sélectionner la source et accéder au dossier lib du téléchargement du Kit de développement logiciel extrait (voir étape 2), puis appuyez sur `Select`
	![][18]

7. Le Kit de développement logiciel sera ajouté en tant que source. Cliquez sur `OK`.
	![][19]


### Connectez votre application au serveur principal de Mobile Engagement à l'aide de la chaîne de connexion

1. Pour fonctionner correctement, le Kit de développement logiciel Engagement a besoin de certaines capacités du Kit de développement logiciel Windows Phone.
Ouvrez votre fichier WMAppManifest.xml et veillez à ce que les capacités suivantes soient déclarées dans le panneau Capacités :
	- ID_CAP_NETWORKING
	- ID_CAP_IDENTITY_DEVICE


	![][20]

2. Retournez sur le portail Azure de la page **Informations de connexion** de votre application et copiez la **chaîne de connexion**.

	![][11]

3. Collez-la dans le fichier  `Resources\EngagementConfiguration.xml`, entre les balises `<connectionString>` et`</connectionString>` :

	![][21]

4. Dans le fichier `App.xaml.cs` :
	1. Ajoutez l'instruction `using`

			using Microsoft.Azure.Engagement;

	2. Initialisez le Kit de développement logiciel dans la méthode `Application_Launching` :
			
			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	3. Insérez les instructions suivantes dans `Application_Activated`

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

### Envoyez un écran vers Mobile Engagement

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont bien actifs, vous devez envoyer au moins un écran (activité) sur le serveur principal de Mobile Engagement. Nous y parviendrons en sous-classant notre `MainPage` à l'aide de la `EngagementPage` fournie par le Kit de développement logiciel Mobile Engagement.

1. Pour ce faire, remplacez la superclasse de `MainPage`, située avant `PhoneApplicationPage`, via la page Engagement indiquée ci-dessous :

	![][22]

	>[AZURE.NOTE] N'oubliez pas de résoudre la classe si celle-ci est soulignée en rouge, en ajoutant `using Microsoft.Azure.Engagement;` aux clauses `using`.

2. Dans votre fichier `MainPage.xml` :
	1. Ajoutez à vos espaces de noms les déclarations suivantes :

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
	2. Remplacez `phone:PhoneApplicationPage`, dans le nom de la balise xml, par `engagement:EngagementPage`

## <a id="monitor"></a>Comment s'assurer que votre application est bien connectée à la surveillance en temps réel

Cette section vous explique comment vous assurer que votre application se connecte bien au serveur principal de Mobile Engagement à l'aide de la fonctionnalité de surveillance en temps réel de Mobile Engagement.

1. Accédez à votre portail Mobile Engagement.

	Depuis votre portail Azure, veillez à ce que vous vous trouviez bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engager** situé en bas de l'écran.

	![][26]

2. Vous atterrirez sur la page Paramètres de votre portail Engagement, relative à votre application. Cliquez ensuite sur l'onglet **Écran**, comme indiqué ci-dessous.
	![][30]

3. L'écran est prêt à vous indiquer tout appareil, en temps réel, qui lancera votre application.
	![][31]

4. Une fois revenu dans Visual Studio, lancez votre application dans l'émulateur ou dans un appareil connecté, en cliquant sur le triangle vert, puis en sélectionnant votre appareil.

5. Si l'opération s'est effectuée correctement, une session doit s'afficher à l'écran ! 
	![][33]

**Félicitations !** Vous êtes parvenu à terminer la première étape de ce didacticiel à l'aide d'une application qui se connecte au serveur principal de Mobile Engagement, et qui se charge déjà d'envoyer des données.


## <a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l'application

Mobile Engagement vous permet de JOINDRE vos utilisateurs, et d'interagir avec eux, à l'aide de notifications Push et d'une messagerie intégrée à l'application dans le cadre de campagnes. Ce module est intitulé REACH dans le portail de Mobile Engagement.
Les sections suivantes vous permettront de configurer votre application afin de les recevoir.

### Permettre à votre application de recevoir les notifications Push MPNS

Ajoutez de nouvelles capacités à votre fichier `WMAppManifest.xml` :

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

### Initialisez le Kit de développement logiciel REACH

1. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.Init();` dans la fonction `Application_Launching` juste après l'initialisation de l'agent :

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. Dans `App.xaml.cs`, appelez `EngagementReach.Instance.OnActivated(e);` dans la fonction `Application_Activated` juste après l'initialisation de l'agent :

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Parfait, nous allons maintenant vérifier si cette intégration de base a été réalisée correctement.

## <a id="send"></a>Envoi d'une notification vers votre application

Nous allons maintenant créer une simple campagne de notification Push qui enverra une notification Push vers notre application.

1. Accédez à l'onglet **REACH** de votre portail Mobile Engagement.
2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push.
	![][35]

3. Configurez le premier champ de votre campagne en procédant comme suit :
	![][36]

	1. Attribuez le nom que vous voulez à votre campagne.
	2. Sélectionnez **Délai de livraison** en tant que *Any time* pour permettre à l'application de recevoir une notification si l'application est lancée ou non.
	3. Dans le texte de notification, saisissez le titre qui apparaîtra en gras lors de l'envoi de données.
	4. Saisissez ensuite votre message.

4. Faites défiler vers le bas, et dans la section contenu, sélectionnez **Notification uniquement**.
![][37]

5. Après avoir configuré une campagne la plus simple possible, faites défiler une nouvelle fois vers le bas et créez votre campagne pour l'enregistrer !
![][38]

6. Il ne vous reste plus qu'à activer votre campagne.
![][39]

Vous devriez voir apparaître, sur votre appareil, une notification, **Félicitations !** :
![][40]


<!-- URLs. -->
[Kit de développement logiciel (SDK) Windows Phone Mobile Engagement]: http://go.microsoft.com/?linkid=9874664

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-windows-phone-get-started/select-app.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-phone-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-phone-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-phone-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-phone-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-create.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!--HONumber=47-->
