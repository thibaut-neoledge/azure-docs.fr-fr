<properties
	pageTitle="À propos des disques et des VHD | Microsoft Azure"
	description="Découvrez les notions de base relatives aux disques et aux VHD pour les machines virtuelles dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="cynthn"/>

# À propos des disques et des VHD pour les machines virtuelles Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement Resource Manager et le modèle de déploiement classique.

Toutes les machines virtuelles dans Azure sont configurées avec au moins deux disques lorsque vous créez la machine virtuelle : l’un est un disque de système d’exploitation et l’autre est un disque local temporaire, parfois appelé disque de ressources. Le disque de système d’exploitation est créé à partir d’une image. Le disque de système d’exploitation et l’image sont en fait des disques durs virtuels (VHD) stockés dans un compte de stockage Azure. Les machines virtuelles peuvent également disposer de disques de données, également stockés sur les VHD.

>[AZURE.WARNING]Ne stockez pas de données sur le disque temporaire. Il assure un stockage temporaire pour les applications et les processus et est destiné à stocker uniquement des données telles que les fichiers d’échange. Pour remapper ce disque pour une machine virtuelle Windows, consultez la page [Modifier la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-change-drive-letter.md).

## À propos des disques

Comme tout autre ordinateur, les machines virtuelles dans Azure utilisent des disques comme emplacement de stockage pour un système d’exploitation, des applications et des données. Toutes les machines virtuelles Azure possèdent au moins deux disques : un disque de système d’exploitation et un disque temporaire. Elles peuvent également disposer d’un ou de plusieurs disques de données.

- **Disque de système d’exploitation** : chaque machine virtuelle dispose d’un disque de système d’exploitation attaché. Il est enregistré comme disque SATA et porte le nom de lecteur C:. Ce disque a une capacité maximale de 1 023 gigaoctets. Quand Azure crée un disque de système d’exploitation, trois copies du disque sont créées pour assurer une durabilité élevée. En outre, si vous configurez la machine virtuelle pour la géo-réplication, votre VHD est également répliqué sur différents sites distants de plus de 640 kilomètres.
- Le **disque temporaire** est automatiquement créé pour vous. Sur les machines virtuelles Windows, ce disque porte le nom de lecteur D:. Sur les machines virtuelles Linux, le disque se nomme généralement /dev/sdb et il est formaté et monté sur /mnt/resource par l’agent Linux Azure.
- Un **disque de données** est un VHD attaché à une machine virtuelle pour stocker des données d’application ou d’autres données que vous souhaitez conserver. Les disques de données sont enregistrés en tant que disques SCSI et sont nommés avec la lettre de votre choix. Chaque disque de données possède une capacité maximale de 1 023 gigaoctets. La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher et le type de stockage que vous pouvez utiliser pour héberger les disques.

	Pour plus d’informations sur les fonctionnalités des machines virtuelles, consultez la page [Tailles des machines virtuelles](virtual-machines-size-specs.md).

Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée un disque de système d’exploitation. Si vous utilisez une image incluant des disques de données, Azure crée également ces derniers lors de la création de la machine virtuelle. (Vous pouvez utiliser une image d’Azure, d’un partenaire ou vous appartenant.) Vous pouvez également ajouter des disques de données après avoir créé la machine virtuelle.

Vous pouvez ajouter des disques de données à une machine virtuelle à tout moment, en « attachant » le disque à la machine virtuelle. Vous pouvez utiliser un VHD que vous avez téléchargé ou copié sur votre compte de stockage ou qui a été créé par Azure pour vous. L’attachement d’un disque de données associe le fichier de VHD de votre compte de stockage à la machine virtuelle en plaçant un « bail » sur le VHD afin qu’il ne puisse pas être supprimé du stockage tant qu’il est attaché à une machine virtuelle.

## À propos des VHD

Les VHD utilisés dans Azure sont des fichiers .vhd stockés comme objets blob de pages dans un compte de stockage Standard ou Premium dans Azure. (Le stockage Premium est disponible dans certaines régions uniquement.) Pour des informations sur les objets blob de pages, consultez la page [Présentation des objets blob de blocs et des objets blob de pages](https://msdn.microsoft.com/library/ee691964.aspx). Pour des informations sur le stockage Premium, consultez la page [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage-premium-storage-preview-portal.md).

En dehors d'Azure, les disques durs virtuels peuvent utiliser le format VHD ou VHDX. Il est également possible de les corriger, de les étendre et de les différencier. Azure prend en charge les disques fixes au format VHD. Le format fixe définit linéairement le disque logique dans le fichier de façon que l'offset du disque X soit stocké à l'offset de l'objet blob X. Un petit pied de page à la fin de l'objet blob décrit les propriétés du disque dur virtuel. Souvent, le format fixe gaspille de l'espace du fait que la plupart des disques comportent de grandes plages inutilisées. Cependant, Azure stocke les fichiers .vhd dans un format fragmenté : vous profitez donc en même temps de disques fixes et dynamiques. Pour plus d’informations, consultez la page [Prise en main des disques durs virtuels](https://technet.microsoft.com/library/dd979539.aspx).

Dans Azure, tous les fichiers .vhd que vous souhaitez utiliser comme source pour créer des disques ou des images sont en lecture seule. Lorsque vous créez un disque ou une image, Azure copie les fichiers .vhd. Ces copies peuvent être accessibles en lecture seule et en lecture/écriture selon votre utilisation du VHD.

 Lorsque vous créez une machine virtuelle à partir d’une image, Azure crée, pour la machine virtuelle, un disque qui est une copie du fichier .vhd source. Pour vous protéger contre une suppression accidentelle, Azure place un bail sur tout fichier .vhd source utilisé pour créer une image, un disque de système d’exploitation ou un disque de données.

Avant de pouvoir supprimer un fichier .vhd source, vous devrez supprimer le bail en supprimant le disque ou l’image. Pour supprimer un fichier .vhd utilisé par une machine virtuelle comme disque de système d’exploitation, vous pouvez supprimer simultanément la machine virtuelle, le disque de système d’exploitation et le fichier .vhd source en supprimant la machine virtuelle et tous les disques associés. Toutefois, la suppression d’un fichier .vhd qui constitue une source pour un disque de données requiert l’exécution de plusieurs étapes dans un ordre défini : détacher le disque de la machine virtuelle, supprimer le disque, puis supprimer le fichier .vhd.

>[AZURE.WARNING]Si vous supprimez un fichier .vhd source d’un stockage ou que vous supprimez votre compte de stockage, Microsoft ne pourra pas récupérer ces données pour vous.

## Étapes suivantes

Machines virtuelles Linux :

-  [Attachement d’un disque et préparation pour l’utilisation](virtual-machines-linux-how-to-attach-disk.md)
-  [Capture d’une machine virtuelle Linux](virtual-machines-linux-capture-image.md)
-  [Détachement d’un disque](virtual-machines-linux-how-to-detach-disk.md)

Machines virtuelles Windows :

-  [Attachement d’un disque et préparation pour l’utilisation](storage-windows-attach-disk.md)
-  [Capture d’une machine virtuelle Windows](virtual-machines-capture-image-windows-server.md)
-  [Détachement d’un disque](storage-windows-detach-disk.md)

<!---HONumber=Sept15_HO4-->