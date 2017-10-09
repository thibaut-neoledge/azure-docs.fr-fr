---
title: "Script PowerShell : copier des données dans le cloud à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Ce script PowerShell copie des données d’un emplacement dans un Stockage Blob Azure vers un autre dans le même Stockage Blob Azure."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ad97a40c5c12aa5a8f69a8d56750bd6f39725be0
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Utiliser PowerShell pour créer un pipeline de fabrique de données afin de copier des données dans le cloud

Cet exemple de script PowerShell crée un compte d’API Azure Cosmos DB. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exemple de script

> [!IMPORTANT]
> Ce script crée des fichiers JSON qui définissent des entités Data Factory (service lié, jeu de données et pipeline) sur votre disque dur dans le dossier c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Après avoir exécuté l’exemple de script, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources et toutes les ressources associées :

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```
Pour supprimer la fabrique de données du groupe de ressources, exécutez la commande suivante : 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<data factory name>" -ResourceGroupName "<resource group name>"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes : 

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Créer une fabrique de données. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Crée un service lié dans la fabrique de données. Un service lié rattache une banque de données ou une ressource de calcul à une fabrique de données. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Crée un jeu de données dans la fabrique de données. Un jeu de données représente les entrées/sorties pour une activité dans un pipeline. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Crée un pipeline dans la fabrique de données. Un pipeline contient une ou plusieurs activités effectuant une opération donnée. Dans ce pipeline, l’activité de copie a pour effet de copier les données d’un emplacement vers un autre dans le Stockage Blob Azure. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Crée une exécution pour le pipeline. En d’autres termes, exécute le pipeline. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtient les détails de l’exécution de l’activité dans le pipeline. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts Azure Cosmos DB PowerShell sur la page [Scripts PowerShell Azure Cosmos DB](../samples-powershell.md).
