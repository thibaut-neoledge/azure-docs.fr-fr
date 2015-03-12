<properties 
	pageTitle="Prise en main des données (iOS) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour tirer profit des données de votre application iOS." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Ajout de Mobile Services à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique montre comment utiliser Azure Mobile Services pour tirer parti des données dans une application iOS. Dans ce didacticiel, vous allez télécharger une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer dans ce didacticiel prendra en charge le runtime .NET dans le service mobile. Vous pourrez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version relative au service principal JavaScript] de cette rubrique.

>[AZURE.NOTE] Ce didacticiel vise à mieux vous faire comprendre en quoi Mobile Services vous permet de stocker et de récupérer des données à partir d'une application iOS à l'aide d'Azure. À cet effet, il vous guide tout au long des nombreuses étapes exécutées automatiquement dans le démarrage rapide Mobile Services. Si vous n'avez aucune expérience de Mobile Services, nous vous invitons à suivre d'abord le didacticiel [Prise en main de Mobile Services].
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Téléchargement du projet d'application iOS]
2. [Création du service mobile]
3. [Téléchargement du service en local]
4. [Test du service mobile]
5. [Publication du service mobile sur Azure]
7. [Mise à jour de l'application pour utiliser Mobile Services]
8. [Test de l'application par rapport à Mobile Services]

Ce didacticiel requiert les éléments suivants :

+ [Kit de développement logiciel (SDK) Mobile Services pour iOS], [XCode 4.5][Installation de Xcode] et iOS 5.0 ou versions ultérieures.
+ Visual Studio 2013 (vous pouvez vous procurer [Visual Studio Express pour le web](http://go.microsoft.com/p/?linkid=9832232) gratuitement).
+ Un compte Microsoft Azure Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A756A2826&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Ffr-fr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F" target="_blank">Version d'évaluation gratuite Azure</a>.

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur [l'application GetStartedWithData][GitHub], qui est une application iOS. L'interface utilisateur de cette application est identique à l'application générée par le démarrage IOS de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1. Téléchargez [l'exemple d'application][GitHub] GetStartedWithData.

2. Dans Xcode, ouvrez le projet téléchargé et étudiez le fichier TodoService.m.

   	Huit commentaires **// TODO** indiquent les étapes à suivre pour que cette application fonctionne avec votre service mobile.

3. Appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

4. Dans l'application, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	![][0]  

   	Le texte enregistré est affiché dans la liste ci-dessous.

##<a name="create-service"></a>Création d'un service mobile dans le portail de gestion

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Téléchargement du service sur votre ordinateur local

[AZURE.INCLUDE [mobile-services-ios-download-service-locally](../includes/mobile-services-ios-download-service-locally.md)]

##<a name="test-the-service"></a>Test du service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-mobile-service"></a>Publication du service mobile sur Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Test de l'application sur la base de votre nouveau service mobile

1. Dans Xcode, sélectionnez un émulateur vers lequel effectuer le déploiement (via iPhone ou iPad), appuyez sur le bouton **Exécuter** (ou Cmd+R) pour régénérer le projet et démarrer l'application.

   	Cela permet d'exécuter votre client Azure Mobile Services, généré à partir du Kit de développement logiciel (SDK) pour iOS, qui effectue des requêtes sur les éléments auprès de votre service mobile.

2. Comme précédemment, tapez du texte dans la zone de texte, puis cliquez sur le bouton **+**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile. Chaque nouvel élément todoItem est stocké et mis à jour dans la base de données SQL que vous avez configurée précédemment pour votre service mobile dans le portail de gestion Azure.

3. Arrêtez et redémarrez l'application pour vous assurer que les modifications ont été conservées dans la base de données dans Azure.

	Vous pouvez également examiner la base de données à l'aide du portail de gestion Azure ou de l'Explorateur d'objets SQL Server de Visual Studio. Au cours des deux prochaines étapes, vous allez utiliser le [portail de gestion Azure] pour consulter les modifications dans votre base de données.

4. Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![][17]

5. Dans le portail de gestion, exécutez une requête pour afficher les modifications apportées par l'application. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de  `todolist`.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

Vous voici parvenu à la fin du didacticiel **Prise en main des données**.

##<a name="next-steps"></a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application iOS pour utiliser les données dans Mobile Services.

Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

* [Validation et modification de données à l'aide de scripts]
  <br/>Obtenez plus d'informations sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

* [Affinage des requêtes au moyen de la pagination]
  <br/>Apprenez à utiliser la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez ces autres didacticiels iOS :

* [Prise en main de l'authentification]
	<br/>Découvrez comment authentifier les utilisateurs de votre application.

* [Prise en main des notifications Push]
  <br/>Apprenez à envoyer une notification Push très basique à votre application avec Mobile Services.

<!-- Anchors. -->
[Téléchargement du projet d'application iOS]: #download-app
[Création du service mobile]: #create-service
[Ajout d'une table de données pour le stockage]: #add-table
[Mise à jour de l'application pour utiliser Mobile Services]: #update-app
[Test de l'application par rapport à Mobile Services]: #test-app
[Étapes suivantes]:#next-steps
[Téléchargement du service en local]: #download-the-service-locally
[Test du service mobile]: #test-the-service
[Publication du service mobile sur Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->
[Validation et modification de données à l'aide de scripts]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-ios
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-ios
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-ios
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios
[Version principale JavaScript]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios

[Portail de gestion Azure]: https://manage.windowsazure.com/
[Portail de gestion]: https://manage.windowsazure.com/
[Installation de Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Kit de développement logiciel (SDK) Mobile Services pour iOS]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkId=268622
[Référentiel GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268784


<!--HONumber=42-->
