---
title: "Publier un élément de Place de Marché personnalisé dans Azure Stack (opérateur cloud) | Microsoft Docs"
description: "En tant qu’opérateur cloud, découvrez comment publier un élément de Place de Marché personnalisé dans Azure Stack."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: be61e746d97fbce166f44262fcc33e2f7118fd82
ms.contentlocale: fr-fr
ms.lasthandoff: 09/15/2017

---
# <a name="the-azure-stack-marketplace-overview"></a>Vue d’ensemble de Place de Marché Azure Stack
Place de Marché est une collection de services, d’applications et de ressources personnalisés pour Azure Stack, tels que des réseaux, des machines virtuelles, du stockage, et ainsi de suite. Les utilisateurs s’y rendent pour créer des ressources et déployer de nouvelles applications. Il s’agit en quelque sorte d’un catalogue où les utilisateurs peuvent choisir les éléments qu’ils souhaitent utiliser. Pour utiliser un élément de Place de Marché, les utilisateurs doivent s’abonner à une offre qui leur accorde l’accès à l’élément.

En tant qu’opérateur cloud, c’est vous qui décidez des éléments à ajouter (publier) dans Place de Marché. Vous pouvez publier des éléments tels que des bases de données, App Services, et ainsi de suite. Cela les rend visibles par tous les utilisateurs. Vous pouvez publier des éléments personnalisés que vous créez. Vous pouvez également publier des éléments à partir d’une [liste croissante d’éléments de Place de Marché Azure](azure-stack-marketplace-azure-items.md). Quand vous publiez un élément sur Place de Marché, les utilisateurs peuvent le voir dans les cinq minutes qui suivent.

Pour ouvrir Place de Marché, cliquez sur **Nouveau**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Éléments de Place de Marché
Un élément Place de Marché Azure Stack est un service, une application ou une ressource que vos utilisateurs peuvent télécharger et utiliser. Tous les éléments de Place de Marché Azure Stack sont visibles par tous les utilisateurs.

Chaque élément de Place de Marché comporte :

* Un modèle Azure Resource Manager pour l’approvisionnement de la ressource
* Les métadonnées telles que les chaînes, les icônes et autres supports marketing
* Des informations de mise en forme permettant d’afficher l’élément dans le portail

Chaque élément publié sur Place de Marché utilise un format appelé Azure Gallery Package (azpkg). Ajoutez les ressources de déploiement ou d’exécution (par exemple du code, des fichiers zip avec des logiciels, ou des images de machine virtuelle) à Azure Stack séparément, et non dans le cadre de l’élément de Place de Marché. 

## <a name="next-steps"></a>Étapes suivantes
[Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)


