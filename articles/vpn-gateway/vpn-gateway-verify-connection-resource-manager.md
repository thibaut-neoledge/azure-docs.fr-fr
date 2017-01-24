---
title: "Vérifier la connexion à une passerelle | Microsoft Docs"
description: "Cet article vous montre comment vérifier une connexion à une passerelle dans le modèle de déploiement Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 3fe204c09eebf7d254a1bf2bb130e2d3498b6b45
ms.openlocfilehash: d1b6d12b1976b317e9ed496857439c86e99253f9


---
# <a name="verify-a-gateway-connection"></a>Vérification de la connexion à une passerelle
Vous pouvez vérifier votre connexion à la passerelle de différentes manières. Cet article vous montre comment vérifier l’état d’une connexion de passerelle Resource Manager à l’aide du portail Azure et à l’aide de PowerShell.

## <a name="verify-using-powershell"></a>Vérifier à l’aide de PowerShell
Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs). Pour plus d'informations, consultez la page [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1-log-in-to-your-azure-account"></a>Étape 1 : Se connecter au compte Azure
1. Ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte.
   
        Login-AzureRmAccount
2. Vérifiez les abonnements associés au compte.
   
        Get-AzureRmSubscription 
3. Spécifiez l’abonnement que vous souhaitez utiliser.
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>Étape 2 : Vérifier votre connexion
[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-portal"></a>Vérifier à l’aide du portail Azure
[!INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour connaître les différentes étapes.




<!--HONumber=Dec16_HO1-->


