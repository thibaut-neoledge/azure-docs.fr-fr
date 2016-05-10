<properties
	pageTitle="Prise en main d’Azure Mobile Engagement"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Android."
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="piyushjo;ricksal" />

# Prise en main d’Azure Mobile Engagement pour les applications Android

[AZURE.INCLUDE [Sélecteur de didacticiel Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d’une application Android. Ce didacticiel montre un scénario de diffusion simple à l'aide de Mobile Engagement. À cette occasion, vous allez créer une application Android vide qui recueille des données de base et reçoit des notifications Push à l'aide de Google Cloud Messaging (GCM).

## Composants requis

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android](https://developer.android.com/sdk/index.html), qui incluent l'environnement de développement intégré Android Studio et la dernière plateforme Android.

Le [Kit de développement logiciel (SDK) Mobile Engagement Android](https://aka.ms/vq9mfn) est également requis.

> [AZURE.IMPORTANT] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fmobile-engagement-android-get-started).

## Configuration de Mobile Engagement pour votre application Android

[AZURE.INCLUDE [Création d’application Mobile Engagement dans le portail](../../includes/mobile-engagement-create-app-in-portal.md)]

## Connexion de votre application au serveur principal Mobile Engagement

Ce didacticiel aborde l'intégration de base qui correspond aux éléments nécessaires à la collection de données et à l'envoi de notifications push. Vous trouverez la documentation complète sur l’intégration dans le [Kit de développement logiciel (SDK) Mobile Engagement pour Android](mobile-engagement-android-sdk-overview.md).

Nous allons créer une application de base avec Android Studio afin d'illustrer l'intégration.

### Créer un projet Android

1. Démarrez **Android Studio** et, dans le menu contextuel, sélectionnez **Démarrer un nouveau projet Android Studio**.

    ![][1]

2. Indiquez un nom d’application et un domaine d’entreprise. Consignez les valeurs renseignées, car vous allez les utiliser ultérieurement. Cliquez sur **Next**.

    ![][2]

3. Sélectionnez le niveau d’API et le facteur de forme cible, puis cliquez sur **Suivant**.

	>[AZURE.NOTE] Mobile Engagement requiert au minimum le niveau d'API 10 (Android 2.3.3).

    ![][3]

4. Sélectionnez ici **Blank Activity**, qui sera le seul écran pour cette application et cliquez sur **Suivant**.

    ![][4]

5. Enfin, laissez les valeurs par défaut et cliquez sur **Terminer**.

    ![][5]

Android Studio crée l’application de démonstration à laquelle nous intégrerons Mobile Engagement.

### Inclure la bibliothèque du Kit de développement logiciel (SDK) dans votre projet

1. Téléchargez le [Kit de développement logiciel (SDK) Mobile Engagement Android].
2. Extrayez le fichier d'archive dans un dossier sur votre ordinateur.
3. Identifiez la bibliothèque .jar correspondant à la version actuelle de ce Kit SDK et copiez-la dans le Presse-papiers.

	  ![][6]

4. Accédez à la section **Projet** (1) et collez le fichier .jar dans le dossier libs (2).

	  ![][7]

5. Synchronisez votre projet pour charger la bibliothèque.

	  ![][8]

### Connectez votre application au serveur principal Mobile Engagement à l'aide de la chaîne de connexion.

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

### Ajouter des autorisations et une déclaration de service

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

### Envoyer un écran à Mobile Engagement

Pour commencer à envoyer des données et vous assurer que les utilisateurs sont actifs, vous devez envoyer au moins un écran (activité) au serveur principal Mobile Engagement.

Accédez à **MainActivity.java** et ajoutez le code suivant pour remplacer la classe de base **MainActivity** par **EngagementActivity** :

	public class MainActivity extends EngagementActivity {

Vous devez commenter (exclure) la ligne suivante pour ce simple exemple de scénario :

    // setSupportActionBar(toolbar);

Si vous souhaitez conserver cette ligne, vous devez consulter le scénario « Rapport de base » sur la page relative à l’[intégration avancée d’Android](mobile-engagement-android-integrate-engagement.md/#basic-reporting).

## Connexion d’application avec l’analyse en temps réel

[AZURE.INCLUDE [Connexion d’application avec l’analyse en temps réel](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## Activation des notifications push et de la messagerie in-app

Mobile Engagement vous permet d’interagir et d’ATTEINDRE vos utilisateurs à l’aide de notifications Push et de la messagerie in-app dans le cadre de campagnes. Ce module s'appelle Couverture dans le portail Mobile Engagement. Les sections suivantes vous permettent de configurer votre application pour la réception des notifications.

### Copier les ressources du SDK dans votre projet

1. Revenez au contenu de téléchargement de votre Kit de développement logiciel (SDK) et copiez le dossier **res**.

	![][10]

2. Revenez à Android Studio, sélectionnez le répertoire **principal** des fichiers de votre projet, puis collez-le pour ajouter les ressources à votre projet.

	![][11]

[AZURE.INCLUDE [Activer Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [Activation de la messagerie in-app](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [Envoyer des notifications à partir du portail](../../includes/mobile-engagement-android-send-push-from-portal.md)]

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
[10]: ./media/mobile-engagement-android-get-started/copy-resources.png
[11]: ./media/mobile-engagement-android-get-started/paste-resources.png

<!---HONumber=AcomDC_0504_2016-->