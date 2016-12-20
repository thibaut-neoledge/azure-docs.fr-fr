---
title: "Création de modèles avec des extensions de machine virtuelle Windows | Microsoft Docs"
description: "En savoir plus sur la création de modèles Azure Resource Manager avec des extensions de machine virtuelle Windows"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 418dd1f7-ded8-45ab-9a5a-a59d245e2555
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: cc2e3c234ef9219d1b836771dbbc456d73a13ac8


---
# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Windows
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Dans Azure PowerShell, exécutez l’applet de commande Azure PowerShell suivante :

      Get-AzureVMAvailableExtension


Cette applet de commande renvoie le nom de l’éditeur, le nom de l’extension et la version, comme suit :

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Ces trois propriétés sont mappées respectivement à « publisher », « type » et « typeHandlerVersion » dans l’extrait du modèle ci-dessus.

> [!NOTE]
> Il est toujours recommandé d’utiliser la dernière version de l’extension pour obtenir les fonctionnalités les plus à jour.
> 
> 

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identification du schéma pour les paramètres de configuration de l’extension
L’étape suivante de la création du modèle d’extension consiste à identifier le format pour fournir les paramètres de configuration. Chaque extension prend en charge son propre ensemble de paramètres.

Pour voir un exemple de configuration pour les extensions Windows, consultez la page [Exemples d’extensions Windows](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Reportez-vous à ce qui suit pour obtenir un modèle totalement terminé avec des extensions de machine virtuelle.

[Extension de script personnalisé sur une machine virtuelle Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

Une fois le modèle créé, vous pouvez le déployer en utilisant Azure PowerShell.




<!--HONumber=Nov16_HO3-->


