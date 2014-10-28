<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Prise en main des données dans Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title="iOS" class="current">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/" title="Android" class="current">Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/" title=".NET backend" class="current">.NET backend</a> | <a href="/fr-fr/develop/mobile/tutorials/get-started-with-data-ios/"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application iOS. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer dans ce didacticiel prendra en charge le runtime .NET dans le service mobile. Vous pourrez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version principale JavaScript][version principale JavaScript] de cette rubrique.

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel requiert Visual&nbsp;Studio&nbsp;2013.</p>
</div>

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel vise &agrave; mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de r&eacute;cup&eacute;rer des donn&eacute;es &agrave; partir d'une application iOS &agrave; l'aide d'Azure. &Agrave; cet effet, il vous guide tout au long des nombreuses &eacute;tapes ex&eacute;cut&eacute;es automatiquement dans le d&eacute;marrage rapide Mobile Services. Si vous n'avez aucune exp&eacute;rience de Mobile Services, nous vous invitons &agrave; suivre d'abord le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-ios">Prise en main de Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement du projet d'application iOS][Téléchargement du projet d'application iOS]
2.  [Création du service mobile][Création du service mobile]
3.  [Téléchargement du service en local][Téléchargement du service en local]
4.  [Test du service mobile][Test du service mobile]
5.  [Publication du service mobile sur Azure][Publication du service mobile sur Azure]
6.  [Mise à jour de l'application pour utiliser Mobile Services][Mise à jour de l'application pour utiliser Mobile Services]
7.  [Test de l'application avec Mobile Services][Test de l'application avec Mobile Services]

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Mobile Services pour iOS][Kit de développement logiciel (SDK) Mobile Services pour iOS], [XCode 4.5][XCode 4.5] et iOS 5.0 ou ultérieur.
-   Visual Studio 2013 (vous pouvez vous procurer [Visual Studio Express pour le web][Visual Studio Express pour le web] gratuitement).
-   Un compte Microsoft Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithData][application GetStartedWithData], qui est une application Android. L'interface utilisateur de cette application est identique à l'application générée par le démarrage IOS de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez l'[exemple d'application][application GetStartedWithData] GetStartedWithData.

2.  Dans Xcode, ouvrez le projet téléchargé et étudiez le fichier TodoService.m.

    Notez l'existence de huit commentaires **// TODO** indiquant les étapes à suivre pour que cette application fonctionne avec votre service mobile.

3.  Appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

    ![][]

    Le texte enregistré est affiché dans la liste ci-dessous.

## <a name="create-service"></a>Création d'un service mobile dans le portail de gestion

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service][mobile-services-dotnet-backend-create-new-service]]

## <a name="download-the-service-locally"></a>Téléchargement du service mobile sur votre ordinateur local

[WACOM.INCLUDE [mobile-services-ios-download-service-locally][mobile-services-ios-download-service-locally]]

## <a name="test-the-service"></a>Test du service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## <a name="publish-mobile-service"></a>Publication du service mobile sur Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## <a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access][mobile-services-ios-enable-mobile-service-access]]

## <a name="test-app"></a>Test de l'application avec votre nouveau service mobile

1.  Dans Xcode, sélectionnez un émulateur vers lequel effectuer le déploiement (via iPhone ou iPad), appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

    Cela permet d'exécuter votre client Azure Mobile Services, généré à partir du Kit de développement logiciel (SDK) pour iOS, qui interroge les éléments de votre service mobile.

2.  Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile. Chaque nouvel élément todoItem est stocké et mis à jour dans la base de données SQL que vous avez configurée précédemment pour votre service mobile dans le portail de gestion Azure.

3.  Arrêtez et redémarrez l'application pour vous assurer que les modifications ont été conservées dans la base de données dans Azure.

    Vous pouvez également examiner la base de données à l'aide du portail de gestion Azure ou de l'Explorateur d'objets SQL Server de Visual Studio. Au cours des deux prochaines étapes, vous allez utiliser le [portail de gestion Azure][portail de gestion Azure] pour consulter les modifications dans votre base de données.

4.  Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![][1]

5.  Dans le portail de gestion, exécutez une requête pour afficher les modifications apportées par l'application. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![][2]

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][Validation et modification des données avec des scripts]
    
	En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][Affinage des requêtes à la pagination]
    
	En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels iOS :

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    
	En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][Prise en main des notifications Push]
    
	En savoir plus sur l'envoi d'une notification Push très basique sur votre application avec Mobile Services.



  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/ "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/ "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/ "Android"
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/ ".NET backend"
  [JavaScript backend]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios/ "JavaScript backend"
  [version principale JavaScript]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
  [Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-ios
  [Téléchargement du projet d'application iOS]: #download-app
  [Création du service mobile]: #create-service
  [Téléchargement du service en local]: #download-the-service-locally
  [Test du service mobile]: #test-the-service
  [Publication du service mobile sur Azure]: #publish-mobile-service
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [Kit de développement logiciel (SDK) Mobile Services pour iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Visual Studio Express pour le web]: http://go.microsoft.com/p/?linkid=9832232
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [application GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  []: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
  [2]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-ios
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios
