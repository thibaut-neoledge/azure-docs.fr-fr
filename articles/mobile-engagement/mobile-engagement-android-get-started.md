<properties
	pageTitle="Prise en main d’Azure Mobile Engagement"
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
	ms.topic="hero-article"
	ms.date="12/02/2015"
	ms.author="piyushjo" />

# Prise en main d’Azure Mobile Engagement pour les applications Android

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d’une application Android. Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. À cette occasion, vous allez créer une application Android vide qui recueille des données de base et reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM).

Ce didacticiel requiert les éléments suivants :

+ Le Kit de développement logiciel (SDK) Android (nous présumons que vous utiliserez Android Studio), que vous pouvez télécharger [ici](http://go.microsoft.com/fwlink/?LinkId=389797)
+ Le [Kit de développement logiciel (SDK) Mobile Engagement pour Android]

> [AZURE.IMPORTANT]L'exécution de ce didacticiel est obligatoire si vous souhaitez effectuer tous les autres didacticiels Mobile Engagement pour les applications Android. Pour ce faire, vous devez avoir un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-FR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Essai gratuit d'Azure</a>.

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application Android

[AZURE.INCLUDE [Création d’application Mobile Engagement dans le portail](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications Push. Vous trouverez la documentation complète sur l’intégration dans le [Kit de développement logiciel (SDK) Mobile Engagement pour Android](../mobile-engagement-android-sdk-overview/).

Nous allons créer une application de base avec Android Studio afin d'illustrer l'intégration.

###Créer un projet Android

1. Démarrez **Android Studio** et, dans le menu contextuel, sélectionnez **Démarrer un nouveau projet Android Studio**.

    ![][1]

2. Indiquez un nom d’application et un domaine d’entreprise. Consignez les valeurs renseignées, car vous allez les utiliser ultérieurement. Cliquez sur **Next**.

    ![][2]

3. Sélectionnez le niveau d’API et le facteur de forme cible, puis cliquez sur **Suivant**.

	>[AZURE.NOTE]Mobile Engagement requiert au minimum le niveau d'API 10 (Android 2.3.3).

    ![][3]

4. Sélectionnez ici **Blank Activity**, qui sera le seul écran pour cette application et cliquez sur **Suivant**.

    ![][4]

5. Enfin, laissez les valeurs par défaut et cliquez sur **Terminer**.

    ![][5]

Android Studio crée l’application de démonstration à laquelle nous intégrerons Mobile Engagement.

###Inclure la bibliothèque du Kit de développement logiciel (SDK) dans votre projet

Télécharger et intégrer la bibliothèque du SDK

1. Téléchargez le [Kit SDK Mobile Engagement Android].
2. Extrayez le fichier d'archive dans un dossier sur votre ordinateur.
3. Identifiez la bibliothèque .jar correspondant à la version actuelle de ce Kit SDK et copiez-la dans le Presse-papiers.

	  ![][6]

4. Accédez à la section **Projet** (1) et collez le fichier .jar dans le dossier libs (2).

	  ![][7]

5. Synchronisez votre projet pour charger la bibliothèque.

	  ![][8]

###Connectez votre application au serveur principal Mobile Engagement à l'aide de la chaîne de connexion.

1. Copiez les lignes de code suivantes dans la création d’activité (cette opération doit être effectuée à un seul emplacement de votre application, généralement dans l’activité principale). Pour cet exemple d’application, ouvrez MainActivity sous src -> main -> dossier java et ajoutez le code suivant :

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Résolvez les références en appuyant sur Alt + Entrée ou en ajoutant les instructions d’importation suivantes :

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. De retour sur le portail Azure Classic, dans la page **Informations de connexion** de votre application, copiez la **chaîne de connexion**.

	  ![][9]

4. Collez-la dans le paramètre `setConnectionString` pour remplacer l’exemple fourni comme illustré ci-dessous :

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###Ajouter des autorisations et une déclaration de service

1. Ajoutez ces autorisations au fichier Manifest.xml de votre projet, juste avant ou après la balise `<application>` :

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. Ajoutez le code suivant entre les balises `<application>`et`</application>` pour déclarer le service de l'agent :

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. Dans le code que vous venez de coller, remplacez `"<Your application name>"` dans l’étiquette. Cet élément s’affiche dans le menu **Paramètres** où les utilisateurs peuvent voir les services qui sont en cours d’exécution sur l’appareil. Vous pouvez, par exemple, ajouter le mot « Service » dans cette étiquette.

###Envoyer un écran à Mobile Engagement

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

Accédez à **MainActivity.java** et ajoutez le code suivant pour remplacer la classe de base **MainActivity** par **EngagementActivity** :

	public class MainActivity extends EngagementActivity {

Vous devez commenter (exclure) la ligne suivante pour ce simple exemple de scénario :

    // setSupportActionBar(toolbar);

Si vous souhaitez conserver cette ligne, vous devez consulter le scénario « Rapport de base » sur la page relative à l’[intégration avancée d’Android].

##<a id="monitor"></a>Connexion d’application avec l’analyse en temps réel

[AZURE.INCLUDE [Connexion d’application avec l’analyse en temps réel](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie in-app

Mobile Engagement vous permet d’interagir et d’ATTEINDRE vos utilisateurs à l’aide de notifications Push et de la messagerie in-app dans le cadre de campagnes. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettent de configurer votre application pour la réception des notifications.

### Activation de la messagerie in-app

1. Copiez les ressources de messagerie in-app ci-dessous dans votre fichier Manifest.xml entre les balises `<application>` et `</application>`.

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
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. Copiez les ressources dans votre projet en effectuant les étapes suivantes :
	1. Revenez au contenu de téléchargement de votre Kit SDK et copiez le dossier « res ».

		 ![][13]

	2. Revenez à Android Studio, sélectionnez le répertoire « principal » des fichiers de votre projet, puis collez-le pour ajouter les ressources à votre projet.

		 ![][14]

###Spécifier une icône pour les notifications

Collez l’extrait de code XML suivant dans votre fichier Manifest.xml entre les balises `<application>` et `</application>`.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Cela définit l’icône qui s’affiche à la fois dans le système et des notifications in-app. Facultative pour les notifications in-app, cette opération est toutefois obligatoire pour les notifications système. Android rejettera les notifications système avec des icônes non valides.

Assurez-vous d’utiliser une icône qui existe dans l’un des dossiers **modifiable** (comme ``engagement_close.png``). Le dossier **mipmap** n’est pas pris en charge.

>[AZURE.NOTE]Vous ne devez pas utiliser l’icône du **Lanceur**. Elle a une résolution différente et se trouve généralement dans les dossiers mipmap, que nous ne prenons pas en charge.

Pour les applications réelles, vous pouvez utiliser une icône adaptée aux notifications conformément aux [instructions sur la conception Android](http://developer.android.com/design/patterns/notifications.html).

>[AZURE.TIP]Pour veiller à utiliser des icônes de résolutions correctes, vous pouvez consulter [ces exemples](https://www.google.com/design/icons). Faites défiler jusqu’à la section **Notification**, cliquez sur une icône, puis sur `PNGS` pour télécharger le jeu d’icônes. Vous pouvez voir quels dossiers dessinables utiliser avec quelle résolution pour chaque version de l’icône.

##Créer un projet Google Cloud Messaging avec une clé API

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Activer votre application pour recevoir des notifications Push GCM

1. Collez le code suivant dans votre fichier Manifest.xml entre les balises `<application>` et `</application>` après le remplacement du `project number` obtenu à partir de votre console Google Play. Le \\n est intentionnel alors assurez-vous que le numéro de projet se termine avec ce dernier.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Collez le code ci-dessous dans votre fichier Manifest.xml entre les balises `<application>`et`</application>`. Remplacez le nom du package <Your package name>.

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
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. Ajoutez le dernier jeu d’autorisations mis en surbrillance avant la balise `<application>`. Remplacez `<Your package name>` par le nom réel du package de votre application.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Accorder à Mobile Engagement l’accès à votre clé d’API GCM

Pour permettre à Mobile Engagement d’envoyer des notifications Push en votre nom, vous devez lui accorder l’accès à votre clé d’API. Pour cela, vous devez configurer et entrer votre clé dans le portail Mobile Engagement.

1. Accédez au portail Mobile Engagement

	Dans votre portail Azure Classic, vérifiez que vous êtes bien dans l’application que nous utilisons pour ce projet, puis cliquez sur le bouton **Engager** en bas de l’écran :

	![][15]

2. Cliquez ensuite sur **Paramètres** -> **Push natif** pour entrer votre clé GCM :

	![][16]

3. Cliquez sur l’icône **Modifier** en face de **Clé API** dans la section **Paramètres GCM**, comme indiqué ci-dessous :

	![][17]

4. Dans la fenêtre contextuelle, collez la clé du serveur GCM obtenue précédemment, puis cliquez sur **OK**.

	![][18]

##<a id="send"></a>Envoi d’une notification vers votre application

Nous allons à présent créer une campagne simple qui envoie une notification Push à notre application.

1. Accédez à l'onglet **REACH** de votre portail Mobile Engagement.

2. Cliquez sur **Nouvelle annonce** pour créer votre campagne de notification Push.

	![][20]

3. Configurez le premier champ de votre campagne en exécutant les étapes suivantes :

	![][21]

	a. Nommez votre campagne.

	b. Définissez le **Type d’envoi** sur *Notification système > Simple* : il s’agit du type de notification Push Android simple qui comprend un titre et une petite ligne de texte.

	c. Sélectionnez l’**Heure de remise** sur *N’importe quand* pour permettre à l’application de recevoir une notification, qu’elle soit démarrée ou non.

	d. Dans le texte de notification, tapez le **Titre** qui apparaîtra en gras dans la notification Push.

	e. Tapez ensuite votre **message**.

4. Faites défiler l’écran vers le bas et, dans la section **Contenu**, sélectionnez **Notification uniquement**.

	![][22]

5. Vous avez terminé de définir la campagne de base la plus simple possible. Maintenant, faites défiler vers le bas de nouveau, puis cliquez sur le bouton **Créer** pour enregistrer votre campagne.

6. Dernière opération : cliquez sur **Activer** pour activer votre campagne et envoyer des notifications Push.

	![][24]

<!-- URLs. -->
[Kit SDK Mobile Engagement Android]: https://aka.ms/vq9mfn
[Kit de développement logiciel (SDK) Mobile Engagement pour Android]: https://aka.ms/vq9mfn
[Mobile Engagement Android SDK documentation]: https://aka.ms/tujlkm
[intégration avancée d’Android]: https://azure.microsoft.com/fr-FR/documentation/articles/mobile-engagement-android-integrate-engagement/#basic-reporting

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=AcomDC_1203_2015-->