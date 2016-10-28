<properties
	pageTitle="Création et téléchargement d'un disque dur virtuel SUSE Linux dans Azure"
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation SUSE Linux."
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
	ms.date="08/24/2016"
	ms.author="szark"/>

# Préparation d'une machine virtuelle SLES ou openSUSE pour Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Composants requis ##

Cet article suppose que vous avez déjà installé un système d'exploitation SUSE ou openSUSE Linux dans un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

### Notes d'installation SLES/openSUSE

- Consultez également les [Notes générales d’installation sous Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.

- Azure ne prend pas en charge le format VHDX, seulement le **VHD fixe**. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de l’applet de commande convert-vhd.

- Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques [LVM](virtual-machines-linux-configure-lvm.md) ou [RAID](virtual-machines-linux-configure-raid.md) peuvent être utilisées sur des disques de données si vous préférez.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.


## Utilisation de SUSE Studio
[SUSE Studio](http://www.susestudio.com) peut facilement créer et gérer vos images SLES et openSUSE pour Azure et Hyper-V. Cette approche est recommandée pour personnaliser vos propres images SLES et openSUSE.

Comme alternative à la création de votre propre disque dur virtuel, SUSE publie également des images BYOS (« Bring Your Own Subscription ») pour SLES sur [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).


## Préparation de SUSE Linux Enterprise Server 11 SP4 ##

1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3. Enregistrez votre système SUSE Linux Enterprise pour lui permettre de télécharger les mises à jour et d’installer des packages.

4. Mettez à jour le système avec les derniers correctifs :

		# sudo zypper update

5. Installez l’agent Linux Azure à partir du référentiel SLES :

		# sudo zypper install WALinuxAgent

6. Vérifiez que waagent est réglé sur « on » dans chkconfig et, dans le cas contraire, configurez son démarrage automatique :
               
		# sudo chkconfig waagent on

7. Vérifiez que le service waagent est en cours d’exécution et, dans le cas contraire, démarrez-le :

		# sudo service waagent start
                
8. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Ceci permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance d'Azure.

9. Vérifiez que /boot/grub/menu.lst et /etc/fstab référencent le disque à l’aide de son UUID (by-uuid) au lieu de son ID (by-id).

	Obtention de l’UUID du disque
	
		# ls /dev/disk/by-uuid/

	Si /dev/disk/by-id est utilisé, mettez à jour /boot/grub/menu.lst et /etc/fstab avec la valeur appropriée de by-uuid.

	Avant la modification
	
		root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1

	Après la modification
	
		root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

10. Modifiez les règles udev pour éviter la génération de règles statiques pour les interfaces Ethernet. Ces règles peuvent causer des problèmes lors du clonage d’une machine virtuelle dans Microsoft Azure ou Hyper-V :

		# sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
		# sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

11.	Il est recommandé de modifier le fichier « /etc/sysconfig/network/dhcp » et le paramètre `DHCLIENT_SET_HOSTNAME` comme suit :

		DHCLIENT_SET_HOSTNAME="no"

12.	Sous « /etc/sudoers », commentez ou supprimez les lignes suivantes (si elles sont présentes) :

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

13.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

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


----------

## Préparation de openSUSE 13.1+ ##

1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3. Sur l’interpréteur de commandes, exécutez la commande `zypper lr`. Si cette commande renvoie un résultat semblable à ce qui suit, alors les référentiels sont configurés comme prévu, aucune modification n'est nécessaire (notez que les numéros de version peuvent varier) :

		# | Alias                 | Name                  | Enabled | Refresh
		--+-----------------------+-----------------------+---------+--------
		1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
		2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
		3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

	Si la commande renvoie « Aucun référentiel défini... », utilisez les commandes suivantes pour ajouter ces référentiels :

		# sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
		# sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
		# sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

	Vous pouvez alors réexécuter la commande `zypper lr` pour vérifier que ces référentiels ont été ajoutés. Si un des référentiels de mise à jour concernés n'est pas activé, exécutez la commande suivante :

		# sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Mettez à jour le noyau vers la dernière version disponible :

		# sudo zypper up kernel-default

	Ou, pour mettre à jour le système avec les derniers correctifs :

		# sudo zypper update

5.	Installez l'agent Linux Azure.

		# sudo zypper install WALinuxAgent

6.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	Ceci permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance d'Azure. Supprimez également les paramètres suivants de la ligne de démarrage du noyau (s'ils sont présents) :

		libata.atapi_enabled=0 reserve=0x1f0,0x8

7.	Il est recommandé de modifier le fichier « /etc/sysconfig/network/dhcp » et le paramètre `DHCLIENT_SET_HOSTNAME` comme suit :

		DHCLIENT_SET_HOSTNAME="no"

8.	**Important :** sous « /etc/sudoers », commentez ou supprimez les lignes suivantes (si elles sont présentes) :

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

10.	Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Vérifiez que l'agent Linux Azure est exécuté au démarrage :

		# sudo systemctl enable waagent.service

13. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel SUSE Linux pour créer des machines virtuelles dans Azure. S’il s’agit de la première fois que vous chargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel contenant le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).

<!---HONumber=AcomDC_0831_2016-->