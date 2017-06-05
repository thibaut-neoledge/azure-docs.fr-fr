---
title: Surveillance Azure et machines virtuelles Windows | Microsoft Docs
description: Didacticiel - Surveiller une machine virtuelle Windows avec Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 2bf6e3cf2f039a9c92617203a3d4cf2aac8901ce
ms.contentlocale: fr-fr
ms.lasthandoff: 05/09/2017

---

# <a name="monitor-a-windows-virtual-machine-with-azure-powershell"></a>Surveiller une machine virtuelle Windows avec Azure PowerShell

Surveillance Azure utilise des agents pour collecter des données de performances et de démarrage à partir des machines virtuelles Azure, stocker ces données dans le stockage Azure et les rendre accessibles via le portail, le module Azure PowerShell et l’interface CLI Azure. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activer les diagnostics de démarrage sur une machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte de machine virtuelle
> * Installer l’extension de diagnostics
> * Afficher les métriques de la machine virtuelle
> * Créer une alerte
> * Configurer la surveillance avancée

Ce didacticiel requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Pour exécuter l’exemple dans ce didacticiel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) peut en créer une pour vous. Au cours du didacticiel, remplacez le groupe de ressources, le nom de la machine virtuelle et l’emplacement, si nécessaire.

## <a name="view-boot-diagnostics"></a>Afficher les diagnostics de démarrage

Au démarrage des machines virtuelles Windows, l’agent de diagnostic de démarrage capture le résultat à l’écran. Celui-ci peut être utilisé pour résoudre certains problèmes. Cette fonctionnalité est activée par défaut. Les captures d’écran sont stockées dans un compte de stockage Azure, qui est également créé par défaut. 

Vous pouvez obtenir les données de diagnostic de démarrage avec la commande [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata). Dans l’exemple suivant, les diagnostics de démarrage sont téléchargés à la racine du lecteur *c:\*. 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Afficher les métriques de l’hôte

Une machine virtuelle Windows possède une machine virtuelle hôte dédiée dans Azure, avec qui elle interagit. Les métriques sont automatiquement collectées pour l’hôte et peuvent être visualisées dans le portail Azure.

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Pour voir comment la machine virtuelle hôte fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle, puis sélectionnez une des métriques de l’hôte sous **Métriques disponibles**.

    ![Afficher les métriques de l’hôte](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>Installer l’extension Diagnostics

Les métriques de base de l’hôte sont disponibles, mais pour voir des métriques plus précises et spécifiques à la machine virtuelle, vous devez installer l’extension Diagnostics Azure sur la machine virtuelle. L’extension Diagnostics Azure permet de récupérer des données supplémentaires de surveillance et de diagnostic auprès de la machine virtuelle. Vous pouvez voir ces métriques de performances et créer des alertes basées sur le fonctionnement de la machine virtuelle. L’extension Diagnostics est installée via le portail Azure comme suit :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Cliquez sur **Paramètres de diagnostic**. La liste montre que les *Diagnostics de démarrage* sont déjà activés depuis la section précédente. Cliquez sur la case à cocher *Métriques de base*.
3. Cliquez sur le bouton **Activer la surveillance au niveau invité**.

    ![Afficher les métriques de diagnostic](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>Afficher les métriques de la machine virtuelle

Vous pouvez afficher les métriques de la machine virtuelle de la même façon que vous avez affiché le mesures de la machine virtuelle hôte :

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Pour voir comment la machine virtuelle fonctionne, cliquez sur **Métriques** dans le panneau de la machine virtuelle puis sélectionnez une des métriques de diagnostic sous **Métriques disponibles**.

    ![Afficher les métriques de la machine virtuelle](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>Créez des alertes

Vous pouvez créer des alertes en fonction de métriques de performances spécifiques. Les alertes peuvent être utilisées par exemple pour notifier que l’utilisation moyenne de l’UC dépasse un certain seuil ou que l’espace disque disponible est inférieur à une certaine quantité. Les alertes sont affichées dans le portail Azure ou peuvent être envoyées par e-mail. Vous pouvez également déclencher des runbooks Azure Automation ou Azure Logic Apps en réponse aux alertes générées.

L’exemple suivant crée une alerte pour l’utilisation moyenne de l’UC.

1. Dans le portail Azure, cliquez sur **Groupes de ressources**, sélectionnez **myResourceGroup** puis sélectionnez **myVM** dans la liste des ressources.
2. Cliquez sur **Règles d’alerte** dans le panneau de la machine virtuelle puis cliquez sur **Ajouter une alerte Métrique** dans la partie supérieure du panneau des alertes.
4. Spécifiez un **Nom** pour votre alerte, comme *myAlertRule*
5. Pour déclencher une alerte quand le pourcentage d’UC dépasse 1,0 pendant cinq minutes, laissez toutes les autres valeurs par défaut sélectionnées.
6. Si vous le souhaitez, cochez la case *Envoyer un e-mail aux propriétaires, aux contributeurs et aux lecteurs* pour envoyer la notification par e-mail. L’action par défaut est de présenter une notification dans le portail.
7. Cliquez sur le bouton **OK**.

## <a name="advanced-monitoring"></a>Surveillance avancée 

Vous pouvez faire une surveillance plus avancée de votre machine virtuelle en utilisant [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview). Si vous ne l’avez pas déjà fait, vous pouvez vous inscrire pour une [version d’évaluation gratuite](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial) d’Operations Management Suite.

Quand vous avez accès au portail OMS, vous pouvez trouver la clé de l’espace de travail et l’identificateur de l’espace de travail dans le panneau Paramètres. Utilisez la commande [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) pour ajouter l’extension OMS à la machine virtuelle. Mettez à jour les valeurs des variables dans l’exemple ci-dessous de manière à refléter la clé et l’ID de l’espace de travail OMS.  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

Après quelques minutes, la nouvelle machine virtuelle s’affiche dans l’espace de travail OMS. 

![Panneau OMS](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez configuré et examiné des machines virtuelles avec Azure Security Center. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créez un réseau virtuel
> * Créer un groupe de ressources et une machine virtuelle 
> * Activer les diagnostics de démarrage sur la machine virtuelle
> * Afficher les diagnostics de démarrage
> * Afficher les métriques de l’hôte
> * Installer l’extension de diagnostics
> * Afficher les métriques de la machine virtuelle
> * Créer une alerte
> * Configurer la surveillance avancée

Passez au didacticiel suivant pour découvrir plus d’informations sur Azure Security Center.

> [!div class="nextstepaction"]
> [Gérer la sécurité des machines virtuelles](./tutorial-azure-security.md)
