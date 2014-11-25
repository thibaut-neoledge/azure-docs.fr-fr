<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="Get Started with Data - Android" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="Azure android data, Azure mobile services data, windows droid, windows android, microsoft droid, microsoft android" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal,glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/29/2014" ms.author="ricksal,glenga" />

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des donn&eacute;es dans une application Android. Dans ce didacticiel, vous allez t&eacute;l&eacute;charger une application qui stocke les donn&eacute;es en m&eacute;moire, cr&eacute;er un service mobile, int&eacute;grer le service mobile &agrave; l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apport&eacute;es aux donn&eacute;es lors de l'ex&eacute;cution de l'application.</p>

</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a><span class="time">15:32:00</span></div>

</div>

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel est con&ccedil;u pour vous aider &agrave; mieux comprendre comment Mobile Services vous permet d'utiliser Azure pour stocker et r&eacute;cup&eacute;rer des donn&eacute;es &agrave; partir d'une application Android. &Agrave; cet effet, il vous guide tout au long des nombreuses &eacute;tapes ex&eacute;cut&eacute;es automatiquement dans le d&eacute;marrage rapide Mobile Services. Si vous n'avez aucune exp&eacute;rience de Mobile Services, nous vous invitons &agrave; suivre d'abord le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-android">Prise en main de Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Android][Téléchargement d'un projet d'application Android]
2.  [Création du service mobile][Création du service mobile]
3.  [Ajout d'une table de données pour le stockage][Ajout d'une table de données pour le stockage]
4.  [Mise à jour de l'application pour utiliser Mobile Services][Mise à jour de l'application pour utiliser Mobile Services]
5.  [Test de l'application avec Mobile Services][Test de l'application avec Mobile Services]

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

Ce didacticiel requiert le [Kit de développement logiciel (SDK) Mobile Services pour Android][Kit de développement logiciel (SDK) Mobile Services pour Android] ; le [Kit de développement logiciel (SDK) Android][Kit de développement logiciel (SDK) Android], qui inclut l'IDE (environnement de développement intégré) Eclipse, le plug-in ADT (outils de développement Android) et Android 4.2 ou une version ultérieure.

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel fournit des instructions sur l'installation des Kits de d&eacute;veloppement logiciel (SDK) Android et Mobile Services pour Android. Le projet GetStartedWithData t&eacute;l&eacute;charg&eacute; requiert Android&nbsp;4.2 ou une version ult&eacute;rieure. Toutefois, le Kit de d&eacute;veloppement logiciel (SDK) Mobile Services requiert uniquement Android&nbsp;2.2 ou une version ult&eacute;rieure.</p>
</div>

## <a name="download-app"></a><span class="short-header">Téléchargement du projet</span>Téléchargement du projet GetStartedWithData

### Obtention de l'exemple de code

[WACOM.INCLUDE [download-android-sample-code](../includes/download-android-sample-code.md)]

### Vérification de la version du Kit de développement logiciel (SDK) Android

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

### Inspection et exécution de l'exemple de code

[WACOM.INCLUDE [mobile-services-android-run-sample-code](../includes/mobile-services-android-run-sample-code.md)]

## <a name="create-service"></a><span class="short-header">Création d'un service mobile</span>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a><span class="short-header">Ajout d'une nouvelle table</span>Ajout d'une nouvelle table au service mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application</span>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data](../includes/mobile-services-android-getting-started-with-data.md)]

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec votre nouveau service mobile

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, vous pouvez la tester avec Mobile Services à l'aide de l'émulateur Android ou d'un téléphone Android.

1.  Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet.

    Cela permet d'exécuter votre application, créée avec le Kit de développement logiciel (SDK) Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

2.  Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][0]

    La table **TodoItem** contient à présent des données, dont certaines valeurs ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour Android.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Android pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][Validation et modification des données avec des scripts]

    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][Affinage des requêtes à la pagination]

    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels Android :

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][Prise en main des notifications Push]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.


  [Téléchargement d'un projet d'application Android]: #download-app
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [portail de gestion]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-android
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-android
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-android
