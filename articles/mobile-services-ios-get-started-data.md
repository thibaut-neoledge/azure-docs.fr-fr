<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/19/2014" ms.author="glenga" />

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application iOS. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

<div class="dev-callout"><b>Remarque</b>
<p>Ce didacticiel vise &agrave; mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de r&eacute;cup&eacute;rer des donn&eacute;es &agrave; partir d'une application iOS &agrave; l'aide d'Azure. &Agrave; cet effet, il vous guide tout au long des nombreuses &eacute;tapes ex&eacute;cut&eacute;es automatiquement dans le d&eacute;marrage rapide Mobile Services. Si vous n'avez aucune exp&eacute;rience de Mobile Services, nous vous invitons &agrave; suivre d'abord le didacticiel <a href="/fr-fr/develop/mobile/tutorials/get-started-ios">Prise en main de Mobile Services</a>.</p>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement du projet d'application iOS][Téléchargement du projet d'application iOS]
2.  [Création du service mobile][Création du service mobile]
3.  [Ajout d'une table de données pour le stockage][Ajout d'une table de données pour le stockage]
4.  [Mise à jour de l'application pour utiliser Mobile Services][Mise à jour de l'application pour utiliser Mobile Services]
5.  [Test de l'application avec Mobile Services][Test de l'application avec Mobile Services]

Ce didacticiel requiert les éléments suivants :

-   [Kit de développement logiciel (SDK) Mobile Services pour iOS][Kit de développement logiciel (SDK) Mobile Services pour iOS], [XCode 4.5][XCode 4.5] et iOS 5.0 ou versions ultérieures.
-   Un compte Microsoft Azure Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].
    </p>
    </div>

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application GetStartedWithData][application GetStartedWithData], qui est une application Android. L'interface utilisateur de cette application est identique à l'application générée par le démarrage IOS de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez l'[exemple d'application][application GetStartedWithData] GetStartedWithData.

2.  Dans Xcode, ouvrez le projet téléchargé et étudiez le fichier TodoService.m.

    Huit commentaires **// TODO** indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

3.  Appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

    ![][0]

    Le texte enregistré est affiché dans la liste ci-dessous.

## <a name="create-service"></a>Création d'un service mobile dans portail de gestion

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Ajout d'une table au service mobile

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

## <a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[WACOM.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

## <a name="test-app"></a>Test de l'application sur base de votre nouveau service mobile

1.  Dans Xcode, sélectionnez un émulateur vers lequel effectuer le déploiement (via iPhone ou iPad), appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

    Cela permet d'exécuter votre client Azure Mobile Services, généré à partir du Kit de développement logiciel (SDK) pour iOS, qui effectue des requêtes sur les éléments auprès de votre service mobile.

2.  Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

    Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion][portail de gestion], cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    ![][1]

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

Cela conclut le didacticiel **Prise en main des données** pour iOS.

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

  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Téléchargement du projet d'application iOS]: #download-app
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [Version d'évaluation gratuite d'Azure]: http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F
  [application GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622
  [0]: ./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png
  [mobile-services-create-new-service-data]: ../includes/mobile-services-create-new-service-data.md
  [mobile-services-create-new-service-data-2]: ../includes/mobile-services-create-new-service-data-2.md
  [mobile-services-ios-enable-mobile-service-access]: ../includes/mobile-services-ios-enable-mobile-service-access.md
  [portail de gestion]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-ios
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios
