<properties
	pageTitle="Prise en main d’Azure Mobile Engagement pour le déploiement de l’application Unity pour Android"
	description="Découvrez comment utiliser Azure Mobile Engagement avec les analyses et les notifications Push pour les applications Unity déployées sur des appareils iOS."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/19/2016"
	ms.author="piyushjo" />

# Prise en main d’Azure Mobile Engagement pour le déploiement de l’application Unity pour Android

[AZURE.INCLUDE [Sélecteur de didacticiel Hero](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Cette rubrique montre comment utiliser Azure Mobile Engagement pour comprendre l’utilisation de votre application et envoyer des notifications Push à des utilisateurs segmentés d’une application Unity lors de son déploiement sur un appareil Android. Ce didacticiel utilise le didacticiel classique Unity Roll-a-Ball comme point de départ. Vous devez suivre la procédure décrite dans ce [didacticiel](mobile-engagement-unity-roll-a-ball.md) avant de procéder à l’intégration de Mobile Engagement que nous présentons dans le didacticiel ci-après.

Ce didacticiel requiert les éléments suivants :

+ [Unity Editor](http://unity3d.com/get-unity)
+ [Kit de développement logiciel (SDK) Mobile Engagement pour Unity](https://aka.ms/azmeunitysdk)
+ Kit de développement logiciel (SDK) pour Google Android

> [AZURE.NOTE] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-FR%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Configuration de Mobile Engagement pour votre application Android

[AZURE.INCLUDE [Création d’application Mobile Engagement dans le portail](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connexion de votre application au serveur principal Mobile Engagement

###Importer le package Unity

1. Téléchargez le [package Unity pour Mobile Engagement](https://aka.ms/azmeunitysdk) et enregistrez-le sur votre ordinateur local.

2. Accédez à **Assets -> Import Package -> Custom Package** et sélectionnez le package que vous avez téléchargé à l’étape précédente.

	![][70]

3. Vérifiez que tous les fichiers sont sélectionnés, puis cliquez sur le bouton **Import**.

	![][71]

4. Une fois l’importation effectuée, les fichiers du Kit de développement logiciel (SDK) importé dans votre projet s’affichent.

	![][72]

###Mettre à jour le fichier EngagementConfiguration

1. Ouvrez le fichier de script **EngagementConfiguration** contenu dans le dossier SDK et mettez à jour la chaîne **ANDROID\_CONNECTION\_STRING** en la remplaçant par la chaîne de connexion obtenue plus tôt à partir du portail Azure.

	![][73]

2. Enregistrez le fichier.

3. Exécutez **File -> Engagement -> Generate Android Manifest**. Il s’agit du plug-in ajouté par le Kit de développement logiciel (SDK) Mobile Engagement. Cliquez dessus pour mettre à jour automatiquement les paramètres du projet.

	![][74]

> [AZURE.IMPORTANT] Veillez à exécuter cette procédure à chaque mise à jour du fichier **EngagementConfiguration**, faute de quoi vos modifications ne seront pas prises en compte dans l’application.

###Configurer l’application pour un suivi de base

1. Ouvrez le script **PlayerController** joint à l’objet Player pour le modifier.

2. Ajoutez les instructions using suivantes :

		using Microsoft.Azure.Engagement.Unity;

3. Ajoutez ce qui suit à la méthode `Start()` :
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Déployer et exécuter l’application
Vérifiez que le Kit de développement logiciel (SDK) Android est installé sur votre ordinateur avant d’essayer de déployer l’application Unity sur votre appareil.

1. Connectez un appareil Android à votre ordinateur.

2. Accédez à **File -> Build Settings**.

	![][40]

3. Sélectionnez **Android**, puis cliquez sur **Switch Platform**.

	![][51]

	![][52]

4. Cliquez sur **Player settings** et indiquez un identificateur Bundle Identifier valide.

	![][53]

5. Pour finir, cliquez sur **Build And Run**.

	![][54]

6. Vous pouvez être invité à fournir un nom de dossier pour y stocker le package Android.

7. Si tout se passe bien, le package doit être déployé sur votre appareil connecté, et votre jeu Unity doit apparaître sur votre téléphone.

##<a id="monitor"></a>Connexion d’application avec l’analyse en temps réel

[AZURE.INCLUDE [Connexion d’application avec l’analyse en temps réel](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Activation des notifications Push et de la messagerie in-app

[AZURE.INCLUDE [Activer Google Cloud Messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Mettre à jour le fichier EngagementConfiguration

1. Ouvrez le fichier de script **EngagementConfiguration** contenu dans le dossier SDK et mettez à jour la chaîne **ANDROID\_GOOGLE\_NUMBER** en la remplaçant par le numéro **Google Project Number** obtenu plus tôt à partir du portail Google Cloud Developer. Comme il s’agit d’une chaîne de valeur, veillez à l’insérer entre des guillemets.

	![][75]

2. Enregistrez le fichier .

3. Exécutez **File -> Engagement -> Generate Android Manifest**. Il s’agit du plug-in ajouté par le Kit de développement logiciel (SDK) Mobile Engagement. Cliquez dessus pour mettre à jour automatiquement les paramètres du projet.

	![][74]

###Configurer l’application pour recevoir des notifications

1. Ouvrez le script **PlayerController** joint à l’objet Player pour le modifier.

2. Ajoutez ce qui suit à la méthode `Start()` :

		EngagementReachAgent.Initialize();

3. À présent que l’application est mise à jour, déployez et exécutez l’application sur un appareil conformément aux instructions ci-dessous.

[AZURE.INCLUDE [Envoyer des notifications à partir du portail](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png

<!---HONumber=AcomDC_0824_2016-->