<properties urlDisplayName="Attach a disk" pageTitle="Association d'un disque &agrave; une machine virtuelle&nbsp;|&nbsp;Azure" metaKeywords="Azure Windows virtual machine, Azure attach disk, Azure initialize disk" description="Apprenez &agrave; associer un disque &agrave; une machine virtuelle&nbsp;Azure et initialisez-le afin qu'il soit pr&ecirc;t &agrave; l'utilisation." metaCanonical="" services="virtual-machines,storage" documentationCenter="" title="Association d&rsquo;un disque de donn&eacute;es avec une machine virtuelle" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav" />

# Association d'un disque de données à une machine virtuelle Windows

Vous pouvez attacher des disques, qu'ils soient vides ou non. Dans les deux cas, les disques sont en fait des fichiers .vhd conservés dans un compte de stockage Azure. De plus, après avoir attaché le disque, vous devrez l'initialiser pour qu'il soit prêt à être utilisé.

> [WACOM.NOTE] Il est recommandé d'utiliser un ou plusieurs disques distincts pour conserver les données d'une machine virtuelle. Lorsque vous créez une machine virtuelle Azure, elle comporte un disque pour le système d'exploitation mappé au lecteur C et un disque temporaire mappé au lecteur D. **Do not use the D drive to store data.** Comme son nom l'indique, il n'offre qu'un stockage temporaire. Il n'offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure.

-   [Association d'un disque vide][Association d'un disque vide]
-   [Association d'un disque existant][Association d'un disque existant]
-   [Initialisation d'un nouveau disque de données dans Windows Server][Initialisation d'un nouveau disque de données dans Windows Server]

[WACOM.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

## <span id="initializeinWS"></span></a> Initialisation d'un nouveau disque de données dans Windows Server

1.  Pour vous connecter à la machine virtuelle, suivez les étapes indiquées dans la rubrique [Connexion à une machine virtuelle exécutant Windows Server][Connexion à une machine virtuelle exécutant Windows Server].

2.  Une fois connecté, ouvrez **Server Manager**. Dans le volet gauche, développez l’entrée **Stockage**, puis cliquez sur **Disk Management**.

    ![Ouvrir le gestionnaire de serveur][Ouvrir le gestionnaire de serveur]

3.  Cliquez avec le bouton droit sur **Disque 2**, puis cliquez sur **Initialiser le disque** et sur **OK**.

    ![Initialiser le disque][Initialiser le disque]

4.  Cliquez avec le bouton droit sur la zone d’allocation d’espace pour le disque 2, cliquez sur **New Simple Volume**, puis terminez l’Assistant en utilisant les valeurs par défaut.

    ![Initialiser le volume][Initialiser le volume]

<!-- -->

    The disk is now online and ready to use with a new drive letter.



    ![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

  [Association d'un disque vide]: #attachempty
  [Association d'un disque existant]: #attachexisting
  [Initialisation d'un nouveau disque de données dans Windows Server]: #initializeinWS
  [Connexion à une machine virtuelle exécutant Windows Server]: ../virtual-machines-log-on-windows-server/
  [Ouvrir le gestionnaire de serveur]: ./media/storage-windows-attach-disk/ServerManager.png
  [Initialiser le disque]: ./media/storage-windows-attach-disk/InitializeDisk.png
  [Initialiser le volume]: ./media/storage-windows-attach-disk/InitializeDiskVolume.png
