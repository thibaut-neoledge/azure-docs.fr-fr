<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-android" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Prise en main des données dans Mobile Services

<div class="dev-center-tutorial-selector sublanding">
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS">iOS</a>
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title=".NET backend" class="current">Serveur principal .NET</a> | 
    <a href="/en-us/develop/mobile/tutorials/get-started-with-data-android/"  title="JavaScript backend">JavaScript backend</a>
</div>

Cette rubrique montre comment utiliser Azure Mobile Services en tant que source de données principale pour une application Android. Dans ce didacticiel, vous allez créer un service mobile, télécharger un projet Eclipse Android pour une application qui stocke les données en mémoire, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer dans ce didacticiel prendra en charge le runtime .NET dans le service mobile. Vous pourrez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript][] de cette rubrique.

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel requiert Visual&nbsp;Studio&nbsp;2013.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Création d'un service mobile][]
2.  [Téléchargement du service en local][]
3.  [Test du service mobile][]
4.  [Publication du service mobile sur Azure][]
5.  [Téléchargement du projet GetStartedWithData][]
6.  [Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données][]
7.  [Test de l'application avec le service mobile publié][]

<div class="dev-callout"><strong>Remarque</strong> <p>Pour effectuer ce didacticiel, vous avez besoin d&rsquo;un compte Azure. Si vous ne poss&eacute;dez pas de compte, vous pouvez cr&eacute;er un compte d'&eacute;valuation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Version d'&eacute;valuation gratuite d'Azure</a>.</p></div>

## <a name="create-service"></a><span class="short-header">Création d'un service mobile </span>Création d'un service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][]]

## <a name="download-the-service"></a><span class="short-header">Téléchargement du service</span>Téléchargement du service sur votre ordinateur local

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">Test du service</span>Test du service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <a name="publish-the-service"></a><span class="short-header">Publication du service</span>Publication du service mobile sur Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="download-app"></a><span class="short-header">Téléchargement du projet</span>Téléchargement du projet GetStartedWithData

### Obtention de l'exemple de code

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][1]]

### Vérification de la version du Kit de développement logiciel (SDK) Android

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

### Inspection et exécution de l'exemple de code

[WACOM.INCLUDE [mobile-services-android-run-sample-code][]]

## <a name="update-app"></a><span class="short-header">Mise à jour de l'application</span>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[WACOM.INCLUDE [mobile-services-android-getting-started-with-data][]]

## <a name="test-app"></a><span class="short-header">Test de l'application</span>Test de l'application avec le service mobile publié

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, vous pouvez la tester avec Mobile Services à l'aide de l'émulateur Android ou d'un téléphone Android.

1.  Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet.

    Cela permet d'exécuter votre application, créée avec le Kit de développement logiciel (SDK) Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

2.  Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

    Vous pouvez redémarrer l'application pour vous assurer que les modifications ont été conservées dans la base de données sous Azure. Vous pouvez également examiner la base de données au moyen du portail de gestion Azure : les deux prochaines étapes vous permettent de consulter les modifications dans votre base de données.

3.  Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![][]

4.  Dans le portail de gestion, exécutez une requête pour afficher les modifications effectuées par l'application Windows Store. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Cela conclut le didacticiel **Prise en main des données** pour Android.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Android pour utiliser les données dans Mobile Services.

<!--Next, consider completing one of the following tutorials that is based on the GetStartedWithData app that you created in this tutorial:  * [Validate and modify data with scripts]   <br/>Learn more about using server scripts in Mobile Services to validate and change data sent from your app.  * [Refine queries with paging]   <br/>Learn how to use paging in queries to control the amount of data handled in a single request.  Once you have completed the data series, try -->

Essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification][]
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][]
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET][]
    En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Windows Store C\#]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Windows Store C#"
  [Windows Store JavaScript]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Windows Store JavaScript"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
  [Android]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
  [Serveur principal .NET]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ ".NET backend"
  [JavaScript backend]: /en-us/develop/mobile/tutorials/get-started-with-data-android/ "JavaScript backend"
  [version principale JavaScript]: /en-us/develop/mobile/tutorials/get-started-with-data-android
  [Création d'un service mobile]: #create-service
  [Téléchargement du service en local]: #download-the-service-locally
  [Test du service mobile]: #test-the-service
  [Publication du service mobile sur Azure]: #publish-mobile-service
  [Téléchargement du projet GetStartedWithData]: #download-app
  [Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données]: #update-app
  [Test de l'application avec le service mobile publié]: #test-app
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [1]: ../includes/download-android-sample-code.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [mobile-services-android-run-sample-code]: ../includes/mobile-services-android-run-sample-code.md
  [mobile-services-android-getting-started-with-data]: ../includes/mobile-services-android-getting-started-with-data.md
  []: ./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png
  [Prise en main de l'authentification]: /en-us/develop/mobile/tutorials/get-started-with-users-android
  [Prise en main des notifications Push]: /en-us/develop/mobile/tutorials/get-started-with-push-android
  [Guide de fonctionnement Mobile Services .NET]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
