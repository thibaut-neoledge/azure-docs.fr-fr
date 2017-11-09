---
title: "Utiliser le module de stratégie Azure Stack | Microsoft Docs"
description: "Découvrez comment contraindre un abonnement Azure à se comporter comme un abonnement Azure Stack."
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e505c52a5e1897d5626ee2cacce9fa3eff12fbbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gérer la stratégie Azure à l’aide du module de stratégie Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Le module de stratégie Azure Stack vous permet de configurer un abonnement Azure avec la même gestion des versions et la même disponibilité de service qu’Azure Stack.  Le module utilise l’applet de commande **New-AzureRMPolicyAssignment** pour créer une stratégie Azure qui limite les types de ressources et les services disponibles dans un abonnement.  Une fois terminé, vous pouvez utiliser votre abonnement Azure pour développer des applications ciblées pour Azure Stack.  

## <a name="install-the-module"></a>Installer le module
1. Installez la version requise du module AzureRM PowerShell, comme décrit à l’étape 1 de [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md).   
2. [Télécharger les outils Azure Stack à partir de GitHub](azure-stack-powershell-download.md)  
3. [Configurer PowerShell pour une utilisation avec Azure Stack](azure-stack-powershell-configure-user.md)

4. Importez le module AzureStack.Policy.psm1 :

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>Appliquer la stratégie à l’abonnement
Vous pouvez utiliser la commande suivante pour appliquer une stratégie Azure Stack par défaut à votre abonnement Azure. Avant l’exécution, remplacez *Azure Subscription Name* par le nom de votre abonnement Azure.

```PowerShell
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
$rgName = 'AzureStack'
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Appliquer la stratégie à un groupe de ressources
Vous souhaiterez peut-être appliquer des stratégies avec davantage de granularité.  Par exemple, vous pourriez avoir d’autres ressources en cours d’exécution dans le même abonnement.  Vous pouvez limiter l’application de la stratégie à un groupe de ressources spécifique, ce qui vous permet de tester vos applications pour Azure Stack à l’aide de ressources Azure. Avant l’exécution, remplacez *Azure Subscription Name* par le nom de votre abonnement Azure.

```PowerShell
$resourceGroupName = ‘myRG01’
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzureStackRmPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Stratégie en action
Une fois que vous avez déployé la stratégie Azure, vous recevez une erreur quand vous essayez de déployer une ressource interdite par la stratégie.  

![Échec de déploiement de ressources en raison de la contrainte de stratégie](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Étapes suivantes
[Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)

[Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)

[Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
