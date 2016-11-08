---
title: Création de modèles avec des extensions de machine virtuelle Windows | Microsoft Docs
description: En savoir plus sur la création de modèles Azure Resource Manager avec des extensions de machine virtuelle Windows
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap

---
# Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Windows
[!INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Dans Azure PowerShell, exécutez l’applet de commande Azure PowerShell suivante :

      Get-AzureVMAvailableExtension


Cette applet de commande renvoie le nom de l’éditeur, le nom de l’extension et la version, comme suit :

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Ces trois propriétés sont mappées respectivement à « publisher », « type » et « typeHandlerVersion » dans l’extrait du modèle ci-dessus.

> [!NOTE]
> Il est toujours recommandé d’utiliser la dernière version de l’extension pour obtenir les fonctionnalités les plus à jour.
> 
> 

## Identification du schéma pour les paramètres de configuration de l’extension
L’étape suivante de la création du modèle d’extension consiste à identifier le format pour fournir les paramètres de configuration. Chaque extension prend en charge son propre ensemble de paramètres.

Pour voir un exemple de configuration pour les extensions Windows, consultez la page [Exemples de configuration d’extensions Windows](virtual-machines-windows-extensions-configuration-samples.md).

Reportez-vous à ce qui suit pour obtenir un modèle totalement terminé avec des extensions de machine virtuelle.

[Extension de script personnalisé sur une machine virtuelle Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

Une fois le modèle créé, vous pouvez le déployer en utilisant Azure PowerShell.

<!---HONumber=AcomDC_0601_2016-->