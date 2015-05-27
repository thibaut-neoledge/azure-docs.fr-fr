<properties 
	pageTitle="Prise en main d'Azure Mobile Engagement" 
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Android."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="piyushjo" />
	
# Prise en main d'Azure Mobile Engagement pour les applications Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l'utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d'une application Android. Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. À cette occasion, vous allez créer une application Android vide qui recueille des données de base et reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM). Quand vous aurez terminé, vous serez en mesure de diffuser des notifications Push vers tous les appareils, ainsi que de cibler des utilisateurs spécifiques en fonction des propriétés de leurs appareils. Veillez à bien suivre le didacticiel qui suit pour savoir comment utiliser Mobile Engagement dans le but de cibler des utilisateurs et des groupes d'appareils spécifiques.


Ce didacticiel requiert les éléments suivants :

+ Le Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Android Studio), que vous pouvez télécharger [ici](http://go.microsoft.com/fwlink/?LinkId=389797)
+ Le [Kit de développement logiciel (SDK) Mobile Engagement pour Android]

> [AZURE.IMPORTANT]L'exécution de ce didacticiel est obligatoire si vous souhaitez effectuer tous les autres didacticiels Mobile Engagement pour les applications Android. Pour ce faire, vous devez avoir un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Essai gratuit d'Azure</a>.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application

1. Connectez-vous au [portail de gestion Azure] et cliquez sur **+NEW** en bas de l'écran.

2. Cliquez sur **App Services**, **Mobile Engagement**, puis **Créer**.

   	![][7]

3. Dans la fenêtre indépendante qui s'affiche, entrez les informations suivantes :
 
   	![][8]

	1. **Nom de l'application** : tapez ici le nom de votre application. Tous les caractères sont autorisés.
	2. **Plateforme** : sélectionnez la plateforme cible de l'application (si l'application cible plusieurs plateformes, répétez ce didacticiel pour chacune des plateformes).
	3. **Nom de la ressource d'application** : il s'agit du nom par lequel l'application sera accessible via les API et les URL. Il est fortement recommandé de n'utiliser que des caractères d'URL conventionnels. Vous pouvez déjà vous baser sur le nom qui est généré automatiquement. Il est également recommandé d'ajouter le nom de la plateforme pour éviter tout conflit, puisque ce nom doit être unique.
	4. **Emplacement** : sélectionnez le centre de données qui hébergera l'application (et encore plus important, sa collection. Voir ci-après).
	5. **Collection** : si vous avez déjà créé une application, sélectionnez une collection créée précédemment, sinon sélectionnez Nouvelle collection.
	6. **Nom de collection** : il correspond à votre groupe d'applications. Cela permet également de garantir que toutes vos applications font partie d'un groupe qui autorise les calculs agrégés. Il est fortement recommandé d'utiliser le nom de votre société ou de votre service.


	Une fois terminé, cochez la case pour finaliser la création de votre application.

4. Cliquez/Sélectionnez l'application que vous venez de créer sous l'onglet **Application**.
 
   	![][9]

5. Ensuite, cliquez sur **Informations de connexion** pour afficher les paramètres de connexion que vous voulez ajouter à l'intégration de votre Kit de développement logiciel (SDK).
 
   	![][10]

6. Enfin, notez la **Chaîne de connexion**, dont vous aurez besoin pour identifier l'application depuis le code de votre application.

   	![][11]

	>[AZURE.TIP]Vous pouvez utiliser l'icône de copie située à droite de la chaîne de connexion pour copier celle-ci dans le Presse-papiers, pour plus de commodité.

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications Push. Vous trouverez la documentation complète sur l'intégration dans la [documentation du Kit de développement logiciel (SDK) Mobile Engagement pour Android].

Nous allons créer une application de base avec Android Studio afin d'illustrer l'intégration.

###Créer un projet Android

Vous pouvez ignorer cette étape si vous avez déjà une application et que vous avez des connaissances en développement Android.

1. Lancez studio Android et, dans le menu contextuel, sélectionnez **Démarrer un nouveau projet Android Studio**.

   	![][12]

2. Renseignez le nom de l'application et le domaine de l'entreprise. Notez-les, vous en aurez besoin plus tard, et cliquez sur **Suivant**.

   	![][13]

3. Sélectionnez le niveau d'API et le facteur de forme cible, puis cliquez sur **Suivant**.
	>[AZURE.NOTE]Mobile Engagement requiert au minimum le niveau d'API 10 (Android 2.3.3).

   	![][14]

4. Nous allons maintenant ajouter une activité à notre application simple, qui sera son principal et unique écran. Assurez-vous que **Blank Activity** est sélectionné et cliquez sur Blank **Suivant**.

   	![][15]

5. Dans l'écran final de l'Assistant, vous pouvez conserver toutes les options telles quelles pour les besoins de ce didacticiel et cliquer sur **Terminer**.

   	![][16]

Android Studio va maintenant créer l'application de démonstration à laquelle nous intégrerons Mobile Engagement.

###Inclure la bibliothèque du Kit de développement logiciel (SDK) dans votre projet

Télécharger et intégrer la bibliothèque du SDK

1. Téléchargez le [Kit SDK Mobile Engagement Android].
2. Extrayez le fichier d'archive dans un dossier sur votre ordinateur.
3. Identifiez la bibliothèque .jar correspondant à la version actuelle de ce Kit SDK (cette documentation a été préparée pour la version 3.0.0) et copiez-la dans le Presse-papiers.

	![][17]

4. Accédez à la section de projet (1) et collez le fichier .jar dans le dossier libs (2).

	![][18]

5. Synchronisez votre projet pour charger la bibliothèque.

	![][19]


###Connectez votre application au serveur principal Mobile Engagement à l'aide de la chaîne de connexion.

1. Copiez les lignes de code suivantes dans la création d'activité (cette opération doit être effectuée à un seul emplacement de votre application, généralement dans l'activité principale).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. De retour sur le portail Azure, dans la page **Informations de connexion** de votre application, copiez la **chaîne de connexion**.

	![][11]

3. Collez-la dans le paramètre `setConnectionString` pour remplacer l'exemple fourni comme indiqué ci-dessous (AppId et Sdkkey ont été masqués ci-dessous).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration et EngagementAgent seront probablement affichées comme non résolues (en rouge dans le code). Cliquez sur chacune des classes non résolues et appuyez sur Alt+Entrée pour les résoudre automatiquement.

	![][20]

###Ajouter des autorisations et une déclaration de service

1. Ajoutez ces autorisations au fichier Manifest.xml de votre projet, juste avant ou après la balise `<application>` :
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

	Le résultat doit être comme indiqué ci-dessous :

	![][21]

2. Ajoutez le code suivant entre les balises < application >et</application > pour déclarer le service de l'agent :

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. Dans le code que vous venez de coller, remplacez « < Your application name> » dans l'étiquette. Par exemple :

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

###Envoyer un écran à Mobile Engagement

Pour commencer à envoyer des données et s'assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement. Nous allons pour cela sous-classer notre activité avec l'EngagementActivity fourni par notre Kit de développement logiciel. Pour cela, remplacez la super classe de MainActivity, qui figure avant ActionBarActivity, par EngagementActivity, comme indiqué ci-dessous :

![][22]

>[AZURE.NOTE]N'oubliez pas de résoudre la classe si elle est affichée en rouge, en cliquant dessus et en appuyant sur Alt+Entrée.

##<a id="monitor"></a>Comment vérifier que l'application est connectée à la surveillance en temps réel

Cette section montre comment vérifier que votre application est connectée au serveur principal Mobile Engagement à l'aide de la fonctionnalité de surveillance en temps réel de Mobile Engagement.

1. Accédez au portail Mobile Engagement.

	Dans votre portail Azure, vérifiez que vous êtes bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engagement** en bas de l'écran :

	![][26]

2. Vous serez redirigé vers la page des paramètres du portail Mobile Engagement de votre application. Dans cette page, cliquez sur l'onglet **Surveiller** comme indiqué ci-dessous. ![][30]

3. Le moniteur est prêt à afficher tous les appareils, en temps réel, qui lancent votre application. ![][31]

4. Dans Android Studio, lancez votre application dans le moniteur ou sur un appareil connecté en cliquant sur le triangle vert, puis en sélectionnant votre appareil. ![][32]

5. Si cela a fonctionné, vous devriez voir une session ! ![][33]

**Félicitations !** Vous avez terminé la première étape de ce didacticiel ; vous disposez d’une application qui se connecte au système principal Mobile Engagement, qui envoie déjà des données.


##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l'application

Mobile Engagement vous permet d'interagir et d'atteindre vos utilisateurs à l'aide de notifications Push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettront de configurer votre application pour la réception des notifications.

### Activation de la messagerie dans l'application

1. Copiez les ressources de messagerie dans l'application ci-dessous dans votre fichier Manifest.xml entre les balises < application >et</application >.

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

2. Copiez les ressources dans votre projet en effectuant les étapes suivantes :
	1. Revenez au contenu de téléchargement de votre Kit SDK et ouvrez le dossier « res ».
	2. Sélectionnez les deux dossiers et copiez-les dans le Presse-papiers.

		![][23]

	4. Revenez à Android Studio, sélectionnez la partie « res » de votre projet et collez-la pour ajouter les ressources à votre projet.

		![][24]

###Spécifier une icône par défaut dans les notifications
Le code suivant définit l'icône par défaut qui s'affiche avec les notifications. Ici, nous avons utilisé l'icône fournie avec le projet créé par Studio Android. Cet extrait de code xml doit être collé dans votre fichier Manifest.xml entre les balises < application > et </application >. Assurez-vous qu'ic_launcher existe dans votre application ou utilisez un autre fichier d'icône, sans quoi la notification ne s'affichera pas.

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###Activer votre application pour recevoir des notifications Push GCM

1. Entrez vos métadonnées gcm:sender en copiant-collant le code suivant dans votre fichier Manifest.xml entre les balises < application >et</application >. La valeur masquée ci-dessous (avec des étoiles) est le `project number` obtenu à partir de votre console Google Play. Le \\n est intentionnel alors assurez-vous que le numéro de projet se termine avec ce dernier. 

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Collez le code ci-dessous dans votre fichier Manifest.xml entre les balises < application >et</application >. Notez que dans `<category android:name="com.mycompany.mysuperapp" />` nous avons utilisé le nom du package du projet. Dans votre propre projet de production, il sera différent.

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

3. Ajoutez le dernier jeu d'autorisations mis en surbrillance ci-dessous ou après la balise < application>. Là encore, nous avons utilisé ce nom de package de projet, que vous devrez remplacer dans votre application de production.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Accorder à Mobile Engagement l'accès à votre clé d'API GCM

Pour permettre à Mobile Engagement d'envoyer des notifications Push en votre nom, vous devez lui accorder l'accès à votre clé d'API. Pour cela, vous devez configurer et entrer votre clé dans le portail Mobile Engagement.

1. Accédez au portail Mobile Engagement

	Dans votre portail Azure, vérifiez que vous êtes bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engagement** en bas de l'écran :

	![][26]

2. Vous accédez alors à la page de paramètres dans votre portail Engagement. À partir de là, cliquez sur la section **Push natif** pour entrer votre clé GCM : ![][27]

3. Cliquez sur l'icône de modification devant **Clé API** dans la section de paramètres GCM, comme indiqué ci-dessous : ![][28]

4. Dans la fenêtre contextuelle, collez la clé du serveur GCM obtenue dans la section [Activer Google Cloud Messaging](#register) puis cliquez sur **OK**.

	![][29]

Parfait, nous allons maintenant vérifier si cette intégration de base a été réalisée correctement.

> [AZURE.IMPORTANT]Assurez-vous de générer, lancer avec ce nouveau code, quitter l'application et attendre environ une minute avant d'effectuer les étapes suivantes.

##<a id="send"></a>Envoi d'une notification vers votre application

Nous allons à présent créer une campagne simple qui enverra une notification Push à notre application.

1. Accédez à l'onglet **REACH** de votre portail Mobile Engagement. ![][34]

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push. ![][35]

3. Configurez le premier champ de votre campagne en suivant les étapes ci-dessous : ![][36]

	1. Attribuez le nom de votre choix à votre campagne.
	2. Définissez le **Type d'envoi** sur *Notification système/Simple* : il s'agit du type de notification Push Android simple qui comprend un titre et une petite ligne de texte.
	3. Définissez l'**Heure de remise** sur *N'importe quand* pour permettre à l'application de recevoir une notification, qu'elle ait été ou non lancée.
	4. Dans le texte de notification, tapez le titre qui apparaîtra en gras dans la notification Push.
	5. Tapez ensuite votre message.

4. Faites défiler vers le bas et, dans la section du contenu, sélectionnez **Notification uniquement**. ![][37]

5. Vous venez de créer une campagne de base. Maintenant, faites défiler à nouveau l'écran vers le bas pour créer et enregistrer votre propre campagne ! ![][38]

6. Enfin, dernière étape, activez votre campagne. ![][39]


<!-- URLs. -->
[Kit SDK Mobile Engagement Android]: http://go.microsoft.com/?linkid=9863935
[Kit de développement logiciel (SDK) Mobile Engagement pour Android]: http://go.microsoft.com/?linkid=9863935
[documentation du Kit de développement logiciel (SDK) Mobile Engagement pour Android]: http://go.microsoft.com/?linkid=9874682
<!-- Images. -->
[7]: ./media/mobile-engagement-common/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-common/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-common/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-common/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[21]: ./media/mobile-engagement-android-get-started/permissions.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-common/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-common/clic-monitor-tab.png
[31]: ./media/mobile-engagement-common/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-common/reach-tab.png
[35]: ./media/mobile-engagement-common/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-common/campaign-content.png
[38]: ./media/mobile-engagement-common/campaign-create.png
[39]: ./media/mobile-engagement-common/campaign-activate.png

<!--HONumber=54-->