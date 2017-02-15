---
title: "Créer une image de machine virtuelle à partir d’une machine virtuelle Azure | Microsoft Docs"
description: "Apprenez à créer une image de machine virtuelle généralisée à partir d’une machine virtuelle Azure existante créée dans le modèle de déploiement Resource Manager"
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
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b6dffec166ffe8e04c5d7b701aef009bf7b72d45


---
# <a name="download-the-template-for-a-vm"></a>Télécharger le modèle d’une machine virtuelle
Lorsque vous créez une machine virtuelle dans Azure à l’aide du portail ou de PowerShell, un modèle Resource Manager est automatiquement créé pour vous. Vous pouvez utiliser ce modèle pour dupliquer rapidement un déploiement. Le modèle contient des informations sur toutes les ressources d’un groupe de ressources. Pour une machine virtuelle, cela signifie les conteneurs de modèles et tout ce qui est créé pour prendre en charge la machine virtuelle dans ce groupe de ressources, y compris les ressources réseau.

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

Le fichier .json est le modèle.

## <a name="download-the-template-using-powershell"></a>Télécharger le modèle à l’aide de PowerShell
Vous pouvez également télécharger le fichier de modèle .json à l’aide de l’applet de commande [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx). Vous pouvez utiliser le paramètre `-path` afin de fournir le nom et le chemin d’accès du fichier .json. Cet exemple montre comment télécharger le modèle pour le groupe de ressources nommé **myResourceGroup** vers le dossier **C:\users\public\downloads** sur votre ordinateur local.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur le déploiement des ressources à l’aide de modèles, consultez la page [Procédure pas à pas du modèle Resource Manager](../resource-manager-template-walkthrough.md).




<!--HONumber=Nov16_HO3-->


