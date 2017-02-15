---
title: "Bureau à distance sur une machine virtuelle Linux | Microsoft Docs"
description: "Découvrez comment installer et configurer le Bureau à distance pour se connecter une machine virtuelle Linux Microsoft Azure."
services: virtual-machines-linux
documentationcenter: 
author: SuperScottz
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 34348659-ddb7-41da-82d6-b5885859e7e4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: mingzhan
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 98b2f3d9108c3f7a4179f5756d56fce9c5acd915


---
# <a name="using-remote-desktop-to-connect-to-a-microsoft-azure-linux-vm"></a>Utilisation du Bureau à distance pour se connecter à une machine virtuelle Linux Microsoft Azure.
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="overview"></a>Vue d'ensemble
Le protocole RDP (Remote Desktop Protocol) est un protocole propriétaire utilisé pour Windows. Comment pouvons-nous utiliser le protocole RDP pour se connecter à distance à une machine virtuelle Linux ?

Ce guide vous donne la réponse ! Il vous aidera à installer et à configurer xrdp sur votre machine virtuelle Linux Microsoft Azure. Vous pourrez vous y connecter avec le Bureau à distance à partir d’un ordinateur Windows. Nous allons utiliser la machine virtuelle Linux exécutant Ubuntu ou OpenSUSE comme exemple dans ce guide.

Xrdp est un serveur RDP open source, qui vous permet de connecter votre serveur Linux avec le Bureau à distance à partir d’un ordinateur Windows. Il fonctionne beaucoup mieux que VNC (Virtual Network Computing). VNC a cette tendance à la qualité « JPEG » et un comportement lent, alors que RDP est rapide et parfaitement net.

> [!NOTE]
> Vous devez déjà disposer d’une machine virtuelle Microsoft Azure exécutant Linux. Pour créer et configurer une machine virtuelle Linux, voir le [Didacticiel sur les machines virtuelles Linux Azure](virtual-machines-linux-classic-createportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
> 
> 

## <a name="create-endpoint-for-remote-desktop"></a>Créer un point de terminaison pour le Bureau à distance
Nous allons utiliser dans ce document le point de terminaison par défaut 3389 pour Bureau à distance. Définissez donc le point de terminaison 3389 comme Bureau à distance pour votre machine virtuelle Linux, comme ci-dessous :

![image](./media/virtual-machines-linux-classic-remote-desktop/no1.png)

Si vous ne savez pas comment configurer un point de terminaison pour votre machine virtuelle, voir [l’aide](virtual-machines-linux-classic-setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="install-gnome-desktop"></a>Installer Gnome Desktop
Connectez-vous à votre machine virtuelle Linux via putty et installez `Gnome Desktop`.

Pour Ubuntu, procédez comme suit :

    #sudo apt-get update
    #sudo apt-get install ubuntu-desktop


Pour OpenSUSE, utilisez :

    #sudo zypper install gnome-session

## <a name="install-xrdp"></a>Installer xdrp
Pour Ubuntu, procédez comme suit :

    #sudo apt-get install xrdp

Pour OpenSUSE, utilisez :

> [!NOTE]
> Mettez à jour la version d’OpenSUSE avec la version que vous utilisez dans la commande suivante. Voici un exemple de commande pour `OpenSUSE 13.2`.
> 
> 

    #sudo zypper in http://download.opensuse.org/repositories/X11:/RemoteDesktop/openSUSE_13.2/x86_64/xrdp-0.9.0git.1401423964-2.1.x86_64.rpm
    #sudo zypper install tigervnc xorg-x11-Xvnc xterm remmina-plugin-vnc


## <a name="start-xrdp-and-set-xdrp-service-at-boot-up"></a>Démarrez xrdp et paramétrez le service xdrp au démarrage
Pour OpenSUSE, utilisez :

    #sudo systemctl start xrdp
    #sudo systemctl enable xrdp

Pour Ubuntu, xrdp sera démarré et activé au démarrage automatiquement après l’installation.

## <a name="using-xfce-if-you-are-using-ubuntu-version-later-than-ubuntu-1204lts"></a>Utilisation de xfce si vous utilisez une version d’Ubuntu ultérieure à 12.04LTS
Comme le xrdp actuel ne prend pas en charge Gnome Desktop depuis la version d’Ubuntu ultérieure à 12.04LTS, nous allons utiliser à la place `xfce` Desktop.

Installez `xfce`. Utilisez :

    #sudo apt-get install xubuntu-desktop

Activez ensuite `xfce`. Utilisez :

    #echo xfce4-session >~/.xsession

Modifiez le fichier de configuration `/etc/xrdp/startwm.sh`. Utilisez :

    #sudo vi /etc/xrdp/startwm.sh   

Ajoutez la ligne `xfce4-session` avant la ligne `/etc/X11/Xsession`.

Redémarrez le service xrdp. Utilisez :

    #sudo service xrdp restart


## <a name="connect-your-linux-vm-from-a-windows-machine"></a>Connectez votre machine virtuelle Linux à partir d’un ordinateur Windows
Sur un ordinateur Windows, démarrez le client Bureau à distance, entrez le nom DNS de votre machine virtuelle Linux, ou accédez au `Dashboard` de votre machine virtuelle dans le portail Azure Classic et cliquez sur `Connect` pour connecter votre machine virtuelle Linux. Vous verrez la fenêtre de connexion ci-dessous :

![image](./media/virtual-machines-linux-classic-remote-desktop/no2.png)

Connectez-vous avec le `user` & `password` de votre machine virtuelle Linux, et profitez du Bureau à distance à partir de votre machine virtuelle Linux Microsoft Azure dès maintenant !

## <a name="next"></a>Suivant
Pour plus d’informations sur l’utilisation de xrdp, voir [ici](http://www.xrdp.org/).




<!--HONumber=Nov16_HO3-->


