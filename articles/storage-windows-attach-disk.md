<properties 
	pageTitle="Association d'un disque à une machine virtuelle | Azure" 
	description="Apprenez à associer un disque à une machine virtuelle Azure et initialisez-le afin qu'il soit prêt à l'utilisation." 
	services="virtual-machines, storage" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2015" 
	ms.author="kathydav"/>

#Association d'un disque de données à une machine virtuelle Windows

Vous pouvez attacher des disques, qu'ils soient vides ou non. Dans les deux cas, les disques sont en fait des fichiers .vhd conservés dans un compte de stockage Azure. De plus, après avoir attaché le disque, vous devrez l'initialiser pour qu'il soit prêt à être utilisé. 

> [AZURE.NOTE] Il est recommandé d'utiliser un ou plusieurs disques distincts pour stocker les données d'un ordinateur virtuel. Lorsque vous créez une machine virtuelle Azure, elle comporte un disque pour le système d'exploitation mappé au lecteur C et un disque temporaire mappé au lecteur D. **N'utilisez pas le lecteur D pour conserver des données.** Comme son nom l'indique, il n'offre qu'un stockage temporaire. Il n'offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure.

- [Procédure : Association d'un disque vide](#attachempty)
- [Procédure : Association d'un disque existant](#attachexisting)
- [Procédure : Initialisation d'un nouveau disque de données dans Windows Server](#initializeinWS)


[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinWS"></a>Procédure : Initialisation d'un nouveau disque de données dans Windows Server

1. Connexion à la machine virtuelle. Pour plus d'informations, consultez [Connexion à une machine virtuelle exécutant Windows Server][logon].

2. Une fois connecté, ouvrez **Server Manager**. Dans le volet gauche, développez l'entrée **Storage**, puis cliquez sur **Disk Management**.



	![Open Server Manager](./media/storage-windows-attach-disk/ServerManager.png)



3. Cliquez avec le bouton droit sur **Disque 2**, puis cliquez sur **Initialiser le disque** et sur **OK**.



	![Initialize the disk](./media/storage-windows-attach-disk/InitializeDisk.png)


4. Cliquez avec le bouton droit sur la zone d'allocation d'espace pour le disque 2, cliquez sur **New Simple Volume**, puis terminez l'Assistant en utilisant les valeurs par défaut.
 

	![Initialize the volume](./media/storage-windows-attach-disk/InitializeDiskVolume.png)


[logon]: ../virtual-machines-log-on-windows-server/



	Le disque est à présent accessible en ligne et prêt à être utilisé avec une nouvelle lettre de lecteur.



	![Volume successfully initialized](./media/storage-windows-attach-disk/InitializeSuccess.png)

> [AZURE.NOTE] Le nombre de disques que vous pouvez attacher à une machine virtuelle dépend de la taille de celle-ci. Par exemple, vous pouvez uniquement attacher 4 disques à l'A2 Standard, mais vous pouvez joindre 32 disques à la D14 Standard et 64 disques au G5 Standard. Vous pouvez trouver plus d'informations sur le nombre de disques pouvant être attachés, selon la taille de la machine virtuelle [ici](https://msdn.microsoft.com/fr-FR/library/azure/dn197896.aspx).





<!--HONumber=42-->
