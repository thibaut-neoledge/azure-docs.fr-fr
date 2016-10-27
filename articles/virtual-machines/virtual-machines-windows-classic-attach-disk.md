<properties
    pageTitle="Attachez un disque à une machine virtuelle | Microsoft Azure"
    description="Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement classique et l'initialiser."
    services="virtual-machines-windows, storage"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="cynthn"/>


# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Attacher un disque de données à une machine virtuelle Windows créée avec le modèle de déploiement classique

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Pour utiliser le nouveau portail, consultez la rubrique [Attachement d’un disque de données à une machine virtuelle Windows dans le portail Azure](virtual-machines-windows-attach-disk-portal.md).

Si vous avez besoin d'un disque de données supplémentaire, vous pouvez joindre un disque vide ou un disque existant avec des données à une machine virtuelle. Dans les deux cas, les disques sont des fichiers .vhd conservés dans un compte de stockage Azure. Dans le cas d'un nouveau disque, après avoir joint le disque, vous devrez également l'initialiser afin qu'il soit prêt à être utilisé par une machine virtuelle Windows.

Pour en savoir plus sur les disques, consultez la section [À propos des disques et VHD pour machines virtuelles](virtual-machines-windows-about-disks-vhds.md).


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a name="initialize-the-disk"></a>Initialiser le disque

1. Connectez-vous à la machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle exécutant ][Windows Server].

2. Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

    ![Ouvrir le gestionnaire de serveur](./media/virtual-machines-windows-classic-attach-disk/fileandstorageservices.png)

3. Développez le menu et sélectionnez **Disques**.

4. La section **Disques** répertorie les disques. Dans la plupart des cas, elle contient le disque 0, le disque 1 et le disque 2. Le disque 0 est le disque du système d'exploitation, le disque 1 est le disque temporaire et le disque 2 est le disque de données que vous venez d'attacher à la machine virtuelle. Le nouveau disque de données répertorie la Partition comme **Inconnue**. Cliquez avec le bouton droit sur le disque et sélectionnez **Initialiser**.

5.  Vous êtes averti que toutes les données seront supprimées lors de l’initialisation du disque. Cliquez sur **Oui** pour accuser réception de l'avertissement et initialiser le disque. Une fois l'opération terminée, la partition sera répertoriée comme **GPT**. Cliquez de nouveau avec le bouton droit sur le disque et sélectionnez **Nouveau volume**.

6.  Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Lorsque l'assistant est terminé, la section **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données.

    ![Volume correctement initialisé](./media/virtual-machines-windows-classic-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] La taille de la machine virtuelle détermine le nombre de disques que vous pouvez attacher à celle-ci. Pour en savoir plus, voir la rubrique [Tailles de machines virtuelles](virtual-machines-linux-sizes.md).

## <a name="additional-resources"></a>Ressources supplémentaires

[Comment détacher un disque d’une machine virtuelle Windows](virtual-machines-windows-classic-detach-disk.md)

[À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md)

[ouverture de session]: virtual-machines-windows-classic-connect-logon.md



<!--HONumber=Oct16_HO2-->


