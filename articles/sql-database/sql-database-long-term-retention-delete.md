---
title: "Suppression de la rétention des sauvegardes à long terme et du coffre Azure Recovery Services | Microsoft Docs"
description: "Aide-mémoire pour supprimer la rétention des sauvegardes à long terme et le coffre Azure Recovery Services"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/22/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: 5e5a868ece1fc661b0bf1a83de5a9a30e9852fb1


---
# <a name="delete-long-term-retention-backups"></a>Supprimer la rétention des sauvegardes à long terme

Dans cette procédure, vous allez apprendre à supprimer la rétention des sauvegardes à long terme et le coffre Azure Recovery Services.

>[!Warning]
>Vérifiez que vous voulez supprimer la rétention des sauvegardes à long terme avant d’exécuter ce code. Pour éviter des frais inutiles, cet extrait de code sert à nettoyer les sauvegardes et les coffres créés lors de l’apprentissage de l’utilisation de la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md) ou les sauvegardes et les coffres dont vous n’avez plus besoin.

## <a name="delete-long-term-retention-backups-using-powershell"></a>Supprimer la rétention des sauvegardes à long terme avec PowerShell

La suppression de la rétention des sauvegardes à long terme requiert la dernière version d’[Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) et utilise les applets de commande suivantes :

- [Get-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/get-azurermrecoveryservicesvault)
- [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem)
- [Disable-AzureRmRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/disable-azurermrecoveryservicesbackupprotection)
- [Unregister-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/unregister-azurermrecoveryservicesbackupcontainer)
- [Remove-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/remove-azurermrecoveryservicesvault)


Pour plus d’informations, consultez [Stockage des sauvegardes d’Azure SQL Database pendant 10 ans](sql-database-long-term-retention.md).

```
# Delete long-term backups
##########################

$resourceGroupName = "{resource-group-name}"
$recoveryServiceVaultName = "{vault-name}"

$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName -Name $recoveryServiceVaultName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

ForEach ($container in $containers)
{
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase

   ForEach ($item in $items)
   {
      # Remove the backups from the vault
      ###################################
      Disable-AzureRmRecoveryServicesBackupProtection -item $item -RemoveRecoveryPoints -ea SilentlyContinue
   }
   
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
# Delete the recovery services vault
####################################
Remove-AzureRmRecoveryServicesVault -Vault $vault
```



## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les sauvegardes automatiques générées par le service, consultez l’article relatif aux [sauvegardes automatiques](sql-database-automated-backups.md).
- Pour plus d’informations sur la rétention des sauvegardes à long terme, consultez l’article décrivant la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md).
- Pour plus d’informations sur la restauration à partir de sauvegardes, consultez l’article concernant la [restauration à l’aide de sauvegardes](sql-database-recovery-using-backups.md).


<!--HONumber=Jan17_HO2-->


