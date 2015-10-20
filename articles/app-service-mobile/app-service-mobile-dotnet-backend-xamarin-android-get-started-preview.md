<properties
	pageTitle="Prise en main d'Azure Mobile Apps pour les applications Xamarin.Android"
	description="Suivez ce didacticiel pour commencer à utiliser Azure Mobile Apps pour le développement d'applications Xamarin Android."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor="" />

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/12/2015"
	ms.author="normesta" />

#Création d'une application Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../../includes/app-service-mobile-selector-get-started-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]
 
##Vue d'ensemble

Ce didacticiel vous montre comment ajouter un service principal cloud à une application Xamarin.Android en utilisant un serveur principal d’applications mobiles Azure. Vous allez créer un serveur principal d’applications mobiles et une simple application Xamarin.Android _Todo list_ qui stocke les données d’application dans Azure.

Voici une capture d'écran de l'application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Apps pour les applications Xamarin.Android.
 
##Conditions préalables

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n’avez pas de compte, vous pouvez vous inscrire pour obtenir une version d’évaluation Azure et jusqu’à 10 applications Mobile App gratuites que vous pourrez conserver après l’expiration de votre période d’évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
 
* [Visual Studio Community 2013] ou version ultérieure. Si vous installez Visual Studio Community 2013, installez [Xamarin] séparément. Vous pouvez installer les outils Xamarin quand vous installez Visual Studio 2015.
 
>[AZURE.NOTE]Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), où vous pouvez créer immédiatement une première application mobile temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.


## Créer un serveur principal d'applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Télécharger le projet de serveur

1. Sur votre PC, accédez au [portail Azure]. Cliquez sur **Parcourir tout** > **Applications mobiles**, puis cliquez sur le serveur principal d’applications mobiles que vous venez de créer.

2. Dans le panneau de l'application mobile, cliquez sur **Paramètres** et sous **Application mobile** cliquez sur **Démarrage rapide** > **Xamarin.Android**.
 
3. Sous **Télécharger et exécuter votre projet de serveur**, cliquez sur **Télécharger**. Extrayez les fichiers projet compressés sur votre ordinateur et ouvrez la solution dans Visual Studio.
 
## Tester localement votre projet de serveur principal

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publier le projet de serveur sur Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Télécharger et exécuter l'application Xamarin.Android

1. Sous **Télécharger et exécuter votre projet Xamarin.Android**, cliquez sur le bouton **Télécharger**.

  	Cette opération télécharge un projet qui contient une application cliente connectée à votre application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

	![][8]

	![][9]

2. Appuyez sur la touche **F5** pour générer le projet et démarrer l'application.

3. Dans l'application, tapez un texte explicite, comme _Suivre le didacticiel_, puis cliquez sur l'icône **Ajouter**.

	![][10]

	Ceci envoie une demande POST vers le nouveau backend d'application mobile hébergé dans Azure. Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le serveur principal d'applications mobiles et les données apparaissent dans la liste.

	> [AZURE.NOTE]Vous pouvez vérifier le code qui se trouve dans le fichier C# ToDoActivity.cs et permet d'accéder à votre backend d'application mobile pour exécuter une requête et insérer des données.

##Étapes suivantes

* [Ajouter l'authentification à votre application](app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md) <br/>Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d'identité.


<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview/mobile-quickstart-startup-android.png

<!-- URLs. -->
[portail Azure]: https://azure.portal.com/
[Xamarin]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin for Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409
 
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Oct15_HO3-->