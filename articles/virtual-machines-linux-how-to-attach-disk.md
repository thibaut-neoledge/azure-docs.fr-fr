<properties
	pageTitle="Association d'un disque à une machine virtuelle exécutant Linux dans Azure"
	description="Découvrez comment attacher un disque à une machine virtuelle Azure et à l'initialiser pour le rendre opérationnel."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2015"
	ms.author="kathydav"/>

#Association d’un disque de données à une machine virtuelle Linux

Vous pouvez attacher des disques, qu'ils soient vides ou non. Dans les deux cas, les disques sont en fait des fichiers .vhd conservés dans un compte de stockage Azure. Après avoir attaché le disque, vous devrez également l'initialiser pour le rendre opérationnel.

> [AZURE.NOTE]Il est recommandé d'utiliser un ou plusieurs disques distincts pour stocker les données d'une machine virtuelle. Lorsque vous créez une machine virtuelle Azure, celle-ci possède un disque de système d'exploitation et un disque temporaire. **N’utilisez pas le disque temporaire pour stocker les données.** Comme son nom l’indique, il ne permet qu’un stockage temporaire. Il n'offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure. Le disque de ressources est habituellement géré par l’agent Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt** pour les images Ubuntu). Par contre, sous Linux, le disque de données peut être nommé `/dev/sdc` par le noyau. Si tel est le cas, il vous faudra partitionner, formater et monter cette ressource. Pour plus d’informations, consultez le [guide d’utilisation de l’agent Linux Azure](http://azure.microsoft.com/manage/linux/how-to-guides/linux-agent-guide/).

- [Association d’un disque vide](#attachempty)
- [Association d’un disque existant](#attachexisting)
- [Initialisation d’un nouveau disque de données dans Linux](#initializeinlinux)

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../includes/howto-attach-disk-windows-linux.md)]

##<a id="initializeinlinux"></a>Initialisation d’un nouveau disque de données dans Linux



1. Pour vous connecter à la machine virtuelle, suivez les étapes indiquées dans la rubrique [Connexion à une machine virtuelle exécutant Linux](logonlinux).



2. Dans la fenêtre SSH, tapez la commande suivante, puis entrez le mot de passe du compte créé pour gérer la machine virtuelle :

		# sudo grep SCSI /var/log/messages

	>[AZURE.NOTE]Pour les distributions Ubuntu récentes, vous devrez peut-être utiliser `sudo grep SCSI /var/log/syslog` car l’enregistrement vers `/var/log/messages` peut être désactivé par défaut.

	L’identificateur du dernier disque de données ajouté est disponible dans les messages qui s’affichent.



	![Obtenir les messages du disque](./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png)



3. Dans la fenêtre SSH, tapez la commande suivante pour créer un appareil, puis entrez le mot de passe du compte :

		# sudo fdisk /dev/sdc

	>[AZURE.NOTE]Dans cet exemple, vous devrez peut-être utiliser `sudo -i` avec certaines distributions, si /sbin ou /usr/sbin ne figurent pas dans votre `$PATH`.


4. Tapez **n** pour créer une partition.


	![Créer un appareil](./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png)

5. Tapez **p** pour définir la partition comme partition principale, tapez **1** pour la définir comme première partition, puis appuyez sur Entrée pour accepter la valeur par défaut du cylindre.


	![Créer une partition](./media/virtual-machines-linux-how-to-attach-disk/DiskCylinder.png)



6. Tapez **p** pour afficher les détails relatifs au disque faisant l’objet de la partition.


	![Répertorier les informations sur le disque](./media/virtual-machines-linux-how-to-attach-disk/DiskInfo.png)



7. Tapez **w** pour écrire les paramètres du disque.


	![Écrire les modifications apportées au disque](./media/virtual-machines-linux-how-to-attach-disk/DiskWrite.png)

8. Créez le système de fichiers sur la nouvelle partition. Par exemple, entrez la commande suivante, puis le mot de passe du compte :

		# sudo mkfs -t ext4 /dev/sdc1

	![Créer le système de fichiers](./media/virtual-machines-linux-how-to-attach-disk/DiskFileSystem.png)

	>[AZURE.NOTE]Notez que les systèmes SUSE Linux Enterprise 11 ne prennent en charge que l’accès en lecture seule aux systèmes de fichiers ext4. Pour ces systèmes, il est recommandé de formater le nouveau système de fichiers en ext3 plutôt que ext4.


9. Créez un répertoire pour monter le nouveau système de fichiers. Par exemple, entrez la commande suivante, puis le mot de passe du compte :

		# sudo mkdir /datadrive


10. Tapez la commande suivante pour monter le lecteur :

		# sudo mount /dev/sdc1 /datadrive

	Le disque de données est désormais prêt à être utilisé en tant que **/datadrive**.


11. Ajoutez le nouveau lecteur à /etc/fstab :

	Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier /etc/fstab. En outre, il est vivement recommandé d’utiliser l’UUID (identificateur global unique) dans /etc/fstab comme référence au lecteur, plutôt que le nom d’appareil uniquement (par exemple, /dev/sdc1). Pour rechercher l’UUID du nouveau lecteur, vous pouvez vous servir de l’utilitaire **blkid** :

		# sudo -i blkid

	Le résultat ressemble à ce qui suit :

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE]si vous ne modifiez pas correctement le fichier **/etc/fstab**, il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.

	Puis, ouvrez le fichier **/etc/fstab** dans un éditeur de texte. /etc/fstab est un fichier système. Vous devez donc utiliser `sudo` pour le modifier, par exemple :

		# sudo vi /etc/fstab

	Dans cet exemple, nous utilisons la valeur UUID pour le nouvel appareil **/dev/sdc1** créé lors des étapes précédentes et le point de montage **/datadrive**. Ajoutez la ligne suivante à la fin du fichier **/etc/fstab** :

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	Ou, sur les systèmes basés sur SUSE Linux, vous pouvez êtes amené à devoir utiliser un format légèrement différent :

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /   ext3   defaults   1   2

	Vous pouvez désormais vérifier si le système de fichiers est monté correctement en le démontant puis en le remontant, par exemple en utilisant l'exemple de point de montage `/datadrive` créé lors des étapes précédentes :

		# sudo umount /datadrive
		# sudo mount /datadrive

	Si la commande `mount` génère une erreur, vérifiez que la syntaxe utilisée dans le fichier /etc/fstab est correcte. Si des lecteurs de données ou partitions supplémentaires sont créés, vous devez également les ajouter séparément au fichier /etc/fstab.


	>[AZURE.NOTE]La suppression ultérieure d’un disque de données sans modifier fstab pourrait entraîner l’échec du démarrage de la machine virtuelle. S’il s’agit d’une occurrence courante, la plupart des distributions proposent les options fstab `nofail` et/ou `nobootwait` qui permettent à un système de démarrer même si le disque n’est pas monté au moment du démarrage. Pour plus d'informations sur ces paramètres, consultez la documentation de votre distribution.

[logonlinux]: virtual-machines-linux-how-to-log-on.md

<!---HONumber=58-->