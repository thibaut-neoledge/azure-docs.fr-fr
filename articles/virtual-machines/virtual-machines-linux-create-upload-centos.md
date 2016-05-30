<properties
	pageTitle="Création et téléchargement d'un disque dur virtuel Linux CentOS dans Azure"
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant le système d'exploitation Linux CentOS."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
		tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/09/2016"
	ms.author="szarkos"/>

# Préparation d'une machine virtuelle CentOS pour Azure

- [Préparation d'une machine virtuelle CentOS 6.x pour Azure](#centos-6.x)
- [Préparation d'une machine virtuelle CentOS 7.0+ pour Azure](#centos-7.0+)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Configuration requise ##

Cet article suppose que vous avez déjà installé un système d'exploitation CentOS (ou une distribution dérivée similaire) de Linux dans un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).


**Notes d'installation CentOS**

- Consultez également les [Notes générales d’installation sous Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.

- Azure ne prend pas en charge le format VHDX, seulement le **VHD fixe**. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peuvent être utilisées sur des disques de données si vous préférez.

- NUMA n'est pas pris en charge pour les tailles de machines virtuelles plus élevées en raison d'un bogue dans les versions du noyau Linux inférieures à 2.6.37. Ce problème touche spécialement les distributions utilisant le noyau Red Hat 2.6.32 en amont. L'installation manuelle de l'Agent Linux Azure (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.


## CentOS 6.x ##

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3. Exécutez la commande suivante pour désinstaller NetworkManager :

		# sudo rpm -e --nodeps NetworkManager

	**Remarque :** si le package n’est pas déjà installé, la commande échoue et un message d’erreur s’affiche. Ceci est normal.

4.	Créez un fichier nommé **network** dans le répertoire `/etc/sysconfig/` et entrez-y le texte suivant :

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Créez un fichier nommé **ifcfg-eth0** dans le répertoire et entrez-y le texte suivant :

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Modifiez les règles udev pour éviter la génération de règles statiques pour les interfaces Ethernet. Ces règles peuvent causer des problèmes lors du clonage d’une machine virtuelle dans Microsoft Azure ou Hyper-V :

		# sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
		# sudo rm -f /etc/udev/rules.d/70-persistent-net.rules


7. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

		# sudo chkconfig network on


8. **CentOS 6.3 uniquement** : installez les pilotes pour les services d’intégration Linux (LIS).

	**Important : cette opération s’applique uniquement à CentOS 6.3 et aux versions antérieures.** Sous CentOS 6.4+, les services d’intégration Linux sont *déjà disponibles dans le noyau standard*.

	- Suivez les instructions d’installation sur la [page de téléchargement des services d’intégration Linux (LIS)](https://www.microsoft.com/en-us/download/details.aspx?id=46842) et installez le package RPM sur votre image.  


9. Exécutez la commande suivante pour installer le package python-pyasn1 :

		# sudo yum install python-pyasn1

10. Si vous voulez utiliser les miroirs OpenLogic hébergés dans les centres de données Azure, remplacez le fichier /etc/yum.repos.d/CentOS-Base.repo par les référentiels suivants. Cette action ajoute également le référentiel **[openlogic]** qui comprend les packages de l'agent Linux Azure :

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0

		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

	**Remarque :** la suite de ce guide suppose que vous utilisez au moins le référentiel [openlogic] qui sera utilisé pour installer l'agent Linux Azure ci-dessous.


11.	Ajoutez la ligne suivante au fichier /etc/yum.conf :

		http_caching=packages

	**Sur CentOS 6.3 uniquement**, ajoutez la ligne suivante :

		exclude=kernel*

12. Désactivez le module yum « fastestmirror » en modifiant le fichier « /etc/yum/pluginconf.d/fastestmirror.conf ». Sous `[main]`, tapez :

		set enabled=0

13.	Exécutez la commande suivante pour effacer les métadonnées yum actuelles :

		# yum clean all

14. **CentOS 6.3 uniquement** : mettez à jour le noyau avec la commande suivante :

		# sudo yum --disableexcludes=all install kernel

15.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Cela désactive NUMA en raison d'une erreur dans la version de noyau utilisée par CentOS 6.

	Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants :

		rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

	L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.


16.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

17. Installez l'agent linux Azure en exécutant la commande suivante :

		# sudo yum install WALinuxAgent

	L'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s'ils n'avaient pas déjà été supprimés comme indiqué à l'étape 2.

18.	Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

20. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


----------


## CentOS 7.0+ ##

**Modifications dans CentOS 7 (et les distributions dérivées)**

La préparation d'une machine virtuelle CentOS 7 pour Azure est très similaire à CentOS 6 ; cependant, certaines différences importantes méritent d'être notées :

 - Le package NetworkManager n'est plus en conflit avec l'agent Azure Linux. Ce package est installé par défaut ; nous recommandons de ne pas le supprimer.
 - GRUB2 est maintenant utilisé comme chargeur de démarrage (bootloader) par défaut ; la modification des paramètres du noyau a donc changé (voir ci-dessous).
 - XFS est maintenant le système de fichiers par défaut. Le système de fichiers ext4 est toujours utilisable si vous le souhaitez.


**Configuration**

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3.	Créez un fichier nommé **network** dans le répertoire `/etc/sysconfig/` et entrez-y le texte suivant :

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.	Créez un fichier nommé **ifcfg-eth0** dans le répertoire et entrez-y le texte suivant :

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Modifiez les règles udev pour éviter la génération de règles statiques pour les interfaces Ethernet. Ces règles peuvent causer des problèmes lors du clonage d’une machine virtuelle dans Microsoft Azure ou Hyper-V :

		# sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

		# sudo chkconfig network on

7. Exécutez la commande suivante pour installer le package python-pyasn1 :

		# sudo yum install python-pyasn1

8. Si vous voulez utiliser les miroirs OpenLogic hébergés dans les centres de données Azure, remplacez le fichier /etc/yum.repos.d/CentOS-Base.repo par les référentiels suivants. Cette action ajoute également le référentiel **[openlogic]** qui comprend les packages de l'agent Linux Azure :

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0

		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7



	**Remarque :** la suite de ce guide suppose que vous utilisez au moins le référentiel [openlogic] qui sera utilisé pour installer l'agent Linux Azure ci-dessous.

9.	Exécutez la commande suivante pour effacer les métadonnées yum actuelles et installer les mises à jour le cas échéant :

		# sudo yum clean all
		# sudo yum -y update

10.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /etc/default/grub » dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`, par exemple :

		GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"

	Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Cela désactive également les nouvelles conventions d’affectation de noms CentOS 7 pour les cartes réseau. Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants :

		rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

	L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

11. Lorsque vous avez fini de modifier le fichier « /etc/default/grub », exécutez la commande suivante pour régénérer la configuration grub :

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

13.	**Uniquement si la génération de l'image se fait à partir de VMWare, VirtualBox ou KVM :** ajouter les modules Hyper-V dans initramfs :

    Modifiez `/etc/dracut.conf`, ajoutez le contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Générez de nouveau initramfs :

        # dracut –f -v

14. Installez l'agent linux Azure en exécutant la commande suivante :

		# sudo yum install WALinuxAgent
		# sudo systemctl enable waagent

15.	Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

17. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel CentOS Linux pour créer des machines virtuelles dans Azure. S’il s’agit de la première fois que vous chargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel contenant le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).

<!---HONumber=AcomDC_0518_2016-->