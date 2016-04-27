<properties
	pageTitle="À propos des disques et des VHD pour les machines virtuelles Linux | Microsoft Azure"
	description="Découvrez les notions de base relatives aux disques et aux VHD pour les machines virtuelles Linux dans Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# À propos des disques et des VHD pour les machines virtuelles Azure

Comme tout autre ordinateur, les machines virtuelles dans Azure utilisent des disques comme emplacement de stockage pour un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation Linux (dans le cas d’une machine virtuelle Linux) et un disque temporaire. Le disque de système d’exploitation est créé à partir d’une image. Le disque de système d’exploitation et l’image sont en fait des disques durs virtuels (VHD) stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer d’un ou plusieurs disques de données, également stockés sur les VHD. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Étapes suivantes

-  [Attacher un disque](virtual-machines-linux-attach-disk-portal.md) pour ajouter un stockage supplémentaire pour votre machine virtuelle.
-  [Configurer un RAID logiciel](virtual-machines-linux-configure-raid.md) pour la redondance.
-  [Capturer une machine virtuelle Linux](virtual-machines-linux-classic-capture-image.md) afin de déployer rapidement des machines virtuelles supplémentaires.

<!---HONumber=AcomDC_0413_2016-->