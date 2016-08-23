<properties
   pageTitle="Modifier des préfixes d’adresses IP de passerelle de réseau local et l’IP de la passerelle | Microsoft Azure"
   description="Cet article vous guide dans la modification des préfixes d’adresses IP de la passerelle de réseau local"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# Modification des paramètres de passerelle de réseau local à l’aide de PowerShell

Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou GatewayIPAddress changent. Les instructions ci-dessous vous permettent de modifier vos paramètres de passerelle de réseau local. Vous pouvez également modifier ces paramètres dans le portail Azure.

## Avant de commencer
	
Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## Pour modifier des préfixes d’adresse IP

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## Pour modifier l'adresse IP de la passerelle

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## Étapes suivantes

Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).

<!---HONumber=AcomDC_0810_2016-->