<properties pageTitle="Prise en main des données (Windows Store) | Centre de développement mobile" description="Découvrez comment utiliser Mobile Services pour tirer profit des données de votre application Windows Store." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc"/>

# Ajout de Mobile Services à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique explique comment utiliser Azure Mobile Services en tant que source de données principale pour une application Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer au cours de ce didacticiel est un service mobile principal .NET. Vous pouvez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile, et vous pouvez exécuter et déboguer votre service mobile sur votre ordinateur local. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la version principale JavaScript de cette rubrique.

>[AZURE.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour connecter un nouveau service mobile à une application Windows universelle. La même procédure peut être utilisée pour connecter un service mobile à une application Windows Store ou Windows Phone Store 8.1. Pour connecter un service mobile à une application Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consultez la page [Prise en main de données pour Windows Phone](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data).

> Si vous ne pouvez pas procéder à la mise à niveau Visual Studio Professional 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application Windows Store, consultez [cette version](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data) de la rubrique.

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement d'un projet d'application Windows Store] 
2. [Création d'un service mobile à partir de Visual Studio]
3. [Test local d'un projet de service mobile]
4. [Mise à jour de l'application pour utiliser le service mobile]
5. [Publication du service mobile sur Azure]
6. [Test de l'application sur le service hébergé dans Azure]
7. [Affichage des données stockées dans la base de données SQL]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* un compte Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite Azure](http://azure.microsoft.com/fr-fr/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-universal-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 ou une version ultérieure). <br/>Une version d'évaluation gratuite est disponible. 

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-download-project](../includes/mobile-services-windows-universal-dotnet-download-project.md)]

##<a name="create-service"></a>Création d'un service mobile à partir de Visual Studio

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service-vs2013](../includes/mobile-services-dotnet-backend-create-new-service-vs2013.md)]

<ol start="7"><li><p>Dans l'Explorateur de solutions, ouvrez le fichier de code App.xaml.cs dans le dossier de projet GetStartedWithData.Shared, et voyez comment le nouveau champ statique a été ajouté à la classe <strong>App</strong> dans un bloc de compilation conditionnelle de l'application Windows Store, pour ressembler à l'exemple suivant :</p> 

		<pre><code>public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
		    todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        "https://todolist.azure-mobile.net/",
		        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		</code></pre>

	<p>Ce code fournit un accès à votre nouveau service mobile dans votre application à l'aide d'une instance de <a href="http://go.microsoft.com/fwlink/p/?LinkId=302030">MobileServiceClient class</a>. Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Ce champ statique est disponible pour toutes les pages de votre application.</p>
</li>
<li><p>Cliquez avec le bouton droit de la souris sur le projet d'application Windows Phone, cliquez sur <strong>Ajouter</strong>, puis sur <strong>Service connecté...</strong>, sélectionnez le service mobile que vous venez de créer, puis cliquez sur <strong>OK</strong>. </p>
<p>Le même code est ajouté au fichier App.xaml.cs partagé, mais dans un bloc de compilation conditionnelle de l'application Windows Phone.</p></li>
</ol>

Les deux applications Windows Store et Windows Phone Store sont maintenant connectées au nouveau service mobile. L'étape suivante consiste à tester le nouveau projet de service mobile.


##<a name="test-the-service-locally"></a>Test local d'un projet de service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]


##<a name="update-app"></a>Mise à jour de l'application pour utiliser le service mobile

Dans cette section, vous allez mettre à jour l'application Windows universelle pour utiliser le service mobile en tant que service principal de l'application. Vous devrez uniquement modifier le fichier de projet MainPage.xaml.cs dans le dossier de projet GetStartedWithData.Shared. 

[AZURE.INCLUDE [mobile-services-windows-dotnet-update-data-app](../includes/mobile-services-windows-dotnet-update-data-app.md)]


##<a name="publish-mobile-service"></a>Publication du service mobile sur Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Test du service mobile hébergé dans Azure

Nous pouvons maintenant tester les deux versions de l'application Windows universelle sur le service mobile hébergé dans Azure.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../includes/mobile-services-windows-universal-test-app.md)]

##<a name="view-stored-data"></a>Affichage des données stockées dans la base de données SQL

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]
 
Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'un projet d'application Windows universelle pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification de données à l'aide de scripts]
  <br/>En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET
  
<!-- Anchors. -->

[Téléchargement d'un projet d'application Windows Store] : #download-app
[Création d'un service mobile à partir de Visual Studio]: #create-service
[Test local d'un projet de service mobile]: #test-the-service-locally
[Mise à jour de l'application pour utiliser le service mobile]: #update-app
[Test de l'application Android sur le service hébergé localement]: #test-locally-hosted
[Publication du service mobile sur Azure]: #publish-mobile-service
[Test de l'application sur le service hébergé dans Azure]: #test-azure-hosted
[Affichage des données stockées dans la base de données SQL]: #view-stored-data
[Étapes suivantes]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Prise en main de la synchronisation des données hors connexion]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Kit de développement logiciel (SDK) Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Site d'exemples de code développeur]:  http://go.microsoft.com/fwlink/p/?LinkID=510826
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!--HONumber=42-->
