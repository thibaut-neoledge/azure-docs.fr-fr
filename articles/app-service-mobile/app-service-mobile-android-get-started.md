<properties
    pageTitle="Créer une application Android sur Azure App Service Mobile Apps | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser des backends d’applications mobiles Azure pour le développement Android"
    services="app-service\mobile"
    documentationCenter="android"
    authors="yuaxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

#Créer une application Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Vue d'ensemble

Ce didacticiel montre comment ajouter un backend cloud à une application mobile Android à l’aide d’un backend d’application mobile Azure. Vous allez créer un backend d’application mobile et une simple application Android _Todo list_ qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Android sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## Composants requis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* [Outils de développement Android](https://developer.android.com/sdk/index.html), qui incluent l’environnement de développement intégré Android Studio et la dernière plateforme Android.
* Kit de développement logiciel (SDK) Azure Mobile Android, qui est automatiquement inclus dans le projet de démarrage rapide que vous téléchargez.
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

## Créer un serveur principal d'applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurer le projet de serveur

[AZURE.INCLUDE [app-service-mobile-Configure-New-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Télécharger et exécuter l’application Android

[AZURE.INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]


<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_1005_2016-->