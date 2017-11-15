---
title: "Exemple JSON Azure Policy - Références (SKU) de passerelles de réseau virtuel autorisées | Microsoft Docs"
description: "Cet exemple de stratégie JSON exige que les passerelles de réseau virtuel utilisent un type de passerelle et une référence (SKU) approuvés."
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: 
ms.topic: sample
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 10/30/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 3155e3c0c7bb7ea58fc7844cb9dbc950ba14b8bb
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2017
---
# <a name="allowed-virtual-network-gateway-skus"></a>Références (SKU) de passerelles de réseau virtuel autorisées

Cette stratégie exige que les passerelles de réseau virtuel utilisent un type de passerelle et une référence (SKU) approuvés. Vous spécifiez un tableau de références (SKU) approuvées et un tableau de types de passerelle approuvés.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Exemple de modèle

[!code-json[main](../../../policy-templates/samples/Network/virtual-network-gateway-skus/azurepolicy.json "Allowed Virtual Network Gateway SKUs")]


Vous pouvez déployer ce modèle en utilisant le [portail Azure](#deploy-with-the-portal), avec [PowerShell](#deploy-with-powershell) ou avec [Azure CLI](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Déployer avec le portail

[![Déploiement sur Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade)

## <a name="deploy-with-powershell"></a>Déployer avec PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]


````powershell
$definition = New-AzureRmPolicyDefinition -Name "virtual-network-gateway-skus" -DisplayName "Allowed Virtual Network Gateway SKUs" -description "This policy enables you to specify a set of virtual network gateway SKUs that your organization can deploy." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope> -PolicyDefinition $definition
$assignment
````

### <a name="clean-up-powershell-deployment"></a>Nettoyer un déploiement PowerShell

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]



````cli

az policy definition create --name 'virtual-network-gateway-skus' --display-name 'Allowed Virtual Network Gateway SKUs' --description 'This policy enables you to specify a set of virtual network gateway SKUs that your organization can deploy.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/virtual-network-gateway-skus/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "virtual-network-gateway-skus"

````

### <a name="clean-up-azure-cli-deployment"></a>Nettoyer un déploiement Azure CLI

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez trouver d’autres exemples de modèles Azure Policy dans [Modèles pour Azure Policy](../json-samples.md).
