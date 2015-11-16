<properties
	pageTitle="Attachez un disque à une machine virtuelle | Microsoft Azure"
	description="Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement classique et l'initialiser."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="cynthn"/>

# Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-attach-disk-preview.md).

Si vous avez besoin d'un disque de données supplémentaire, vous pouvez joindre un disque vide ou un disque existant avec des données à une machine virtuelle. Dans les deux cas, les disques sont en fait des fichiers .vhd qui doivent résider dans un compte de stockage Azure. Dans le cas d'un nouveau disque, après avoir joint le disque, vous devrez également l'initialiser afin qu'il soit prêt à être utilisé par une machine virtuelle Windows.

Il est recommandé d'utiliser un ou plusieurs disques distincts pour stocker les données d'une machine virtuelle. Quand vous créez une machine virtuelle Azure, celle-ci comporte un disque pour le système d'exploitation mappé au lecteur C et un disque temporaire mappé au lecteur D. **N'utilisez pas le disque temporaire pour stocker des données**. Comme son nom l'indique, le disque temporaire ne permet qu'un stockage temporaire. Il n’offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans Azure Storage.

## Vidéo de procédure pas à pas

Voici une procédure pas à pas de ce didacticiel.

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Procédure d’initialisation d’un nouveau disque de données dans Windows Server

1. Connectez-vous à la machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant Windows Server][logon].

2. Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

	![Ouvrir le gestionnaire de serveur](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Développez le menu et sélectionnez **Disques**.

4. La section **Disques** répertorie les disques. Dans la plupart des cas, elle contient le disque 0, le disque 1 et le disque 2. Le disque 0 est le disque du système d'exploitation, le disque 1 est le disque temporaire (qui ne doit pas être utilisé pour le stockage des données) et le disque 2 est le disque de données que vous venez d'attacher à la machine virtuelle. Le nouveau disque de données répertorie la Partition comme **Inconnue**. Cliquez avec le bouton droit sur le disque et sélectionnez **Initialiser**.

5.	Vous êtes averti que toutes les données seront supprimées lors de l’initialisation du disque. Cliquez sur **Oui** pour accuser réception de l'avertissement et initialiser le disque. Une fois l'opération terminée, la partition sera répertoriée comme **GPT**. Cliquez de nouveau avec le bouton droit sur le disque et sélectionnez **Nouveau volume**.

6.	Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Lorsque l'assistant est terminé, la section **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données.

	![Volume correctement initialisé](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]La taille de la machine virtuelle détermine le nombre de disques que vous pouvez attacher à celle-ci. Pour en savoir plus, consultez la rubrique [Tailles de machines virtuelles](virtual-machines-size-specs.md).

## Ressources supplémentaires

[Comment détacher un disque d’une machine virtuelle Windows](storage-windows-detach-disk.md)

[À propos des disques et VHD pour machines virtuelles](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=Nov15_HO2-->