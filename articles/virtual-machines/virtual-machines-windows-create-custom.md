<properties
	pageTitle="Création d’une machine virtuelle exécutant Windows | Microsoft Azure"
	description="Apprenez à créer une machine virtuelle personnalisée exécutant Windows dans Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#Création d’une machine virtuelle exécutant Windows dans Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Une machine virtuelle *personnalisée* correspond à une machine virtuelle que vous avez créée à l’aide de l’option **À partir de la galerie** car elle propose davantage de possibilités de configuration que l’option **Création rapide**. Les choix sont les suivants :

- Connecter la machine virtuelle à un réseau virtuel
- Installation de l'agent et des extensions de machine virtuelle, par exemple un logiciel anti-programme malveillant
- Ajouter la machine virtuelle à un service cloud existant
- Ajouter la machine virtuelle à un compte de stockage existant
- Ajouter la machine virtuelle à un groupe à haute disponibilité

> [AZURE.IMPORTANT]si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d’hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu’elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](virtual-networks-overview.md).

##Création de la machine virtuelle

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Oct15_HO3-->