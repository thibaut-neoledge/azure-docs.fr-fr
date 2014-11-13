<properties urlDisplayName="Upload an Ubuntu Linux VHD" pageTitle="Cr&eacute;ation et t&eacute;l&eacute;chargement d'un disque dur virtuel&nbsp;Linux&nbsp;Ubuntu dans&nbsp;Azure" metaKeywords="Azure VHD, uploading Linux VHD, Ubuntu" description="Apprenez &agrave; cr&eacute;er et &agrave; charger un disque dur virtuel&nbsp;(VHD)&nbsp;Azure contenant un syst&egrave;me d'exploitation&nbsp;Linux&nbsp;Ubuntu." metaCanonical="" services="virtual-machines" documentationCenter="" title="Cr&eacute;ation et chargement d'un disque dur virtuel contenant un syst&egrave;me d'exploitation&nbsp;Linux&nbsp;Ubuntu" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# Préparation d'une machine virtuelle Linux Ubuntu pour Azure

## Configuration requise

Cet article suppose que vous avez déjà installé un système d'exploitation Linux Ubuntu dans un disque dur virtuel. Il existe de nombreux outils de création de fichiers .vhd, par exemple une solution de virtualisation telle que Hyper-V. Pour des instructions à ce sujet, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle][Installation du rôle Hyper-V et configuration d'une machine virtuelle].

**Notes d'installation Ubuntu**

-   Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

-   Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou [RAID][RAID] sont utilisables sur les disques de données si vous le souhaitez.

-   Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

-   La taille des disques durs virtuels doit être un multiple de 1 Mo.

## <span id="ubuntu"></span> </a>Ubuntu 12.04+

1.  Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2.  Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3.  Remplacez les référentiels actuels dans l'image par les référentiels Azure Ubuntu. Les étapes varient légèrement selon la version d'Ubuntu.

    Avant de modifier /etc/apt/sources.list, il est recommandé d'effectuer une sauvegarde

        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04 :

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        # sudo apt-get update

    Ubuntu 12.10 :

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        # sudo apt-get update

    Ubuntu 14.04+ :

        # sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        # sudo apt-get update

4.  Exécutez la commande suivante pour mettre à jour le système d'exploitation avec le dernier noyau :

    Ubuntu 12.04 :

        # sudo apt-get update
        # sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 12.10 :

        # sudo apt-get update
        # sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    Ubuntu 14.04+ :

        # sudo apt-get update
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

5.  (Facultatif) Si le système Ubuntu rencontre une erreur et redémarre, il attend souvent la saisie de l'utilisateur à l'invite de démarrage grub, ce qui empêche le système de démarrer correctement. Pour éviter ceci, procédez comme suit :

    a) Ouvrez le fichier /etc/grub.d/00\_header.

    b) Dans la fonction **make\_timeout()**, recherchez **if ["\\${recordfail}" = 1 ]; then**

    c) Modifiez le texte sous cette ligne comme suit : **set timeout=5**.

    d) Exécutez 'sudo update-grub'.

6.  Modifiez la ligne de démarrage du noyau afin que Grub y inclue les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier /etc/default/grub dans un éditeur de texte. Localisez la variable `GRUB_CMDLINE_LINUX_DEFAULT` (ou ajoutez-la le cas échéant) et modifiez-la pour inclure les paramètres suivants :

        GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

    Enregistrez ce fichier, puis fermez-le. Exécutez ensuite la commande `sudo update-grub`. Ce permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance technique d'Azure.

7.  Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.

8.  Installez l'agent Linux Azure :

        # sudo apt-get update
        # sudo apt-get install walinuxagent

    Notez que l'installation du package `walinuxagent` supprime les packages `NetworkManager` et `NetworkManager-gnome` (s'ils sont installés).

9.  Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

10. Cliquez sur **Action -\> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

  [Installation du rôle Hyper-V et configuration d'une machine virtuelle]: http://technet.microsoft.com/library/hh846766.aspx
  [RAID]: ../virtual-machines-linux-configure-raid
