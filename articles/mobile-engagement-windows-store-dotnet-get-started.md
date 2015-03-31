<properties 
	pageTitle="Prise en main d'Azure Mobile Engagement" 
	description="Découvrez comment utiliser les fonctionnalités d'analyse et les notifications Push dans Azure Mobile Engagement."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Prise en main de Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store"  class="current">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

Cette rubrique explique comment utiliser Azure Mobile Engagement pour analyser l'utilisation de votre application et envoyer des notifications Push à des segments d'utilisateurs d'une application Windows Store. 
Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. Dans ce didacticiel, vous allez créer une application Windows Store vide destinée à collecter des données de base et à recevoir des notifications Push à l'aide du service de notification Windows. Quand vous aurez terminé, vous serez en mesure de diffuser des notifications Push vers tous les appareils, ainsi que de cibler des utilisateurs spécifiques en fonction des propriétés de leurs appareils. Veillez à bien suivre le didacticiel qui suit pour savoir comment utiliser Mobile Engagement dans le but de cibler des utilisateurs et des groupes d'appareils spécifiques.


Ce didacticiel requiert les éléments suivants :

+ Visual Studio 2013
+ [Kit de développement logiciel (SDK) Mobile Engagement Windows Store]

> [AZURE.IMPORTANT] Il est obligatoire de suivre ce didacticiel avant tous les autres didacticiels Mobile Engagement pour applications Windows Store. Ce didacticiel nécessite que vous disposiez d'un compte Azure actif. Si ce n'est pas le cas, vous pouvez créer gratuitement un compte d'évaluation, et ce, en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite Azure</a>.

##<a id="setup-azme"></a>Configurer Mobile Engagement pour votre application

1. Connectez-vous au Portail de gestion Azure, puis cliquez sur **+NOUVEAU** au bas de l'écran.

2. Cliquez sur **App Services**, **Mobile Engagement**, puis **Créer**.

   	![][7]

3. Dans la fenêtre indépendante qui s'affiche, entrez les informations suivantes :
 
   	![][8]

	1. **Nom de l'application** : tapez ici le nom de votre application. Tous les caractères sont autorisés.
	2. **Plateforme** : sélectionnez la plateforme cible de l'application (si l'application cible plusieurs plateformes, répétez ce didacticiel pour chacune des plateformes).
	3. **Nom de la ressource d'application** : nom utilisé dans les API et les URL pour faire référence à l'application. Il est fortement recommandé de n'utiliser que des caractères d'URL conventionnels. Vous pouvez déjà vous baser sur le nom qui est généré automatiquement. Il est également recommandé d'ajouter le nom de la plateforme pour éviter tout conflit, puisque ce nom doit être unique.
	4. **Emplacement** : sélectionnez le centre de données qui hébergera l'application (et encore plus important, sa collection. Voir ci-après).
	5. **Collection** : si vous avez déjà créé une application, sélectionnez une collection créée précédemment, sinon, sélectionnez Nouvelle collection.
	6. **Nom de la collection** : correspond à votre groupe d'applications. Cela permet également de garantir que toutes vos applications font partie d'un groupe qui autorise les calculs agrégés. Il est fortement recommandé d'utiliser le nom de votre société ou de votre service.


	Une fois terminé, cochez la case pour finaliser la création de votre application.

4. Cliquez/Sélectionnez l'application que vous venez de créer sous l'onglet **Application**.
 
   	![][9]

5. Ensuite, cliquez sur **Informations de connexion** pour afficher les paramètres de connexion que vous voulez ajouter à l'intégration de votre Kit de développement logiciel (SDK).
 
   	![][10]

6. Enfin, notez la **Chaîne de connexion**, dont vous aurez besoin pour identifier l'application depuis le code de votre application.

   	![][11]

	>[AZURE.TIP] Vous pouvez utiliser l'icône de copie située à droite de la chaîne de connexion pour copier celle-ci dans le Presse-papiers, pour plus de commodité.

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications Push. La documentation complète concernant l'intégration se trouve dans la [Documentation du Kit de développement logiciel (SDK) Mobile Engagement Windows Store].

Nous allons créer une application basique à l'aide de Visual Studio pour la démonstration de l'intégration.

###Créer un projet Windows Store

Vous pouvez sauter cette étape si vous disposez déjà d'une application et si vous maîtrisez bien le développement Windows.

1. Lancez Visual Studio, puis, dans la page d'accueil, sélectionnez **Nouveau projet**.

   	![][12]

2. Dans la fenêtre indépendante, (1) sélectionnez  `Windows Store`, puis (2) sélectionnez  `Application vide (XAML)`. (3) Entrez le  `Nom` de l'application, puis (4) le  `Nom de la solution`, puis cliquez sur **OK**.

   	![][13]

Votre projet vient d'être créé avec l'application de démonstration à laquelle nous allons intégrer Mobile Engagement.

###Inclure la bibliothèque du Kit de développement logiciel (SDK) dans votre projet

Télécharger et intégrer la bibliothèque du SDK

1. Téléchargez le [Kit de développement logiciel (SDK) Mobile Engagement Windows Store].
2. Extrayez le fichier .zip dans un dossier de votre ordinateur.
3. Accédez à  `PROJECT`, puis à Gérer les packages NuGet...
4. Dans la fenêtre indépendante, cliquez sur Paramètres.
5. Cliquez sur le bouton + pour créer une source.

	![][17]

6. Cliquez sur le bouton  `...`  situé au bas de l'écran pour sélectionner la source. Accédez au dossier de bibliothèque vers lequel le téléchargement du SDK a été extrait (voir étape 2), puis cliquez sur `Sélectionner`

	![][18]

7. Le Kit de développement logiciel (SDK) sera jouté en tant que source. Cliquez sur  `OK`. Ensuite, sélectionnez le package dans la liste en ligne des packages, puis cliquez sur Installer.

	![][19]


###Connectez votre application au serveur principal Mobile Engagement à l'aide de la chaîne de connexion.

1. Ouvrez  `Package.appxmanifest`, puis vérifiez que les éléments suivants sont déclarés (si ce n'est pas le cas, ajoutez-les) :
		
		Internet (Client)

	![][20]

2. Dans le même fichier, ouvrez l'onglet  `Déclarations` :
	1. dans  `Déclarations disponibles`, ajoutez  `Associations de types de fichiers`
	2. Dans la section  `Propriétés` située sur la droite, définissez  `Nom` sur  `engagement_log_file`
	3. Toujours dans la section  `Propriétés`, définissez  `Type de fichier` sur  `.set`
	4. Enfin, dans  `Déclarations disponibles`, ajoutez  `Programme de mise à jour des fichiers mis en cache`

		![][21]
 
3. De retour sur le portail Azure, dans la page **Informations de connexion** de votre application, copiez la **Chaîne de connexion**.

	![][11]

4. Collez-la dans le fichier  `Resources\EngagementConfiguration.xml`, entre les balises `<connectionString>` et `</connectionString>`  :

	![][22]

5. Dans le fichier  `App.xaml.cs` :
	1. Ajoutez l'instruction  `using`

			using Microsoft.Azure.Engagement;

	2. Initialisez le Kit de développement logiciel (SDK) dans la méthode  `OnLaunched` :
			
			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

	3. Insérez ce qui suit dans  `Application_Activated` (vous devrez ajouter cette opération si elle n'est pas déjà présente)

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

###Envoyer un écran à Mobile Engagement

Pour commencer à envoyer des données et s'assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement. Dans ce but, nous allons sous-classer notre  `MainPage` avec la  `EngagementPage` fournie par le Kit de développement logiciel (SDK) Mobile Engagement.

1. Pour cela, remplacez la superclasse de  `MainPage`, qui se trouve avant  `Page`, par EngagementPage, comme indiqué ci-dessous :

	![][23]

	>[AZURE.NOTE] N'oubliez pas de corriger la classe si elle est soulignée de rouge en ajoutant  `using Microsoft.Azure.Engagement;` aux clauses `using`.

2. Dans le fichier  `MainPage.xaml` :
	1. Ajoutez les déclarations des espaces de noms :

			xmlns:engagement="using:Microsoft.Azure.Engagement"
	2. Remplacez  `Page` dans le nom de balise xml par  `engagement:EngagementPage`

##<a id="monitor"></a>Comment vérifier que l'application est connectée à la surveillance en temps réel

Cette section montre comment vérifier que votre application est connectée au serveur principal Mobile Engagement à l'aide de la fonctionnalité de surveillance en temps réel de Mobile Engagement.

1. Accédez au portail Mobile Engagement.

	Depuis le portail Azure, vérifiez que vous vous trouvez dans l'application utilisée pour ce projet, puis cliquez sur le bouton **Activer** situé au bas de l'écran.

	![][26]

2. Vous serez redirigé vers la page des paramètres du portail Mobile Engagement de votre application. Dans cette page, cliquez sur l'onglet **Surveiller** comme indiqué ci-dessous.
![][30]

3. Sous cet onglet, vous verrez tous les appareils qui lancent votre application, en temps réel.
![][31]

4. De retour dans Visual Studio, lancez votre application dans l'émulateur ou dans un appareil connecté en cliquant sur le triangle vert, puis en sélectionnant votre appareil.

5. Si cela a fonctionné, vous devriez voir une session. 
![][33]

**Félicitations !** Vous venez d'effectuer la première étape de ce didacticiel avec une application connectée au serveur principal Mobile Engagement qui envoie déjà des données.


##<a id="integrate-push"></a>Activation des notifications Push

Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs à l'aide de notifications Push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement.
Les sections suivantes vous permettront de configurer votre application pour la réception des notifications.

###Activer la réception des notifications Push des Services de notifications Push Windows pour votre application

1. Ajoutez une nouvelle  `Association de types de fichiers` au fichier  `Package.appxmanifest` dans le panneau Déclarations :

		Name: engagement_reach_content
		File type: .txt

	![][34]

2. Toujours dans le fichier  `Package.appxmanifest`, sous l'onglet  `Application`, sous  `Notifications`, sélectionnez  `Oui` pour `Compatible toast` :

	![][35]

###Initialiser le SDK du module Couverture

1. Dans  `App.xaml.cs`, appelez  `EngagementReach.Instance.Init(args);`  dans la fonction  `OnLaunched` située immédiatement après l'initialisation de l'agent :

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		   EngagementAgent.Instance.Init(args);
		   EngagementReach.Instance.Init(args);
		}

2. Dans  `App.xaml.cs`, appelez  `EngagementReach.Instance.OnActivated(args);`  dans la fonction  `Application_Activated` située immédiatement après l'initialisation de l'agent :

		protected override void OnActivated(IActivatedEventArgs args)
		{
		   EngagementAgent.Instance.OnActivated(args);
		   EngagementReach.Instance.OnActivated(args);
		}

3. Exécutez l'application. 
Vous êtes prêt à envoyer un Toast. Nous allons maintenant vérifier que l'intégration de base est correcte.

###Accorder l'accès à Mobile Engagement pour l'envoi de notifications Push

1. Vous devrez ensuite associer votre application au Windows Store pour obtenir votre  `Package security identifier (SID)` et votre  `clé secrète` (*clé secrète client*).

2. Ensuite, accédez aux paramètres du portail Mobile Engagement, puis cliquez sur la section  `Push natif` située sur la gauche.

3. Cliquez sur le bouton  `Modifier` pour entrer l' `identificateur de sécurité (SID) du package` et votre  `clé secrète`, comme indiqué ci-dessous :

	![][36]

##<a id="send"></a>Comment envoyer une notification à votre application

Nous allons à présent créer une campagne simple qui enverra une notification Push à notre application.

1. Accédez à l'onglet **Couverture** du portail Mobile Engagement.
2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push.
![][37]

3. Configurez le premier champ de votre campagne en suivant les étapes ci-dessous :
![][38]

	1. Attribuez le nom de votre choix à votre campagne.
	2. Définissez **Heure de remise** sur  *N'importe quand* pour permettre à l'application de recevoir une notification, qu'elle ait été ou non lancée.
	3. Dans le texte de notification, tapez le titre qui apparaîtra en gras dans la notification Push.
	4. Tapez ensuite votre message.

4. Faites défiler l'écran vers le bas, puis, dans la section du contenu, sélectionnez **Notification uniquement**.
![][39]

5. Vous venez de créer une campagne de base. Maintenant, faites défiler l'écran vers le bas pour créer et enregistrer votre propre campagne.
![][40]

6. Enfin, dernière étape, activez votre campagne.
![][41]

Vous devriez à présent voir la notification suivante sur votre appareil : **Félicitations !** :



<!-- URLs. -->
[Documentation sur le Kit de développement logiciel (SDK) Mobile Engagement Windows Store]: ../mobile-engagement-windows-store-integrate-engagement/
[Kit de développement logiciel (SDK) Mobile Engagement Windows Store]: http://go.microsoft.com/?linkid=9864592

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-get-started/aux-create-app.png
[9]: ./media/mobile-engagement-windows-store-get-started/aux-select-app.png
[10]: ./media/mobile-engagement-windows-store-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-store-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-store-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-store-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-store-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-store-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-store-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-store-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-store-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-get-started/campaign-content.png
[40]: ./media/mobile-engagement-windows-store-get-started/campaign-create.png
[41]: ./media/mobile-engagement-windows-store-get-started/campaign-activate.png

<!--HONumber=47-->
