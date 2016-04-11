<properties
	pageTitle="Configurer des points de terminaison sur une machine virtuelle Windows classique | Microsoft Azure"
	description="Apprenez à configurer des points de terminaison dans le portail Azure Classic pour permettre la communication avec une machine virtuelle Windows dans Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Comment configurer des points de terminaison sur une machine virtuelle Windows classique dans Azure



Toutes les machines virtuelles Windows créées dans Azure à l’aide du modèle de déploiement classique peuvent automatiquement communiquer à travers un canal réseau privé avec d’autres machines virtuelles dans le même service cloud ou réseau virtuel. Toutefois, les ordinateurs sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour diriger le trafic réseau entrant vers une machine virtuelle. Si vous souhaitez plus d’informations sur les points de terminaison sur des machines virtuelles Linux, consultez [Comment configurer des points de terminaison sur une machine virtuelle Linux classique dans Azure](virtual-machines-linux-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

Quand vous créez une machine virtuelle dans le portail Azure Classic, les points de terminaison courants, notamment ceux du Bureau à distance et de l’Accès distant Windows PowerShell, sont généralement créés pour vous automatiquement selon le système d’exploitation que vous choisissez. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle, ou ultérieurement si nécessaire.



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Étapes suivantes

* Pour configurer un point de terminaison de machine virtuelle à l’aide d’une applet de commande Azure PowerShell, consultez l’article [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).

* Pour gérer une liste de contrôle d’accès sur un point de terminaison à l’aide d’une applet de commande Azure PowerShell, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

* Si vous avez créé une machine virtuelle dans le modèle de déploiement Resource Manager, vous pouvez également utiliser Azure PowerShell pour [créer un équilibrage de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

<!---HONumber=AcomDC_0330_2016-->