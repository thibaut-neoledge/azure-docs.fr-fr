<properties
	pageTitle="Attacher un disque de données à une machine virtuelle Linux | Microsoft Azure"
	description="Découvrez comment attacher un disque de données nouveau ou existant à une machine virtuelle Linux dans le portail Azure à l’aide du modèle de déploiement Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="cynthn"/>

# Attachement d’un disque de données à une machine virtuelle Linux dans le portail Azure

Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Linux par le biais du portail Azure. Vous pouvez également [attacher un disque de données à une machine virtuelle Windows dans le portail Azure](virtual-machines-windows-attach-disk-portal.md). Avant cela, passez en revue les conseils suivants :

- La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).
- Pour utiliser le stockage de Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage-preview-portal.md).
- Les disques attachés aux machines virtuelles sont en fait des fichiers .vhd dans un compte de stockage Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md).
- Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.
- Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un fichier .vhd présent, s’il n’est attaché à aucune autre machine virtuelle, ou charger votre propre fichier .vhd sur le compte de stockage.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## Étapes suivantes

Une fois le disque ajouté, vous devez le préparer pour utilisation. Voir la section « Initialisation d’un nouveau disque de données dans Linux » dans cet [article](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux) pour le système d’exploitation de la machine virtuelle.

<!---HONumber=AcomDC_0330_2016-->