<properties
	pageTitle="Création d’une machine virtuelle personnalisée dans Azure"
	description="Apprenez à créer une machine virtuelle personnalisée dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="kathydav"/>

#Création d’une machine virtuelle personnalisée

Une machine virtuelle *personnalisée* correspond à une machine virtuelle que vous avez créée à l’aide de l’option **À partir de la galerie**, car elle propose davantage de possibilités de configuration que l’option **Création rapide**. Les choix sont les suivants :

- Connecter la machine virtuelle à un réseau virtuel
- Installer l’agent de machine virtuelle et les extensions de machine virtuelle Azure, telles que l’extension anti-programme malveillant
- Ajouter la machine virtuelle aux services cloud existants
- Ajouter la machine virtuelle à un compte de stockage existant
- Ajouter la machine virtuelle à un groupe à haute disponibilité

> [AZURE.IMPORTANT]Si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par le nom d'hôte ou configurer des connexions entre différents emplacements, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu’elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d'informations sur les réseaux virtuels, consultez la rubrique [Vue d'ensemble d'Azure Virtual Network](virtual-networks-overview.md).

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=August15_HO7-->