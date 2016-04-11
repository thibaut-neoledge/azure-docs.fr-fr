<properties
	pageTitle="Configurer des points de terminaison sur une machine virtuelle Linux classique | Microsoft Azure"
	description="Apprenez à configurer des points de terminaison dans le portail Azure Classic pour permettre la communication avec une machine virtuelle Linux dans Azure"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Comment configurer des points de terminaison sur une machine virtuelle Linux classique dans Azure

Toutes les machines virtuelles Linux créées dans Azure à l’aide du modèle de déploiement classique peuvent automatiquement communiquer sur un canal réseau privé avec d’autres machines virtuelles dans le même service cloud ou réseau virtuel. Toutefois, les ordinateurs sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour diriger le trafic réseau entrant vers une machine virtuelle. Si vous souhaitez plus d’informations sur les points de terminaison sur des machines virtuelles Windows, consultez [Comment configurer des points de terminaison sur une machine virtuelle Windows classique dans Azure](virtual-machines-windows-classic-setup-endpoints.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager

Lorsque vous créez une machine virtuelle Linux dans le portail Azure Classic, les points de terminaison communs tels que pour Secure Shell (SSH) sont généralement automatiquement créés pour vous. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle, ou ultérieurement si nécessaire.
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Étapes suivantes

* Vous pouvez également créer un point de terminaison de machine virtuelle à l'aide de l’interface de ligne en [mode Service Management](../virtual-machines-command-line-tools.md). Exécutez la commande **azure vm endpoint create**.

<!---HONumber=AcomDC_0330_2016-->