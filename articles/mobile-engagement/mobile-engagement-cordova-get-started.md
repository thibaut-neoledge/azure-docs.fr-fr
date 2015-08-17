<properties
	pageTitle="Prise en main d'Azure Mobile Engagement pour Cordova/Phonegap"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Cordova/Phonegap."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# Prise en main d'Azure Mobile Engagement pour Cordova/Phonegap

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Cette rubrique explique comment utiliser Azure Mobile Engagement pour analyser l'utilisation de votre application et envoyer des notifications Push à des segments d'utilisateurs d'une application développée avec Cordova.

Dans ce didacticiel, nous allons créer une application Cordova vide à l'aide de Mac et intégrer le Kit de développement logiciel (SDK) Mobile Engagement. Elle collectera des données d'analyse de base et recevra des notifications Push à l'aide du système de notifications Push Apple (APNS) pour iOS et de Google Cloud Messaging (GCM) pour Android. Nous déploierons cette application sur un appareil iOS ou Android pour le test.

> [AZURE.IMPORTANT]Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Version d'évaluation gratuite d'Azure</a>.

Ce didacticiel requiert les éléments suivants :

+ Xcode, que vous pouvez installer depuis le Mac App Store (pour un déploiement sur iOS)
+ [Kit de développement logiciel (SDK) et émulateur Android](http://developer.android.com/sdk/installing/index.html) (pour un déploiement sur Android)
+ Certificat de notification Push (.p12) que vous pouvez obtenir dans votre centre de développement Apple pour l’APNS
+ Numéro du projet GCM que vous pouvez obtenir à partir de votre Console de développement Google pour GCM
+ [Plug-in Mobile Engagement Cordova](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE]Vous trouverez le code source et le fichier Lisezmoi du plug-in Cordova sur [Github](https://github.com/Azure/azure-mobile-engagement-cordova)

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application

1. Connectez-vous au portail de gestion Azure et cliquez sur **+NOUVEAU** en bas de l’écran.

2. Cliquez sur **App Services**, **Mobile Engagement**, puis **Créer**.

   	![][1]

3. Dans la fenêtre indépendante qui s'affiche, entrez les informations suivantes :

   	![][2]

	- **Nom de l'application** : nom de votre application. 
	- **Plateforme**: plateforme cible pour l'application (choisissez **iOS** ou **Android** selon le système sur lequel vous déployez votre application Cordova)
	- **Nom de la ressource d’application** : nom utilisé dans les API et les URL pour faire référence à l’application. 
	- **Emplacement** : centre de données où seront hébergées cette application et cette collection d’applications.
	- **Collection** : sélectionnez un regroupement créé précédemment ou sélectionnez « Nouvelle Collection ».
	- **Nom de collection** : correspond à votre groupe d'applications. Cela permet également de garantir que toutes vos applications font partie d'un groupe qui autorise les calculs agrégés de statistiques. Vous devez utiliser ici le nom de votre société ou service le cas échéant.

4. Sélectionnez l'application que vous venez de créer sous l'onglet **Applications**.

5. Ensuite, cliquez sur **Informations de connexion** pour afficher les paramètres de connexion que vous voulez ajouter à l'intégration de votre Kit de développement logiciel (SDK) dans votre application mobile.

   	![][3]

6. Copiez la **chaîne de connexion** : cela vous sert à identifier cette application dans votre code d'application et à vous connecter avec Engagement Mobile à partir de votre application mobile.

   	![][4]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications Push.

Nous allons créer une application de base à l’aide de Cordova afin d’illustrer l’intégration :

###Création d’un nouveau projet Cordova

1. Exécutez la fenêtre *Terminal* sur votre ordinateur Mac et saisissez les lignes suivantes pour créer un nouveau projet Cordova à partir du modèle par défaut :

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]Assurez-vous que le profil de publication que vous utilisez pour déployer votre application iOS utilise « com.mycompany.myapp » comme ID d’application.

2. Exécutez l’instruction suivante pour configurer votre projet pour **iOS** et le lancer dans iOS Simulator :

		$ cordova platform add ios 
		$ cordova run ios

3. Exécutez l’instruction suivante pour configurer votre projet pour **Android** et le lancer dans l’émulateur Android :

		$ cordova platform add android
		$ cordova run android

4. 	Ajout du plug-in Cordova Console.

		$ cordova plugin add cordova-plugin-console 

###Connexion de votre application au serveur principal Mobile Engagement

1. Installez le plug-in Azure Mobile Engagement Cordova tout en fournissant les valeurs des variables pour configurer le plug-in :

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	Par exemple :

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]L'AppId, la SDKKey et la Collection peuvent être récupérés dans la chaîne de connexion **Endpoint={YourAppCollection.Domain} ; SdkKey = {YourSDKKey} ; AppId = {YourAppId}**

##<a id="monitor"></a>Activation de la surveillance en temps réel

1. Dans le projet Cordova : modifiez **www/js/index.js** pour ajouter l'appel de Mobile Engagement pour déclarer une nouvelle activité à la réception de l’événement *deviceReady*.

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. Exécutez l'application :
		
	- **Pour iOS**
	
		Dans la fenêtre `Terminal`, exécutez votre application dans une nouvelle instance de Simulator en exécutant le code suivant :

			cordova run ios

	- **Pour Android**
		
		Dans la fenêtre `Terminal`, exécutez votre application dans une nouvelle instance de l’émulateur en exécutant le code suivant :

			cordova run android

3. Les éléments suivants s’affichent dans les journaux de la console :

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###Vérification de la connexion de l'application à la surveillance en temps réel

Cette section montre comment vérifier que votre application est connectée au serveur principal Mobile Engagement à l'aide de la fonctionnalité de surveillance en temps réel de Mobile Engagement.

1. Accédez au portail Mobile Engagement

	Dans votre portail Azure, vérifiez que vous êtes bien dans l'application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engagement** en bas de l'écran :

	![][6]

2. Vous accéderez à la page de paramètres de votre application via le **portail Engagement**. De là, cliquez sur l'onglet **Surveiller** :

	![][7]

3. Si votre application en cours d'exécution dans l'émulateur est correctement configurée. vous verrez une session ouverte en temps réel pendant l'exécution de votre application :

	![][8]

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie intégrée à l'application

Mobile Engagement vous permet d'interagir avec vos utilisateurs à l'aide de notifications Push et de la messagerie dans l'application, dans le cadre d'une campagne. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettront de configurer votre application pour la réception des notifications.

###Configuration des informations d'identification Push pour Mobile Engagement

Pour permettre à Mobile Engagement d'envoyer des notifications Push en votre nom, vous devez lui accorder l'accès à votre certificat Apple iOS ou à votre clé d’API serveur GCM.
	
1. Accédez au portail Mobile Engagement. Vérifiez que vous vous trouvez dans l’application utilisée pour ce projet, puis cliquez sur le bouton **Activer** situé en bas :
	
	![][10]
	
2. Vous accédez à la page de paramètres via le portail Engagement. À partir de là, cliquez sur la section **Push natif** :
	
	![][11]

3. Configuration du certificat iOS/de la clé d’API serveur GCM

	**[iOS]**

	a. Sélectionnez votre .p12, téléchargez-le et tapez votre mot de passe :
	
	![][12]

	**[Android]**

	a. Cliquez sur l'icône de modification devant **Clé API** dans la section de paramètres GCM, comme indiqué ci-dessous :
		
	![][20]
	
	b. Dans la fenêtre contextuelle, collez la clé du serveur GCM, puis cliquez sur **Ok**.
	
	![][21]

###Activation des notifications Push dans l'application Cordova

Modifiez **www/js/index.js** pour ajouter l'appel de Mobile Engagement afin de demander des notifications Push et de déclarer un gestionnaire :

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###Exécution de l'application

**[iOS]**

1. Nous allons utiliser XCode pour créer et déployer l'application sur l’appareil afin de tester les notifications Push, car iOS autorise uniquement ces notifications sur un appareil réel. Accédez à l'emplacement où votre projet Cordova a été créé et accédez à l’emplacement **...\\platforms\\ios**. Ouvrez le fichier .xcodeproj natif dans XCode. 
	
2. Générez et déployez l'application Cordova sur l’appareil iOS en utilisant le compte ayant le profil d’approvisionnement contenant le certificat que vous avez téléchargé vers le portail Mobile Engagement et l'ID d'application correspondant à celui fourni lors de la création de l'application Cordova. Vous pouvez consulter l’*identificateur de faisceau* dans votre fichier **Resources*-info.plist** dans XCode pour qu’il corresponde.

3. La fenêtre contextuelle iOS standard apparaîtra sur votre appareil pour indiquer que l'application demande l'autorisation d'envoyer des notifications. Accordez l'autorisation.

**[Android]**

Vous pouvez utiliser l'émulateur pour exécuter l'application Android, car les notifications GCM sont prises en charge par l'émulateur Android.

	cordova run android

##<a id="send"></a>Envoi d'une notification vers votre application

Nous allons maintenant créer une campagne simple de notification Push qui enverra une notification Push à votre application en cours d’exécution sur l’appareil :

1. Accédez à l'onglet Couverture de votre portail Mobile Engagement

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne Push.

	![][13]

3. Fournissez des informations pour créer votre campagne :

	![][14]

	- 	Entrez un nom pour votre campagne. 
	- 	**[Android]** Sélectionnez le **Type de distribution** *Notification système* - *Simple*
	- 	Sélectionnez comme heure de distribution : 
		- 	Pour **iOS** : *« De l’application uniquement »*
		- 	Pour **Android** : *« N’importe quelle heure »*
		
		Ceci est le type de notification Push simple qui comprend du texte.
	- 	Dans le texte de notification, tapez d'abord le titre, qui sera la première ligne de la notification push
	- 	Tapez ensuite votre message, qui sera la deuxième ligne

4. Faites défiler l’écran vers le bas puis, dans la section du contenu, sélectionnez **Notification uniquement**.

	![][15]

5. [Facultatif] Vous pouvez également fournir une URL d’action. Assurez-vous qu'elle utilise un schéma d’URL fourni lors de la configuration de la variable **AZME REDIRECT URL** du plug-in, par exemple **myapp://test*.

5. Vous venez de créer une campagne de base. Maintenant, faites défiler à nouveau l'écran vers le bas pour **créer** et enregistrer votre propre campagne.
	
	![][16]

6. Enfin, dernière étape, **activez** votre campagne.
	
	![][17]

7. Vous devriez maintenant voir une notification Push sur votre appareil ou votre émulateur dans le cadre de cette campagne.

##<a id="next-steps"></a>Étapes suivantes
[Vue d'ensemble de toutes les méthodes disponibles avec le Kit de développement logiciel (SDK) Cordova Mobile Engagement](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=August15_HO6-->