<properties 
	pageTitle="Création et téléchargement d'un disque dur virtuel Linux dans Azure"
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/15/2015"
	ms.author="szarkos"/>


# <a id="nonendorsed"> </a>Informations concernant les distributions non approuvées #

**Important** : le contrat SLA de la plateforme Azure s’applique aux machines virtuelles exécutant le système d’exploitation Linux uniquement lorsqu’une des [distributions approuvées](virtual-machines-../linux-endorsed-distributions.md) est utilisée. Toutes les distributions Linux fournies dans la galerie d'images Azure sont des distributions reconnues répondant à la configuration requise.

- [Linux sur Azure : Distributions approuvées](virtual-machines-../linux-endorsed-distributions.md)
- [Prise en charge d’images Linux dans Microsoft Azure](http://support2.microsoft.com/kb/2941892)

Toutes les distributions exécutées sur Azure doivent remplir les conditions suivantes pour fonctionner correctement sur la plateforme. Cet article n'est pas exhaustif, car chaque distribution est différente. Il est également possible que, même en répondant à tous les critères ci-dessous, il s'avère nécessaire de modifier votre système Linux pour garantir son fonctionnement correct sur la plateforme.

C'est pourquoi nous recommandons de commencer avec une de nos [distributions Linux approuvées sur Azure](../linux-endorsed-distributions.md) dans la mesure du possible. Les articles suivants vous montrent comment préparer les diverses distributions Linux approuvées prises en charge dans Azure :

- **[Distributions CentOS](virtual-machines-linux-create-upload-vhd-centos.md)**
- **[Oracle Linux](virtual-machines-linux-create-upload-vhd-oracle.md)**
- **[SLES et openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](virtual-machines-linux-create-upload-vhd-ubuntu.md)**

La suite de cet article fournit des conseils généraux pour exécuter votre distribution Linux sur Azure.


## <a id="linuxinstall"> </a>Notes générales d’installation de Linux ##

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou [RAID](virtual-machines-linux-configure-raid.md) sont utilisables sur les disques de données si vous le souhaitez.

- NUMA n'est pas pris en charge pour les tailles de machines virtuelles plus élevées en raison d'un bogue dans les versions du noyau Linux inférieures à 2.6.37. Ce problème touche spécialement les distributions utilisant le noyau Red Hat 2.6.32 en amont. L'installation manuelle de l'Agent Linux Azure (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.


### Installation de Linux sans Hyper-V ###

Dans certains cas, les programmes d'installation de Linux ne comprennent pas les pilotes pour Hyper-V dans le ramdisk initial (initrd ou initramfs), sauf s'ils détectent qu'il s'exécute sur un environnement Hyper-V. Lorsque vous utilisez un système de virtualisation différent (Virtualbox, KVM, etc.) pour préparer votre image Linux, vous devrez peut-être recréer le fichier initrd pour vous assurer que les modules noyaux `hv_vmbus` et `hv_storvsc` sont disponibles dans le ramdisk initial. Ceci est un problème connu, touchant au moins les systèmes basés sur la distribution Red Hat en amont.

Le mécanisme de reconstruction d'image initrd ou initramfs varie en fonction de la distribution. Consultez la documentation ou le support de votre distribution pour trouver la procédure appropriée. L’exemple suivant permet de régénérer le fichier initrd, à l’aide de l’utilitaire `mkinitrd` :

Tout d'abord, sauvegardez l'image initrd existante :

	# cd /boot
	# sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

Puis, régénérez initrd avec les modules noyau `hv_vmbus` et `hv_storvsc` :

	# sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### Redimensionnement des disques durs virtuels ###

Les images de disque dur virtuel sur Azure doivent avoir une taille virtuelle alignée à 1 Mo. En règle générale, les disques durs virtuels créés à l'aide d'Hyper-V sont déjà alignés correctement. Si le disque dur virtuel n’est pas correctement aligné, un message d’erreur semblable au suivant peut s’afficher lorsque vous tentez de créer une *image* à partir de votre disque dur virtuel :

	"The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Pour résoudre ce problème, vous pouvez redimensionner la machine virtuelle à l’aide de la console Gestionnaire Hyper-V ou de l’applet de commande Powershell [ Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx).

Si vous n'utilisez pas un environnement Windows, il est recommandé d'utiliser qemu-img pour convertir (si nécessaire) et redimensionner le disque dur virtuel :

 1. Redimensionner le disque dur virtuel directement à l’aide d’outils comme `qemu-img` ou `vbox-manage` peut rendre le disque dur virtuel non démarrable. Il est donc recommandé de convertir d'abord le disque dur virtuel en image disque RAW. Si l'image de machine virtuelle a déjà été créée comme image disque RAW (c'est la valeur par défaut pour certains hyperviseurs comme KVM), vous pouvez ignorer cette étape :

		# qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

 2. Calculez la taille requise pour l'image disque afin de vous assurer que la taille virtuelle est alignée à 1 Mo. Le script shell bash suivant peut vous y aider. Le script utilise « `qemu-img info` » pour déterminer la taille virtuelle de l’image disque, puis calcule la taille au 1 Mo supérieur :

		rawdisk="MyLinuxVM.raw"
		vhddisk="MyLinuxVM.vhd"

		MB=$((1024*1024))
		size=$(qemu-img info -f raw --output json "$rawdisk" | \
		       gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

		rounded_size=$((($size/$MB + 1)*$MB))
		echo "Rounded Size = $rounded_size"

 3. Redimensionnez le disque brut à l'aide de $rounded_size défini dans le script ci-dessus :

		# qemu-img resize MyLinuxVM.raw $rounded_size

 4. À présent, convertissez le disque RAW en disque dur virtuel à taille fixe :

		# qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd



## Conditions requises pour le noyau Linux ##

Les pilotes LIS (Linux Integration Services) pour Hyper-V et Azure sont directement liés au noyau Linux en amont. Ces pilotes sont déjà disponibles dans de nombreuses distributions qui comprennent une version récente du noyau Linux (3.x et supérieures). Sinon, ces distributions fournissent des versions rétroportées de ces pilotes avec leurs noyaux. Ces pilotes sont mis à jour en permanence dans le noyau en amont avec de nouveaux correctifs et de nouvelles fonctionnalités. Aussi, dans la mesure du possible, il est recommandé d'exécuter une [distribution approuvée](../linux-endorsed-distributions.md) comportant ces correctifs et ces modifications.

Si vous exécutez une variante des versions Red Hat Enterprise Linux **6.0-6.3**, vous devez installer les pilotes LIS les plus récents pour Hyper-V. Les pilotes sont disponibles [ici](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Pour RHEL **6.4+** (et les distributions dérivées), les pilotes LIS sont déjà inclus dans le noyau ; aucun package d'installation supplémentaire n'est donc nécessaire pour exécuter ces systèmes sur Azure.

Si un noyau personnalisé est requis, il est recommandé d’utiliser une version plus récente du noyau (c.à.d. **3.8+**). Pour ces distributions ou les fournisseurs qui maintiennent leur propre noyau, il est nécessaire de rétroporter régulièrement les pilotes LIS du noyau en amont vers votre noyau personnalisé. Même si vous exécutez une version relativement récente du noyau, il est fortement recommandé de conserver une trace des correctifs en amont des pilotes LIS et de les rétroporter en fonction des besoins. L'emplacement des fichiers sources du pilote LIS est disponible dans le fichier [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) dans l'arborescence source du noyau Linux :

	F:	arch/x86/include/asm/mshyperv.h
	F:	arch/x86/include/uapi/asm/hyperv.h
	F:	arch/x86/kernel/cpu/mshyperv.c
	F:	drivers/hid/hid-hyperv.c
	F:	drivers/hv/
	F:	drivers/input/serio/hyperv-keyboard.c
	F:	drivers/net/hyperv/
	F:	drivers/scsi/storvsc_drv.c
	F:	drivers/video/fbdev/hyperv_fb.c
	F:	include/linux/hyperv.h
	F:	tools/hv/

Au minimum, l'absence des correctifs suivants a été reconnue pour poser des problèmes sur Azure : ils doivent donc être inclus dans le noyau. Cette liste n’est pas exhaustive ni complète pour toutes les distributions :

- [ata_piix : reporter des disques dans les pilotes Hyper-V par défaut](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc : compte des paquets en transit dans le chemin RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
- [storvsc : éviter l’utilisation de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
- [storvsc :désactiver WRITE_SAME pour RAID et les pilotes adaptateurs de l’hôte virtuel](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
- [storvsc : correctif de déréférence du pointeur NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
- [storvsc : des défaillances de la mémoire tampon de l’anneau peuvent entraîner un gel des E/S](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)


## agent Linux Azure ##

L'[Agent Linux Azure](virtual-machines-linux-agent-user-guide.md) (waagent) est requis pour approvisionner une machine virtuelle Linux dans Azure. La dernière version, les problèmes des fichiers ou l'envoi de requêtes d'extraction sont disponibles dans le [référentiel de l'agent Linux sur GitHub](https://github.com/Azure/WALinuxAgent).

- L'agent Linux est publié avec la licence Apache 2.0. De nombreuses distributions fournissent déjà des packages RPM ou deb pour l'agent : dans certains cas, l'installation et la mise à jour ne nécessitent aucun travail.

- L'agent Linux Azure nécessite Python v2.6+.

- L'agent nécessite également le module python-pyasn1. La plupart des distributions le fournissent sous la forme d'un package indépendant que vous pouvez installer.

- Dans certains cas, l'agent Linux Azure n'est pas compatible avec NetworkManager. Dans la plupart des cas, NetworkManager, configuré dans les packages RPM/Deb fournis avec les distributions, entre en conflit avec le package waagent, ce qui entraîne la désinstallation de NetworkManager lors de l'installation du package de l'agent Linux.


## Configuration générale requise Linux ##

- Modifiez la ligne de démarrage du noyau dans GRUB ou GRUB2 afin d'y inclure les paramètres suivants. Ceci permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure :

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure.

	Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants s'ils sont présents :

		rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

	L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

- Installation de l'agent Linux Azure

	L'agent Linux Azure est requis pour approvisionner une image Linux sur Azure. De nombreuses distributions fournissent cet agent sous forme de package RPM ou Deb (ce package est généralement nommé WALinuxAgent ou walinuxagent). Il est également possible d'installer manuellement cet agent en suivant les instructions du [Guide de l'agent Linux](virtual-machines-linux-agent-user-guide.md).

- Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

- Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Sous « /etc/sudoers », vous devez supprimer ou commenter les lignes suivantes (si elles sont présentes) :

		Defaults targetpw
		ALL    ALL=(ALL) ALL

- Enfin, exécutez la commande suivante pour annuler l'approvisionnement de la machine virtuelle :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

- Vous devez ensuite arrêter la machine virtuelle et télécharger le disque dur virtuel dans Azure.

<!---HONumber=July15_HO3-->