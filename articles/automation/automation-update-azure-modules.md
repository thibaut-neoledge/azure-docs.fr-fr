---
title: "Mise à jour de modules Azure dans Azure Automation | Microsoft Docs"
description: "Cet article décrit comment vous pouvez désormais mettre à jour les modules Azure PowerShell courants fournis par défaut dans Azure Automation."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: ed8c97b642d406a05817ec6c67f31a1b4bce93b0
ms.contentlocale: fr-fr
ms.lasthandoff: 06/14/2017

---

# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Guide de mise à jour des modules Azure PowerShell dans Azure Automation

Les modules Azure PowerShell courants sont fournis par défaut dans chaque compte Automation.  L’équipe Azure met à jour régulièrement les modules Azure. Nous fournissons donc dans le compte Automation une méthode permettant de mettre à jour les modules dans le compte lorsque de nouvelles versions sont disponibles dans le portail.  

Étant donné que les modules sont régulièrement mis à jour par le groupe de produits, les modifications peuvent se produire avec les cmdlets inclus. Cela peut avoir un impact négatif sur vos runbooks suivant le type de modification, comme renommer un paramètre ou désapprouver entièrement un cmdlet. Pour éviter toute incidence sur vos runbooks ainsi que les processus qu’ils automatisent, il est fortement conseillé de tester et valider les mises à jour avant de les appliquer.  Si vous ne possédez pas de compte Automation dédié destiné à cet usage, pensez à en créer un. Vous pourrez tester de nombreux scénarios et différentes permutations lors du développement de vos runbooks, en plus des modifications répétitives comme la mise à jour des modules PowerShell.  Dès que les résultats sont validés et une fois que vous avez appliqué les modifications requises, passez à la coordination de la migration de tout runbook nécessitant une modification, puis réalisez la mise à jour comme décrit ci-dessous.     

## <a name="updating-azure-modules"></a>Mise à jour de modules Azure

1. Le panneau Modules de votre compte Automation comprend une option de **mise à jour des modules Azure**.  Elle est toujours activée.<br><br> ![Option de mise à jour de modules Azure dans le panneau Modules](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Cliquez sur **Mettre à jour les modules Azure** et un message de confirmation s’affichera vous demandant si vous souhaitez continuer.<br><br> ![Notification de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Cliquez sur **Oui** et le processus de mise à jour du module commence.  Le processus de mise à jour prend environ 15 à 20 minutes pour les modules suivants :

  * Microsoft Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Si les modules sont déjà à jour, le processus se termine en quelques secondes.  Vous serez averti lorsque le processus de mise à jour sera terminé.<br><br> ![Mise à jour d’état de mise à jour des modules Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation utilisera les derniers modules de votre compte Automation lors de l’exécution d’un nouveau travail planifié.    

Si vous utilisez des applets de commande à partir de ces modules Azure PowerShell dans des runbooks pour gérer des ressources Azure, vous devez effectuer cette mise à jour chaque mois pour vous assurer que vous avez les derniers modules.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les modules d’intégration et la création de modules personnalisés pour intégrer Automation dans d’autres systèmes, services ou solutions, consultez [Modules d’intégration](automation-integration-modules.md).

* Envisagez d’intégrer un contrôle de code source à l’aide de [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) ou de [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md). Vous pourrez gérer et contrôler votre runbook Automation et votre portefeuille de configuration de manière centralisée.  
