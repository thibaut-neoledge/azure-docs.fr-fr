---
title: "Mise à jour de modules Azure dans Azure Automation | Microsoft Docs"
description: "Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation."
services: automation
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 6765ea93dd4e4e2594fb147dd19120aec058a2f5
ms.contentlocale: fr-fr
ms.lasthandoff: 04/20/2017


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Les modules Azure PowerShell courants sont fournis par défaut dans chaque compte Automation.  L’équipe Azure met à jour régulièrement les modules Azure. Nous fournissons donc dans le compte Automation une méthode permettant de mettre à jour les modules dans le compte lorsque de nouvelles versions sont disponibles dans le portail.  

## <a name="updating-azure-modules"></a>Mise à jour de modules Azure

1. Le panneau Modules de votre compte Automation comprend une option de **mise à jour des modules Azure**.  Elle est toujours activée.<br><br> ![Option de mise à jour de modules Azure dans le panneau Modules](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Cliquez sur **Mettre à jour les modules Azure** et un message de confirmation s’affichera vous demandant si vous souhaitez continuer.<br><br> ![Notification de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Cliquez sur **Oui** et le processus de mise à jour du module commence.  Le processus de mise à jour prend environ 15 à 20 minutes pour les modules suivants :

  * Microsoft Azure
  *    Azure.Storage
  *    AzureRm.Automation
  *    AzureRm.Compute
  *    AzureRm.Profile
  *    AzureRm.Resources
  *    AzureRm.Sql
  * AzureRm.Storage

    Si les modules sont déjà à jour, le processus se termine en quelques secondes.  Vous serez averti lorsque le processus de mise à jour sera terminé.<br><br> ![Mise à jour d’état de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

Lorsque vous créez une planification, toutes les tâches suivantes s’exécutant sur cette planification utilisent les modules du compte Automation correspondant au moment où la planification a été créée.  Pour utiliser des modules mis à jour avec les runbooks planifiés, vous devez dissocier puis lier à nouveau la planification avec ce runbook.   

Si vous utilisez des applets de commande à partir de ces modules Azure PowerShell dans des runbooks pour gérer des ressources Azure, vous devez effectuer cette mise à jour chaque mois pour vous assurer que vous avez les derniers modules.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les modules d’intégration et la création de modules personnalisés pour intégrer Automation dans d’autres systèmes, services ou solutions, consultez [Modules d’intégration](automation-integration-modules.md).
