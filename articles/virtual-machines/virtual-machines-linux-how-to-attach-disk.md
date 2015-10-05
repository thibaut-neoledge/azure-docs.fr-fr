<properties
	pageTitle="Attachement d’un disque à une machine virtuelle Linux | Microsoft Azure"
	description="Découvrez comment attacher un disque à une machine virtuelle Linux exécutée sur Azure et l'initialiser pour le rendre opérationnel."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="dkshir"/>

# Association d’un disque de données à une machine virtuelle Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de l’attachement d’un disque avec le modèle de déploiement classique.

Vous pouvez attacher des disques, qu'ils soient vides ou non. Dans les deux cas, les disques sont en fait des fichiers .vhd conservés dans un compte de stockage Azure. Après avoir attaché le disque, vous devrez également l'initialiser pour le rendre opérationnel. Cet article fait référence aux machines virtuelles créées à l'aide du modèle de déploiement classique.

> [AZURE.NOTE]Il est recommandé d'utiliser un ou plusieurs disques distincts pour stocker les données d'une machine virtuelle. Lorsque vous créez une machine virtuelle Azure, celle-ci possède un disque de système d'exploitation et un disque temporaire. **N’utilisez pas le disque temporaire pour stocker les données.** Comme son nom l’indique, il ne permet qu’un stockage temporaire. Il n'offre aucune possibilité de redondance ou de sauvegarde, car il ne réside pas dans le stockage Azure. Le disque de ressources est habituellement géré par l’agent Linux Azure et monté automatiquement dans **/mnt/resource** (ou **/mnt** pour les images Ubuntu). Par contre, un disque de données peut être nommé par le noyau Linux par exemple en `/dev/sdc`, et les utilisateurs devront partitionner, formater et monter cette ressource. Consultez le [Guide de l’utilisateur de l’Agent Linux Azure][Agent] pour plus d’informations.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## Initialisation d’un nouveau disque de données dans Linux

1. Connexion à la machine virtuelle. Pour plus d’informations, consultez la page [Connexion à une machine virtuelle exécutant Linux][Logon].



2. Ensuite, vous devez rechercher l'identificateur de périphérique pour initialiser le disque de données. Il existe deux façons d'effectuer cette opération :

	a) Dans la fenêtre SSH, tapez la commande suivante, puis entrez le mot de passe du compte créé pour gérer la machine virtuelle :

			$sudo grep SCSI /var/log/messages

	Pour les distributions Ubuntu récentes, vous devrez peut-être utiliser `sudo grep SCSI /var/log/syslog` car l’enregistrement vers `/var/log/messages` peut être désactivé par défaut.

	L’identificateur du dernier disque de données ajouté est disponible dans les messages qui s’affichent.

	![Obtenir les messages du disque](./media/virtual-machines-linux-how-to-attach-disk/DiskMessages.png)

	OU

	b) Utilisez la commande `lsscsi` pour rechercher l'ID de périphérique. `lsscsi` peut être installé par `yum install lsscsi` (dans des distributions Red Hat) ou `apt-get install lsscsi` (dans des distributions Debian). Vous pouvez retrouver le disque que vous recherchez par son _LUN_ ou **numéro d'unité logique**. Par exemple, le _LUN_ pour les disques attachés peuvent être vus facilement à partir de `azure vm disk list <virtual-machine>` en tant que :

			~$ azure vm disk list ubuntuVMasm
			info:    Executing command vm disk list
			+ Fetching disk images
			+ Getting virtual machines
			+ Getting VM disks
			data:    Lun  Size(GB)  Blob-Name                         OS
			data:    ---  --------  --------------------------------  -----
			data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
			data:    1    10        test.VHD
			data:    2    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
			info:    vm disk list command OK

	Effectuez une comparaison avec la sortie de `lsscsi` pour le même exemple de machine virtuelle :

			adminuser@ubuntuVMasm:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
			[5:0:0:2]    disk    Msft     Virtual Disk     1.0   /dev/sde

	Le dernier numéro du tuple dans chaque ligne est le _LUN_. Pour plus d'informations, consultez `man lsscsi`.

3. Dans la fenêtre SSH, tapez la commande suivante pour créer un appareil, puis entrez le mot de passe du compte :

		$sudo fdisk /dev/sdc

	>[AZURE.NOTE]Dans cet exemple, vous devrez peut-être utiliser `sudo -i` avec certaines distributions, si /sbin ou /usr/sbin ne figurent pas dans votre `$PATH`.


4. Lorsque vous y êtes invité, tapez **n** pour créer une partition.


	![Créer un appareil](./media/virtual-machines-linux-how-to-attach-disk/DiskPartition.png)

5. Lorsque vous y êtes invité, tapez **p** pour définir la partition comme partition principale, tapez **1** pour la définir comme première partition, puis appuyez sur Entrée pour accepter la valeur par défaut du cylindre.


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

## Ressources supplémentaires
[Connexion à une machine virtuelle exécutant Linux][Logon]

[Détachement d'un disque d'une machine virtuelle Linux](virtual-machines-linux-how-to-detach-disk.md)

[Utilisation de l'interface de ligne de commande Azure avec l’API de gestion des services](virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-how-to-log-on.md

<!---HONumber=Sept15_HO4-->