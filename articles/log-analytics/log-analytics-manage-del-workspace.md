---
title: Supprimer un espace de travail Azure Log Analytics | Microsoft Docs
description: "Découvrez comment supprimer votre espace de travail Log Analytics si vous en avez créé un dans votre abonnement personnel ou restructurer votre modèle d’espace de travail."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3d99f9869dfdfbe18f82e873bd367cc85f9414f1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Supprimer un espace de travail Azure Log Analytics avec le portail Azure
Cette rubrique montre comment utiliser le portail Azure pour supprimer un espace de travail Log Analytics dont vous n’aurez peut-être plus besoin. 

## <a name="to-delete-a-workspace"></a>Pour supprimer un espace de travail 
Quand vous supprimez un espace de travail Log Analytics, toutes les données relatives à votre espace de travail sont supprimées du service dans un délai de 30 jours.  Faites attention quand vous supprimez un espace de noms, car celui-ci peut contenir une configuration et des données importantes dont la suppression risque d’avoir un impact négatif sur les opérations de votre service.  
 
Si vous êtes administrateur et que plusieurs utilisateurs sont associés à l’espace de travail, l’association entre les utilisateurs et l’espace de travail est rompue. Si les utilisateurs sont associés à d’autres espaces de travail, ils peuvent continuer à utiliser Log Analytics avec ces autres espaces de travail. Toutefois, s’ils ne sont pas associés à des espaces de travail, ils doivent créer un espace de travail pour utiliser Log Analytics. 

1. Connectez-vous au [portail Azure](http://portal.azure.com). 
2. Dans le portail Azure, cliquez sur **Autres services** en bas à gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
3. Dans le volet des abonnements Log Analytics, sélectionnez un espace de travail, puis cliquez sur **Supprimer** en haut du volet central.<br><br> ![Option Supprimer dans le volet des propriétés de l’espace de travail](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Quand la fenêtre de message de confirmation s’affiche vous invitant à confirmer la suppression de l’espace de travail, cliquez sur **Oui**.<br><br> ![Confirmer la suppression de l’espace de travail](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

