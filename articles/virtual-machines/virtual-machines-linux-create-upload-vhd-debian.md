<properties
	pageTitle="Préparer un disque dur virtuel Debian Linux | Microsoft Azure"
	description="Apprenez à créer des fichiers de disque dur virtuel Debian 7 et 8 pour un déploiement dans Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




#Préparer le disque dur virtuel Debian pour Azure

##Composants requis
Cette section suppose que vous avez déjà installé un système d’exploitation Debian Linux à l’aide d’un fichier .iso téléchargé à partir du [site web Debian](https://www.debian.org/distrib/) sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Hyper-V n’est qu’un exemple parmi d’autres. Pour obtenir des instructions sur l’utilisation de Hyper-V, consultez [Installation du rôle Hyper-V et configuration d’une machine virtuelle](https://technet.microsoft.com/library/hh846766.aspx).


## Notes d’installation

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de l’applet de commande **convert-vhd**.
- Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d’éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d’exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou RAID peuvent être utilisées sur des disques de données si vous préférez.
- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. Vous pouvez configurer l’agent Linux Azure pour créer un fichier d’échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.
- La taille des disques durs virtuels doit être un multiple de 1 Mo.


##Debian 7.x et 8.x

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3. Si vous configurez la machine virtuelle par rapport à un fichier ISO, commentez la ligne **deb cdrom** dans `/etc/apt/source.list`.

4. Modifiez le fichier `/etc/default/grub` et le paramètre **GRUB\_CMDLINE\_LINUX** comme suit pour y inclure des paramètres de noyau supplémentaires pour Azure.

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. Régénérez le grub et exécutez-le :

        # sudo update-grub 

6. Installez les packages de dépendances pour l’agent Linux Azure :

        # apt-get install -y git parted

7.	Installez l’agent Linux Azure à partir de Github en suivant les [instructions](virtual-machines-linux-update-agent.md), puis choisissez la version 2.0.14 :

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. Mettez hors service la machine virtuelle, préparez-la pour un approvisionnement sur Azure, puis exécutez :

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
 
9. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

##Utilisation du script Credativ pour créer un disque dur virtuel Debian

Un script est disponible sur le site web Credativ, qui peut vous aider à créer le disque dur virtuel Debian automatiquement. Vous pouvez le télécharger [ici](https://gitlab.credativ.com/de/azure-manage) et l’installer sur votre machine virtuelle Linux. Pour créer un disque dur virtuel Debian (par exemple, Debian 7), exécutez :

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

Si vous rencontrez un problème avec l’utilisation de ce script, signalez le problème à Credativ [ici](https://gitlab.credativ.com/groups/de/issues).

## Étapes suivantes

Vous êtes maintenant prêt à utiliser votre .vhd Debian pour créer des machines virtuelles Azure.

<!---HONumber=AcomDC_1203_2015-->