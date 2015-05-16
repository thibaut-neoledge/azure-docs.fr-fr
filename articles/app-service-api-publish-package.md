<properties 
	pageTitle="Créer un package d&#39;application API" 
	description="Découvrir comment publier un package d&#39;application API sur Azure Marketplace" 
	services="app-service\api" 
	documentationCenter=".net"
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="guayan"/>

# Publier un package d'application API sur Azure Marketplace

## Vue d'ensemble

Cet article explique comment publier un package d'application API sur [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).

- Pour savoir comment créer une application API, consultez [Créer une application API à l'aide de Visual Studio](app-service-dotnet-create-api-app.md).
- Pour découvrir comment créer un package d'application API, consultez [Créer un package d'application API](app-service-api-create-package).

## Flux de publication général

Voici le flux de publication général

1. Créez un package NuGet pour votre application API en suivant les instructions du didacticiel [Créer un package d'application API](app-service-api-create-package).
2. Publiez-le dans une galerie supportée par NuGet à l'adresse https://apiapps.nuget.org.
3. Il sera ensuite synchronisé automatiquement avec [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/).
4. Accédez à [Azure Marketplace](http://azure.microsoft.com/marketplace/api-apps/) et au [portail Azure en version préliminaire](https://portal.azure.com) pour vérifier l'expérience de bout en bout.

## Publier dans une galerie supportée par NuGet

1. Accédez à https://apiapps.nuget.org

    ![Page d'accueil de la galerie supportée par NuGet](./media/app-service-api-publish-package/nuget-homepage.png)

2. Cliquez sur **Sign in** et connectez-vous à votre compte Azure.
3. Cliquez sur l'onglet **Applications API**, cliquez sur **Télécharger une application API**, puis téléchargez votre package d'application API.

    ![Page de téléchargement du package de la galerie supportée par NuGet](./media/app-service-api-publish-package/nuget-upload-package-page.png)

4. Pour **Espace de noms**, la liste déroulante affiche tous les domaines vérifiés des clients Azure Active Directory de votre compte Azure. Assurez-vous d'en sélectionner un qui correspond à la propriété namespace du fichier apiapp.json de votre package d'application API. Ce point est vérifié pour garantir que les éditeurs demandent des espaces de noms valides pour leurs packages d'application API.
5. Assurez-vous que l'option **Retirer cette application API de la liste** est désactivée.
6. Cliquez sur **Publier**.
7. En cas d'erreur de validation, résolvez-la et effectuez à nouveau le téléchargement.

## Visualiser votre package d'application API dans Azure Marketplace

Au bout de quelques minutes, votre package d'application API sera synchronisé avec Azure Marketplace. Vous pouvez accéder [ici](http://azure.microsoft.com/marketplace/api-apps/) pour en vérifier le titre, la description, l'icône, etc. Si vous devez modifier quelque chose, changez-le simplement dans votre package d'application API et publiez-le à nouveau.

![Page Application API d'Azure Marketplace](./media/app-service-api-publish-package/acom-marketplace-apiapp-page.png)

## Déployer votre package d'application API dans le portail Azure en version préliminaire

Vous pouvez également vous connecter au [portail Azure en version préliminaire](https://portal.azure.com) en utilisant votre compte Azure \(il peut s'agir d'un compte différent de celui que vous utilisez pour publier le package d'application API\). Vous pouvez vérifier ici l'expérience de création du portail Azure en version préliminaire pour votre package d'application API. Si vous devez modifier quelque chose, changez-le simplement dans votre package d'application API et publiez-le à nouveau.

Pour des détails sur la façon de déployer un package d'application API dans le portail Azure, vous pouvez consulter un exemple de déploiement DropboxConnector [ici](app-service-api-connnect-your-app-to-saas-connector.md).

<!--HONumber=52-->
