<properties 
	pageTitle="Prise en main des données (Android) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour tirer profit des données de votre application Android." 
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
	ms.date="02/06/2015" 
	ms.author="ricksal,glenga"/>

# Ajout de Mobile Services à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data-EC.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application Android. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">voir le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a><span class="time">15:32</span></div>
</div>

> [AZURE.NOTE] Ce didacticiel est conçu pour vous aider à mieux comprendre comment Mobile Services vous permet d'utiliser Azure pour stocker et récupérer des données à partir d'une application Android. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services](/fr-fr/develop/mobile/tutorials/get-started-android-EC).
> 
> Pour consulter le code source de l'application terminée, cliquez [ici](https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithData/Android/GetStartedWithData).


Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement du projet d'application Android] 
2. [Création du service mobile]
3. [Ajout d'une table de données pour le stockage]
4. [Mise à jour de l'application pour utiliser Mobile Services]
5. [Test de l'application par rapport à Mobile Services]

> [AZURE.IMPORTANT] Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AED8DE357"%20target="_blank). 

Ce didacticiel requiert le [Kit de développement logiciel (SDK) Mobile Services pour Android], le <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Kit de développement logiciel (SDK) Android</a>, qui inclut l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et Android 4.2 ou une version ultérieure. 

> [AZURE.NOTE] Ce didacticiel fournit des instructions sur l'installation des Kits de développement logiciel (SDK) Android et Mobile Services pour Android. Le projet GetStartedWithData téléchargé requiert Android 4.2 ou une version ultérieure. Toutefois, le Kit de développement logiciel (SDK) Mobile Services requiert uniquement Android 2.2 ou une version ultérieure.

<!-- -->

> [AZURE.NOTE] Ce didacticiel utilise la dernière version du Kit de développement logiciel (SDK) Mobile Services. Vous en trouverez une version antérieure <a href="http://go.microsoft.com/fwlink/p/?LinkID=280126"> ici</a> pour des raisons de compatibilité descendante, mais le code contenu dans ces didacticiels ne fonctionne pas avec elle.

<h2><a name="download-app"></a>Téléchargement du projet GetStartedWithData</h2>

###Obtention de l'exemple de code

[AZURE.INCLUDE [download-android-sample-code](../includes/download-android-sample-code-EC.md)]

###Vérification de la version du Kit de développement logiciel (SDK) Android

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version-EC.md)]


###Inspection et exécution de l'exemple de code

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code-EC.md)]

<h2><a name="create-service"></a>Création d'un service mobile dans le portail de gestion</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Ajout d'une nouvelle table au service mobile</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données</h2>

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data-EC.md)]


<h2><a name="test-app"></a>Test de l'application sur la base de votre nouveau service mobile</h2>

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, vous pouvez la tester avec Mobile Services à l'aide de l'émulateur Android ou d'un téléphone Android.

1. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet.

	Cela permet d'exécuter votre application, créée avec le Kit de développement logiciel (SDK) Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

5. Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3. Dans le [portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4. Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

   	![][9]
  
   	La table **TodoItem** contient à présent des données, dont certaines valeurs ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Android.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Android pour utiliser les données dans Mobile Services. 

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification de données à l'aide de scripts]
  <br/>Obtenez plus d'informations sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>Apprenez à utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels Android :

* [Prise en main de l'authentification] 
	<br/>Découvrez comment authentifier les utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>Apprenez à envoyer une notification Push très basique à votre application avec Mobile Services.

<!-- Anchors. -->
[Téléchargement du projet d'application Android]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une table de données pour le stockage]: #add-table
[Mise à jour de l'application pour utiliser Mobile Services]: #update-app
[Test de l'application par rapport à Mobile Services]: #test-app
[Étapes suivantes]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-android
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-android
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-android
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Mobile Services pour Android]: http://aka.ms/Iajk6q
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Kit de développement logiciel (SDK) Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125

<!--HONumber=45--> 
