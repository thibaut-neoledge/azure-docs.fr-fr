<properties
    pageTitle="Créer une application Cordova sur Azure App Service Mobile Apps | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser des backends d’applications mobiles Azure pour le développement Apache Cordova"
    services="app-service\mobile"
    documentationCenter="javascript"
    authors="adrianhall"
    manager="ggailey777"
    editor=""
    tags=""
    keywords="cordova, javascript, mobile, client" />

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="hero-article"
    ms.date="02/09/2016"
    ms.author="adrianha"/>

#Créer une application Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Vue d’ensemble

Ce didacticiel montre comment ajouter un backend cloud à une application mobile Apache Cordova à l’aide d’un backend d’application mobile Azure. Vous allez créer un backend d’application mobile et une simple application Apache Cordova _Todo list_ qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Apache Cordova sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## Configuration requise

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un PC avec [Visual Studio Community 2015] ou version ultérieure.
* [Visual Studio Tools pour Apache Cordova].
* Un [compte Azure actif](https://azure.microsoft.com/pricing/free-trial/).

Vous pouvez également contourner Visual Studio et utiliser directement la ligne de commande Apache Cordova. Cela est utile lorsque vous terminez le didacticiel sur un ordinateur Mac. La compilation d’applications client Apache Cordova à l'aide de la ligne de commande n'est pas couverte par ce didacticiel.

## Créer un serveur principal d'applications mobiles Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Configurer le projet de serveur

[AZURE.INCLUDE [app-service-mobile-Configure-New-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## Télécharger et exécuter l'application Apache Cordova

[AZURE.INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## Étapes suivantes

Maintenant que vous avez terminé ce didacticiel de démarrage rapide, passez à l’un des didacticiels suivants :

* [Ajout de l’authentification] à votre application Apache Cordova.
* [Ajout de notifications Push] à votre application Apache Cordova.

En savoir plus sur les concepts clés avec Azure App Service.

* [Authentification]
* [Notifications Push]

Découvrez comment utiliser les Kits de développement logiciel.

* [Kit de développement logiciel du serveur ASP.NET]
* [Kit de développement logiciel du serveur Node.js]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools pour Apache Cordova]: https://www.visualstudio.com/fr-FR/features/cordova-vs.aspx
[Ajout de l’authentification]: app-service-mobile-cordova-get-started-users.md
[Ajout de notifications Push]: app-service-mobile-cordova-get-started-push.md
[Authentification]: app-service-mobile-auth.md
[Notifications Push]: ../notification-hubs/notification-hubs-overview.md
[Kit de développement logiciel du serveur ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Kit de développement logiciel du serveur Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0224_2016-->