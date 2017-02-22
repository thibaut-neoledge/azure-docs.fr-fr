---
title: "Ressources associées et liées dans la Galerie de vignettes"
description: "Apprenez en quoi consistent les ressources associées et liées qui sont affichées dans la Galerie de vignettes du portail Azure en version préliminaire."
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: ebc5dbf790ca6012cfe9a7ea9ccee9fdacb46ffd
ms.openlocfilehash: b4c8ad69674c553f8b521a85765edbad0ce1faf2


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Ressources associées et liées dans la Galerie de vignettes
La Galerie de vignettes vous permet de rechercher des vignettes pour une ressource spécifique et de les faire glisser jusqu’à votre panneau actif. À l’aide de la Galerie de vignettes, vous pouvez créer des vues de gestion portant sur les ressources. Pour n’importe quelle ressource donnée, les ressources associées englobent toutes les ressources qui partagent le même groupe de ressources que cette ressource, ainsi que toutes les ressources qui établissent des liens à destination ou en provenance de la ressource.

## <a name="linked-resources-in-azure-resource-manager"></a>Ressources liées dans Azure Resource Manager
La liaison est une fonctionnalité d’Azure Resource Manager.  Elle vous permet de déclarer des relations entre les ressources, même si ces dernières n’appartiennent pas au même groupe de ressources. La liaison n’a aucun impact sur le moment de l’exécution de vos ressources, sur la facturation, ni sur l’accès en fonction du rôle.  Il s’agit simplement d’un mécanisme qui vous permet de représenter les relations afin que les outils comme la Galerie de vignettes puissent offrir une expérience de gestion enrichie.  Vos outils peuvent inspecter les liens à l’aide de l’API de liens et fournir également des expériences de gestion des relations personnalisées. 

## <a name="how-do-i-link-my-resources"></a>Comment lier mes ressources ?
Lorsque vous créez des ressources par le biais du portail ou en déployant un modèle par l’intermédiaire d’Azure PowerShell ou de l’interface de ligne de commande Azure, des liens sont automatiquement créés pour certaines ressources dépendantes. Vous pouvez également lier des ressources par programme à l’aide de l’ [API REST Ressources liées](https://msdn.microsoft.com/library/azure/mt238499.aspx) ou en déclarant les relations dans le modèle. Pour découvrir une description complète de la procédure d’utilisation des ressources liées, voir l’article [Liaison des ressources dans Azure Resource Manager](../azure-resource-manager/resource-group-link-resources.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur l’écriture de modèles Azure Resource Manager, voir l’article [Création de modèles](../azure-resource-manager/resource-group-authoring-templates.md).
* Pour plus d’informations sur la création de liens entre les ressources, voir l’article [Liaison des ressources dans Azure Resource Manager](../azure-resource-manager/resource-group-link-resources.md).
* Pour plus d’informations sur l’utilisation des groupes de ressources par le biais du portail en version préliminaire, voir l’article [Utilisation du portail Azure en version préliminaire pour gérer vos ressources Azure](../azure-resource-manager/resource-group-portal.md).




<!--HONumber=Feb17_HO3-->


