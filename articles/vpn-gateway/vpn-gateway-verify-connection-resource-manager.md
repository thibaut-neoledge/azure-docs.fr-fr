<properties
   pageTitle="Vérification de la connexion à une passerelle | Microsoft Azure"
   description="Cet article vous montre comment vérifier une connexion à une passerelle dans le modèle de déploiement Resource Manager."
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
   ms.date="08/03/2016"
   ms.author="cherylmc"/>

# Vérification de la connexion à une passerelle

Vous pouvez vérifier votre connexion à la passerelle de différentes manières. Cet article vous montre comment vérifier l’état d’une connexion de passerelle Resource Manager à l’aide du portail Azure et à l’aide de PowerShell.


## Avant de commencer

Si vous prévoyez d’utiliser PowerShell, vous devrez installer la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Pour plus d'informations, consultez la page [Utilisation d’Azure PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

1. Ouvrez la console PowerShell et connectez-vous à votre compte.

		Login-AzureRmAccount

2. Vérifiez les abonnements associés au compte.

		Get-AzureRmSubscription 

3. Spécifiez l’abonnement que vous souhaitez utiliser.

		Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## Vérification de votre connexion


[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]


## Étapes suivantes

- Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour connaître les différentes étapes.

<!---HONumber=AcomDC_0810_2016-->