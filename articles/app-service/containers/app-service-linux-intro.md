---
title: "Présentation d’Azure Web Apps for Containers | Microsoft Docs"
description: "Découvrez Azure Web Apps for Containers."
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
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f46f59a69d13a4e702eccded810cf3c7a8b30904
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="introduction-to-azure-web-apps-for-containers"></a>Présentation d’Azure Web Apps for Containers

[Web App](../../app-service-web/app-service-web-overview.md) est une plateforme de calcul entièrement gérée, optimisée pour l’hébergement de sites et d’applications web. Les clients peuvent utiliser Web Apps for Containers afin d’héberger des applications web en mode natif sur Linux pour les piles d’applications prises en charge. Les sections suivantes listent les piles d’applications actuellement prises en charge.

## <a name="languages"></a>Langues

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>Déploiements

* FTP
* Git local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Environnements intermédiaires
* Intégration/livraison continue [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) et DockerHub

## <a name="console-publishing-and-debugging"></a>Console, publication et débogage

* Environnements
* Déploiements
* Console de base
* SSH

## <a name="scaling"></a>Mise à l'échelle

* Les clients peuvent faire monter ou descendre en puissance les applications web en modifiant le niveau de leur [plan App Service](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Emplacements

Consultez le [tableau de bord Statut Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limites

Le Portail Azure affiche uniquement les fonctionnalités compatibles avec Web Apps for Containers. Les fonctionnalités seront visibles sur le portail au fur et à mesure de leur activation.

Certaines fonctionnalités, telles que l’intégration de réseau virtuel, l’authentification Azure Active Directory/tierce, ou les extensions de site Kudu, ne sont pas encore disponibles. Dès qu’elles le seront, nous mettrons à jour notre documentation et nous annoncerons les modifications sur notre blog.

Web Apps for Containers n’est pris en charge que par les plans App Service [De base et Standard](https://azure.microsoft.com/pricing/details/app-service/plans/) et ne dispose pas d’un niveau [Gratuit ou Partagé](https://azure.microsoft.com/pricing/details/app-service/plans/). Voici d’autres restrictions importantes concernant Web Apps for Containers :

* Il n’est pas possible de créer des applications Web Apps for Containers dans un plan App Service qui héberge déjà des applications web autres que Linux.
* Quand vous créez des applications Web Apps for Containers dans un groupe de ressources contenant des applications web autres que Linux, vous devez créer un plan App Service dans une région autre que celle du plan App Service existant.

## <a name="troubleshooting"></a>Résolution des problèmes

Lorsque votre application ne démarre pas ou que vous souhaitez vérifier la journalisation à partir de votre application, consultez les journaux Docker dans le répertoire LogFiles. Vous pouvez accéder à ce répertoire par le biais de votre site SCM ou d’un FTP.
Pour journaliser `stdout` et `stderr` à partir de votre conteneur, vous devez activer **Journalisation de conteneur Docker** sous **Journaux de diagnostic**.

![Activation de la journalisation][2]

![Affichage des journaux Docker avec Kudu][1]

Vous pouvez accéder au site SCM à partir d’**Outils avancés** dans le menu **Outils de développement**.

## <a name="next-steps"></a>Étapes suivantes

Consultez les liens ci-dessous pour vous familiariser avec App Service sur Linux. Vous pouvez poser des questions et signaler vos préoccupations sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Comment utiliser une image Docker personnalisée pour Azure Web Apps for Containers](quickstart-custom-docker-image.md)
* [Utilisation de .NET Core dans Azure App Service Web Apps for Containers](quickstart-dotnetcore.md)
* [Utilisation de Ruby dans Azure App Service Web Apps for Containers](quickstart-ruby.md)
* [Forum aux questions sur Azure App Service Web Apps for Containers](app-service-linux-faq.md)
* [Prise en charge SSH pour Azure Web Apps for Containers](app-service-linux-ssh-support.md)
* [Configurer des environnements intermédiaires dans Azure App Service](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Déploiement continu Docker Hub avec Azure Web Apps for Containers](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

