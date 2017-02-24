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
ms.date: 02/13/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7a2999b3b1a54668f6ef45433efabd5a495418fe
ms.openlocfilehash: ec84df70d4a77e3b81a88aa286fc492d92e3e753


---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Les modules Azure PowerShell courants sont fournis par défaut dans chaque compte Automation.  L’équipe Azure met à jour régulièrement les modules Azure. Nous fournissons donc dans le compte Automation une méthode permettant de mettre à jour les modules dans le compte lorsque de nouvelles versions sont disponibles.

## <a name="updating-azure-modules"></a>Mise à jour de modules Azure

1. Le panneau Modules de votre compte Automation comprend une option de **mise à jour des modules Azure**.  Elle est toujours activée.<br><br> ![Option de mise à jour de modules Azure dans le panneau Modules](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Cliquez sur **Mettre à jour les modules Azure** et un message de confirmation s’affichera vous demandant si vous souhaitez continuer.<br><br> ![Notification de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-notification.png)

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

Dans le cadre du processus de mise à jour, les planifications pour tous les runbooks prévus seront mises à jour pour utiliser la dernière version des modules.

Si vous utilisez des applets de commande à partir de ces modules Azure PowerShell dans des runbooks pour gérer des ressources Azure, vous devez effectuer cette mise à jour chaque mois pour vous assurer que vous avez les derniers modules.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les modules d’intégration et la création de modules personnalisés pour intégrer Automation dans d’autres systèmes, services ou solutions, consultez [Modules d’intégration](automation-integration-modules.md).


<!--HONumber=Feb17_HO2-->


