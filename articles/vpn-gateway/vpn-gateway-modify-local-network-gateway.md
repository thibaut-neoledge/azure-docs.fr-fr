---
title: "Modifier des préfixes d’adresses IP de passerelle de réseau local et des adresses IP de passerelle | Microsoft Docs"
description: "Cet article vous guide dans la modification des préfixes d’adresses IP de la passerelle de réseau local"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e88511058b1de79ca222d87ada6c9abdf4daf11c


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modification des paramètres de passerelle de réseau local à l’aide de PowerShell
Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou GatewayIPAddress changent. Les instructions ci-dessous vous permettent de modifier vos paramètres de passerelle de réseau local. Vous pouvez également modifier ces paramètres dans le portail Azure.

## <a name="before-you-begin"></a>Avant de commencer
Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) .

## <a name="to-modify-ip-address-prefixes"></a>Pour modifier des préfixes d’adresse IP
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>Pour modifier l'adresse IP de la passerelle
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).




<!--HONumber=Nov16_HO3-->


