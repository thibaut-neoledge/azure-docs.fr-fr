---
title: "Création et téléchargement d’une image de machine virtuelle FreeBSD | Microsoft Docs"
description: "Découvrez comment créer et télécharger un disque dur virtuel (VHD) contenant le système d'exploitation FreeBSD pour créer une machine virtuelle Azure."
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Création et téléchargement d’un disque dur virtuel FreeBSD dans Azure
Cet article vous montre comment créer et télécharger un disque dur virtuel (VHD) contenant le système d'exploitation FreeBSD. Après l’avoir téléchargé, vous pouvez l’utiliser comme image personnelle pour créer une machine virtuelle (VM) dans Azure.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur le téléchargement d'un disque virtuel avec le modèle Resource Manager, suivez [ce lien](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Composants requis
Cet article part du principe que vous disposez des éléments suivants :

* **Abonnement Azure**: si vous ne possédez pas de compte, vous pouvez en créer un en quelques minutes. Si vous disposez d’un abonnement MSDN, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dans le cas contraire, découvrez comment [créer un compte d'essai gratuit](https://azure.microsoft.com/pricing/free-trial/).  
* **Outils Azure PowerShell**: le module Azure PowerShell doit être installé et configuré de façon à utiliser votre abonnement Azure. Pour télécharger le module, consultez la page [Téléchargements Azure](https://azure.microsoft.com/downloads/). Un didacticiel expliquant comment installer et configurer le module est disponible ici. Utilisez l’applet de commande [Téléchargements Azure](https://azure.microsoft.com/downloads/) pour télécharger le disque dur virtuel.
* **Système d’exploitation FreeBSD installé dans un fichier .vhd**: un système d’exploitation FreeBSD pris en charge doit être installé sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Par exemple, vous pouvez utiliser une solution de virtualisation comme Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions pour installer et utiliser Hyper-V, consultez la page [Installer Hyper-V et créer une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide du Gestionnaire Hyper-V ou de l’applet de commande [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). En outre, il existe un [didacticiel sur MSDN sur l’utilisation de FreeBSD avec Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Cette tâche comprend les quatre étapes suivantes :

## <a name="step-1-prepare-the-image-for-upload"></a>Étape 1 : préparation de l'image pour le téléchargement
Sur la machine virtuelle où vous avez installé le système d’exploitation FreeBSD, effectuez les procédures suivantes :

1. Activez DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Activez SSH.

    Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. Il est activé par défaut après l’installation à partir du disque FreeBSD. 
3. Configurez une console série.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Installez sudo.

    Le compte racine est désactivé dans Azure. Cela signifie que vous devez utiliser sudo à partir d'un utilisateur sans privilège pour exécuter des commandes avec des privilèges élevés.

        # pkg install sudo
   
5. Composants requis pour l'Agent Azure.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Installez l’Agent Azure.

    La dernière version de l’agent Azure se trouve toujours sur [github](https://github.com/Azure/WALinuxAgent/releases). La version 2.0.10 + prend officiellement en charge FreeBSD 10 et 10.1 et la version 2.1.4 + (2.2.x inclus) prend officiellement en charge FreeBSD 10.2 et les versions ultérieures.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    Pour la version 2.0, utilisons 2.0.16 comme exemple :

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Pour la version 2.1, utilisons 2.1.4 comme exemple :

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > Après avoir installé l’Agent Azure, il est judicieux de vérifier qu’il s’exécute :
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Annulez l’approvisionnement du système.

    Annulez l’approvisionnement du système pour le nettoyer et le préparer pour son réapprovisionnement. La commande suivante supprime également le dernier compte d'utilisateur alloué et les données associées :

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Vous pouvez à présent arrêter votre machine virtuelle.

## <a name="step-2-prepare-the-connection-to-azure"></a>Étape 2 : préparation de la connexion à Azure
Assurez-vous que vous utilisez l’interface de ligne de commande Azure dans le modèle de déploiement Classic (`azure config mode asm`), puis connectez-vous à votre compte :

```azurecli
azure login
```


<a id="upload"></a>


## <a name="step-3-upload-the-vhd-file"></a>Étape 3 : téléchargement du fichier .vhd

Vous avez besoin d’un compte de stockage vers lequel charger votre fichier de disque dur virtuel. Vous pouvez choisir un compte de stockage existant ou [en créer un](../../../storage/common/storage-create-storage-account.md).

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n’importe où dans votre stockage d’objets blob. Voici certains termes que vous utiliserez quand vous chargez le fichier :

* **BlobStorageURL** correspond à l’URL du compte de stockage que vous avez créé à l’étape 2.
* **YourImagesFolder** est le conteneur au sein du stockage d’objets blob dans lequel vous souhaitez stocker vos images.
* **VHDName** est l’étiquette affichée dans le portail Azure Classic pour identifier le disque dur virtuel.
* **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd.

Depuis la fenêtre Azure PowerShell utilisée lors de l’étape précédente, tapez :

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>Étape 4 : création d’une machine virtuelle avec le fichier .vhd chargé
Après avoir téléchargé le fichier .vhd, vous pouvez l'ajouter en tant qu'image à la liste des images personnalisées associées à votre abonnement et créer une machine virtuelle avec cette image personnalisée.

1. Depuis la fenêtre Azure PowerShell utilisée lors de l’étape précédente, tapez :

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Utilisez Linux comme type de système d’exploitation. La version actuelle d’Azure PowerShell accepte uniquement « Linux » et « Windows » comme paramètre.
   >
   >
2. À l’issue de ces étapes, la nouvelle image apparaît sous l’onglet **Images** du portail Azure Classic.  

    ![Choose an image](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Créez une machine virtuelle à partir de la galerie. Cette nouvelle image est maintenant disponible sous **Mes Images**.
4. Sélectionnez la nouvelle image. Ensuite, suivez les invites pour configurer un nom d'hôte, le mot de passe, la clé SSH, etc.

    ![Image personnalisée](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. Une fois l’approvisionnement terminé, vous verrez votre machine virtuelle FreeBSD s’exécuter dans Azure.

    ![Image FreeBSD dans Azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
