<properties
	pageTitle="Créer une application iOS sur Azure App Service Mobile Apps | Microsoft Azure"
	description="Suivez ce didacticiel pour commencer à utiliser des backends Azure Mobile App pour le développement iOS en Objective-C ou Swift"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="03/09/2016"
	ms.author="krisragh"/>

#Création d'une application iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Vue d'ensemble

Ce didacticiel montre comment utiliser un backend cloud ([Azure Mobile Apps](app-service-mobile-value-prop.md)) sur une application mobile iOS. Vous allez créer un backend mobile et utiliser une simple application iOS _Todo list_ qui stocke les données d’application dans Azure.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

* Un PC avec [Visual Studio Community 2013] ou version ultérieure.

* Un Mac avec Xcode v7.0 ou version ultérieure.

* L’[infrastructure Azure Mobile iOS](https://go.microsoft.com/fwLink/?LinkID=529823), qui est automatiquement incluse dans le cadre du projet de démarrage rapide que vous téléchargez.

## Créer un serveur principal d'applications mobiles Azure

Suivez ces étapes pour créer un serveur principal d’application mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Vous allez ensuite télécharger un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

## Télécharger le projet de serveur

1. Sur votre PC, accédez au [portail Azure]. Cliquez sur **Parcourir tout** > **Mobile Apps**, puis cliquez sur le serveur principal d’application mobile que vous venez de créer.

2. Dans le panneau Application mobile, cliquez sur **Paramètres** et sous **Application mobile**, cliquez sur **Démarrage rapide** > **iOS (Objective-C)**. Si vous préférez Swift, cliquez sur **Démarrage rapide** > **iOS (Swift)**.

## Configurer le projet de serveur

[AZURE.INCLUDE [app-service-mobile-Configure-New-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]


## Télécharger et exécuter l'application iOS

[AZURE.INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[portail Azure]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0316_2016-->