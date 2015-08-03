<properties 
	pageTitle="Création et téléchargement d'un disque dur virtuel Linux CentOS dans Azure" 
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant le système d'exploitation Linux CentOS." 
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

# Préparation d'une machine virtuelle CentOS pour Azure

- [Préparation d’une machine virtuelle CentOS 6.x pour Azure](#centos6)
- [Préparation d’une machine virtuelle CentOS 7.0+ pour Azure](#centos7)

##Composants requis##

Cet article suppose que vous avez déjà installé un système d'exploitation CentOS (ou une distribution dérivée similaire) de Linux dans un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).


**Notes d’installation CentOS**

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou [RAID](virtual-machines-linux-configure-raid.md) sont utilisables sur les disques de données si vous le souhaitez.

- NUMA n'est pas pris en charge pour les tailles de machines virtuelles plus élevées en raison d'un bogue dans les versions du noyau Linux inférieures à 2.6.37. Ce problème touche spécialement les distributions utilisant le noyau Red Hat 2.6.32 en amont. L'installation manuelle de l'Agent Linux Azure (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.


## <a id="centos6"> </a>CentOS 6.x ##

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

6.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Microsoft Azure ou Hyper-V :

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/


7. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

		# sudo chkconfig network on


8. **CentOS 6.3 uniquement** : installez les pilotes pour les services d'intégration Linux

	**Important : cette opération s’applique uniquement à CentOS 6.3 et aux versions antérieures.** Sous CentOS 6.4+, les services d'intégration Linux sont *déjà disponibles dans le noyau*.

	a) Récupérez le fichier .iso qui contient les pilotes des services d'intégration Linux sur le [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=41554).

	b) Dans Hyper-V Manager, dans le volet **Actions**, cliquez sur **Settings**.

	![Ouvrez les paramètres Hyper-V](./media/virtual-machines-linux-create-upload-vhd-centos/settings.png)

	c) Dans le volet **Hardware**, cliquez sur **IDE Controller 1**.

	![Ajoutez le lecteur DVD via le support d'installation](./media/virtual-machines-linux-create-upload-vhd-centos/installiso.png)

	d) Dans la zone **IDE Controller**, cliquez sur **DVD Drive**, puis sur **Add**.

	e) Sélectionnez **Image file**, accédez à **Linux IC v3.2.iso**, puis cliquez sur **Open**.

	f) Sur la page **Settings**, cliquez sur **OK**.

	g) Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

	h) Dans la fenêtre d'invite de commande, saisissez la commande suivante :

		# sudo mount /dev/cdrom /media
		# sudo /media/install.sh
		# sudo reboot

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

14. **CentOS 6.3 uniquement** : mettez à jour le noyau avec la commande suivante :

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


## <a id="centos7"> </a>CentOS 7.0+ ##

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

5.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Microsoft Azure ou Hyper-V :

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

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

		GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants :

		rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

	L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

11. Lorsque vous avez fini de modifier le fichier « /etc/default/grub », exécutez la commande suivante pour régénérer la configuration grub :

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

13. Installez l'agent linux Azure en exécutant la commande suivante :

		# sudo yum install WALinuxAgent

14.	Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

16. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


 

<!---HONumber=July15_HO4-->