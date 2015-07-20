<properties 
	pageTitle="Association d'un disque à une machine virtuelle | Azure" 
	description="Découvrez comment attacher un disque à une machine virtuelle Azure et à l'initialiser pour le rendre opérationnel." 
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
	ms.date="04/24/2015" 
	ms.author="kathydav"/>

# Attachement d'un disque de données à une machine virtuelle Windows

Vous pouvez attacher des disques, qu’ils soient vides ou non. Dans les deux cas, les disques sont en fait des fichiers .vhd conservés dans un compte de stockage Azure. Après avoir attaché le disque, vous devrez également l'initialiser pour le rendre opérationnel.

> [AZURE.NOTE]Il est recommandé d'utiliser un ou plusieurs disques distincts pour stocker les données d'une machine virtuelle. Lorsque vous créez une machine virtuelle Azure, elle comporte un disque pour le système d’exploitation mappé au lecteur C et un disque temporaire mappé au lecteur D. **Ne stockez pas de données dans le lecteur D.** Comme son nom l’indique, il ne permet qu’un stockage temporaire. Il n'offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Initialisation d’un nouveau disque de données dans Windows Server

1. Connexion à la machine virtuelle. Pour connaître les instructions à suivre, consultez [Connexion à une machine virtuelle exécutant Windows Server][logon].

2. Une fois que vous êtes connecté à la machine virtuelle, ouvrez **Server Manager**. Dans le volet gauche, sélectionnez **Services de fichiers et de stockage**.

	![Ouvrir le gestionnaire de serveur](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Développez le menu et sélectionnez **Disques**.

4. La section **Disques** répertorie les disques 0, 1 et 2. Le disque 0 est le disque du système d’exploitation, le disque 1 est le disque temporaire (qui ne doit pas être utilisé pour le stockage des données), et le disque 2 est le disque de données que vous avez attaché à la machine virtuelle. Le disque de données a une capacité de 5 Go, conformément à ce que vous avez spécifié lorsque vous avez attaché le disque. Cliquez avec le bouton droit sur le disque 2, puis sélectionnez **Initialiser**.

5.	Vous êtes averti que toutes les données seront supprimées lors de l’initialisation du disque. Cliquez sur **Oui** pour accuser réception de l’avertissement et initialiser le disque. Cliquez de nouveau avec le bouton droit sur le disque 2, puis sélectionnez **Nouveau volume**.

6.	Parcourez les étapes de l’Assistant en acceptant les valeurs par défaut. Lorsque l’Assistant est terminé, la section **Volumes** répertorie le nouveau volume. Le disque est désormais en ligne et prêt à stocker des données.

	![Volume correctement initialisé](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]La taille de la machine virtuelle détermine le nombre de disques que vous pouvez attacher à celle-ci. Pour plus d’informations, consultez [Tailles des services cloud et des machines virtuelles](https://msdn.microsoft.com/library/azure/dn197896.aspx).

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=July15_HO2-->