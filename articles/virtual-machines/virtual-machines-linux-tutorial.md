<properties
	pageTitle="Création d'une machine virtuelle exécutant Linux | Microsoft Azure"
	description="Découvrez comment créer une machine virtuelle Azure exécutant Linux ou Ubuntu en utilisant une image provenant d’Azure et l’interface de ligne de commande Azure."
	keywords="machine virtuelle linux, linux machine virtuelle, Azure Linux,azure ubuntu" 
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/21/2015"
	ms.author="rasquill"/>

# Création d’une machine virtuelle exécutant Linux

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [Azure preview portal](virtual-machines-linux-tutorial-portal-rm.md)

<br> Il est simple de créer une machine virtuelle Azure exécutant Linux à partir de la ligne de commande ou du portail. Ce didacticiel vous explique comment utiliser l’interface de ligne de commande Azure pour Mac, Linux et Windows afin de créer rapidement une machine virtuelle de serveur Ubuntu s’exécutant dans Azure, de procéder à la connexion via **ssh**, et enfin de créer et de monter un nouveau disque. Dans cette rubrique, une machine virtuelle de serveur Ubuntu est utilisée, mais vous pouvez également créer des machines virtuelles Linux en utilisant vos [propres images en tant que modèles](virtual-machines-linux-create-upload-vhd.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Vidéo de procédure pas à pas

Voici une procédure pas à pas de ce didacticiel.

[AZURE.VIDEO building-a-linux-virtual-machine-tutorial]

## Installer l’interface de ligne de commande Microsoft Azure

Il vous faut dans un premier temps [installer l’interface de ligne de commande Microsoft Azure](../xplat-cli-install.md).

Parfait. Maintenant, pour vous assurer d’être dans le mode Resource Manager, tapez `azure config mode arm`.

Encore mieux. À présent, [connectez-vous avec votre ID professionnel ou scolaire](../xplat-cli-connect.md#use-the-log-in-method) en tapant `azure login`, puis en suivant les invites pour une expérience de connexion interactive à votre compte Azure.

> [AZURE.NOTE]Si vous disposez d’un ID professionnel ou scolaire et que vous savez que l’authentification à deux facteurs n’est pas activée, vous pouvez utiliser `azure login -u` avec l’ID professionnel ou scolaire pour vous connecter sans session interactive. Si vous ne disposez pas d’un ID professionnel ou scolaire, vous pouvez [créer un ID professionnel ou scolaire à partir de votre compte Microsoft personnel](resource-group-create-work-id-from-personal.md).

## Créer une machine virtuelle Azure.

Saisissez `azure group create <my-group-name> westus` en remplaçant _&lt;nom-de-mon-groupe&gt;_ par une désignation personnelle (au besoin, utilisez une région différente). Le résultat suivant pourrait s’afficher :

	azure group create myuniquegroupname westus
	info:    Executing command group create
	+ Getting resource group myuniquegroupname
	+ Creating resource group myuniquegroupname
	info:    Created resource group myuniquegroupname
	data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
	data:    Name:                myuniquegroupname
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags:
	data:
	info:    group create command OK

Créez maintenant votre machine virtuelle en saisissant `azure vm quick-create` ; des messages vous inviteront à entrer les paramètres restants. Utilisez le nom du groupe de ressources que vous venez de créer, ci-dessus, et pour la valeur **ImageURN**, saisissez `canonical:ubuntuserver:14.04.2-LTS:latest`. Votre expérience devrait ressembler à ce qui suit. Notez que la commande `azure vm quick-create` vous invite à saisir des informations de base nécessaires pour créer, héberger et se connecter à une machine virtuelle Linux, notamment :

- le nom du groupe de ressources et le nom de la machine virtuelle
- un emplacement de déploiement
- le type de système d’exploitation et l’image de chaîne URN
- un nom d’utilisateur et un mot de passe

Ensuite, cette commande crée l’infrastructure nécessaire pour héberger la machine virtuelle, notamment :

- un compte de stockage Azure pour le stockage de disques durs virtuels et de disques supplémentaires
- une carte d’interface réseau pour la machine virtuelle
- un réseau virtuel avec un sous-réseau
- une adresse IP publique
- un sous-domaine

	informations d’aperçu rapide de la machine virtuelle azure : Exécution de la commande d’aperçu rapide de la machine virtuelle Nom du groupe de ressources : myuniquegroupname Nom de la machine virtuelle : myuniquevmname Nom de l’emplacement : westus Type de système d’exploitation [Windows, Linux] : Linux ImageURN (format : "publisherName:offer:skus:version") : canonical:ubuntuserver:14.04.2-LTS:latest Nom d’utilisateur : ops Mot de passe : ********* Confirmer le mot de passe : ********* + Recherche des informations "myuniquevmname" de la machine virtuelle : Utilisation des informations de taille de machine virtuelle "Standard\_D1" : La configuration du disque ou de l’image [Système d’exploitation, données] requiert un compte de stockage + Récupération des informations de compte de stockage : comptes de stockage introuvables dans la région "westus", tentative de création d’un compte de stockage + Création d’un compte de stockage "cli3c0464f24f1bf4f014323" dans "westus" + Recherche du compte de stockage cli3c0464f24f1bf4f014323 + Recherche des informations de la carte d’interface réseau "myuni-westu-1432328437727-nic" : carte d’interface réseau nommée "myuni-westu-1432328437727-nic" introuvable, création d’une carte + Recherche des informations de réseau virtuel "myuni-westu-1432328437727-vnet" : Préparation à la création d’un réseau virtuel et d’un sous-réseau / Création d’un réseau virtuel "myuni-westu-1432328437727-vnet" [préfixe de l’adresse : "10.0.0.0/16"] avec sous-réseau "myuni-westu-1432328437727-snet"+[préfixe de l’adresse : "10.0.1.0/24"] + Recherche du réseau virtuel "myuni-westu-1432328437727-vnet" + Recherche du sous-réseau "myuni-westu-1432328437727-snet" sous les informations du réseau virtuel "myuni-westu-1432328437727-vnet" : Détection de paramètres d’adresses IP publiques, tentative de configuration d’un profil d’adresse IP publique + Recherche des informations d’adresse IP publique "myuni-westu-1432328437727-pip" : Adresse IP publique nommée "myuni-westu-1432328437727-pip" introuvable, création d’une adresse + Création d’une adresse IP publique "myuni-westu-1432328437727-pip" + Recherche de l’adresse IP publique "myuni-westu-1432328437727-pip" + Création d’une carte d’interface réseau "myuni-westu-1432328437727-nic" + Recherche de la carte d’interface réseau "myuni-westu-1432328437727-nic" + Création de la machine virtuelle "myuniquevmname" + Recherche de la machine virtuelle "myuniquevmname" + Recherche de la carte d’interface réseau "myuni-westu-1432328437727-nic" + Recherche des données de l’adresse IP publique "myuni-westu-1432328437727-pip" : ID :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname data: ProvisioningState : Réussi data: Nom : myuniquevmname data: Emplacement : westus data: FQDN : myuni-westu-1432328437727-pip.westus.cloudapp.azure.com data: Type : Microsoft.Compute/virtualMachines data: data: Profil matériel : data: Taille : Standard\_D1 data: data: Profil de stockage : data: Référence à l’image : data: Éditeur :canonical data: Offre : ubuntuserver data: SKU : 14.04.2-LTS data: Version : la plus récente data: data: Disque du système d’exploitation : data: Type du système d’exploitation : Linux data: Nom : cli3c0464f24f1bf4f0-os-1432328438224 data: Mise en cache : ReadWrite data: CreateOption : FromImage data: Vhd : data: Uri : https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd data: data: Profil du système d’exploitation : data: Nom de l’ordinateur : myuniquevmname data: Nom d’utilisateur : ops data: Configuration Linux : data: Désactiver l’authentification par mot de passe : false data: data: Profil réseau : data: Interfaces réseau : data: Interface réseau 1 : data: Id :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic data: Principale :true data: Adresse MAC :00-0D-3A-31-55-31 data: État d’approvisionnement : Réussi : Nom :myuni-westu-1432328437727-nic data: Emplacement : westus data: Adresse IP privée alloc-method : Dynamique data: Adresse IP privée : 10.0.1.4 data: Adresse IP publique : 191.239.51.1 data: FQDN : myuni-westu-1432328437727-pip.westus.cloudapp.azure.com info: commande d’aperçu rapide de la machine virtuelle OK

Votre machine virtuelle est en cours d’exécution. Il vous suffit désormais de vous connecter.

## Se connecter à la machine virtuelle Linux

Sur les machines virtuelles Linux, vous vous connectez généralement via **ssh**.

> [AZURE.NOTE]Dans cette rubrique, la connexion à la machine virtuelle est effectuée à l’aide de noms d’utilisateurs et de mots de passe. Pour utiliser des paires de clés publiques et privées pour interagir avec votre machine virtuelle, consultez la page [Utilisation de SSH avec Linux sur Azure](virtual-machines-linux-use-ssh-key.md). Vous pouvez modifier la connectivité **SSH** des machines virtuelles créées avec la commande `azure vm quick-create` à l’aide de la commande `azure vm reset-access` pour complètement réinitialiser l’accès **SSH**, ajouter ou supprimer des utilisateurs ou ajouter des fichiers de clés publiques pour sécuriser l’accès. Cet article utilise le nom d’utilisateur et le mot de passe avec **SSH** par souci de concision.

Si vous ne maîtrisez pas la connexion via **ssh**, la commande prend la forme `ssh <username>@<publicdnsaddress> -p <the ssh port>`. Le cas échéant, nous utilisons le nom d’utilisateur et le mot de passe de l’étape précédente et le port 22, qui est le port **ssh** par défaut.

	ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
	The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
	ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
	ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Fri May 22 21:02:32 UTC 2015

	  System load: 0.37              Memory usage: 2%   Processes:       207
	  Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.



	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@myuniquevmname:~$

Maintenant que vous êtes connecté à votre machine virtuelle, vous êtes prêt à attacher un disque.

## Attacher et monter un disque

La procédure d’attachement d’un nouveau disque est rapide. Il vous suffit de saisir `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>` afin de créer un nouveau disque (Go) et l’associer à votre machine virtuelle. Le résultat suivant doit s’afficher :

	azure vm disk attach-new myuniquegroupname myuniquevmname 5
	info:    Executing command vm disk attach-new
	+ Looking up the VM "myuniquevmname"
	info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
	+ Updating VM "myuniquevmname"
	info:    vm disk attach-new command OK


Nous allons maintenant rechercher le disque, à l’aide de `dmesg | grep SCSI` (la méthode de découverte des nouveaux disques peut varier). Le résultat est alors le suivant :

	dmesg | grep SCSI
	[    0.294784] SCSI subsystem initialized
	[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
	[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
	[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
	[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

et dans le scénario évoqué dans cette rubrique, le disque `sdc` est celui que nous voulons. Partitionnons maintenant le disque avec `sudo fdisk /dev/sdc`, en supposant que dans votre cas, il s’agissait d’un disque `sdc` et utilisons-le en tant que disque principal sur la partition 1, en acceptant les autres valeurs par défaut.

	sudo fdisk /dev/sdc
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x2a59b123.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-10485759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
	Using default value 10485759

Créez la partition en saisissant `p` lors de l’invite :

	Command (m for help): p

	Disk /dev/sdc: 5368 MB, 5368709120 bytes
	255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x2a59b123

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdc1            2048    10485759     5241856   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

Écrivez un système de fichiers sur la partition à l’aide de la commande **mkfs**, en spécifiant le type de votre système de fichier et le nom de l’appareil. Dans cette rubrique, nous utilisons `ext4` et `/dev/sdc1` (voir ci-dessus) :

	sudo mkfs -t ext4 /dev/sdc1
	mke2fs 1.42.9 (4-Feb-2014)
	Discarding device blocks: done
	Filesystem label=
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	327680 inodes, 1310464 blocks
	65523 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=1342177280
	40 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks:
		32768, 98304, 163840, 229376, 294912, 819200, 884736

	Allocating group tables: done
	Writing inode tables: done
	Creating journal (32768 blocks): done
	Writing superblocks and filesystem accounting information: done

Nous créons désormais un répertoire afin de monter le système de fichiers à l’aide de `mkdir` :

	sudo mkdir /datadrive

Vous montez le répertoire à l’aide de `mount` :

	sudo mount /dev/sdc1 /datadrive

Le disque de données est désormais utilisable en tant que `/datadrive`.

	ls
	bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
	boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE]Vous pouvez également vous connecter à votre machine virtuelle Linux à l'aide d'une clé SSH pour l'identification. Pour en savoir plus, consultez la page [Utilisation de SSH avec Linux sur Microsoft Azure](virtual-machines-linux-use-ssh-key.md).

## Étapes suivantes

N’oubliez pas que votre nouveau disque n’est généralement pas disponible sur votre machine virtuelle en cas de redémarrage, sauf si vous écrivez les informations sur votre fichier [fstab](http://en.wikipedia.org/wiki/Fstab). Si vous le souhaitez, vous pouvez ajouter plusieurs disques supplémentaires et [configurer RAID](virtual-machines-linux-configure-raid.md).

Pour en savoir plus sur Linux sur Microsoft Azure, consultez les pages suivantes :

- [Linux et informatique open-source sur Microsoft Azure](virtual-machines-linux-opensource.md)

- [Utilisation des outils en ligne de commande Azure](../virtual-machines-command-line-tools.md)

- [Déployer une application LAMP à l’aide de l’extension CustomScript Azure pour Linux](virtual-machines-linux-script-lamp.md)

- [Extension Docker VM pour Linux sur Azure](virtual-machines-docker-vm-extension.md)

<!---HONumber=AcomDC_1125_2015-->