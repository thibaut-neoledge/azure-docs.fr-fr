---
title: Validation de la configuration de compte Azure Automation | Microsoft Docs
description: "Cet article décrit comment vérifier que votre compte Automation est correctement configuré."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Test d’authentification du compte d’identification Azure Automation
Une fois qu’un compte Automation est correctement créé, vous pouvez effectuer un test simple pour confirmer que vous êtes en mesure de vous authentifier dans Azure Resource Manager ou un déploiement classique Azure à l’aide de votre compte d’identification Automation nouvellement créé ou mis à jour.    

## <a name="automation-run-as-authentication"></a>Authentification d’identification Automation

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.  
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer.  Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau Travail s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.  
5. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
6. À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.<br> ![Test de Runbook du principal de sécurité](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie** .
8. Le panneau **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les ressources disponibles dans le groupe de ressources.
9. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
10. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureAutomationTutorialScript** correspondant.

## <a name="classic-run-as-authentication"></a>Authentification d’identification Classic
Procédez comme suit si vous gérez les ressources dans le modèle de déploiement classique pour confirmer que vous êtes en mesure de vous authentifier à l’aide du nouveau compte d’identification Classic.     

1. Dans le portail Azure, ouvrez le compte Automation que vous avez créé.  
2. Cliquez sur la vignette **Runbooks** pour ouvrir la liste des runbooks.
3. Sélectionnez le Runbook **AzureClassicAutomationTutorialScript**, puis cliquez sur **Démarrer** pour le lancer.  Vous allez recevoir une invite de confirmation de démarrage du Runbook.
4. Un [travail du Runbook](automation-runbook-execution.md) est créé. Le panneau Travail s’affiche alors et l’état du travail est affiché dans la mosaïque **Résumé du travail**.  
5. L’état initial du travail est *Mis en file d’attente* pour indiquer qu’il attend la disponibilité d’un Runbook Worker dans le cloud. La tâche prend ensuite l’état *Démarrage en cours* lorsqu’un Worker sélectionne la tâche, puis l’état *En cours d’exécution* lorsque le Runbook commence à s’exécuter.  
6. À l’issue du travail du Runbook, l’état **Terminé** doit s’afficher.<br><br> ![Test de Runbook du principal de sécurité](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Pour afficher les résultats détaillés du Runbook, cliquez sur la mosaïque **Sortie** .
8. Le panneau **Sortie** doit indiquer qu’il a authentifié et retourné la liste de toutes les machines virtuelles Classic de l’abonnement.
9. Fermez le panneau **Sortie** pour revenir au panneau **Résumé du travail**.
10. Fermez le panneau **Résumé du travail** et le panneau du Runbook **AzureClassicAutomationTutorialScript** correspondant.

## <a name="next-steps"></a>Étapes suivantes
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).

