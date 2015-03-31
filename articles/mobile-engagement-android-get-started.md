<properties 
	pageTitle="Prise en main d'Azure Mobile Engagement" 
	description="Découvrez comment utiliser Azure Mobile Engagement avec l'analyse et les notifications Push." 					services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Prise en main de Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android" class="current">Android</a></div>

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d'une application Android. 
Ce didacticiel illustre le scénario de diffusion simple à l'aide de Mobile Engagement. À cette occasion, vous allez créer une application Android vide qui recueille des données de base et reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Une fois terminé, vous serez en mesure de diffuser des notifications Push à tous les appareils ou cibler des utilisateurs spécifiques en fonction des propriétés de leur appareil. N'oubliez pas d'effectuer le didacticiel suivant pour découvrir comment gérer des utilisateurs et des groupes d'appareils spécifiques à l'aide de Mobile Engagement.


Ce didacticiel requiert les éléments suivants :

+ Le Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Android Studio), que vous pouvez télécharger [ici](http://go.microsoft.com/fwlink/?LinkId=389797)
+ Le [Kit de développement logiciel (SDK) Mobile Engagement pour Android]

> [AZURE.IMPORTANT] L'exécution de ce didacticiel est obligatoire si vous souhaitez effectuer tous les autres didacticiels Mobile Engagement pour les applications Android. Pour ce faire, vous devez avoir un compte Azure actif. Si vous n'avez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Configurer Mobile Engagement pour votre application

1. Connectez-vous au [portail de gestion Azure], puis cliquez sur **+NOUVEAU** en bas de l'écran.

2. Cliquez sur **Services d'application**, sur **Mobile Engagement**, puis sur **Créer**.

   	![][7]

3. Dans la fenêtre contextuelle qui s'affiche, entrez les informations suivantes :
 
   	![][8]

	1. **Nom de l'application** : vous pouvez taper le nom de votre application. Vous pouvez utiliser n'importe quel caractère.
	2. **Plateforme** : sélectionnez la plateforme cible pour cette application (si votre application cible plusieurs plateformes, répétez ce didacticiel pour chaque plateforme).
	3. **Nom de la ressource d'application** : il s'agit du nom par lequel cette application sera accessible via les API et les URL. Nous vous conseillons d'utiliser uniquement des caractères d'URL conventionnels : le nom généré automatiquement doit vous fournir une base solide. Nous vous conseillons également d'ajouter le nom de la plateforme afin d'éviter tout conflit de nom, car il doit être unique.
	4. **Emplacement** : sélectionnez le centre de données où cette application (et surtout sa collection - voir ci-dessous) sera hébergée.
	5. **Collection** : si vous avez déjà créé une application, sélectionnez une collection existante, sinon sélectionnez Nouvelle collection.
	6. **Nom de la collection** : représente votre groupe d'applications. Il permet également de s'assurer que toutes vos applications se trouvent dans un groupe qui autorise les calculs agrégés. Nous vous conseillons vivement d'utiliser le nom de votre société ou de votre service.


	Une fois terminé, cliquez sur le bouton de vérification pour terminer la création de votre application.

4. Maintenant, sélectionnez ou cliquez sur l'application que vous venez de créer sous l'onglet **Application**.
 
   	![][9]

5. Ensuite, cliquez sur **Informations de connexion** pour afficher les paramètres de connexion à placer dans l'intégration du Kit de développement logiciel.
 
   	![][10]

6. Pour finir, notez la **Chaîne de connexion**, dont vous aurez besoin pour identifier cette application à partir de votre code d'application.

   	![][11]

	>[AZURE.TIP] Vous pouvez utiliser l'icône " Copier " qui figure à droite de la chaîne de connexion pour la copier dans le Presse-papiers.

##<a id="connecting-app"></a>Connexion de votre application au système principal Mobile Engagement

Ce didacticiel présente une " intégration de base " qui correspond au minimum nécessaire pour recueillir des données et envoyer une notification Push. Vous trouverez la documentation complète sur l'intégration dans la [documentation du Kit de développement logiciel (SDK) Mobile Engagement pour Android].

Nous allons créer une application de base avec Android Studio afin d'illustrer l'intégration.

###Créer un projet Android

Vous pouvez ignorer cette étape si vous avez déjà une application et que vous avez des connaissances en développement Android.

1. Lancez studio Android et, dans le menu contextuel, sélectionnez **Start a new Android Studio project**.

   	![][12]

2. Renseignez le nom de l'application et le domaine de l'entreprise. Notez ces éléments, car vous en aurez besoin ultérieurement, puis cliquez sur **Next**.

   	![][13]

3. Sélectionnez le niveau d'API et le facteur de forme cible, puis cliquez sur **Next**. 

	>[AZURE.NOTE] Mobile Engagement requiert au minimum le niveau d'API 10 (Android 2.3.3).

   	![][14]

4. Nous allons maintenant ajouter une activité à notre application simple, qui sera son principal et unique écran. Assurez-vous que **Blank Activity** est sélectionné et cliquez sur **Blank Next**.

   	![][15]

5. Dans l'écran final de l'Assistant, vous pouvez conserver toutes les options telles quelles pour les besoins de ce didacticiel et cliquer sur **Finish**.

   	![][16]

Android Studio va maintenant créer l'application de démonstration à laquelle nous intégrerons Mobile Engagement.

###Inclure la bibliothèque SDK dans votre projet

Télécharger et intégrer la bibliothèque du Kit de développement logiciel

1. Téléchargez le [Kit SDK Mobile Engagement Android].
2. Extrayez le fichier d'archive dans un dossier sur votre ordinateur.
3. Identifiez la bibliothèque .jar correspondant à la version actuelle de ce Kit SDK (cette documentation a été préparée pour la version 3.0.0) et copiez-la dans le Presse-papiers.

	![][17]

4. Accédez à la section de projet (1) et collez le fichier .jar dans le dossier libs (2).

	![][18]

5. Synchronisez votre projet pour charger la bibliothèque.

	![][19]


###Connecter votre application aux système principal Mobile Engagement avec la chaîne de connexion

1. Copiez les lignes de code suivantes dans la création d'activité (cette opération doit être effectuée à un seul emplacement de votre application, généralement dans l'activité principale).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Revenez au portail Azure dans la page **Informations sur la connexion** de votre application et copiez la **Chaîne de connexion**.

	![][11]

3. Collez-la dans le paramètre `setConnectionString` pour remplacer l'exemple fourni comme indiqué ci-dessous (AppId et Sdkkey ont été masqués ci-dessous).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration et EngagementAgent seront probablement affichées comme non résolues (en rouge dans le code). Cliquez sur chacune des classes non résolues et appuyez sur Alt+Entrée pour les résoudre automatiquement.

	![][20]

###Ajouter des autorisations et une déclaration de service

1. Ajoutez ces autorisations au fichier Manifest.xml de votre projet, juste avant la balise `<application>` :
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>


2. Ajoutez le code suivant entre les balises <application> et </application> pour déclarer le service de l'agent :

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. Dans le code que vous venez de coller, remplacez " <Your application name> " dans l'étiquette. Par exemple :

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

###Envoyer un écran à Mobile Engagement

Pour commencer à envoyer des données et s'assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (une activité) au système principal Mobile Engagement. Nous allons pour cela sous-classer notre activité avec l'EngagementActivity fourni par notre Kit de développement logiciel.
Pour cela, remplacez la super classe de MainActivity, qui figure avant ActionBarActivity, par EngagementActivity, comme indiqué ci-dessous :

![][22]

>[AZURE.NOTE] N'oubliez pas de résoudre la classe si elle est affichée en rouge, en cliquant dessus et en appuyant sur Alt+Entrée.

##<a id="monitor"></a>Comment vérifier que votre application est connectée à la surveillance en temps réel

Cette section montre comment s'assurer que votre application se connecte au système principal Mobile Engagement à l'aide des fonctionnalités de surveillance en temps réel de Mobile Engagement.

1. Accédez à votre portail Mobile Engagement.

	À partir de votre portail Azure, vérifiez que vous êtes dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton **Envoyer** en bas.

	![][26]

2. Vous accéderez alors à la page de paramètres du portail Engagement pour votre application. À partir de là, cliquez sur l'onglet **Surveiller** comme illustré ci-dessous.
	![][30]

3. Le moniteur est prêt à vous montrer n'importe quel appareil, en temps réel, qui lancera votre application.
	![][31]

4. Dans Android Studio, lancez votre application dans le moniteur ou sur un appareil connecté en cliquant sur le triangle vert, puis en sélectionnant votre appareil.
	![][32]

5. Si cela a fonctionné, vous devriez maintenant voir une session dans le moniteur. 
	![][33]

**Félicitations !** Vous avez terminé la première étape de ce didacticiel ; vous avez une application qui se connecte au système principal Mobile Engagement, qui envoie déjà des données.


##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie dans l'application

Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs grâce aux notifications Push et à la messagerie dans l'application dans le contexte de campagnes marketing. Ce module se nomme COUVERTURE dans le portail Mobile Engagement.
Les sections qui suivent permettent de configurer votre application pour recevoir ces notifications.

### Activation de la messagerie dans l'application

1. Copiez les ressources de messagerie dans l'application ci-dessous dans votre fichier Manifest.xml entre les balises <application> et </application>.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>

2. Copiez les ressources dans votre projet en effectuant les étapes suivantes :
	1. Revenez au contenu de téléchargement de votre Kit SDK et ouvrez le dossier  'res'.
	2. Sélectionnez les deux dossiers et copiez-les dans le Presse-papiers.

		![][23]

	4. Revenez à Android Studio, sélectionnez la partie  'res' de votre projet et collez-la pour ajouter les ressources à votre projet.

		![][24]

###Spécifier une icône par défaut dans les notifications
Le code suivant définit l'icône par défaut qui s'affiche avec les notifications. Ici, nous avons utilisé l'icône fournie avec le projet créé par Studio Android. Cet extrait de code xml doit être collé dans votre fichier Manifest.xml entre les balises <application> et </application>.

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###Activer votre application pour recevoir des notifications Push GCM

1. Entrez vos métadonnées gcm:sender en copiant-collant le code suivant dans votre fichier Manifest.xml entre les balises <application> et </application>. La valeur masquée ci-dessous (avec des étoiles) est le  `project number` obtenu à partir de votre console Google Play.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Collez le code ci-dessous dans votre fichier Manifest.xml entre les balises <application> et </application>. Notez que dans `<category android:name="com.mycompany.mysuperapp" />` nous avons utilisé le nom du package du projet. Dans votre propre projet de production, il sera différent.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>
		
		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="com.mycompany.mysuperapp" />
			</intent-filter>
		</receiver>

3. Ajoutez le dernier jeu d'autorisations mis en surbrillance ci-dessous avant la balise <application>. Là encore, nous avons utilisé ce nom de package de projet, que vous devrez remplacer dans votre application de production.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Accorder à Mobile Engagement l'accès à votre clé d'API GCM

Pour permettre à Mobile Engagement d'envoyer des notifications Push en votre nom, vous devez lui accorder l'accès à votre clé d'API. Pour cela, vous devez configurer et entrer votre clé dans le portail Mobile Engagement.

1. Accédez à votre portail Mobile Engagement

	À partir de votre portail Azure, vérifiez que vous êtes dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engager** en bas :

	![][26]

2. Vous accédez alors à la page de paramètres dans votre portail Engagement. À partir de là, cliquez sur la section **Push natif** pour entrer votre clé GCM :
	![][27]

3. Cliquez sur l'icône de modification devant **Clé API** dans la section de paramètres GCM, comme indiqué ci-dessous :
	![][28]

4. Dans la fenêtre contextuelle, collez la clé du serveur GCM obtenue dans la section [Activer Google Cloud Messaging](#register) puis cliquez sur **OK**.

	![][29]

Voilà, c'est terminé. Nous allons maintenant vérifier que vous avez correctement effectué cette intégration de base.

> [AZURE.IMPORTANT] Assurez-vous de générer, lancer avec ce nouveau code, quitter l'application et attendre environ une minute avant d'effectuer les étapes suivantes.

##<a id="send"></a>Envoi d'une notification vers votre application

Nous allons maintenant créer une campagne de notifications Push simple qui envoie une notification Push à notre application.

1. Accédez à l'onglet **COUVERTURE** dans votre portail Mobile Engagement.
	![][34]

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push.
	![][35]

3. Configurez le premier champ de votre campagne à l'aide des étapes suivantes :
	![][36]

	1. Affectez à votre campagne le nom de votre choix.
	2. Sélectionnez  *System notification / Simple* comme **Type de remise** : Il s'agit du type de notification Push Android simple qui comprend un titre et une petite ligne de texte.
	3. Sélectionnez  *Any time* comme **Heure de remise** pour autoriser l'application à recevoir une notification même si elle n'a pas été lancée.
	4. Dans le texte de notification, tapez le titre, qui sera en gras dans la notification Push.
	5. Tapez votre message.

4. Faites défiler vers le bas et, dans la section de contenu, sélectionnez **Notification uniquement**.
	![][37]

5. Vous avez terminé de définir la campagne la plus simple possible. Maintenant, vous pouvez créer votre campagne pour l'enregistrer.
![][38]

6. Dernière étape : activez votre campagne.
![][39]


<!-- URLs. -->
[Kit de développement logiciel (SDK) Mobile Engagement pour Android]: http://go.microsoft.com/?linkid=9863935
[Documentation du Kit de développement logiciel (SDK) Mobile Engagement pour Android]: http://go.microsoft.com/?linkid=9874682
[Portail de gestion Azure]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!--HONumber=47-->
