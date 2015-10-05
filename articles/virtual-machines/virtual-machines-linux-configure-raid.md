<properties 
	pageTitle="Configuration logicielle de RAID sur une machine virtuelle exécutant Linux | Microsoft Azure" 
	description="Apprenez à utiliser mdadm pour configurer RAID sur Linux dans Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	writer="szark" 
	manager="timlt" 
	editor=""
	tag="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



# Configuration d’un RAID logiciel sur Linux
L'utilisation d'un RAID logiciel pour les machines virtuelles Linux sur Azure est un scénario fréquent afin de regrouper plusieurs disques de données attachés sous la forme d'un périphérique RAID unique. En règle générale, ce scénario permet d'optimiser les performances et d'améliorer le débit par rapport à l'utilisation d'un disque unique.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

## Disques de données attachés
En règle générale, au moins deux disques de données sont nécessaires pour configurer un périphérique RAID. Cet article n'abordera pas en détail la marche à suivre pour attacher des disques de données sur une machine virtuelle Linux. Consultez l’article Microsoft Azure [Attacher un disque](storage-windows-attach-disk.md#attachempty) pour obtenir des instructions détaillées sur la marche à suivre pour attacher un disque de données vide à une machine virtuelle Linux dans Azure.

>[AZURE.NOTE]La taille de machine virtuelle ExtraSmall ne prend pas en charge plus d'un disque de données attaché à la machine virtuelle. Consultez les [Tailles de machines virtuelles et services cloud pour Windows Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx) pour plus d'informations concernant les tailles de machines virtuelles et le nombre de disques de données pris en charge.


## Installation de l'utilitaire mdadm

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install mdadm

- **CentOS et Oracle Linux**

		# sudo yum install mdadm

- **SLES et openSUSE**

		# zypper install mdadm


## Création des partitions de disque
Dans cet exemple, nous allons créer une partition de disque unique sur /dev/sdc. La nouvelle partition de disque sera ensuite appelée /dev/sdc1.

- Démarrez fdisk pour lancer la création des partitions

		# sudo fdisk /dev/sdc
		Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
		Building a new DOS disklabel with disk identifier 0xa34cb70c.
		Changes will remain in memory only, until you decide to write them.
		After that, of course, the previous content won't be recoverable.

		WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
				 switch off the mode (command 'c') and change display units to
				 sectors (command 'u').

- Appuyez sur « n » à l'invite pour créer une **n**ouvelle partition :

		Command (m for help): n

- Ensuite, appuyez sur « p » à l'invite pour créer une partition **p**rincipale :

		Command action
			e   extended
			p   primary partition (1-4)
		p

- Appuyez sur « 1 » pour sélectionner la partition numéro 1 :

		Partition number (1-4): 1

- Sélectionnez le point de départ de la nouvelle partition ou appuyez tout simplement sur `<enter>` pour accepter le paramètre par défaut et placer la partition au début de l'espace libre sur le disque :

		First cylinder (1-1305, default 1):
		Using default value 1

- Sélectionnez la taille de la partition. Par exemple, tapez « +10G » pour créer une partition de 10 Go. Vous pouvez aussi appuyer simplement sur `<enter>` pour créer une seule partition pour l'intégralité du disque :

		Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
		Using default value 1305

- Ensuite, remplacez l'ID et le **t**ype de partition de l'ID par défaut « 83 » (Linux) par l'ID « fd » (raid auto Linux) :

		Command (m for help): t
		Selected partition 1
		Hex code (type L to list codes): fd

- Enfin, écrivez la table de partition sur le disque et quittez fdisk :

		Command (m for help): w
		The partition table has been altered!


## Création du contrôleur RAID

1. L'exemple suivant permet de créer un volume agrégé par bandes (RAID 0) de trois partitions sur trois disques de données distincts (sdc1, sdd1, sde1) :

		# sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
		  /dev/sdc1 /dev/sdd1 /dev/sde1

Dans cet exemple, après l'exécution de cette commande, un nouveau périphérique RAID dénommé **/dev/md127** sera créé. Notez que si les disques de données précédemment utilisés font partie d'un autre contrôleur RAID qui n'existe plus, il peut s'avérer nécessaire d'ajouter le paramètre `--force` à la commande `mdadm`.


2. Créez le système de fichiers sur le nouveau périphérique RAID.

	**CentOS, Oracle Linux, SLES 12, openSUSE et Ubuntu**

		# sudo mkfs -t ext4 /dev/md127

	**SLES 11**

		# sudo mkfs -t ext3 /dev/md127

3. **SLES 11 et openSUSE** : activez boot.md et créez mdadm.conf

		# sudo -i chkconfig --add boot.md
		# sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

	>[AZURE.NOTE]Un redémarrage peut être nécessaire après avoir apporté ces modifications sur des systèmes SUSE. Cette étape n’est *pas* requise pour SLES 12.


## Ajout du nouveau système de fichiers à /etc/fstab

**Attention** : si vous ne modifiez pas correctement le fichier /etc/fstab, il se peut que le système ne puisse plus démarrer. En cas de doute, reportez-vous à la documentation de la distribution pour obtenir des informations sur la modification adéquate de ce fichier. Il est par ailleurs vivement recommandé de créer une sauvegarde du fichier /etc/fstab avant de le modifier.

1. Créez le point de montage désiré pour le nouveau système de fichiers. Par exemple :

		# sudo mkdir /data

2. Lors de la modification du fichier /etc/fstab, l'**identificateur unique universel** doit être utilisé pour faire référence au système de fichiers plutôt qu'au nom de périphérique. Servez-vous de l'utilitaire `blkid` pour déterminer l'identificateur unique universel (UUID) du nouveau système de fichiers :

		# sudo /sbin/blkid
		...........
		/dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3. Ouvrez /etc/fstab dans un éditeur de texte, puis ajoutez une entrée pour le nouveau système de fichiers, par exemple :

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

	Sur **SLES 11 et openSUSE** :

		/dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

	Ensuite, enregistrez et fermez /etc/fstab.

4. Vérifiez si l'entrée /etc/fstab est correcte :

		# sudo mount -a

	Si cette commande génère un message d'erreur, vérifiez que la syntaxe utilisée dans le fichier /etc/fstab est correcte.

	Ensuite, exécutez la commande `mount` pour vérifier que le système de fichiers est monté :

		# mount
		.................
		/dev/md127 on /data type ext4 (rw)

5. (Facultatif) Paramètres de démarrage fiables

	**Configuration fstab**

	De nombreuses distributions comprennent les paramètres de montage `nobootwait` ou `nofail` pouvant être ajoutés au fichier /etc/fstab. Ces paramètres autorisent les échecs lors du montage d'un système de fichiers donné et permettent au système Linux de continuer à démarrer même s'il n'a pas été en mesure de monter le système de fichiers RAID. Pour plus d'informations sur ces paramètres, reportez-vous à la documentation de votre distribution.

	Exemple (Ubuntu) :

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

	**Paramètres de démarrage Linux**

	En plus des paramètres ci-dessus, le paramètre de noyau « `bootdegraded=true` » peut permettre au système de démarrer, même si le RAID est identifié comme endommagé ou détérioré, par exemple si un disque de données est supprimé par inadvertance de la machine virtuelle. Par défaut, cette situation peut également rendre impossible le démarrage du système.

	Pour plus d'informations sur la modification adéquate des paramètres de noyau, reportez-vous à la documentation de votre distribution. Par exemple, dans de nombreuses distributions (CentOS, Oracle Linux, SLES 11), ces paramètres peuvent être ajoutés manuellement au fichier « `/boot/grub/menu.lst` ». Sur Ubuntu, ce paramètre peut être ajouté à la variable `GRUB_CMDLINE_LINUX_DEFAULT` dans « /etc/default/grub ».

 

<!---HONumber=Sept15_HO4-->