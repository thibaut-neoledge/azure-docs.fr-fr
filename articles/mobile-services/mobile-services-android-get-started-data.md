<properties
	pageTitle="Prendre en main les données sur Android (backend JavaScript) | Microsoft Azure"
	description="Découvrez comment utiliser Mobile Services pour tirer profit des données de votre application Android (backend JavaScript)."
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
	ms.date="01/20/2016"
	ms.author="ricksal"/>

# Ajouter Mobile Services à une application Android existante (backend JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## Résumé

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment utiliser Azure Mobile Services pour ajouter des données persistantes à une application Android. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer l'application au service mobile pour qu'elle stocke et mette à jour les données dans Azure Mobile Services plutôt que localement, puis utiliser le portail Azure Classic pour afficher les modifications qui ont été apportées aux données en exécutant l'application.</p>

</div>


<div class="dev-onpage-video-wrapper">
<a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a><span class="time">15:32:00</span></div>
</div>


<p>Ce didacticiel vous aide à comprendre plus en détail comment Azure Mobile Services peut stocker et récupérer des données d'une application Android. À cet effet, il vous guide tout au long des nombreuses étapes déjà exécutées automatiquement dans le didacticiel du démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel <a href="/fr-FR/develop/mobile/tutorials/get-started-android">Prise en main de Mobile Services</a>.</p>

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

- Un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d’essai gratuit en quelques minutes. Pour plus d’informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Version d’essai gratuite d’Azure</a>.


- Le [Kit de développement logiciel (SDK) Azure Mobile Services Android].
- L’<a  href="https://developer.android.com/sdk/index.html" target="_blank">environnement de développement intégré Android Studio</a>, qui inclut le Kit de développement logiciel (SDK) Android, et Android 4.2 ou version ultérieure. Le projet GetStartedWithData téléchargé requiert Android 4.2 ou une version ultérieure. Toutefois, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

## Exemple de code

Pour afficher le code source terminé, vous pouvez y accéder <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/AndroidStudio">ici</a>.

## Téléchargement du projet GetStartedWithData

###Obtention de l'exemple de code

[AZURE.INCLUDE [download-android-sample-code](../../includes/download-android-sample-code.md)]

### Inspection et exécution de l'exemple de code

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

## Création d'un service mobile dans le portail Azure Classic

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## Ajout d'une nouvelle table au service mobile

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../../includes/mobile-services-create-new-service-data-2.md)]

## Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]


## Test de l'application sur la base de votre nouveau service mobile

L'application a été mise à jour pour utiliser Mobile Services pour le stockage principal. Vous pouvez maintenant la tester avec Mobile Services à l'aide de l'émulateur Android ou d'un téléphone Android.

1. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet.

	Cela permet d'exécuter votre application, créée avec le Kit de développement logiciel (SDK) Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

5. Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail Azure Classic], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]

   	La table **TodoItem** contient à présent des données, dont certaines valeurs ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Android.

## Résolution de problèmes

### Vérification de la version du Kit de développement logiciel (SDK) Android

[AZURE.INCLUDE [Vérification du Kit de développement logiciel (SDK)](../../includes/mobile-services-verify-android-sdk-version.md)]



## Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Android pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification des données avec des scripts] <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes à la pagination] <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels Android :

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.

<!-- Anchors. -->
[Download the Android app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Validation et modification des données avec des scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes à la pagination]: /develop/mobile/tutorials/add-paging-to-data-android
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-android
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /develop/mobile/tutorials/get-started-with-push-android

[portail Azure Classic]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Azure Mobile Services Android]: http://aka.ms/Iajk6q
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!---HONumber=AcomDC_0121_2016-->