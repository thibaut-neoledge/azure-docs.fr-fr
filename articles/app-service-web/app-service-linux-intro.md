---
title: "Présentation d’Azure Web App sur Linux | Microsoft Docs"
description: En savoir plus sur Azure Web App sur Linux.
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
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 69156ec555b34d066a65bdc202267cfc53de47a0
ms.contentlocale: fr-fr
ms.lasthandoff: 08/17/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Présentation d’Azure Web App sur Linux

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>Vue d'ensemble
Les clients peuvent utiliser Web App sur Linux pour héberger des applications web en mode natif sur Linux pour les piles d’applications prises en charge. La section suivante répertorie les piles d’applications qui sont actuellement prises en charge. 

## <a name="features"></a>Caractéristiques
Web App sur Linux prend actuellement en charge les piles d’applications suivantes :

* Node.js
    * 4.4
    * 4.5
    * 6.2
    * 6.6
    * 6.9
    * 6.10
* PHP
    * 5.6
    * 7.0
* .Net Core
    * 1.0
    * 1.1
* Ruby
    * 2.3

Les clients peuvent déployer leurs applications à l’aide de :

* FTP
* Git local
* GitHub
* Bitbucket

Pour la mise à l’échelle des applications :

* Les clients peuvent faire monter ou descendre en puissance les applications web en modifiant le niveau dans leur plan App Service
* Les clients peuvent augmenter la taille des instances de leurs applications et en exécuter plusieurs instances, dans les limites de leur référence (SKU)

Pour Kudu, certaines fonctionnalités de base sont :

* Environnements
* Déploiements
* Console de base
* SSH

Pour DevOps :

* Environnements intermédiaires
* ACR et DockerHub CI/CD

## <a name="limitations"></a>Limites
Le portail Azure affiche uniquement les fonctionnalités actuellement compatibles avec Web App sur Linux, et masque les autres. Les fonctionnalités seront visibles sur le portail au fur et à mesure de leur activation.

Certaines fonctionnalités, telles que l’intégration de réseau virtuel, l’authentification Azure Active Directory/tierce, ou les extensions de site Kudu, ne sont pas encore disponibles. Dès qu’elles le seront, nous mettrons à jour notre documentation et nous annoncerons les modifications sur notre blog.

Cette version préliminaire publique est actuellement disponible uniquement dans les régions suivantes :

* Ouest des États-Unis
* Est des États-Unis
* Europe de l'Ouest
* Europe du Nord
* Centre-Sud des États-Unis
* États-Unis - partie centrale septentrionale
* Asie du Sud-Est
* Est de l'Asie
* Est de l’Australie
* Est du Japon
* Sud du Brésil
* Inde du Sud

Web Apps sur Linux est uniquement pris en charge dans les plans App Service dédiés et ne dispose pas d’un niveau Gratuit ou Partagé. En outre, les plans App Service pour les applications web standards et Linux s’excluent mutuellement : vous ne pouvez pas créer une application web Linux dans un plan App Service non Linux.

Sous Linux, les applications web doivent être créées dans un groupe de ressources qui ne contient pas d’applications web non Linux dans la même région.

## <a name="next-steps"></a>Étapes suivantes
Consultez les liens ci-dessous pour vous familiariser avec App Service sur Linux. Vous pouvez poser des questions et signaler vos préoccupations sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Créer des applications web dans Azure Web App sur Linux](app-service-linux-how-to-create-web-app.md)
* [Comment utiliser une image Docker personnalisé pour Azure Web App sur Linux](app-service-linux-using-custom-docker-image.md)
* [Utiliser la configuration PM2 pour Node.js dans Azure Web App sur Linux](app-service-linux-using-nodejs-pm2.md)
* [Utiliser .NET Core dans Web App d’Azure App Service sur Linux](app-service-linux-using-dotnetcore.md)
* [Utiliser Ruby dans Web Apps d’Azure App Service sur Linux](app-service-linux-ruby-get-started.md)
* [FAQ de Web App d’Azure App Service sur Linux](app-service-linux-faq.md)
* [Prise en charge SSH pour Azure Web App sur Linux](./app-service-linux-ssh-support.md)
* [Configurer des environnements intermédiaires dans Azure App Service](./web-sites-staged-publishing.md)
* [Déploiement continu Docker Hub avec l’application web Azure sur Linux](./app-service-linux-ci-cd.md)


