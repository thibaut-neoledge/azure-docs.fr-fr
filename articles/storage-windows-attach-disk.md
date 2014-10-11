<properties linkid="manage-linux-howto-attach-a-disk" urlDisplayName="Attach a disk" pageTitle="Attach a disk to a virtual machine | Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Learn how to attach a data disk to an Azure virtual machine and initialize it so it's ready for use." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="How to Attach a Data Disk to a Virtual Machine" authors="kathydav" solutions="" manager="dongill" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav"></tags>

# Association d'un disque de données avec une machine virtuelle Windows

Vous pouvez associer des disques vides et des disques contenant des données. Dans les deux cas, les disques correspondent à des fichiers .vhd résidant dans un compte de stockage Azure et chaque disque doit être initialisé à l'issue de son association.

> [WACOM.NOTE] Il est d'usage d'utiliser un ou plusieurs disques distincts pour stocker les données d'une machine virtuelle. Lorsque vous créez une machine virtuelle Azure, celle-ci contient un disque pour le système d'exploitation mappé au lecteur C et un disque temporaire mappé au lecteur D. **N'utilisez pas le lecteur D pour stocker des données.** Comme son nom l'indique, celui-ci n'est qu'un emplacement de stockage temporaire. Il n'offre pas de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure.

-   [Association d'un disque vide][]
-   [Association d'un disque existant][]
-   [Initialisation d'un nouveau disque de données dans Windows Server][]

[WACOM.INCLUDE [howto-attach-disk-windows-linux][]]

## <span id="initializeinWS"></span></a> Initialisation d'un nouveau disque de données dans Windows Server

1.  Pour vous connecter à la machine virtuelle, suivez les étapes indiquées dans la rubrique [Connexion à une machine virtuelle exécutant Windows Server][].

2.  Une fois connecté, ouvrez **Server Manager**. Dans le volet gauche, développez l’entrée **Stockage**, puis cliquez sur **Disk Management**.

    ![Ouvrir le gestionnaire de serveur][]

3.  Cliquez avec le bouton droit sur **Disque 2**, puis cliquez sur **Initialiser le disque** et sur **OK**.

    ![Initialiser le disque][]

4.  Cliquez avec le bouton droit sur la zone d’allocation d’espace pour le disque 2, cliquez sur **New Simple Volume**, puis terminez l’Assistant en utilisant les valeurs par défaut.

    ![Initialiser le volume][]

    The disk is now online and ready to use with a new drive letter.

    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [Association d'un disque vide]: #attachempty
  [Association d'un disque existant]: #attachexisting
  [Initialisation d'un nouveau disque de données dans Windows Server]: #initializeinWS
  [howto-attach-disk-windows-linux]: ../includes/howto-attach-disk-windows-linux.md
  [Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
  [Ouvrir le gestionnaire de serveur]: ./media/storage-windows-attach-disk/ServerManager.png
  [Initialiser le disque]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [Initialiser le volume]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
