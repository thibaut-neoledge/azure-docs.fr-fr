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
	ms.date="02/03/2015" 
	ms.author="ricksal,/glenga"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel présente l'ajout d'un service principal cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application <em>To do list</em> qui stocke les données d'application dans le nouveau service mobile.</p>
<p>Voici une capture d'écran de l'application terminée :</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">voir le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a><span class="time">07:26</span></div>
</div>

![][0]

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android][Android Studio], qui incluent l'environnement de développement intégré Android Studio et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire. 

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Azure Mobile Services pour Android.

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

<!-- -->

> [AZURE.NOTE] Le code source de l'application terminée est disponible <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStarted/AndroidStudio" target="_blank">ici,</a>.

<!-- -->

> [AZURE.NOTE] Si vous souhaitez consulter la version d'Eclipse de ce didacticiel, allez à : [Prise en main (Eclipse)].


## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## <h2>Création d'une application Android</h2>

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile. 

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

   	![][6]

   	Cette opération affiche les trois étapes faciles permettant de créer une application Android connectée à votre service mobile.

  	![][7]

3. Si vous ne l'avez pas déjà fait, téléchargez et installez les [Outils de développement Android][Kit de développement logiciel (SDK)] sur votre ordinateur local ou un ordinateur virtuel.

4. Cliquez sur **Créer une table TodoItem** pour créer une table permettant de stocker les données d'application.


5. Téléchargez maintenant votre application :
	- La dernière version de l'application utilise Mobile Services Android SDK 2.0. Vous pouvez télécharger cette version <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">ici,</a>. Cliquez sur **Télécharger le Zip** et décompressez-le : le projet se trouve dans Prise en main sous le dossier Android.
	 
	- Une version antérieure utilise la version précédente du Kit de développement logiciel (SDK). Pour l'utiliser, sous **Télécharger et exécuter l'application**, cliquez sur **Télécharger**. Cette opération télécharge le projet de votre exemple d'application _To do list_ connectée à votre service mobile. Les fichiers projet sont compressés. Accédez à leur emplacement et décompressez-les sur votre ordinateur.


## Exécution de votre application Android

[WACOM.INCLUDE [mobile-services-run-your-app](../includes/mobile-services-android-get-started.md)]


## <a name="next-steps"> </a>Étapes suivantes
Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services : 

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.



<!-- Anchors. -->
[Prise en main de Mobile Services]:#getting-started
[Création d'un service mobile]:#create-new-service
[Définition de l'instance de service mobile]:#define-mobile-service-instance
[Étapes suivantes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-android-get-started/mobile-quickstart-steps-android-AS.png
[8]: ./media/mobile-services-android-get-started/mobile-eclipse-quickstart.png
[10]: ./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-android-get-started/mobile-data-browse.png
[14]: ./media/mobile-services-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Prise en main (Eclipse)]: /documentation/articles/mobile-services-android-get-started-EC/
[Prise en main des données]: /documentation/articles/mobile-services-android-get-started-data/
[Prise en main de l'authentification]: /documentation/articles/mobile-services-android-get-started-users/
[Prise en main des notifications Push]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Kit de développement logiciel (SDK)]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Android Studio]: https://developer.android.com/sdk/index.html
[Kit de développement logiciel (SDK) Mobile Services pour Android]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portail de gestion]: https://manage.windowsazure.com/

<!--HONumber=47-->
