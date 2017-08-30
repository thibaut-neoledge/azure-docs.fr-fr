---
title: "Vue d’ensemble du runtime d’Azure Functions | Microsoft Docs"
description: "Vue d’ensemble du runtime d’Azure Functions en version préliminaire"
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 98d1e2ad162f4b69be4fdf9a5b004f640fbb5d7e
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017

---

# <a name="azure-functions-runtime-overview"></a>Vue d’ensemble du runtime d’Azure Functions

Le runtime d’Azure Functions vous fournit une nouvelle façon de tirer parti de la simplicité et de la flexibilité du modèle de programmation Azure Functions sur site. Basé sur les mêmes racines open source qu’Azure Functions, le runtime d’Azure Functions est déployé sur site pour fournir une expérience de développement quasiment identique à celle du service cloud.

![Portail du runtime d’Azure Functions en version préliminaire][1]

Le runtime d’Azure Functions vous permet de découvrir Azure Functions avant même d’adopter le cloud. De cette façon, les ressources de code que vous créez peuvent ensuite être dirigées sur le cloud lors de la migration.  Le runtime vous donne également accès à de nouvelles options, comme l’utilisation de la puissance de calcul de secours de vos ordinateurs locaux pour exécuter des traitements par lots pendant la nuit. Vous pouvez également utiliser des appareils au sein de votre organisation pour envoyer de manière conditionnelle des données à d’autres systèmes, sur site et sur le cloud.

Le runtime d’Azure Functions se compose de deux éléments :
* Rôle de gestion du runtime d’Azure Functions
* Rôle de travail du runtime d’Azure Functions

## <a name="azure-functions-management-role"></a>Rôle de gestion d’Azure Functions

Le rôle de gestion d’Azure Functions fournit un hôte pour la gestion de vos fonctions sur site. Ce rôle effectue les tâches suivantes :

* Hébergement du portail de gestion d’Azure Functions, qui est identique à celui que vous voyez dans le [portail Azure](https://portal.azure.com). Il vous permet de développer vos fonctions de la même manière que vous le feriez dans le portail Azure.
* Répartition des fonctions entre plusieurs Workers Functions.
* Mise à disposition d’un point de terminaison de publication pour vous permettre de publier vos fonctions directement à partir de Microsoft Visual Studio.

## <a name="azure-functions-worker-role"></a>Rôle de travail d’Azure Functions

Les rôles de travail d’Azure Functions sont déployés dans des conteneurs Windows et c’est là que le code de votre fonction s’exécute.  Vous pouvez déployer plusieurs rôles de travail au sein de votre organisation. Il s’agit là d’une solution clé grâce à laquelle les clients peuvent utiliser la puissance de calcul de secours.

## <a name="minimum-requirements"></a>Configuration minimale requise

Pour bien démarrer avec le runtime d’Azure Functions, vous devez disposer d’un ordinateur **Windows Server 2016 ou Windows 10 Creators Update** avec accès à une instance **SQL Server**.

## <a name="next-steps"></a>Étapes suivantes

Installer la [version préliminaire du runtime d’Azure Functions](https://aka.ms/azafr)

<!--Image references-->
[1]: ./media/functions-runtime-overview/AzureFunctionsRuntime_Portal.png

