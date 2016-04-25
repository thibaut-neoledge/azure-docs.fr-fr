<properties
	pageTitle="À propos des disques et des VHD pour les machines virtuelles Windows | Microsoft Azure"
	description="Découvrez les notions de base relatives aux disques et aux VHD pour les machines virtuelles Windows dans Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/10/2016"
	ms.author="cynthn"/>

# À propos des disques et des VHD pour les machines virtuelles Azure

Comme tout autre ordinateur, les machines virtuelles dans Azure utilisent des disques comme emplacement de stockage pour un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation Windows (dans le cas d’une machine virtuelle Windows) et un disque temporaire. Le disque de système d’exploitation est créé à partir d’une image. Le disque de système d’exploitation et l’image sont en fait des disques durs virtuels (VHD) stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer d’un ou plusieurs disques de données, également stockés sur les VHD. Cet article est également disponible pour les [machines virtuelles Linux](virtual-machines-linux-about-disks-vhds.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-about-disks-vhds](../../includes/virtual-machines-common-about-disks-vhds.md)]

## Étapes suivantes
-  [Capturer une machine virtuelle Windows](virtual-machines-windows-capture-image.md) pour pouvoir monter en charge votre déploiement de machine virtuelle.
-  [Télécharger une image de machine virtuelle Windows dans Azure](virtual-machines-windows-upload-image.md) à utiliser lors de la création d’une machine virtuelle.
-  [Changer la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md) pour que votre application utilise le lecteur D: pour les données.

<!---HONumber=AcomDC_0413_2016-->