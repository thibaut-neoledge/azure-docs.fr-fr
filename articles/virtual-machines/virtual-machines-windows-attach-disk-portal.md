<properties
	pageTitle="Attacher un disque de données à une machine virtuelle Windows | Microsoft Azure"
	description="Découvrez comment attacher un disque de données nouveau ou existant à une machine virtuelle Windows dans le portail Azure à l’aide du modèle de déploiement Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="cynthn"/>

# Attachement d’un disque de données à une machine virtuelle Windows dans le portail Azure

Cet article vous explique comment attacher des disques nouveaux et existants à une machine virtuelle Windows par le biais du portail Azure. Vous pouvez également [attacher un disque de données à une machine virtuelle Linux dans le portail Azure](virtual-machines-linux-attach-disk-portal.md). Avant cela, passez en revue les conseils suivants :

- La taille de la machine virtuelle détermine le nombre de disques de données que vous pouvez attacher . Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-windows-sizes.md).
- Pour utiliser le stockage de Premium, vous avez besoin d’une machine virtuelle de série DS ou GS. Vous pouvez utiliser des disques de comptes de stockage Premium et Standard avec ces machines virtuelles. Le stockage Premium est disponible dans certaines régions. Pour plus d’informations, voir l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md).
- Les disques attachés aux machines virtuelles sont en fait des fichiers .vhd dans un compte de stockage Azure. Pour en savoir plus, voir la section [À propos des disques et VHD pour machines virtuelles](virtual-machines-windows-about-disks-vhds.md).
- Pour un nouveau disque, vous n’avez pas besoin de le créer au préalable, car Azure le crée lorsque vous l’attachez.
- Pour un disque existant, le fichier .vhd doit être disponible dans un compte de stockage Azure. Vous pouvez utiliser un fichier .vhd présent, s’il n’est attaché à aucune autre machine virtuelle, ou charger votre propre fichier .vhd sur le compte de stockage.

[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a id="initializeinWS"></a>Procédure d’initialisation d’un nouveau disque de données dans Windows Server

1. Connectez-vous à la machine virtuelle. Pour plus d’informations, voir [Connexion à une machine virtuelle Azure exécutant Windows](virtual-machines-windows-connect-logon.md).

2. Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

	![Ouvrir le gestionnaire de serveur](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Développez le menu et sélectionnez **Disques**.

4. La section **Disques** répertorie les disques. Dans la plupart des cas, elle contient le disque 0, le disque 1 et le disque 2. Le disque 0 est le disque du système d'exploitation, le disque 1 est le disque temporaire et le disque 2 est le disque de données que vous venez d'attacher à la machine virtuelle. Le nouveau disque de données répertorie la Partition comme **Inconnue**. Cliquez avec le bouton droit sur le disque et sélectionnez **Initialiser**.

5.	Vous êtes averti que toutes les données seront supprimées lors de l’initialisation du disque. Cliquez sur **Oui** pour accuser réception de l'avertissement et initialiser le disque. Une fois l'opération terminée, la partition sera répertoriée comme **GPT**. Cliquez de nouveau avec le bouton droit sur le disque et sélectionnez **Nouveau volume**.

6.	Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Lorsque l'assistant est terminé, la section **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données.


	![Volume correctement initialisé](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] La taille de la machine virtuelle détermine le nombre de disques que vous pouvez attacher à celle-ci. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).


## Étapes suivantes

Si votre application doit utiliser le lecteur D: pour stocker des données, vous pouvez [changer la lettre de lecteur du disque temporaire Windows](virtual-machines-windows-classic-change-drive-letter.md).

<!---HONumber=AcomDC_0928_2016-->