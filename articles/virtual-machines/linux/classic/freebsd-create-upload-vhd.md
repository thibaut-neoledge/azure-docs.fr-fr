---
title: "Création et téléchargement d’une image de machine virtuelle FreeBSD | Microsoft Docs"
description: "Découvrez comment créer et télécharger un disque dur virtuel (VHD) contenant le système d&quot;exploitation FreeBSD pour créer une machine virtuelle Azure."
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/29/2016
ms.author: kyliel
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 2d44a2d9a247ffce8bcf35152170562ac0b86710
ms.lasthandoff: 04/27/2017


---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Création et téléchargement d’un disque dur virtuel FreeBSD dans Azure
Cet article vous montre comment créer et télécharger un disque dur virtuel (VHD) contenant le système d'exploitation FreeBSD. Après l’avoir téléchargé, vous pouvez l’utiliser comme image personnelle pour créer une machine virtuelle (VM) dans Azure.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur le téléchargement d'un disque virtuel avec le modèle Resource Manager, suivez [ce lien](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Composants requis
Cet article part du principe que vous disposez des éléments suivants :

* **Abonnement Azure**: si vous ne possédez pas de compte, vous pouvez en créer un en quelques minutes. Si vous disposez d’un abonnement MSDN, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Dans le cas contraire, découvrez comment [créer un compte d'essai gratuit](https://azure.microsoft.com/pricing/free-trial/).  
* **Outils Azure PowerShell**: le module Azure PowerShell doit être installé et configuré de façon à utiliser votre abonnement Azure. Pour télécharger le module, consultez la page [Téléchargements Azure](https://azure.microsoft.com/downloads/). Un didacticiel sur l’installation et la configuration du module est disponible ici. Utilisez l’applet de commande [Téléchargements Azure](https://azure.microsoft.com/downloads/) pour télécharger le disque dur virtuel.
* **Système d’exploitation FreeBSD installé dans un fichier .vhd**: un système d’exploitation FreeBSD pris en charge doit être installé sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Par exemple, vous pouvez utiliser une solution de virtualisation comme Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions pour installer et utiliser Hyper-V, consultez la page [Installer Hyper-V et créer une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide du Gestionnaire Hyper-V ou de l’applet de commande [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). En outre, il existe un [didacticiel sur MSDN sur l’utilisation de FreeBSD avec Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Cette tâche comprend les cinq étapes suivantes.

## <a name="step-1-prepare-the-image-for-upload"></a>Étape 1 : préparation de l'image pour le téléchargement
Sur la machine virtuelle où vous avez installé le système d’exploitation FreeBSD, effectuez les procédures suivantes :

1. Activez DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Activez SSH.

    SSH est activé par défaut après l’installation à partir du disque. S’il n’est pas activé pour une raison quelconque ou si vous utilisez un disque dur virtuel FreeBSD directement, tapez ce qui suit :

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart
3. Configurez une console série.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Installez sudo.

    Le compte racine est désactivé dans Azure. Cela signifie que vous devez utiliser sudo à partir d'un utilisateur sans privilège pour exécuter des commandes avec des privilèges élevés.

        # pkg install sudo
   ;
5. Composants requis pour l'Agent Azure.

        # pkg install python27  
        # pkg install Py27-setuptools27   
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Installez l’Agent Azure.

    La dernière version de l’agent Azure se trouve toujours sur [github](https://github.com/Azure/WALinuxAgent/releases). La version 2.0.10 + prend officiellement en charge FreeBSD 10 et 10.1 et la version 2.1.4 prend officiellement en charge FreeBSD 10.2 et les versions ultérieures.

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
        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log
7. Annulez l’approvisionnement du système.

    Annulez l’approvisionnement du système pour le nettoyer et le préparer pour son réapprovisionnement. La commande suivante supprime également le dernier compte d'utilisateur alloué et les données associées :

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Vous pouvez à présent arrêter votre machine virtuelle.

## <a name="step-2-create-a-storage-account-in-azure"></a>Étape 2 : création d'un compte de stockage dans Azure
Vous avez besoin d’un compte de stockage dans Azure pour télécharger un fichier .vhd qui permettra de créer une machine virtuelle. Pour créer un tel compte, vous pouvez utiliser l’édition classique du portail Azure.

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Dans la barre de commandes, sélectionnez **Nouveau**.
3. Sélectionnez **Services de données** > **Stockage** > **Création rapide**.

    ![Créer rapidement un compte de stockage](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. Remplissez les champs comme suit :

   * Dans le champ **URL** , tapez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L’entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte figurant dans l'URL utilisée pour adresser les ressources de stockage d'objets blob Azure, de stockage de files d'attente Azure et de stockage de tables Azure pour l'abonnement.
   * Dans le menu déroulant **Groupe d'emplacements ou d'affinités**, choisissez le **groupe d'emplacements ou d'affinités** pour le compte de stockage. Un groupe d’affinités vous permet de mettre votre stockage et vos services cloud dans le même centre de données.
   * Dans le champ **Réplication**, indiquez si vous souhaitez utiliser la réplication **géoredondante** pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers cet emplacement en cas de panne sur l’emplacement principal. L'emplacement secondaire est affecté automatiquement. Vous ne pouvez pas le modifier. Si vous avez besoin de disposer d’un contrôle accru sur l’emplacement de votre stockage reposant sur le cloud du fait d’exigences juridiques ou de la stratégie de l’organisation, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d’une réduction pour les services de stockage sans géo-réplication. Vous trouverez plus d'informations sur la gestion de la géoréplication des comptes de stockage ici : [Réplication Azure Storage](../../../storage/storage-redundancy.md).

     ![Entrer les détails du compte de stockage](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. Sélectionnez **Créer un compte de stockage**. Le compte apparaît à présent sous **stockage**.

    ![Compte de stockage correctement créé](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. Ensuite, créez un conteneur pour vos fichiers .vhd téléchargés. Sélectionnez le nom du compte de stockage, puis **Conteneurs**.

    ![Détails du compte de stockage](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. Sélectionnez **Créer un conteneur**.

    ![Détails du compte de stockage](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. Dans le champ **Nom** , tapez un nom pour votre conteneur. Ensuite, dans le menu déroulant **Accès** , sélectionnez le type de stratégie d’accès que vous souhaitez.

    ![Nom du conteneur](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l’accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés ni à ses métadonnées, utilisez l’option **Objet BLOB public** . Pour autoriser un accès public total en lecture au conteneur et aux objets blob, utilisez l’option **Conteneur public** .
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>Étape 3 : préparation de la connexion à Azure
Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure. Pour ce faire, vous pouvez utiliser la méthode Azure Active Directory (Azure AD) ou la méthode par certificat.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Utilisez la méthode Azure AD pour télécharger un fichier .vhd
1. Ouvrez la console Azure PowerShell.
2. Tapez la commande suivante :   
    `Add-AzureAccount`

    Cette commande ouvre une fenêtre de connexion qui vous permet de vous connecter avec votre compte professionnel ou scolaire.

    ![Fenêtre PowerShell](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure authentifie et enregistre les informations d’identification. Ensuite, la fenêtre est fermée.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Utilisez la méthode par certificat pour télécharger un fichier .vhd
1. Ouvrez la console Azure PowerShell.
2. Entrez :  `Get-AzurePublishSettingsFile`.
3. Une fenêtre de navigateur apparaît et vous invite à télécharger un fichier .publishsettings. Ce fichier contient des informations et un certificat pour votre abonnement Azure.

    ![Page de téléchargement du navigateur](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. Enregistrez le fichier .publishsettings.
5. Tapez :  `Import-AzurePublishSettingsFile <PathToFile>`, où `<PathToFile>` est le chemin d'accès complet au fichier .publishsettings.

   Pour plus d'informations, consultez la page [Prise en main des applets de commande Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Pour plus d’informations sur l’installation et la configuration de PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="step-4-upload-the-vhd-file"></a>Étape 4 : téléchargement du fichier .vhd
Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n’importe où dans votre stockage d’objets blob. Voici certains termes que vous utiliserez quand vous téléchargerez le fichier :

* **BlobStorageURL** correspond à l’URL du compte de stockage que vous avez créé à l’étape 2.
* **YourImagesFolder** est le conteneur au sein du stockage d’objets blob dans lequel vous souhaitez stocker vos images.
* **VHDName** est l’étiquette affichée dans le portail Azure Classic pour identifier le disque dur virtuel.
* **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd.

Depuis la fenêtre Azure PowerShell utilisée lors de l’étape précédente, tapez :

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Étape 5 : création d’une machine virtuelle avec le fichier .vhd téléchargé
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

