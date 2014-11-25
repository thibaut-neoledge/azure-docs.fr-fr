<properties linkid="mobile-services-javascript-backend-windows-universal-dotnet-get-started-data" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Universal) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your universal Windows app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Prise en main des données dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique explique comment utiliser Azure Mobile Services pour exploiter les données dans une application Windows universelle. Les solutions d'application Windows universelles incluent des projets pour Windows Store 8.1 et Windows Phone Store 8.1, et un projet partagé commun. Pour plus d'informations, consultez la page [Créer des applications Windows universelles qui ciblent Windows et Windows Phone][Créer des applications Windows universelles qui ciblent Windows et Windows Phone].

Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application Windows universelle qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications apportées aux données lors de l'exécution de l'application.

> [WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour connecter un nouveau service mobile à une application Windows universelle. La même procédure peut être utilisée pour connecter un service mobile à une application Windows Store ou Windows Phone Store 8.1. Pour connecter un service mobile à une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, voir la section [Prise en main de données pour Windows Phone][Prise en main de données pour Windows Phone].

> Si vous ne pouvez pas procéder à la mise à niveau Visual Studio Professional 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application Windows Store, consultez [cette version][cette version] de la rubrique.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store][Téléchargement d'un projet d'application Windows Store]
2.  [Création du service mobile à partir de Visual Studio][Création du service mobile à partir de Visual Studio]
3.  [Ajout d'une table de données pour le stockage][Ajout d'une table de données pour le stockage]
4.  [Mise à jour de l'application pour utiliser le service mobile][Mise à jour de l'application pour utiliser le service mobile]
5.  [Test de l'application avec Mobile Services][Test de l'application avec Mobile Services]
6.  [Affichage des données téléchargées dans le portail de gestion Azure][Affichage des données téléchargées dans le portail de gestion Azure]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][Version d'évaluation gratuite d'Azure].
-   [Visual Studio Express 2013 pour Windows][Visual Studio Express 2013 pour Windows] (Update 2 ou une version ultérieure).

## <a name="download-app"></a>Téléchargement du projet GetStartedWithData

[WACOM.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

## <a name="create-service"></a>Création d'un service mobile à partir de Visual Studio

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

<ol start="8"><li><p>Dans l'Explorateur de solutions, ouvrez le fichier de code App.xaml.cs dans le dossier de projet GetStartedWithData.Shared, et voyez comment le nouveau champ statique a été ajouté à la classe <b>App</b> dans un bloc de compilation conditionnelle de l'application Windows Store, pour ressembler à l'exemple suivant :</p>

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

    <p>Ce code fournit un accès à votre nouveau service mobile dans votre application à l'aide d'une instance de la <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient class</a>. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Ce champ statique est disponible pour toutes les pages de votre application.</p>
</li>

<li><p>Cliquez avec le bouton droit de la souris sur le projet d'application Windows Phone, cliquez sur <b>Ajouter</b>, puis sur <b>Service connecté...</b>, sélectionnez le service mobile que vous venez de créer, puis cliquez sur <b>OK</b>.</p>
<p>Le même code est ajouté au fichier App.xaml.cs partagé, mais dans un bloc de compilation conditionnelle de l'application Windows Phone.</p></li>
</ol>
Les deux applications Windows Store et Windows Phone Store sont maintenant connectées au nouveau service mobile. L'étape suivante consiste à créer une table TodoItem dans le service mobile.

## <a name="add-table"></a>Ajout d'une nouvelle table au service mobile

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

> [WACOM.NOTE]Des tables sont créées avec les colonnes Id, \_\_createdAt, \_\_updatedAt et \_\_version. Lorsqu'un schéma dynamique est activé, Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet JSON dans la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique][Schéma dynamique].

# <a name="update-app"></a>Mise à jour de l'application pour utiliser le service mobile

[WACOM.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]

## <a name="test-azure-hosted"></a>Test du service mobile hébergé dans Azure

Nous pouvons maintenant tester les deux versions de l'application Windows universelle sur le service mobile hébergé dans Azure.

[WACOM.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]
<ol start="4">
<li><p>Dans le <a href="https://manage.windowsazure.com/" target="_blank">portail de gestion</a>, cliquez sur <b>Mobile Services</b>, puis sur le service mobile.</p></li>


<li><p>Cliquez sur l'onglet <b>Données</b>, puis sur <b>Parcourir</b>.</p>

    <p>La table <b>TodoItem</b> contient à présent des données, dont les valeurs d'ID ont été générées par Mobile Services, et les colonnes ont été automatiquement ajoutées à la table de manière à correspondre à la classe TodoItem au sein de l'application.</p></li>
</ol>

![][0]

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Windows universelle pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

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
    En savoir plus sur l'utilisation de Mobile Services avec .NET.


  [Créer des applications Windows universelles qui ciblent Windows et Windows Phone]: http://msdn.microsoft.com/fr-fr/library/windows/apps/xaml/dn609832.aspx
  [Prise en main de données pour Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data
  [cette version]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Téléchargement d'un projet d'application Windows Store]: #download-app
  [Création du service mobile à partir de Visual Studio]: #create-service
  [Ajout d'une table de données pour le stockage]: #add-table
  [Mise à jour de l'application pour utiliser le service mobile]: #update-app
  [Test de l'application avec Mobile Services]: #test-app
  [Affichage des données téléchargées dans le portail de gestion Azure]: #view-data
  [Version d'évaluation gratuite d'Azure]: http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-universal-dotnet-get-started-data%2F
  [Visual Studio Express 2013 pour Windows]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [Schéma dynamique]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj193175.aspx
  [0]: ./media/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data/mobile-todoitem-data-browse.png
  [Validation et modification des données avec des scripts]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/
  [Affinage des requêtes à la pagination]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data/
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
