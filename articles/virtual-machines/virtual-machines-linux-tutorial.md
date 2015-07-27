<properties
	pageTitle="Création d'une machine virtuelle exécutant Linux dans Azure"
	description="Apprenez à créer une machine virtuelle Azure exécutant Linux en utilisant une image provenant d'Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-management" />

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/13/2015"
	ms.author="rasquill"/>

# Création d’une machine virtuelle exécutant Linux

> [AZURE.SELECTOR]
- [Azure Portal](virtual-machines-linux-tutorial-portal-rm.md)
- [Azure CLI](virtual-machines-linux-tutorial.md)

Il est simple de créer une machine virtuelle Microsoft Azure exécutant Linux à partir de la ligne de commande ou du portail. Ce didacticiel vous explique comment utiliser l’interface de ligne de commande Microsoft Azure pour Mac, Linux et Windows afin de créer rapidement une machine virtuelle de serveur Ubuntu s’exécutant dans Microsoft Azure, de procéder à la connexion via **ssh**, et enfin de créer et de monter un nouveau disque. (Dans cette rubrique, une machine virtuelle de serveur Ubuntu est utilisée, mais vous pouvez également créer des machines virtuelles Linux en utilisant vos [propres images en tant que modèles](virtual-machines-linux-create-upload-vhd.md).)

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Installer l’interface de ligne de commande Microsoft Azure

Il vous faut dans un premier temps [installer l’interface de ligne de commande Microsoft Azure](../xplat-cli-install.md).

Parfait. Maintenant, pour vous assurer d’être dans le mode de gestion des ressources, saisissez `azure config mode arm`.

Encore mieux. Connectez-vous avec votre ID professionnel ou scolaire en saisissant `azure login`, puis en suivant les invites.

> [AZURE.NOTE]Si vous rencontrez une erreur de connexion, il vous faudra peut-être [créer un ID professionnel ou scolaire à partir de votre compte Microsoft personnel](resource-group-create-work-id-from-personal.md).

## Créer votre machine virtuelle Microsoft Azure

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

Créez maintenant votre machine virtuelle en saisissant `azure vm quick-create` ; des messages vous inviteront à entrer les paramètres restants. Utilisez le nom de groupe de ressources que vous venez de créer, ci-dessus, et pour la valeur **ImageURN**, saisissez `canonical:ubuntuserver:14.04.2-LTS:latest`. Votre expérience ressemble à ce qui suit :

	azure vm quick-create
	info:    Executing command vm quick-create
	Resource group name: myuniquegroupname
	Virtual machine name: myuniquevmname
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
	User name: ops
	Password: *********
	Confirm password: *********
	+ Looking up the VM "myuniquevmname"
	info:    Using the VM Size "Standard_D1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
	+ Looking up the storage account cli3c0464f24f1bf4f014323
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	info:    Preparing to create new virtual network and subnet
	/ Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
	+ Looking up the virtual network "myuni-westu-1432328437727-vnet"
	+ Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
	info:    Found public ip parameters, trying to setup PublicIP profile
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
	+ Creating public ip "myuni-westu-1432328437727-pip"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	+ Creating NIC "myuni-westu-1432328437727-nic"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Creating VM "myuniquevmname"
	+ Looking up the VM "myuniquevmname"
	+ Looking up the NIC "myuni-westu-1432328437727-nic"
	+ Looking up the public ip "myuni-westu-1432328437727-pip"
	data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
	data:    ProvisioningState               :Succeeded
	data:    Name                            :myuniquevmname
	data:    Location                        :westus
	data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_D1
	data:
	data:    Storage Profile:
	data:      Image reference:
	data:        Publisher                   :canonical
	data:        Offer                       :ubuntuserver
	data:        Sku                         :14.04.2-LTS
	data:        Version                     :latest
	data:
	data:      OS Disk:
	data:        OSType                      :Linux
	data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :myuniquevmname
	data:      User Name                     :ops
	data:      Linux Configuration:
	data:        Disable Password Auth       :false
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-31-55-31
	data:          Provisioning State        :Succeeded
	data:          Name                      :myuni-westu-1432328437727-nic
	data:          Location                  :westus
	data:            Private IP alloc-method :Dynamic
	data:            Private IP address      :10.0.1.4
	data:            Public IP address       :191.239.51.1
	data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
	info:    vm quick-create command OK

Votre machine virtuelle est en cours d’exécution. Il vous suffit désormais de vous connecter.

## Connexion à votre machine virtuelle

Sur les machines virtuelles Linux, vous vous connectez généralement via **ssh**. Dans cette rubrique, la connexion à la machine virtuelle est effectuée à l’aide de noms d’utilisateurs et de mots de passe. Pour utiliser des paires de clés publiques et privées pour interagir avec votre machine virtuelle, consultez la page [Utilisation de SSH avec Linux sur Azure](virtual-machines-linux-use-ssh-key.md).

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

N’oubliez pas que votre nouveau disque n’est généralement pas disponible sur votre machine virtuelle en cas de redémarrage, sauf si vous écrivez les informations sur votre fichier [fstab](http://en.wikipedia.org/wiki/Fstab).

Pour en savoir plus sur Linux sur Microsoft Azure, consultez les pages suivantes :

- [Linux et informatique open-source sur Microsoft Azure](virtual-machines-linux-opensource.md)

- [Utilisation des outils en ligne de commande Azure](../virtual-machines-command-line-tools.md)

- [Déployer une application LAMP à l’aide de l’extension CustomScript Azure pour Linux](virtual-machines-linux-script-lamp.md)

- [À propos des paramètres de configuration de machine virtuelle Azure](http://msdn.microsoft.com/library/azure/dn763935.aspx)

- [Extension Docker VM pour Linux sur Azure](virtual-machines-docker-vm-extension.md)
 

<!---HONumber=July15_HO3-->