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
	ms.date="06/03/2015"
	ms.author="ricksal"/>

# <a name="getting-started"> </a>Prise en main de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Ce didacticiel présente l'ajout d'un service backend cloud à une application Android à l'aide d'Azure Mobile Services. Dans ce didacticiel, vous allez créer un service mobile et une simple application <em>To do list</em> qui stocke les données d'application dans le nouveau service mobile.</p>
<p>Voici une capture d'écran de l'application terminée&#160;:</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-get-started-android-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Support-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a><span class="time">7:26</span></div>
</div>

![][0]

Pour suivre ce didacticiel, vous avez besoin des [Outils de développement Android][Android SDK], qui incluent l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et la dernière plateforme Android. Android 4.2 ou une version ultérieure est nécessaire.

Le projet de démarrage rapide téléchargé contient le Kit de développement logiciel (SDK) Mobile Services pour Android. Alors que ce projet requiert Android 4.2 ou une version ultérieure, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

> [AZURE.IMPORTANT]Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

<!-- -->

>[AZURE.NOTE]Pour consulter le code source de l'application terminée, cliquez <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStarted/Android" target="_blank">ici</a>.

## <a name="create-new-service"> </a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Création d’une application Android

Après avoir créé votre service mobile, vous pouvez suivre un démarrage rapide facile dans le portail de gestion pour créer une application ou modifier une application existante afin de vous connecter au service mobile.

Dans cette section, vous allez créer une application Android connectée à votre service mobile.

1.  Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

2. Dans l'onglet de démarrage rapide, cliquez sur **Android** sous **Choisissez une plateforme** et développez **Créer une application Android**.

   	![][6]

   	Cette opération affiche les trois étapes faciles permettant de créer une application Android connectée à votre service mobile.

  	![][7]

3. Si ce n'est pas déjà fait, téléchargez et installez les [outils de développement Android][Android SDK] sur votre ordinateur local ou sur votre machine virtuelle.

4. Cliquez sur **Create TodoItem table** pour créer une table permettant de stocker les données d'application.


5. Téléchargez maintenant votre application :
	- La dernière version de l’application utilise la version 2.0 du Kit de développement logiciel (SDK) Mobile Services pour Android. Vous pouvez télécharger cette version <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures">ici</a>. Cliquez sur **Télécharger le Zip** et décompressez-le : le projet se trouve dans Prise en main sous le dossier Android.

	- Une version antérieure utilise la version précédente du Kit de développement logiciel (SDK). Pour l’utiliser, sous **Télécharger et exécuter l’application**, cliquez sur **Télécharger**. Cette opération télécharge le projet de votre exemple d'application _To do list_ qui est connectée à votre service mobile. Les fichiers projet sont compressés. Accédez à leur emplacement et décompressez-les sur votre ordinateur.


## Exécution de votre application Android

La dernière étape de ce didacticiel consiste à générer et à exécuter votre nouvelle application.

2. Dans Eclipse, cliquez sur **Fichier**, sur **Importer**, développez **Android**, puis cliquez sur **Existing Android Code into Workspace** et sur **Suivant.**

 	![][14]

3. Cliquez sur **Parcourir**, accédez à l'emplacement des fichiers projet développés, cliquez sur **OK**, veillez à ce que le projet TodoActivity soit sélectionné, puis cliquez sur **Terminer**.

 	![][15]

	Cette opération importe les fichiers projet dans l'espace de travail actuel.

   	![][8]

4. Si vous avez téléchargé la version 2.0 du Kit de développement logiciel (SDK), vous devez mettre à jour le code avec l'URL et la clé de votre service mobile :
	- 	Recherchez la méthode **OnCreate** dans **TodoActivity.java** et recherchez le code qui instancie le client des services mobiles. Le code est visible dans l’image précédente.
	- 	Remplacez « MobileServiceUrl » par l'URL réelle de votre service mobile.
	- 	Remplacez « AppKey » par la clé de votre service mobile.
	- 	Pour plus d’informations, consultez le didacticiel <a href="http://azure.microsoft.com/documentation/articles/mobile-services-android-get-started-data/">Ajout de Mobile Services à une application existante</a>.



4. À partir du menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet dans l'émulateur Android.

	> [AZURE.IMPORTANT]Afin d'exécuter le projet dans l'émulateur Android, vous devez définir au moins un AVD (appareil virtuel Android). Utilisez le gestionnaire AVD pour créer et gérer ces appareils.

5. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, puis cliquez sur **Ajouter**.

   	![][10]

   	Ceci envoie une demande POST vers le nouveau service mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans la table sont renvoyés par le service mobile et les données sont affichées dans la liste.

	> [AZURE.NOTE]Vous pouvez vérifier le code qui se trouve dans le fichier ToDoActivity.java et permet d'accéder au service mobile pour exécuter une requête et insérer des données.

6. De retour dans le portail de gestion, cliquez sur l'onglet **Données**, puis sur la table **TodoItems**.

   	![][11]

   	Cela vous permet de parcourir les données insérées par l'application dans la table.

   	![][12]

## <a name="next-steps"> </a>Étapes suivantes
Maintenant que vous avez effectué le démarrage rapide, découvrez comment exécuter d'autres tâches importantes dans Mobile Services :

* [Prise en main des données] <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de Mobile Services.

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un fournisseur d'identité.

* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

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
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO6-->