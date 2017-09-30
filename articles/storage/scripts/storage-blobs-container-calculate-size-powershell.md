---
title: "Exemple de script Azure PowerShell - Calculer la taille d’un conteneur d’objets blob | Microsoft Docs"
description: "Calculez la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles de chacun de ses objets blob."
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b78462fd182af52cb058e9a95a77c24656658767
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calculer la taille totale d’un conteneur de stockage d’objets blob

Ce script calcule la taille d’un conteneur dans le stockage d’objets blob Azure en effectuant le total des tailles des objets blob inclus dans le conteneur.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, le conteneur et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour calculer la taille du conteneur de stockage d’objets blob. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Obtient un compte de stockage spécifié ou tous les comptes de stockage dans un groupe de ressources ou l’abonnement. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Répertorie les objets blob inclus dans un conteneur. ||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour Stockage Azure](../blobs/storage-samples-blobs-powershell.md).

