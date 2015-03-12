<properties 
	pageTitle="Prise en main d'Azure Mobile Services pour les applications Android" 
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Services pour le développement Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/13/2015" 
	ms.author="ricksal"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-EC.md)]

Ce didacticiel présente l'ajout d'un service principal cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application _To do list_ qui stocke les données d'application dans le nouveau service mobile. Le service mobile que vous allez créer utilise les langages .NET pris en charge à l'aide de Visual Studio pour la logique métier côté serveur et pour la gestion du service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version relative au service principal JavaScript](/fr-fr/documentation/articles/mobile-services-android-get-started-EC/) de cette rubrique.

Voici une capture d'écran de l'application terminée :

![][0]

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android][Kit de développement logiciel (SDK) Android], qui incluent l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire. 

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Mobile Services pour Android. Alors que ce projet requiert Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 services mobiles gratuits que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Téléchargement du service mobile sur votre ordinateur local

Maintenant que vous avez créé le service mobile, téléchargez votre projet de service mobile personnalisé afin de l'exécuter sur votre ordinateur local ou sur votre machine virtuelle.

1. Cliquez sur le service mobile que vous venez de créer, puis dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

	![][1]  

2. Si ce n'est pas déjà fait, téléchargez et installez [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou une version ultérieure.

3. Cliquez sur **Télécharger** sous **Télécharger et publier votre service dans le cloud**.

	Cela permet de télécharger le projet Visual Studio qui implémente votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

4. Téléchargez également votre profil de publication, enregistrez le fichier téléchargé sur votre ordinateur local et notez où vous l'enregistrez.

## Test du service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publication de votre service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Création d'une application Android

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**. 
 
	![][2]  

3. Si vous n'avez pas déjà fait, téléchargez et installez les [Outils de développement Android][Kit de développement logiciel (SDK)] sur votre ordinateur local ou un ordinateur virtuel.

4. Sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**. 

  	Cette opération télécharge le projet de votre exemple d'application _To do list_ connectée à votre service mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Exécution de votre application Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

1. Accédez à l'emplacement où vous avez enregistré les fichiers projet compressés, puis développez les fichiers sur votre ordinateur.

2. Dans Eclipse, cliquez sur **Fichier** puis sur **Importer**, développez **Android**, cliquez sur **Existing Android Code into Workspace**, puis sur **Suivant**. 

 	![][14]

3. Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, assurez-vous que le projet TodoActivity est sélectionné, puis cliquez sur **Terminer**. 

 	![][15]

	Cette opération importe les fichiers projet dans l'espace de travail actuel.

   	![][8]

4. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

	> [AZURE.IMPORTANT] Pour exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

5. Dans l'application, tapez un texte explicite, par exemple _Terminer le didacticiel_, puis cliquez sur **Ajouter**.

   	![][10]

   	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE] Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.java et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Étapes suivantes
Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services : 

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Résolution des problèmes d'un service principal .NET de Mobile Services]
  <br/> Apprenez à diagnostiquer et résoudre les problèmes pouvant survenir avec un service principal Mobile Services .NET. 

<!-- Anchors. -->
[Prise en main de Mobile Services]:#getting-started
[Création d'un service mobile]:#create-new-service
[Définition de l'instance de service mobile]:#define-mobile-service-instance
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-EC.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-EC.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[Kit de développement logiciel (SDK) Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Kit de développement logiciel (SDK) Mobile Services pour Android]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Résolution des problèmes d'un service principal .NET de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Portail de gestion]: https://manage.windowsazure.com/

<!--HONumber=45--> 
