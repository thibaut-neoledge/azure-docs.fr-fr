<properties urlDisplayName="Upload a Linux VHD" pageTitle="Création et téléchargement d'un disque dur virtuel Linux dans Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Linux." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains a Linux Operating System" authors="szarkos" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="szarkos" />


# <a id="nonendorsed"> </a>Informations concernant les distributions non validées #

**Important** : le contrat SLA de la plateforme Azure s'applique aux machines virtuelles exécutant le système d'exploitation Linux uniquement lorsqu'une des [distributions approuvées est utilisée](../virtual-machines-linux-endorsed-distributions). Toutes les distributions Linux fournies dans la galerie d'images Azure sont des distributions reconnues répondant à la configuration requise.

- [Linux sur Azure : distributions approuvées](../virtual-machines-linux-endorsed-distributions)
- [Prise en charge d'images Linux dans Microsoft Azure](http://support2.microsoft.com/kb/2941892)

Toutes les distributions exécutées sur Azure doivent remplir les conditions suivantes pour fonctionner correctement sur la plateforme.  Cet article n'est pas exhaustif, car chaque distribution est différente. Il est également possible que, même en répondant à tous les critères ci-dessous, il s'avère nécessaire de modifier votre système Linux pour garantir son fonctionnement correct sur la plateforme.

C'est pourquoi nous recommandons de commencer avec une de nos [distributions Linux approuvées sur Azure](../linux-endorsed-distributions) dans la mesure du possible. Les articles suivants vous montrent comment préparer les diverses distributions Linux approuvées prises en charge dans Azure :

- **[Distributions CentOS](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES & openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**

La suite de cet article fournit des conseils généraux pour exécuter votre distribution Linux sur Azure.


## Notes générales d'installation de Linux##

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner.  Les techniques LVM ou [RAID](../virtual-machines-linux-configure-raid) sont utilisables sur les disques de données si vous le souhaitez.

- NUMA n'est pas pris en charge pour les tailles de machines virtuelles plus élevées en raison d'un bogue dans les versions du noyau Linux inférieures à 2.6.37. Ce problème touche spécialement les distributions utilisant le noyau Red Hat 2.6.32 en amont. L'installation manuelle de l'Agent Linux Azure (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire.  Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.


## Conditions requises pour le noyau Linux ##

Les pilotes LIS (Linux Integration Services) pour Hyper-V et Azure sont directement liés au noyau Linux en amont. Ces pilotes sont déjà disponibles dans de nombreuses distributions qui comprennent une version récente du noyau Linux (3.x et supérieures). Sinon, ces distributions fournissent des versions rétroportées de ces pilotes avec leurs noyaux.  Ces pilotes sont mis à jour en permanence dans le noyau en amont avec de nouveaux correctifs et de nouvelles fonctionnalités. Aussi, dans la mesure du possible, il est recommandé d'exécuter une  [distribution approuvée](../linux-endorsed-distributions) comportant ces correctifs et ces modifications.

Si vous exécutez une variante des versions Red Hat Enterprise Linux **6.0-6.3**, vous devez installer les pilotes LIS les plus récents pour Hyper-V, disponibles [à cette adresse](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). À compter de RHEL **6.4+** (et les distributions dérivées), les pilotes LIS sont déjà inclus dans le noyau ; aucun package d'installation supplémentaire n'est donc nécessaire pour exécuter ces systèmes sur Azure.

Si un noyau personnalisé est requis, il est fortement recommandé d'utiliser une version plus récente du noyau (c.à.d. **3.8+**). Pour ces distributions ou les fournisseurs qui maintiennent leur propre noyau, il est recommandé de rétroporter les pilotes LIS du noyau en amont vers votre noyau personnalisé.  Même si vous exécutez une version relativement récente du noyau, il est généralement recommandé de conserver une trace des correctifs en amont des pilotes LIS et de les rétroporter en fonction des besoins. L'emplacement des fichiers sources du pilote LIS est disponible dans le fichier [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) dans l'arborescence source du noyau Linux :

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

Au minimum, l'absence des correctifs suivants a été reconnue pour poser des problèmes sur Azure : ils doivent donc être inclus dans le noyau. Cette liste n'est peut-être pas exhaustive ou complète pour toutes les distributions :

- [ata_piix : reporter des disques dans les pilotes Hyper-V par défaut](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
- [storvsc : compte des paquets en transit dans le chemin RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)


## Agent Linux Azure ##

L'[Agent Linux Azure](../virtual-machines-linux-agent-user-guide) (waagent) est nécessaire pour approvisionner une machine virtuelle Linux dans Azure. La dernière version, les problèmes des fichiers ou l'envoi de requêtes d'extraction sont disponibles dans le [référentiel de l'agent Linux sur GitHub](https://github.com/Azure/WALinuxAgent).

- L'agent Linux est publié avec la licence Apache 2.0. De nombreuses distributions fournissent déjà des packages RPM ou deb pour l'agent : dans certains cas, l'installation et la mise à jour ne nécessitent aucun travail.

- L'agent Linux Azure nécessite Python v2.6+.

- L'agent nécessite également le module python-pyasn1. La plupart des distributions le fournissent sous la forme d'un package indépendant que vous pouvez installer.

- Dans certains cas, l'agent Linux Azure n'est pas compatible avec NetworkManager. Dans la plupart des cas, NetworkManager, configuré dans les packages RPM/Deb fournis avec les distributions, entre en conflit avec le package waagent, ce qui entraîne la désinstallation de NetworkManager lors de l'installation du package de l'agent Linux.


## Configuration générale requise Linux ##

- Modifiez la ligne de démarrage du noyau dans GRUB ou GRUB2 afin d'y inclure les paramètres suivants. Ceci permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure :

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure.

	Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants s'ils sont présents :

		rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

	L'option `crashkernel` peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.

- Installation de l'agent Linux Azure

	L'agent Linux Azure est requis pour approvisionner une image Linux sur Azure.  De nombreuses distributions fournissent cet agent sous forme de package RPM ou Deb (ce package est généralement nommé WALinuxAgent ou walinuxagent).  Il est également possible d'installer manuellement cet agent en suivant les instructions du [Guide de l'agent Linux](../virtual-machines-linux-agent-user-guide).

- Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.

- Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

- Sous " /etc/sudoers ", vous devez supprimer ou commenter les lignes suivantes (si elles sont présentes) :

		Defaults targetpw
		ALL    ALL=(ALL) ALL

- Enfin, exécutez la commande suivante pour annuler l'approvisionnement de la machine virtuelle :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

- Vous devez ensuite arrêter la machine virtuelle et télécharger le disque dur virtuel dans Azure.



<!--HONumber=35.1-->
