<properties
	pageTitle="Ajout de Mobile Services à une application existante dans iOS"
	description="Découvrez comment utiliser Mobile Services pour tirer parti des données dans votre application iOS."
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
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# Ajout de Mobile Services à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

Dans ce didacticiel, vous téléchargez une application existante qui stocke des données en mémoire et la modifiez pour utiliser un service mobile Azure.

Avant de commencer ce didacticiel, vous devez avoir effectué le didacticiel [Démarrage rapide]. Vous allez réutiliser le service mobile que vous créez dans le démarrage rapide.


##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

Ce didacticiel est basé sur l'[application iOS GetStartedWithData]. L'application est identique au [Démarrage rapide], à la différence que les éléments ajoutés sont stockés en mémoire.

Téléchargez l'[application iOS GetStartedWithData]. Dans Xcode, ouvrez le projet et étudiez **TodoService.m**. Il existe huit commentaires **// TODO** qui indiquent les étapes à suivre pour que cette application fonctionne.

##<a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[AZURE.INCLUDE [mobile-services-ios-enable-mobile-service-access](../../includes/mobile-services-ios-enable-mobile-service-access.md)]

##<a name="test-app"></a>Test de l'application

1. Dans Xcode, cliquez sur **Exécuter** pour démarrer l'application. Ajoutez des éléments à la liste de tâches en tapant du texte et en cliquant sur **+**.

2. Vérifiez que les modifications ont été conservées dans la base de données sous Azure. Examinez la base de données à l'aide du portail de gestion Azure ou de l'Explorateur d'objets SQL Server de Visual Studio.

3. Pour examiner la base de données à l'aide du portail, dans la page Tableau de bord de votre service mobile, cliquez sur le nom de la base de données, cliquez sur « Gérer » pour gérer la base de données, puis connectez-vous. Exécutez la requête suivante, en utilisant le nom de votre service mobile à la place de `todolist`.

```
        SELECT * FROM [todolist].[todoitems]
```

<!-- Anchors. -->
[Download the iOS app project]: #download-app
[Create the mobile service]: #create-service
[Add a data table for storage]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]: #next-steps
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Publish the mobile service to Azure]: #publish-mobile-service


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-quickstart-startup-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-dotnet-backend-ios-get-started-data/mobile-todoitem-data-browse.png
[17]: ./media/mobile-services-dotnet-backend-ios-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-ios-get-started-data/sql-azure-query.png


<!-- URLs. -->

[Validate and modify data with scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[JavaScript backend version]: /develop/mobile/tutorials/get-started-with-data-ios


[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=268622
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784

[Démarrage rapide]: mobile-services-dotnet-backend-ios-get-started.md
[application iOS GetStartedWithData]: http://go.microsoft.com/fwlink/p/?LinkId=268622

<!---HONumber=July15_HO2-->