---
title: "Téléchargement du modèle d’une machine virtuelle Azure | Microsoft Docs"
description: "Téléchargez un modèle de machine virtuelle pour faciliter l’automatisation de déploiements dans le modèle de déploiement Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 9e4c0c3cf0e233447369a24b1d5fe27495abd1cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="download-the-template-for-a-vm"></a>Télécharger le modèle d’une machine virtuelle
Lorsque vous créez une machine virtuelle dans Azure à l’aide du portail ou de PowerShell, un modèle Resource Manager est automatiquement créé pour vous. Vous pouvez utiliser ce modèle pour dupliquer rapidement un déploiement. Le modèle contient des informations sur toutes les ressources d’un groupe de ressources. Pour une machine virtuelle, cela signifie que le modèle contient tout ce qui est créé pour prendre en charge la machine virtuelle dans ce groupe de ressources, notamment les ressources réseau.

## <a name="download-the-template-using-the-portal"></a>Télécharger le modèle à l’aide du portail
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le menu hub, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle dans la liste.
4. Sélectionnez **Script Automation**.
5. Sélectionnez **Télécharger** et enregistrez le fichier .zip sur votre ordinateur local.
6. Ouvrez le fichier .zip et extrayez les fichiers dans un dossier. Le fichier .zip contient :
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

Le fichier template.json est le modèle.

## <a name="download-the-template-using-powershell"></a>Télécharger le modèle à l’aide de PowerShell
Vous pouvez également télécharger le fichier de modèle .json à l’aide de l’applet de commande [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx). Vous pouvez utiliser le paramètre `-path` afin de fournir le nom et le chemin d’accès du fichier .json. Cet exemple montre comment télécharger le modèle pour le groupe de ressources nommé **myResourceGroup** vers le dossier **C:\users\public\downloads** sur votre ordinateur local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le déploiement des ressources à l’aide de modèles, consultez la page [Procédure pas à pas du modèle Resource Manager](../../azure-resource-manager/resource-manager-template-walkthrough.md).

