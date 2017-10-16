---
title: Gestion des notifications de maintenance pour les machines virtuelles Windows dans Azure | Microsoft Docs
description: "Affichez les notifications de maintenance pour les machines virtuelles Windows s’exécutant dans Azure et démarrez la maintenance en libre-service."
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.openlocfilehash: 90129bee5771534b01ac12473c6cfed77840ae20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>Gestion de notifications de maintenance planifiées pour les machines virtuelles Windows

Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte des machines virtuelles. Les mises à jour sont des modifications telles que la mise à jour corrective de l’environnement d’hébergement ou la mise à niveau et la désactivation de matériel. Une majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées. Cependant, il existe des cas où les mises à jour ont un impact :

- Lorsque la maintenance ne nécessite pas de redémarrage, Azure utilise une migration sur place pour mettre en pause la machine virtuelle pendant la mise à jour de l’hôte.

- Si la maintenance nécessite un redémarrage, une notification vous dira pour quand est prévue la maintenance. Dans ces cas, vous disposez d’une période pour commencer la maintenance vous-même, au moment qui vous convient.


La maintenance planifiée nécessitant un redémarrage s’effectue par vagues. Chaque vague a une portée différente (régions).

- Une vague commence par une notification aux clients. Par défaut, la notification est envoyée aux propriétaire et copropriétaires de l’abonnement. Vous pouvez ajouter plusieurs destinataires et options de messagerie, telles que les e-mails, les SMS et les Webhooks, aux notifications.  
- Peu après la notification, une fenêtre de libre-service est définie. Pendant cet intervalle, vous pouvez rechercher quelles machines virtuelles sont incluses dans cette vague et démarrer la maintenance à l’aide d’un redéploiement proactif. 
- Après la fenêtre de libre-service, une fenêtre de maintenance planifiée commence. À ce stade, Azure planifie et applique la maintenance requise à votre machine virtuelle. 

L’objectif de ces deux fenêtres est de vous donner suffisamment de temps pour commencer la maintenance et redémarrer votre machine virtuelle tout en sachant à quel moment Azure démarrera automatiquement la maintenance.


Vous pouvez utiliser le portail Azure, PowerShell, les API REST et l’interface de ligne de commande pour demander les fenêtres de maintenance pour vos machines virtuelles et démarrer la maintenance en libre-service.

 > [!NOTE]
 > Si vous essayez de démarrer la maintenance mais que cette dernière échoue, Azure marque votre machine virtuelle en tant que **ignorée** et ne la redémarre pas au cours de la fenêtre de maintenance planifiée. À la place, vous êtes contacté ultérieurement avec une nouvelle planification. 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>Vérifier l’état de maintenance à l’aide de PowerShell

Vous pouvez également utiliser Azure Powershell pour voir quand les machines virtuelles sont planifiées pour la maintenance. Les informations de maintenance planifiée sont disponibles à partir de l’applet de commande [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) lorsque vous utilisez le paramètre `-status`.
 
Les informations de maintenance sont retournées uniquement en cas de maintenance planifiée. S’il n’existe aucune maintenance planifiée qui affecte la machine virtuelle, l’applet de commande ne retourne pas d’informations de maintenance. 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

Les propriétés suivantes sont retournées sous MaintenanceRedeployStatus : 
| Valeur | Description   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indique si vous pouvez démarrer la maintenance sur la machine virtuelle maintenant ||
| PreMaintenanceWindowStartTime         | Début de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| PreMaintenanceWindowEndTime           | Fin de la fenêtre de maintenance en libre-service lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowStartTime            | Début de la fenêtre de maintenance planifiée lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| MaintenanceWindowEndTime              | Fin de la fenêtre de maintenance planifiée lorsque vous pouvez lancer la maintenance sur votre machine virtuelle ||
| LastOperationResultCode               | Résultat de la dernière tentative de lancement de la maintenance sur la machine virtuelle ||



Vous pouvez également obtenir l’état de maintenance pour toutes les machines virtuelles dans un groupe de ressources à l’aide de [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) sans spécifier de machine virtuelle.
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName --Status
```

La fonction PowerShell suivante accepte votre ID d’abonnement et imprime une liste de machines virtuelles qui sont planifiées pour la maintenance.

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Démarrer la maintenance sur votre machine virtuelle à l’aide de PowerShell

À l’aide des informations de la fonction de la section précédente, la commande suivante démarre la maintenance sur une machine virtuelle si **IsCustomerInitiatedMaintenanceAllowed** est défini sur true.

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```
 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment vous pouvez vous inscrire aux événements de maintenance à partir de la machine virtuelle à l’aide de [Événements planifiés](scheduled-events.md).