---
title: "Présentation d’App Service sur Linux | Microsoft Docs"
description: "Découvrez App Service sur Linux."
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: 78fbef5aa26c52f5dd264581fd1525ada763d4c6
ms.openlocfilehash: fa5d210c5e73074803d5cfb874ad7891563aefd0
ms.lasthandoff: 02/22/2017


---
# <a name="introduction-to-app-service-on-linux"></a>Présentation d’App Service sur Linux
Actuellement disponible en version préliminaire publique, App Service sur Linux prend en charge l’exécution d’applications web en mode natif sur Linux.

## <a name="overview"></a>Vue d'ensemble
Les clients peuvent utiliser App Service sur Linux pour héberger des applications web en mode natif sur Linux pour les piles d’applications prises en charge. La section suivante répertorie les piles d’applications qui sont actuellement prises en charge. 

## <a name="features"></a>Caractéristiques
App Service sur Linux prend actuellement en charge les piles d’applications suivantes :

* Node.js
* PHP
* .Net Core
* Ruby

Les clients peuvent déployer leurs applications à l’aide de :

* FTP
* Git local
* GitHub

Pour la mise à l’échelle des applications :

* Les clients peuvent mettre à l’échelle les applications web en modifiant le niveau de leur plan App Service.
* Les clients peuvent augmenter la taille de leurs applications et en exécuter plusieurs instances dans les limites de leur référence (SKU).

Pour Kudu, certaines fonctionnalités de base fonctionnent avec les éléments suivants :

* Environnements
* Déploiements
* Consoles de base

## <a name="limitations"></a>Limites
Le portail Azure affiche uniquement les fonctionnalités actuellement compatibles avec App Service sur Linux et masque les autres. Les fonctionnalités seront visibles sur le portail au fur et à mesure de leur activation.

Certaines fonctionnalités, telles que l’intégration de réseau virtuel, l’authentification Azure Active Directory/tierce ou les extensions de site Kudu ne sont pas finalisées. Dès qu’elles le seront, nous mettrons à jour notre documentation et nous annoncerons les modifications sur notre blog.

Cette version préliminaire publique est actuellement disponible uniquement dans les régions suivantes :

* Ouest des États-Unis
* Europe de l'Ouest 
* Asie du Sud-Est

Web Apps sur Linux est uniquement pris en charge dans les plans App Service dédiés et ne dispose pas d’un niveau Gratuit ou Partagé. En outre, les plans App Service pour les applications web standards et Linux s’excluent mutuellement : vous ne pouvez pas créer une application web Linux dans un plan App Service non Linux.

Sous Linux, les applications web doivent être créées dans un groupe de ressources qui ne contient pas d’applications web non Linux dans la même région.

Sous Linux, Web Apps ne prend pas en charge le déploiement d’applications .NET Core à partir d’une source non compilée. Pour commencer, vous devez publier/compiler votre application .NET Core en local, puis envoyer les bits de site publiés vers votre application.

## <a name="next-steps"></a>Étapes suivantes
Consultez les liens ci-dessous pour vous familiariser avec App Service sur Linux. Vous pouvez poser des questions et signaler vos préoccupations sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Création d’applications Web dans App Service sur Linux](app-service-linux-how-to-create-a-web-app.md)
* [Utilisation d’une image Docker personnalisée pour App Service sur Linux](app-service-linux-using-custom-docker-image.md)
* [Utilisation de la configuration PM2 pour Node.js dans les applications Web sur Linux](app-service-linux-using-nodejs-pm2.md)
* [Utilisation de .NET Core dans Azure App Service Web Apps sur Linux](app-service-linux-using-dotnetcore.md)
* [Utilisation de Ruby dans Azure App Service Web Apps sur Linux](app-service-linux-using-ruby.md)
* [FAQ Azure App Service Web Apps sous Linux](app-service-linux-faq.md)


