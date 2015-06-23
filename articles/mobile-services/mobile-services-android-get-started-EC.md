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
	ms.topic="hero-article" 
	ms.date="02/13/2015" 
	ms.author="ricksal;glenga"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel présente l'ajout d'un service principal cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une application simple (<em>To do list</em>) qui stocke les données d'application dans le nouveau service mobile.</p>
<p>Voici une capture d'écran de l'application terminée :</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">Regarder le didacticiel</a><a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a><span class="time">07:26</span></div>
</div>

![][0]

Ce didacticiel requiert les [Outils de développement Android][Kit de développement logiciel (SDK) Android] qui inclut l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire. 

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Mobile Services pour Android. Alors que ce projet requiert Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

<!-- -->

>[AZURE.NOTE] Pour consulter le code source de l'application terminée, cliquez <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">ici</a>.

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## <h2>Création d'une application Android</h2>

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile. 

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Sous l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

   	![][6]

   	Cette opération affiche les trois étapes faciles permettant de créer une application Android connectée à votre service mobile.

  	![][7]

3. Si vous ne l'avez pas déjà fait, téléchargez et installez les [Outils de développement Android][Kit de développement logiciel (SDK) Android] sur votre ordinateur local ou une machine virtuelle.

4. Cliquez sur **Créer une table TodoItem** pour créer une table permettant de stocker les données d'application.


5. Téléchargez maintenant votre application :
	- La dernière version de l'application utilise la version 2.0 du Kit de développement logiciel (SDK) Mobile Services pour Android. Vous pouvez télécharger cette version à partir d'<a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">ici</a>. Cliquez sur **Télécharger le Zip** et décompressez-le : le projet se trouve dans Prise en main sous le dossier Android.
	 
	- Une version antérieure utilise la version précédente du Kit de développement logiciel (SDK). Pour l'utiliser, sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**. Cette opération télécharge le projet de votre exemple d'application _To do list_ connectée à votre service mobile. Les fichiers projet sont compressés. Accédez à leur emplacement et décompressez-les sur votre ordinateur.


## Exécution de votre application Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

2. Dans Eclipse, cliquez sur **Fichier**, sur **Importer**, développez **Android**, puis cliquez sur **Existing Android Code into Workspace** et sur **Suivant**. 

 	![][14]

3. Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, assurez-vous que le projet TodoActivity est sélectionné, puis cliquez sur **Terminer**. 

 	![][15]

	Cette opération importe les fichiers projet dans l'espace de travail actuel.

   	![][8]

4. Si vous avez téléchargé la version 2.0 du Kit de développement logiciel (SDK), vous devez mettre à jour le code avec l'URL et la clé de votre service mobile :
	- 	Recherchez la méthode **OnCreate** dans **TodoActivity.java** et recherchez le code qui instancie le client des services mobiles. Le code est visible dans l'image précédente.
	- 	Remplacez " MobileServiceUrl " par l'URL réelle de votre service mobile.
	- 	Remplacez " AppKey " par la clé de votre service mobile.
	- 	Pour plus d'informations, consultez le didacticiel <a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">Ajouter des Services mobiles à une application existante</a>. 



4. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

	> [AZURE.IMPORTANT] Pour exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

5. Dans l'application, tapez un texte explicite, par exemple _Terminer le didacticiel_, puis cliquez sur **Ajouter**.

   	![][10]

   	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la demande sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE] Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.java et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

6. De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

   	![][11]

   	Cela vous permet de parcourir les données insérées par l'application dans la table.

   	![][12]

## <a name="next-steps"> </a>Étapes suivantes
Vous avez terminé les étapes de démarrage rapide. Découvrez ensuite comment effectuer d'autres tâches importantes dans Mobile Services : 

* [Prise en main des données]
  <br/>Découvrez comment stocker et interroger des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] 
  <br/>Découvrez comment envoyer une notification Push très simple à votre application.

<!-- Anchors. -->
[Prise en main de Mobile Services]:#getting-started
[Création d'un service mobile]:#create-new-service
[Définition de l'instance de service mobile]:#define-mobile-service-instance
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-completed-android.png





[6]: ./media/mobile-services-android-get-started-EC/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-steps-android-EC.png
[8]: ./media/mobile-services-android-get-started-EC/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-android-get-started-EC/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started-EC/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started-EC/mobile-data-browse.png

[14]: ./media/mobile-services-android-get-started-EC/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-EC/mobile-services-import-android-project.png

<!-- URLs. -->
[Prise en main des données]: mobile-services-android-get-started-data.md
[Prise en main de l'authentification]: mobile-services-android-get-started-users.md
[Prise en main des notifications Push]: mobile-services-javascript-backend-android-get-started-push.md
[Kit de développement logiciel (SDK) Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Kit de développement logiciel (SDK) Mobile Services pour Android]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portail de gestion]: https://manage.windowsazure.com/


<!--HONumber=52--> 