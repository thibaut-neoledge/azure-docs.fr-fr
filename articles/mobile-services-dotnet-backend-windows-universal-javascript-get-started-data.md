<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique explique comment utiliser Azure Mobile Services en tant que source de données principale pour une application Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer au cours de ce didacticiel est un service mobile principal .NET. Vous pouvez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile, et vous pouvez exécuter et déboguer votre service mobile sur votre ordinateur local. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la version principale JavaScript de cette rubrique.

> [WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour connecter un nouveau service mobile à une application Windows universelle. Si vous ne pouvez pas procéder à la mise à niveau Visual Studio Professional 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application Windows Store, consultez [cette version][cette version] de la rubrique.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][Téléchargement d'un projet d'application Windows Store]
2.  [Création d'un service mobile à partir de Visual Studio][Création d'un service mobile à partir de Visual Studio]
3.  [Test local d'un projet de service mobile][Test local d'un projet de service mobile]
4.  [Mise à jour de l'application pour utiliser le service mobile][Mise à jour de l'application pour utiliser le service mobile]
5.  [Publication du service mobile sur Azure][Publication du service mobile sur Azure]
6.  [Test de l'application sur le service hébergé dans Azure][Test de l'application sur le service hébergé dans Azure]
7.  [Affichage des données stockées dans la base de données SQL][Affichage des données stockées dans la base de données SQL]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Une version d'évaluation gratuite est disponible.

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project](../includes/mobile-services-windows-universal-javascript-download-project.md)]

## <a name="create-service"></a>Création d'un service mobile à partir de Visual Studio

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

1.  Dans l'Explorateur de solutions, accédez au sous-dossier **services\\mobileService\\scripts**, ouvrez le fichier de script service.js, et notez la nouvelle variable globale, similaire à celle présentée dans l'exemple suivant :

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    Ce code permet d'accéder à votre nouveau service mobile dans votre application en utilisant une variable globale. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Dans la mesure où une référence à ce script a été ajoutée dans le fichier default.html, cette variable est disponible pour tous les fichiers de script qui sont également référencés sur cette page.

2.  Ouvrez le fichier de projet default.html, identifiez la référence au nouveau fichier de script service.js et assurez-vous que le chemin d'accès référencé se présente comme suit :

        <script src="/services/mobileServices/scripts/todolist.js">

    Visual Studio comporte actuellement un bogue qui génère un nom de dossier incorrect dans le chemin d'accès.

3.  Cliquez avec le bouton droit de la souris sur le projet d'application Windows Phone, cliquez sur **Ajouter**, puis sur **Service connecté...**, sélectionnez le service mobile que vous venez de créer, puis cliquez sur **OK**.

    Le même nouveau fichier de code est ajouté au projet d'application Windows Phone Store. Assurez-vous de corriger également le chemin d'accès référencé ajouté au fichier default.html.

Les deux applications Windows Store et Windows Phone Store sont maintenant connectées au nouveau service mobile. L'étape suivante consiste à tester le nouveau projet de service mobile.

## <a name="test-the-service-locally"></a>Test local d'un projet de service mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## <a name="update-app"></a>Mise à jour de l'application pour utiliser le service mobile

Dans cette section, vous allez mettre à jour l'application Windows universelle pour utiliser le service mobile en tant que service principal de l'application. Vous devrez uniquement modifier le fichier de projet default.js dans le dossier de projet GetStartedWithData.Shared.

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app](../includes/mobile-services-windows-javascript-update-data-app.md)]

## <a name="publish-mobile-service"></a>Publication du service mobile sur Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Test du service mobile hébergé dans Azure

Nous pouvons maintenant tester les deux versions de l'application Windows universelle sur le service mobile hébergé dans Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

## <a name="view-stored-data"></a>Affichage des données stockées dans la base de données SQL

[WACOM.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'un projet d'application Windows universelle pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][Validation et modification des données avec des scripts]
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][Affinage des requêtes à la pagination]
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][Prise en main des notifications Push]
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET][Guide de fonctionnement Mobile Services .NET]
    En savoir plus sur l'utilisation de Mobile Services avec HTML et JavaScript.


  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [cette version]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Création d'un service mobile à partir de Visual Studio]: #create-service
  [Test local d'un projet de service mobile]: #test-the-service-locally
  [Mise à jour de l'application pour utiliser le service mobile]: #update-app
  [Publication du service mobile sur Azure]: #publish-mobile-service
  [Test de l'application sur le service hébergé dans Azure]: #test-azure-hosted
  [Affichage des données stockées dans la base de données SQL]: #view-stored-data
  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-javascript-download-project.md
  [mobile-services-dotnet-backend-create-new-service-vs2013]: ../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md
  [mobile-services-dotnet-backend-test-local-service-api-documentation]: ../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [mobile-services-dotnet-backend-view-sql-data]: ../includes/mobile-services-dotnet-backend-view-sql-data.md
  [Validation et modification des données avec des scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Affinage des requêtes à la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-js
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library/
