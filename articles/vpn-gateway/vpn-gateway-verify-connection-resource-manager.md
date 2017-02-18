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
ms.date: 01/30/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b5bad71095e4b7e3b26df15780467526200ffa10
ms.openlocfilehash: 68d94a6402b1497f65c4d03fb987ba800e86c2a3


---
# <a name="verify-a-vpn-gateway-connection"></a>Vérifier une connexion de passerelle VPN
Vous pouvez vérifier votre connexion de passerelle VPN de réseau virtuel en utilisant le portail ou PowerShell. Cet article contient des étapes pour les modèles de déploiement classique et Resource Manager.

## <a name="verify-using-the-azure-portal"></a>Vérifier à l’aide du portail Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>Vérifier à l’aide de PowerShell

Pour vérifier avec PowerShell, installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Pour plus d'informations, consultez la page [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="log-in-to-your-azure-account"></a>Connectez-vous à votre compte Azure
1. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte.
   
        Login-AzureRmAccount
2. Vérifiez les abonnements associés au compte.
   
        Get-AzureRmSubscription 
3. Spécifiez l’abonnement que vous souhaitez utiliser.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="verify-your-connection"></a>Vérifier votre connexion

[!INCLUDE [Powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Vérifier avec le portail Azure (classique)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>Vérifier avec PowerShell (classique)
Pour vérifier avec PowerShell, installez les dernières versions des applets de commande Azure PowerShell. Veillez à télécharger et à installer les versions Resource Manager et Service Management (SM). Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). 

### <a name="log-in-to-your-azure-account"></a>Connectez-vous à votre compte Azure
1. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte.
   
        Login-AzureRmAccount
2. Vérifiez les abonnements associés au compte.
   
        Get-AzureRmSubscription 
3. Spécifiez l’abonnement que vous souhaitez utiliser.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Connectez-vous pour utiliser les applets de commande Service management pour le modèle de déploiement classique.

        Add-AzureAccount

### <a name="verify-your-connection"></a>Vérifier votre connexion
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour connaître les différentes étapes.




<!--HONumber=Jan17_HO5-->


