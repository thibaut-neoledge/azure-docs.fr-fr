<properties
	pageTitle="Prise en main des backends d'applications mobiles pour les applications du Windows Store | Centre de développement mobile"
	description="Suivez ce didacticiel pour commencer à utiliser des backends d'applications mobiles Azure pour le développement d'applications Windows Store en C#, VB ou JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/24/2015"
	ms.author="chrande"/>


# <a name="getting-started"> </a>Création d'une application Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-preview](../includes/app-service-mobile-selector-get-started-preview.md)]

Ce didacticiel montre comment ajouter un backend cloud à une application Windows universelle à l'aide d'un backend d'applications mobiles Azure. Les solutions d'application Windows universelles incluent des projets pour Windows Store 8.1 et Windows Phone Store 8.1, et un projet partagé commun.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started-preview](../includes/app-service-mobile-windows-universal-get-started-preview.md)]

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>.

>[AZURE.NOTE]Si vous voulez prendre en main Azure App Service avant de créer un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), où vous pouvez créer immédiatement une première application mobile temporaire dans App Service. Aucune carte de crédit n'est requise ; vous ne prenez aucun engagement.

## <a name="create-new-service"> </a>Création d'un backend d'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-preview](../includes/app-service-mobile-dotnet-backend-create-new-service-preview.md)]

## Création d'une application Windows universelle

Une fois que vous avez créé votre backend d'application mobile, vous pouvez suivre un démarrage rapide facile dans le portail Azure pour créer une application ou modifier une application existante à connecter à votre backend d'application mobile.

Dans cette section, vous allez créer une application Windows universelle connectée à votre backend d'application mobile.

1. Dans le portail Azure, cliquez sur **Application mobile**, puis sur l'application mobile que vous venez de créer.

2. En haut du panneau, cliquez sur **Ajouter un client** et développez **Windows (C#)**.

   ![Étapes du démarrage rapide avec les applications mobiles](./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/windows-quickstart.png)

   Cette opération affiche les trois étapes faciles permettant de créer une application du Windows Store connectée à votre backend d'application mobile.

3. Si ce n'est déjà fait, téléchargez et installez <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> sur votre ordinateur local ou sur votre machine virtuelle.

4. Sous **Télécharger et exécuter votre application et votre service en local**, sélectionnez la langue de votre application Windows Store, puis cliquez sur **Télécharger**.

   Cette opération lance le téléchargement d'une solution contenant les projets du backend d'application mobile et de l'exemple d'application _To do list_ connecté à votre backend d'application mobile. Enregistrez le fichier projet compressé sur votre ordinateur local et notez l'emplacement où vous l'avez enregistré.

## Test de l'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service-preview](../includes/app-service-mobile-dotnet-backend-test-local-service-preview.md)]

## Publication de votre backend d'application mobile

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

## Exécution de l'application Windows

Maintenant que le backend d'application mobile est publié et que le client est connecté au backend d'application mobile distant hébergé dans Azure, nous pouvons exécuter l'application à l'aide d'Azure pour le stockage des éléments.

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-app-preview](../includes/app-service-mobile-windows-universal-test-app-preview.md)]

<!-- Anchors. -->

[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Define the mobile app backend instance]: #define-mobile-app-backend-instance
[Next Steps]: #next-steps

<!-- Images. -->



<!-- URLs. -->
[Get started with authentication]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/

<!--HONumber=52-->
