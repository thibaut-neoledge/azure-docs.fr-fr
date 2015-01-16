<properties urlDisplayName="Upload an Oracle Linux VHD" pageTitle="Création et téléchargement d'un disque dur virtuel Oracle Linux dans Azure" metaKeywords="Azure VHD, uploading Linux VHD, Oracle Linux" description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Oracle Linux." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains an Oracle Linux Operating System" authors="szarkos" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="szarkos" />

# Préparation d'une machine virtuelle Oracle Linux pour Azure

- [Préparation d'une machine virtuelle Oracle Linux 6.4+ Virtual Machine pour Azure](#oracle6)
- [Préparation d'une machine virtuelle Oracle Linux 7.0+ Virtual Machine pour Azure](#oracle7)

##Conditions préalables##

Cet article suppose que vous avez déjà installé un système d'exploitation Oracle Linux dans un disque dur virtuel. Il existe de nombreux outils de création de fichiers .vhd, par exemple une solution de virtualisation telle que Hyper-V. Pour obtenir des instructions à ce sujet, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx). 


**Notes générales d'installation de Oracle Linux**

- Le noyau Oracle compatible Red Hat et leur noyau UEK3 (Unbreakable Enterprise Kernel) sont tous les deux pris en charge sur Hyper-V et Azure. Pour de meilleurs résultats, n'oubliez pas de mettre à jour le noyau lorsque vous préparez votre disque dur virtuel Oracle Linux.

- Le noyau UEK2 d'Oracle n'est pas pris en charge sur Hyper-V et Azure, car il ne comporte pas les pilotes nécessaires.

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner.  Les techniques LVM ou [RAID](../virtual-machines-linux-configure-raid) sont utilisables sur les disques de données si vous le souhaitez.

- NUMA n'est pas pris en charge pour les tailles de machines virtuelles plus élevées en raison d'un bogue dans les versions du noyau Linux inférieures à 2.6.37. Ce problème touche spécialement les distributions utilisant le noyau Red Hat 2.6.32 en amont. L'installation manuelle de l'Agent Linux Azure (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire.  Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.


## <a id="oracle6"> </a> Oracle Linux 6.4+ ##

Vous devez suivre des étapes de configuration spécifiques dans le système d'exploitation afin que la machine virtuelle fonctionne sur Azure.

1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir la fenêtre de la machine virtuelle.

3. Exécutez la commande suivante pour désinstaller NetworkManager :

		# sudo rpm -e --nodeps NetworkManager

	**Remarque :** si le package n'est pas déjà installé, la commande échoue et un message d'erreur s'affiche. Ceci est normal.

4.	Créez un fichier nommé **network** dans le répertoire `/etc/sysconfig/` et entrez-y le code suivant :

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Créez un fichier nommé **ifcfg-eth0** dans le répertoire `/etc/sysconfig/network-scripts/` et entrez-y le code suivant :

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet.  Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Windows Azure ou Hyper-V :

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

7. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

		# chkconfig network on

8. Saisissez la commande suivante pour installer python-pyasn1 :

		# sudo yum install python-pyasn1

9.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier " /boot/grub/menu.lst " dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Cela désactive NUMA en raison d'une erreur dans le noyau compatible Red Hat d'Oracle.

	Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants :

		rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

	L'option `crashkernel` peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.


10.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.

11. Installez l'agent linux Azure en exécutant la commande suivante :

		# sudo yum install WALinuxAgent

	L'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s'ils n'avaient pas déjà été supprimés comme indiqué à l'étape 2.

12.	Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

14. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


----------


## <a id="oracle7"> </a> Oracle Linux 7.0+ ##

**Modifications dans Oracle Linux 7**

La préparation d'une machine virtuelle Oracle Linux 7 pour Azure est très similaire à Oracle Linux 6 ; cependant, certaines différences importantes méritent d'être notées :

 - Le noyau compatible Red Hat et le noyau UEK3 d'Oracle sont pris en charge dans Azure.  Le noyau UEK3 est recommandé.
 - Le package NetworkManager n'est plus en conflit avec l'agent Azure Linux. Ce package est installé par défaut ; nous recommandons de ne pas le supprimer.
 - GRUB2 est maintenant utilisé comme chargeur de démarrage (bootloader) par défaut ; la modification des paramètres du noyau a donc changé (voir ci-dessous).
 - XFS est maintenant le système de fichiers par défaut. Le système de fichiers ext4 est toujours utilisable si vous le souhaitez.


**Configuration**

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3.	Créez un fichier nommé **network** dans le répertoire `/etc/sysconfig/` et entrez-y le code suivant :

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.	Créez un fichier nommé **ifcfg-eth0** dans le répertoire `/etc/sysconfig/network-scripts/` et entrez-y le code suivant :

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

5.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Microsoft Azure ou Hyper-V :

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

		# sudo chkconfig network on

7. Exécutez la commande suivante pour installer le package python-pyasn1 :

		# sudo yum install python-pyasn1

8.	Exécutez la commande suivante pour effacer les métadonnées yum actuelles et installer les mises à jour le cas échéant :

		# sudo yum clean all
		# sudo yum -y update

9.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier " /etc/default/grub " dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`, par exemple :

		GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Outre les précautions ci-dessus, il est recommandé de *supprimer* les paramètres suivants :

		rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

	L'option `crashkernel` peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.


10. Lorsque vous avez fini de modifier le fichier " /etc/default/grub ", exécutez la commande suivante pour régénérer la configuration grub :

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

11.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.  C'est généralement le cas par défaut.

12. Installez l'agent linux Azure en exécutant la commande suivante :

		# sudo yum install WALinuxAgent

13.	Ne créez pas d'espace swap sur le disque du système d'exploitation.

	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

15. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.



<!--HONumber=35.1-->
