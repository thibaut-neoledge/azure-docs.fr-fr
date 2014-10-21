<properties linkid="develop-mobile-tutorials-get-started-with-data-js-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store JavaScript app." metaCanonical="https://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data][]]

Cette rubrique explique comment utiliser Azure Mobile Services pour exploiter les données dans une application Windows universelle. Les solutions d'application Windows universelles incluent des projets pour Windows Store 8.1 et Windows Phone Store 8.1, et un projet partagé commun. Pour plus d'informations, consultez la page [Créer des applications Windows universelles qui ciblent Windows et Windows Phone][].

Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application Windows universelle qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

> [WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Express 2013 Update 3 pour Windows pour connecter un nouveau service mobile à une application Windows universelle. La même procédure peut être utilisée pour connecter un service mobile à une application Windows Store ou Windows Phone Store 8.1. Pour connecter un service mobile à une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, voir la section [Prise en main de données pour Windows Phone][].

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][]
2.  [Création du service mobile][]
3.  [Ajout d'une table de données pour le stockage][]
4.  [Mise à jour de l'application pour utiliser Mobile Services][]
5.  [Test de l'application avec Mobile Services][]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][].
-   [Visual Studio Express 2013 pour Windows][] (Update 2 ou une version ultérieure).

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-javascript-download-project][]]

## <a name="create-service"></a>Création d'un service mobile à partir de Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013][]]

1.  Dans l'Explorateur de solutions, accédez au sous-dossier **services\\mobileService\\scripts**, ouvrez le fichier de script service.js, et notez la nouvelle variable globale, similaire à celle présentée dans l'exemple suivant :

        var todolistClient = new WindowsAzure.MobileServiceClient(
                        "https://todolist.azure-mobile.net/",
                        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

    Ce code permet d'accéder à votre nouveau service mobile dans votre application en utilisant une variable globale. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Dans la mesure où une référence à ce script a été ajoutée dans le fichier default.html, cette variable est disponible pour tous les fichiers de script qui sont également référencés sur cette page.

2.  Ouvrez le fichier de projet default.html, identifiez la référence au nouveau fichier de script service.js et assurez-vous que le chemin d'accès référencé se présente comme suit :

        <script src="/services/mobileServices/scripts/todolist.js">

    Visual Studio comporte actuellement un bogue qui génère un nom de dossier incorrect dans le chemin d'accès.

3.  Cliquez avec le bouton droit de la souris sur le projet d'application Windows Phone, cliquez sur **Ajouter**, puis sur **Service connecté...**, sélectionnez le service mobile que vous venez de créer, puis cliquez sur **OK**.

    Le même nouveau fichier de code est ajouté au projet d'application Windows Phone Store. Assurez-vous de corriger également le chemin d'accès référencé ajouté au fichier default.html.

## <a name="add-table"></a>Ajout d'une nouvelle table au service mobile

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013][]]

> [WACOM.NOTE]Des tables sont créées avec les colonnes Id, \_\_createdAt, \_\_updatedAt et \_\_version. Lorsqu'un schéma dynamique est activé, Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet JSON dans la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique][].

# <a name="update-app"></a>Mise à jour de l'application pour utiliser le service mobile

[WACOM.INCLUDE [mobile-services-windows-javascript-update-data-app][]]

## <a name="test-azure-hosted"></a>Test du service mobile hébergé dans Azure

Nous pouvons maintenant tester les deux versions de l'application Windows universelle sur le service mobile hébergé dans Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app][]]

1.  Dans le [portail de gestion][], cliquez sur **Mobile Services**, puis sur le service mobile.

    <p>
2.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

    La table **TodoItem** contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.

![][]

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows universelle pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts][]
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination][]
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification][]
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push][]
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services HTML/JavaScript][]
     
    En savoir plus sur l'utilisation de Mobile Services avec HTML et JavaScript


  [mobile-services-selector-get-started-data]: ../includes/mobile-services-selector-get-started-data.md
  [Créer des applications Windows universelles qui ciblent Windows et Windows Phone]: http://msdn.microsoft.com/en-us/library/windows/apps/xaml/dn609832.aspx
  [Prise en main de données pour Windows Phone]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Création du service mobile]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser Mobile Services]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-javascript-get-started-data%2F
  [Visual Studio Express 2013 pour Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [mobile-services-windows-universal-javascript-download-project]: ../includes/mobile-services-windows-universal-dotnet-download-project.md
  [mobile-services-create-new-service-vs2013]: ../includes/mobile-services-create-new-service-vs2013.md
  [mobile-services-create-new-table-vs2013]: ../includes/mobile-services-create-new-table-vs2013.md
  [Schéma dynamique]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193175.aspx
  [mobile-services-windows-javascript-update-data-app]: ../includes/mobile-services-windows-javascript-update-data-app.md
  [mobile-services-windows-universal-test-app]: ../includes/mobile-services-windows-universal-test-app.md
  [portail de gestion]: https://manage.windowsazure.com/
  []: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts/
  [Affinage des requêtes à la pagination]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/
  [Guide de fonctionnement Mobile Services HTML/JavaScript]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library/
