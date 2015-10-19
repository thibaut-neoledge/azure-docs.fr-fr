<properties
	pageTitle="Création et téléchargement d'un disque dur virtuel Linux Ubuntu dans Azure"
	description="Apprenez à créer et à charger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Linux Ubuntu."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="szarkos"/>

# Préparation d'une machine virtuelle Linux Ubuntu pour Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

##Composants requis##

Cet article suppose que vous avez déjà installé un système d'exploitation Linux Ubuntu dans un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d’installation Ubuntu**

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou [RAID](virtual-machines-linux-configure-raid.md) sont utilisables sur les disques de données si vous le souhaitez.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.


## <a id="ubuntu"> </a>Ubuntu 12.04 ##

1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3.	Remplacez les référentiels actuels dans l'image par les référentiels Azure Ubuntu. Les étapes varient légèrement selon la version d'Ubuntu.

	Avant de modifier /etc/apt/sources.list, il est recommandé d’effectuer une sauvegarde :

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04 :

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		# sudo apt-get update

	Ubuntu 14.04 :

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. Les images Ubuntu Azure suivent désormais le noyau *HWE* (HardWare Enablement). Exécutez les commandes suivantes pour mettre à jour le système d’exploitation avec le dernier noyau :

	Ubuntu 12.04 :

		# sudo apt-get update
		# sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 14.04 :

		# sudo apt-get update
		# sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

5.	(Facultatif) Si le système Ubuntu rencontre une erreur et redémarre, il attend souvent la saisie de l'utilisateur à l'invite de démarrage grub, ce qui empêche le système de démarrer correctement. Pour éviter ceci, procédez comme suit :

	a) Ouvrez le fichier /etc/grub.d/00\_header.

	b) Dans la fonction **make\_timeout()**, recherchez **if ["\\${recordfail}" = 1 ]; then**

	c) Modifiez le texte sous cette ligne comme suit : **set timeout=5**.

	d) Exécutez 'sudo update-grub'.

6. Modifiez la ligne de démarrage du noyau afin que Grub y inclue les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /etc/default/grub » dans un éditeur de texte. Recherchez la variable nommée `GRUB_CMDLINE_LINUX_DEFAULT` (ou ajoutez-la le cas échéant) et modifiez-la pour inclure les paramètres suivants :

		GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	Enregistrez ce fichier, puis fermez-le. Exécutez ensuite la commande `sudo update-grub`. Ce permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance technique d'Azure.

8.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

9.	Installez l'agent Linux Azure :

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	Notez que l’installation du package `walinuxagent` entraîne la suppression des packages `NetworkManager` et `NetworkManager-gnome` (s’ils sont installés).

10.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

11. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## Références ##

Noyau HWE (HardWare Enablement) Ubuntu

- [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
- [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

<!---HONumber=Oct15_HO2-->