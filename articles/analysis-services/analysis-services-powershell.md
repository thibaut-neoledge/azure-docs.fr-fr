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
ms.date: 01/24/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: ef3f31c633eeba92f343e2126626bd029aebbf64
ms.openlocfilehash: 170657601a0ea6b0c0ebabfd34befdce290cebd8


---

# <a name="manage-azure-analysis-services-with-powershell"></a>Gérer Azure Analysis Services avec PowerShell

Cet article décrit les applets de commande PowerShell permettant d’effectuer les tâches de gestion de base de données et de serveur Azure Analysis Services. 

Les tâches de gestion de serveur telles que la création ou la suppression d’un serveur, l’interruption ou la reprise des opérations du serveur ou la modification du niveau de service utilisent les applets de commande Azure Resource Manager (AzureRM). D’autres tâches de gestion des bases de données comme l’ajout ou la suppression de membres de rôle, le traitement ou le partitionnement utilisent les mêmes applets de commande du module [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que SQL Server Analysis Services.

## <a name="permissions"></a>Autorisations
La plupart des tâches PowerShell nécessitent que vous disposiez de privilèges d’administrateur sur le serveur Analysis Services que vous gérez. Les tâches PowerShell planifiées s’exécutent sans assistance. Le compte exécutant le planificateur doit disposer de privilèges d’administrateur sur le serveur Analysis Services. 

Pour les opérations de serveur utilisant des applets de commande AzureRm, votre compte ou le compte exécutant Scheduler doivent également appartenir au rôle Propriétaire associé à la ressource dans le [contrôle d’accès en fonction du rôle (RBAC) d’Azure](../active-directory/role-based-access-control-what-is.md). 

## <a name="server-operations"></a>Opérations de serveur 
Les applets de commande Azure Analysis Services sont incluses dans le module de composant [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Pour installer les modules d’applet de commande AzureRM, consultez [Azure Resource Manager cmdlets](https://docs.microsoft.com/powershell/resourcemanager/) (Applets de commande Azure Resource Manager) dans PowerShell Gallery.

|Applet de commande|Description| 
|------------|-----------------| 
|Get-AzureRmAnalysisServicesServer|Obtient les détails d’une instance de serveur.|  
|New-AzureRmAnalysisServicesServer|Crée une instance de serveur.|
|Remove-AzureRmAnalysisServicesServer|Supprime une instance de serveur.|  
|Suspend-AzureRmAnalysisServicesServe|Interrompt une instance de serveur.| 
|Resume-AzureRmAnalysisServicesServer|Reprend une instance de serveur.|  
|Set-AzureRmAnalysisServicesServer|Modifie une instance de serveur.|   
|Test-AzureRmAnalysisServicesServer|Teste l’existence d’une instance de serveur.| 

## <a name="database-operations"></a>Opérations de base de données
Les opérations de base de données Azure Analysis Services utilisent le même module [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) que SQL Server Analysis Services. Toutefois, certaines applets de commande ne sont pas prises en charge par la version préliminaire d’Azure Analysis Services. 

Le module SQLASCMDLETS fournit des applets de commande de gestion de base de données spécifiques à chaque tâche, ainsi que l’applet de commande Invoke-ASCmd à usage général, qui accepte un script ou une requête utilisant le langage de script de modèle tabulaire (TMSL). Les applets de commande suivantes du module SQLASCMDLETS sont prises en charge par la version préliminaire d’Azure Analysis Services.
  
|Applet de commande|Description|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Ajoute un membre à un rôle de base de données.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Supprime un membre d’un rôle de base de données.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Exécute un script TMSL.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Traite une base de données.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Traite une partition.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Traiter une table.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Fusionne une partition.|  
  

## <a name="related-information"></a>Informations connexes
* [Scripts PowerShell dans Analysis Services](https://msdn.microsoft.com/library/hh213141.aspx).
* [Tabular Model Programming for Compatibility Level 1200](https://msdn.microsoft.com/library/mt712541.aspx) (Programmation de modèle tabulaire pour le niveau de compatibilité 1200)


<!--HONumber=Jan17_HO5-->


