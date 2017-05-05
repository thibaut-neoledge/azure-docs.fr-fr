---
title: "Gérer Azure Analysis Services avec PowerShell | Microsoft Docs"
description: "Gestion d’Azure Analysis Services avec PowerShell."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: dbe1dba78fb2597dc595f9e6895d0a15181c1f05
ms.lasthandoff: 04/27/2017


---

# <a name="manage-azure-analysis-services-with-powershell"></a>Gérer Azure Analysis Services avec PowerShell

Cet article décrit les applets de commande PowerShell permettant d’effectuer les tâches de gestion de base de données et de serveur Azure Analysis Services. 

Les tâches de gestion de serveur telles que la création ou la suppression d’un serveur, l’interruption ou la reprise des opérations du serveur ou la modification du niveau de service utilisent les applets de commande Azure Resource Manager (AzureRM). D’autres tâches de gestion des bases de données comme l’ajout ou la suppression de membres de rôle, le traitement ou le partitionnement utilisent les mêmes applets de commande du module [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que SQL Server Analysis Services.

## <a name="permissions"></a>Autorisations
La plupart des tâches PowerShell nécessitent que vous disposiez de privilèges d’administrateur sur le serveur Analysis Services que vous gérez. Les tâches PowerShell planifiées s’exécutent sans assistance. Le compte exécutant le planificateur doit disposer de privilèges d’administrateur sur le serveur Analysis Services. 

Pour les opérations de serveur utilisant des applets de commande AzureRm, votre compte ou le compte exécutant Scheduler doivent également appartenir au rôle Propriétaire associé à la ressource dans le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Opérations de serveur 
Les applets de commande Azure Analysis Services sont incluses dans le module de composant [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Pour installer les modules d’applet de commande AzureRM, consultez [Azure Resource Manager cmdlets](/powershell/azure/overview) (Applets de commande Azure Resource Manager) dans PowerShell Gallery.

|Applet de commande|Description| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Obtient les détails d’une instance de serveur.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Crée une instance de serveur.|
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Supprime une instance de serveur.|  
|[Suspend-AzureRmAnalysisServicesServe](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Interrompt une instance de serveur.| 
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Reprend une instance de serveur.|  
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifie une instance de serveur.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Teste l’existence d’une instance de serveur.| 

## <a name="database-operations"></a>Opérations de base de données
Les opérations de base de données Azure Analysis Services utilisent le même module [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que SQL Server Analysis Services. Toutefois, certaines applets de commande ne sont pas prises en charge par Azure Analysis Services. 

Le module SQLASCMDLETS fournit des applets de commande de gestion de base de données spécifiques à chaque tâche, ainsi que l’applet de commande Invoke-ASCmd à usage général, qui accepte un script ou une requête utilisant le langage de script de modèle tabulaire (TMSL). Les applets de commande suivantes du module SQLASCMDLETS sont prises en charge par Azure Analysis Services.
  
|Applet de commande|Description|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Ajoute un membre à un rôle de base de données.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Sauvegarde une base de données Analysis Services.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Supprime un membre d’un rôle de base de données.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Exécute un script TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Traite une base de données.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Traite une partition.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Traiter une table.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Fusionne une partition.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Restaurer une base de données Analysis Services.| 
  

## <a name="related-information"></a>Informations connexes
* [Scripts PowerShell dans Analysis Services](https://msdn.microsoft.com/library/hh213141.aspx).
* [Tabular Model Programming for Compatibility Level 1200](https://msdn.microsoft.com/library/mt712541.aspx) (Programmation de modèle tabulaire pour le niveau de compatibilité 1200)

