---
title: "Déploiement d’Azure Web App sur Linux : image personnalisée ou image de plateforme intégrée ?  | Microsoft Docs"
description: "Comment choisir entre le déploiement de conteneur Docker personnalisé et un framework d’application intégré"
keywords: azure app service, application web, linux, oss
services: app-service
documentationCenter: 
authors: nickwalk
manager: cfowler
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: nickwalk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: b89a8c45a5baf5f863f935dc4c669e29c30a951e
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="custom-image-or-built-in-platform-image"></a>Image personnalisée ou image de plateforme intégrée ?

[Web App sur Linux](app-service-linux-intro.md) offre deux moyens de publier votre application sur le web :

- **Déploiement d’image personnalisée** : « Dockerisez » votre application dans une image Docker qui contient tous les fichiers et dépendances dans un package prêt à s’exécuter.
- **Déploiement d’application avec une image de plateforme intégrée** : nos images de plateforme intégrées contiennent des runtimes et des dépendances Web App courants, tels que Node et PHP. Appliquez l’une des [méthodes de déploiement Azure App Service](../../app-service-web/web-sites-deploy.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) pour déployer votre application sur le stockage de votre application web, puis utilisez une image de plateforme intégrée pour l’exécuter.

Quelle est la méthode la plus adaptée à votre application ? Les principaux facteurs à prendre en compte sont les suivants :

- **Disponibilité de Docker dans votre flux de travail de développement** : le développement d’image personnalisée nécessite une connaissance élémentaire des flux de travail de développement Docker. Le déploiement d’une image personnalisée sur une application web nécessite la publication de votre image personnalisée dans un hôte de référentiels tel que Docker Hub. Si vous connaissez bien Docker et que vous pouvez ajouter des tâches Docker à votre flux de travail de build, ou si vous publiez déjà votre application en tant qu’image Docker, une image personnalisée est certainement le meilleur choix.
- **Exigences d’exécution uniques** : les images de plateforme intégrées sont conçues pour répondre aux besoins de la plupart des applications web, mais sont limitées dans leurs possibilités de personnalisation. Votre application peut avoir des dépendances uniques ou d’autres exigences d’exécution qui vont au-delà des capacités des images intégrées.
- **Exigences de build** : avec le [déploiement continu](../../app-service-web/app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), vous pouvez rendre votre application opérationnelle sur Azure directement à partir du code source. Aucun processus de publication ou de génération externe n’est nécessaire. Toutefois, il existe une limite quant à la personnalisation et la disponibilité des outils de génération dans le moteur de déploiement [Kudu](https://github.com/projectkudu/kudu/wiki). Votre application peut aller au-delà des capacités de Kudu à mesure que ses dépendances ou ses exigences en termes de logique de génération personnalisée augmentent.
- **Exigences de lecture/écriture de disque** : un volume de stockage pour le contenu web est alloué à toutes les applications web. Ce volume, fourni par le stockage Azure, est monté sur `/home` dans le système de fichiers de l’application. Contrairement au fichiers du système de fichiers conteneur, les fichiers du volume de contenu sont accessibles par toutes les instances d’échelle d’une application, et les modifications sont conservées lors des redémarrages de l’application. Toutefois, la latence du disque du volume du contenu est plus élevée et plus variable que celle du système de fichiers conteneur local, et l’accès peut être impacté par les mises à niveau de la plateforme, les temps d’arrêt non planifiés et les problèmes de connectivité réseau. Le déploiement d’image personnalisée peut être bénéfique aux applications qui nécessitent un accès en lecture seule intensif aux fichiers de contenu, car les fichiers sont placés dans le système de fichiers image plutôt que sur le volume de contenu.
- **Utilisation des ressources de build** : quand une application est déployée à partir de la source, les scripts de déploiement exécutés par Kudu utilisent les mêmes ressources de stockage et de calcul de plan App Service que l’application en cours d’exécution. Les déploiements d’applications à grande échelle peuvent consommer plus de ressources ou de temps que vous ne le souhaitez. En particulier, de nombreux flux de travail de déploiement génèrent une activité élevée du disque sur le volume de contenu d’application, qui n’est pas optimisé pour une telle activité. Une image personnalisée fournit tous les fichiers et les dépendances de votre application à Azure dans un package unique, sans qu’aucun transfert de fichiers ou action de déploiement supplémentaire ne soit nécessaire.
- **Nécessité d’une itération rapide** : La Dockerisation d’une application exige des étapes de génération supplémentaires. Pour que les modifications prennent effet, vous devez envoyer votre nouvelle image vers un référentiel lors de chaque mise à jour. Ces mises à jour sont ensuite tirées vers l’environnement Azure. Si l’un des conteneurs intégrés répond aux besoins de votre application, le déploiement à partir de la source peut offrir un flux de travail de développement plus rapide.
