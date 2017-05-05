---
title: "Vérifier la connexion d’une passerelle VPN | Microsoft Docs"
description: "Cet article vous montre comment vérifier une connexion de passerelle VPN de réseau virtuel."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a67ea630cc8b5a3e9deab7733aa5cd2055949ec0
ms.lasthandoff: 04/27/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>Vérifier une connexion de passerelle VPN

Cet article vous montre comment vérifier votre connexion à une passerelle VPN dans le modèle de déploiement Resource Manager et le modèle de déploiement classique.

## <a name="verify-using-the-azure-portal"></a>Vérifier à l’aide du portail Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Vérifier à l’aide de PowerShell

Pour vérifier avec PowerShell, installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). Pour plus d'informations, consultez la page [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Connectez-vous à votre compte Azure
1. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```
2. Vérifiez les abonnements associés au compte.

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. Spécifiez l’abonnement que vous souhaitez utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>Vérifier votre connexion

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>Procéder à une vérification à l’aide de l’interface de ligne de commande Azure

Pour procéder à une vérification à l’aide de l’interface de ligne de commande Azure, installez la dernière version des commandes CLI (2.0 ou version ultérieure). Pour plus d’informations sur l’installation des commandes CLI, consultez l’article [Installer l’interface de ligne de commande Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="log-in-to-your-azure-account"></a>Connexion à votre compte Azure

1. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

  ```azurecli
  az login
  ```
2. Si vous disposez de plusieurs abonnements Azure, répertoriez les abonnements associés au compte.

  ```azurecli
  Az account list --all
  ```
3. Spécifiez l’abonnement que vous souhaitez utiliser.

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>Vérifier votre connexion

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Vérifier avec le portail Azure (classique)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Vérifier avec PowerShell (classique)
Pour vérifier avec PowerShell, installez les dernières versions des applets de commande Azure PowerShell. Veillez à télécharger et à installer les versions Resource Manager et Service Management (SM). Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). 

### <a name="log-in-to-your-azure-account"></a>Connectez-vous à votre compte Azure
1. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```
2. Vérifiez les abonnements associés au compte.

  ```powershell
  Get-AzureRmSubscription 
  ```
3. Spécifiez l’abonnement que vous souhaitez utiliser.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. Connectez-vous pour utiliser les applets de commande Service management pour le modèle de déploiement classique.

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>Vérifier votre connexion
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour connaître les différentes étapes.


