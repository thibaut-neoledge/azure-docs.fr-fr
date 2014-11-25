<properties linkid="manage-linux-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Linux VHD in Azure" metaKeywords="Azure VHD, uploading Linux VHD" description="Learn to create and upload an Azure virtual hard disk (VHD) that has the Linux operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Linux Operating System" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="06/05/2014" ms.author="kathydav, szarkos" />

# Création et téléchargement d'un disque dur virtuel contenant le système d'exploitation Linux

Cet article vous montre comment créer et télécharger un disque dur virtuel pour que vous puissiez l'utiliser comme image personnelle pour créer des machines virtuelles dans Azure. Vous apprendrez comment préparer le système d'exploitation pour créer plusieurs machines virtuelles basées sur cette image.

> [WACOM.NOTE] Vous n'avez besoin d'aucune expérience préalable avec les machines virtuelles Azure pour suivre les étapes de cet article. Vous devez cependant posséder un compte Azure. Vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la page [Création d’un compte Azure][Création d’un compte Azure].

Une machine virtuelle Azure exécute le système d'exploitation basé sur l'image que vous avez choisie lors de sa création. Vos images sont conservées au format VHD, dans des fichiers .vhd au sein de votre compte de stockage. Pour plus d'informations sur les disques et les images dans Azure, consultez la page [Gestion des disques et des images][Gestion des disques et des images].

Lors de la création de la machine virtuelle, vous pouvez personnaliser certains paramètres du système d'exploitation pour qu'ils correspondent à l'application que vous souhaitez exécuter. Pour obtenir des instructions, consultez le guide [Création d'une machine virtuelle personnalisée][Création d'une machine virtuelle personnalisée].

**Important** : L'accord de niveau de service de la plateforme Azure s'applique aux machines virtuelles exécutant le système d'exploitation Linux uniquement lorsqu'une des distributions reconnues est utilisée avec les détails de configuration définis dans [cet article][cet article]. Toutes les distributions Linux fournies dans la galerie d'images Azure sont des distributions reconnues répondant à la configuration requise.

## Configuration requise

Cet article part du principe que vous disposez des éléments suivants :

-   **Un certificat de gestion** : vous avez créé un certificat de gestion pour l'abonnement pour lequel vous souhaitez télécharger un disque dur virtuel, puis exporté ce certificat vers un fichier .cer. Pour plus d'informations sur la création de certificats, consultez la page [Création d'un certificat de gestion pour Azure][Création d'un certificat de gestion pour Azure].

-   **Un système d'exploitation Linux installé dans un fichier .vhd.** - Vous avez installé un système d'exploitation Linux pris en charge sur un disque dur virtuel. Plusieurs outils permettent de créer des fichiers .vhd. Vous pouvez utiliser des solutions de virtualisation comme Hyper-V pour créer le fichier .vhd et installer le système d'exploitation. Pour obtenir des instructions, consultez la page [Installation du rôle Hyper-V et configuration d'une machine virtuelle][Installation du rôle Hyper-V et configuration d'une machine virtuelle].

    **Important** : Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide de Hyper-V Manager ou de la cmdlet convert-vhd.

    Pour accéder à la liste des distributions approuvées, consultez la page [Linux dans les distributions approuvées par Azure][Linux dans les distributions approuvées par Azure]. Vous pouvez également consulter la section située à la fin de cet article : [Informations pour les distributions non approuvées][Informations pour les distributions non approuvées].

-   **Outil en ligne de commande Linux Azure.** Si vous utilisez un système d'exploitation Linux pour créer votre image, utilisez cet outil pour télécharger le fichier VHD. Pour télécharger l'outil, consultez la page [Outils en ligne de commande Azure pour Linux et Mac][Outils en ligne de commande Azure pour Linux et Mac].

-   La **cmdlet Add-AzureVhd**, qui fait partie du module Azure PowerShell. Pour télécharger le module, consultez la page [Téléchargements Azure][Téléchargements Azure]. Pour les informations de référence, consultez la page [Add-AzureVhd][Add-AzureVhd].

**Attention aux éléments suivants pour l'ensemble des distributions :**

-   Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner.

-   L'agent Linux Azure (waagent) n'est pas compatible avec NetworkManager. La configuration réseau doit être contrôlée via les scripts ifup/ifdown. Dans la plupart des cas, NetworkManager, tel que configuré dans les packages RPM/Deb fournis avec les distributions, entre en conflit avec le package waagent, ce qui entraîne la désinstallation de NetworkManager lors de l'installation du package d'agent Linux. L'agent Linux Azure requiert également que le package python-pyasn1 soit installé.

-   NUMA n'est pas pris en charge pour les machines virtuelles de grande taille, en raison d'une erreur dans le noyau Linux dans les versions antérieures à 2.6.37. L'installation manuelle de waagent désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Ce problème touche spécialement les distributions utilisant le noyau Red Hat 2.6.32 en amont.

-   Il est déconseillé de créer une partition SWAP lors de l'installation. Il est possible de configurer l'espace SWAP en utilisant l'agent Linux Azure. Il est également déconseillé d'utiliser le noyau Linux principal avec une machine virtuelle Azure sans utiliser le correctif disponible sur le [site Web de Microsoft][site Web de Microsoft] (de nombreux noyaux/distributions actuels sont déjà susceptibles d'inclure cette rectification).

-   La taille des disques durs virtuels doit être un multiple de 1 Mo.

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : préparation de l'image pour le téléchargement][Étape 1 : préparation de l'image pour le téléchargement]
-   [Étape 2 : création d'un compte de stockage dans Azure][Étape 2 : création d'un compte de stockage dans Azure]
-   [Étape 3 : préparation de la connexion à Azure][Étape 3 : préparation de la connexion à Azure]
-   [Étape 4 : téléchargement de l'image sur Azure][Étape 4 : téléchargement de l'image sur Azure]

## <span id="prepimage"></span> </a>Étape 1 : préparation de l'image pour le téléchargement

### Préparation de CentOS 6.2+

Assurez-vous de suivre ces étapes de configuration dans le système d'exploitation pour pouvoir utiliser la machine virtuelle comme image.

1.  Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2.  Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3.  Exécutez la commande suivante pour désinstaller NetworkManager :

        rpm -e --nodeps NetworkManager

    **Remarque :** si le package n'est pas déjà installé, la commande échoue et un message d'erreur s'affiche. Ceci est normal.

4.  Créez un fichier nommé **network** dans le répertoire `/etc/sysconfig/` et entrez-y le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Créez un fichier nommé **ifcfg-eth0** dans le répertoire `/etc/sysconfig/network-scripts/` et entrez-y le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Windows Azure ou Hyper-V :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # chkconfig network on

8.  **CentOS 6.2 ou 6.3 uniquement** : Installez les pilotes pour les services d'intégration Linux

    **Remarque :** l'étape est uniquement valide pour CentOS 6.2 et 6.3. Sous CentOS 6.4+, les services d'intégration Linux sont déjà disponibles dans le noyau.

    a) Récupérez le fichier .iso qui contient les pilotes des services d'intégration Linux sur le [Centre de téléchargement][Centre de téléchargement].

    b) Dans Hyper-V Manager, dans le volet **Actions**, cliquez sur **Settings**.

    ![Ouvrez les paramètres Hyper-V][Ouvrez les paramètres Hyper-V]

    c) Dans le volet **Hardware**, cliquez sur **IDE Controller 1**.

    ![Ajoutez le lecteur DVD via le support d'installation][Ajoutez le lecteur DVD via le support d'installation]

    d) Dans la zone **IDE Controller**, cliquez sur **DVD Drive**, puis sur **Add**.

    e) Sélectionnez **Image file**, accédez à **Linux IC v3.2.iso**, puis cliquez sur **Open**.

    f) Sur la page **Settings**, cliquez sur **OK**.

    g) Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

    h) Dans la fenêtre d'invite de commande, saisissez la commande suivante :

        mount /dev/cdrom /media
        /media/install.sh
        reboot

9.  Saisissez la commande suivante pour installer python-pyasn1 :

        # sudo yum install python-pyasn1

10. Remplacez le fichier /etc/yum.repos.d/CentOS-Base.repo par le texte suivant :

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

11. Ajoutez la ligne suivante au fichier /etc/yum.conf :

        http_caching=packages

    Sur CentOS 6.2 et 6.3, ajoutez uniquement la ligne suivante :

        exclude=kernel*

12. Désactivez le module yum « fastestmirror » en modifiant le fichier "/etc/yum/pluginconf.d/fastestmirror.conf". Sous [main], entrez le texte suivant :

        set enabled=0

13. Exécutez la commande suivante pour effacer les métadonnées yum actuelles :

        yum clean all

14. Pour CentOS 6.2 et 6.3, mettez à jour le noyau d'une machine virtuelle en fonctionnement en exécutant les commandes suivantes.

    Pour CentOS 6.2, exécutez les commandes suivantes :

        sudo yum remove kernel-firmware
        sudo yum --disableexcludes=all install kernel

    Saisissez le texte suivant pour CentOS 6.3+ :

        sudo yum --disableexcludes=all install kernel

15. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez le fichier /boot/grub/menu.lst dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. De plus, cela désactivera NUNA, en raison d'une erreur dans la version du noyau utilisé par CentOS 6.

16. Sous /etc/sudoers, placez la ligne suivante en commentaire (si elle est présente) :

        Defaults targetpw

17. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.

18. Installez l'agent linux Azure en exécutant la commande suivante :

        yum install WALinuxAgent

    L'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s'ils n'avaient pas déjà été supprimés comme indiqué à l'étape 2.

19. Ne créez pas d'espace swap sur le disque du système d'exploitation.

    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

20. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        waagent -force -deprovision
        export HISTSIZE=0
        logout

21. Cliquez sur **Shutdown** dans Hyper-V Manager.

### Préparation de Ubuntu 12.04+

1.  Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2.  Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3.  Remplacez les référentiels actuels de votre image pour utiliser les référentiels Azure qui comportent le noyau et le package d'agent nécessaires à la mise à niveau de la machine virtuelle. Les étapes varient légèrement selon la version d'Ubuntu.

    Avant de modifier le fichier /etc/apt/sources.list, il est recommandé d'effectuer une sauvegarde
     sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04 :

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
        sudo apt-get update

    Ubuntu 12.10 :

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
        sudo apt-get update

    Ubuntu 13.04+ :

        sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
        sudo apt-get update

4.  Exécutez la commande suivante pour mettre à jour le système d'exploitation avec le dernier noyau :

    Ubuntu 12.04 :

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 12.10 :

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

    Ubuntu 13.04, 13.10 et 14.04 :

        sudo apt-get update
        sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
        sudo reboot

5.  Ubuntu marque une pause à l'invite Grub pour la saisie utilisateur après un échec système. Pour éviter ceci, procédez comme suit :

    a) Ouvrez le fichier /etc/grub.d/00\_header.

    b) Dans la fonction **make\_timeout()**, recherchez **if ["\\${recordfail}" = 1 ]; then**

    c) Modifiez le texte sous cette ligne comme suit : **set timeout=5**.

    d) Exécutez 'sudo update-grub'.

6.  Modifiez la ligne de démarrage du noyau afin que Grub y inclue les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez le fichier /etc/default/grub dans un éditeur de texte. Localisez la variable nommée « GRUB\_CMDLINE\_LINUX\_DEFAULT » (ou ajoutez-la le cas échéant) et modifiez-la pour inclure les paramètres suivants :

        GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

    Enregistrez ce fichier, puis fermez-le. Exécutez ensuite 'sudo update-grub'. Ce permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance technique d'Azure.

7.  Sous /etc/sudoers, placez la ligne suivante en commentaire (si elle est présente) :

        Defaults targetpw

8.  Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.

9.  Exécutez la commande suivante avec sudo pour installer l'agent :

        apt-get update
        apt-get install walinuxagent

    Notez que l'installation du package walinuxagent entraîne la suppression des packages NetworkManager et NetworkManager-gnome (s'ils sont installés).

10. Ne créez pas d'espace swap sur le disque du système d'exploitation.

    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Cliquez sur **Shutdown** dans Hyper-V Manager.

### Préparation d'Oracle Linux 6.4+

Vous devez suivre des étapes de configuration spécifiques dans le système d'exploitation afin que la machine virtuelle fonctionne sur Azure.

1.  Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2.  Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3.  Exécutez la commande suivante pour désinstaller NetworkManager :

        rpm -e --nodeps NetworkManager

    **Remarque :** si le package n'est pas déjà installé, la commande échoue et un message d'erreur s'affiche. Ceci est normal.

4.  Créez un fichier nommé **network** dans le répertoire `/etc/sysconfig/` et entrez-y le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5.  Créez un fichier nommé **ifcfg-eth0** dans le répertoire `/etc/sysconfig/network-scripts/` et entrez-y le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6.  Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Windows Azure ou Hyper-V :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.  Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # chkconfig network on

8.  Saisissez la commande suivante pour installer python-pyasn1 :

        # sudo yum install python-pyasn1

9.  Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez le fichier /boot/grub/menu.lst dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. De plus, cela désactivera NUNA, en raison d'une erreur dans la version du noyau utilisé par CentOS 6.

10. Sous /etc/sudoers, placez la ligne suivante en commentaire (si elle est présente) :

        Defaults targetpw

11. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.

12. Installez l'agent linux Azure en exécutant la commande suivante :

        yum install WALinuxAgent

    Notez que l'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome (s'ils sont installés).

13. Ne créez pas d'espace swap sur le disque du système d'exploitation.

    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        waagent -force -deprovision
        export HISTSIZE=0
        logout

15. Cliquez sur **Shutdown** dans Hyper-V Manager.

### Préparation de SUSE Linux Enterprise Server 11 SP2 & SP3

**REMARQUE :** [SUSE Studio][SUSE Studio] peut facilement créer et gérer vos images SLES/opeSUSE pour Azure et Hyper-V. En outre, les images officielles suivantes de SUSE Studio Gallery peuvent être téléchargées ou clonées dans votre propre compte SUSE Studio pour une personnalisation simplifiée -- [SLES 11 SP3 pour Azure dans SUSE Studio Gallery][SLES 11 SP3 pour Azure dans SUSE Studio Gallery].

1.  Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2.  Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3.  Ajoutez le référentiel contenant le dernier noyau et l'agent Linux Azure. Exécutez la commande `zypper lr`. Par exemple, avec SLES 11 SP3, le résultat devrait ressembler à l'exemple suivant :

        # | Alias                        | Name               | Enabled | Refresh
        --+------------------------------+--------------------+---------+--------
        1 | susecloud:SLES11-SP1-Pool    | SLES11-SP1-Pool    | No      | Yes
        2 | susecloud:SLES11-SP1-Updates | SLES11-SP1-Updates | No      | Yes
        3 | susecloud:SLES11-SP2-Core    | SLES11-SP2-Core    | No      | Yes
        4 | susecloud:SLES11-SP2-Updates | SLES11-SP2-Updates | No      | Yes
        5 | susecloud:SLES11-SP3-Pool    | SLES11-SP3-Pool    | Yes     | Yes
        6 | susecloud:SLES11-SP3-Updates | SLES11-SP3-Updates | Yes     | Yes

    Si la commande renvoie un message d'erreur similaire au suivant :

        "No repositories defined. Use the 'zypper addrepo' command to add one or more repositories."

    il peut être nécessaire de réactiver les référentiels ou d'enregistrer le système. Ces actions sont possibles via l'utilitaire suse\_register. Pour plus d'informations, consultez la [documentation SLES][documentation SLES].

	Si un des référentiels de mise à jour concernés n'est pas activé, exécutez la commande suivante :

        zypper mr -e [REPOSITORY NUMBER]

	Dans l'exemple ci-dessus, la commande exacte serait :

        zypper mr -e 1 2 3 4

1.  Mettez à jour le noyau vers la dernière version disponible :

        zypper up kernel-default

2.  Installez l'agent Linux Azure :

        zypper up WALinuxAgent

    Le résultat devrait ressemble à ce qui suit :

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor.
        Use 'zypper install WALinuxAgent-1.2-1.1.noarch' to install this candidate."

    Le fournisseur du package ayant changé de « Microsoft Corporation » vers « SUSE LINUX Products GmbH, Nuernberg, Germany », il est nécessaire d'installer explicitement le package mentionné dans le message.

    Remarque : La version du package WALinuxAgent peut être légèrement différente.

3.  Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez le fichier /boot/grub/menu.lst dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Ceci permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance d'Azure.

4.  Il est recommandé de modifier /etc/sysconfig/network/dhcp (ou son équivalent) de DHCLIENT\_SET\_HOSTNAME="yes" vers DHCLIENT\_SET\_HOSTNAME="no"

5.  Sous /etc/sudoers, placez la ligne suivante en commentaire (si elle est présente) :

        Defaults targetpw

6.  Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.

7.  Ne créez pas d'espace swap sur le disque du système d'exploitation.

    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Cliquez sur **Shutdown** dans Hyper-V Manager.

### Préparation d'openSUSE 12.3+

**REMARQUE :** [SUSE Studio][SUSE Studio] peut facilement créer et gérer vos images SLES/opeSUSE pour Azure et Hyper-V. En outre, les images officielles suivantes de SUSE Studio Gallery peuvent être téléchargées ou clonées dans votre propre compte SUSE Studio pour une personnalisation simplifiée :

> -   [openSUSE 13.1 pour Azure dans SUSE Studio Gallery][openSUSE 13.1 pour Azure dans SUSE Studio Gallery]

1.  Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.

2.  Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.

3.  Effectuez la mise à jour du système d'exploitation pour utiliser les derniers correctifs et la version la plus récente du noyau

4.  Sur l'interpréteur de commandes, exécutez la commande '`zypper lr`'. Si cette commande renvoie un résultat similaire au suivant (les numéros de version peuvent varier) :

        # | Alias                     | Name                      | Enabled | Refresh
        --+---------------------------+---------------------------+---------+--------
        1 | Cloud:Tools_openSUSE_12.3 | Cloud:Tools_openSUSE_12.3 | Yes     | Yes
        2 | openSUSE_12.3_OSS         | openSUSE_12.3_OSS         | Yes     | Yes
        3 | openSUSE_12.3_Updates     | openSUSE_12.3_Updates     | Yes     | Yes

	cela signifie que les référentiels sont configurés comme attendu ; aucune modification n'est nécessaire.

	Si la commande renvoie « No repositories defined. Use the 'zypper addrepo' command to add one or more repositories », cela signifie que les référentiels doivent être réactivés :

        zypper ar -f http://download.opensuse.org/distribution/12.3/repo/oss openSUSE_12.3_OSS
        zypper ar -f http://download.opensuse.org/update/12.3 openSUSE_12.3_Updates

	Vérifiez que vos référentiels ont été ajoutés en invoquant à nouveau 'zypper lr'.

	Si un des référentiels de mise à jour concernés n'est pas activé, exécutez la commande suivante :

        zypper mr -e [NUMBER OF REPOSITORY]

1.  Désactivez l'analyse automatique de DVD-ROM.

2.  Installez l'agent Linux Azure :

    Tout d'abord, ajoutez le référentiel contenant le nouvel agent WALinuxAgent: :

        zypper ar -f -r http://download.opensuse.org/repositories/Cloud:/Tools/openSUSE_12.3/Cloud:Tools.repo

    Exécutez ensuite la commande suivante :

        zypper up WALinuxAgent

    Après avoir exécuté cette commande, un message similaire à l'exemple ci-dessous peut s'afficher :

        "There is an update candidate for 'WALinuxAgent', but it is from different vendor. 
        Use 'zypper install WALinuxAgent' to install this candidate."

    Ceci est normal. Le fournisseur du package ayant changé de « Microsoft Corporation » vers « obs://build.opensuse.org/Cloud », il est nécessaire d'installer explicitement le package mentionné dans le message.

3.  Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour ce faire, ouvrez le fichier /boot/grub/menu.lst dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

    Ceci permet d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour l'assistance d'Azure. Supprimez également les paramètres suivants de la ligne de démarrage du noyau (s'ils sont présents) :

        libata.atapi_enabled=0 reserve=0x1f0,0x8

4.  Il est recommandé de modifier /etc/sysconfig/network/dhcp (ou son équivalent) de DHCLIENT\_SET\_HOSTNAME="yes" vers DHCLIENT\_SET\_HOSTNAME="no"

5.  Sous /etc/sudoers, placez la ligne suivante en commentaire (si elle est présente) :

        Defaults targetpw

6.  Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.

7.  Ne créez pas d'espace swap sur le disque du système d'exploitation.

    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

8.  Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        waagent -force -deprovision
        export HISTSIZE=0
        logout

9.  Vérifiez que l'agent Linux Azure est exécuté au démarrage :

        systemctl enable waagent.service

10. Cliquez sur **Shutdown** dans Hyper-V Manager.

## <span id="createstorage"></span> </a>Étape 2 : création d'un compte de stockage dans Azure

Un compte de stockage représente le plus haut niveau d'espace de noms pour l'accès aux services de stockage et est associé avec votre abonnement Azure. Pour télécharger vers Azure un fichier .vhd permettant de créer une machine virtuelle, vous avez besoin d'un compte de stockage Azure. Vous pouvez créer un compte de stockage à partir du portail de gestion Azure..

1.  Connectez-vous au portail de gestion Azure.

2.  Dans la barre de commandes, cliquez sur **New**.

    ![Créez un compte de stockage][Créez un compte de stockage]

3.  Cliquez sur **Storage Account**, puis sur **Quick Create**.

    ![Créer rapidement un compte de stockage][Créer rapidement un compte de stockage]

4.  Remplissez les champs comme suit :

    ![Entrer les détails du compte de stockage][Entrer les détails du compte de stockage]

-   Sous **URL**, entrez un nom de sous-domaine à utiliser dans l'URL du compte de stockage. L'entrée peut être composée de 3 à 24 lettres minuscules et chiffres. Ce nom devient le nom d'hôte contenu dans l'URL utilisée pour adresser les ressources d'objets blob, de files d'attente et de tables pour l'abonnement.

-   Sélectionnez l'emplacement ou le groupe d'affinités pour le compte de stockage. En indiquant un groupe d'affinités, vous pouvez localiser vos services cloud dans le même centre de données, avec votre stockage.

-   Indiquez si vous souhaitez utiliser la géo-réplication pour le compte de stockage. La géo-réplication est activée par défaut. Cette option permet une réplication gratuite de vos données vers un emplacement secondaire, pour que votre stockage puisse basculer vers l'emplacement secondaire en cas de panne sur l'emplacement principal. L'emplacement secondaire est attribué automatiquement. Vous ne pouvez pas le modifier. Si des obligations légales ou des stratégies organisationnelles requièrent un meilleur contrôle de l'emplacement de votre stockage sur le cloud, vous pouvez désactiver la géo-réplication. Cependant, sachez que si vous réactivez la géo-localisation ultérieurement, la réplication de vos données vers un emplacement secondaire sera facturée au tarif d'un transfert unique. Vous pouvez bénéficier d'une réduction pour les services de stockage sans géo-réplication.

1.  Cliquez sur **Create Storage Account**.

    Le compte apparaît à présent sous **Comptes de stockage**.

    ![Compte de stockage correctement créé][Compte de stockage correctement créé]

## <span id="#connect"></span> </a>Étape 3 : préparation de la connexion à Azure

Avant de pouvoir télécharger un fichier .vhd, vous devez établir une connexion sécurisée entre votre ordinateur et votre abonnement Azure.

1.  Ouvrez une fenêtre Azure PowerShell.

2.  Type :

    `Get-AzurePublishSettingsFile`

    Cette commande ouvre une fenêtre de navigateur et télécharge automatiquement un fichier .publishsettings contenant des informations et un certificat pour votre abonnement Azure.

3.  Enregistrez le fichier .publishsettings.

4.  Type :

    `Import-AzurePublishSettingsFile <PathToFile>`

    Où `<PathToFile>` correspond au chemin d'accès complet vers le fichier .publishsettings.

    Pour plus d'informations, consultez la page [Prise en main des cmdlets Azure][Prise en main des cmdlets Azure]

## <span id="upload"></span> </a>Étape 4 : téléchargement de l'image sur Azure

Lorsque vous téléchargez le fichier .vhd, vous pouvez le placer n'importe où dans votre stockage d'objets blob. Dans les exemples de commandes suivants, **BlobStorageURL** correspond à l'URL du compte de stockage que vous avez créé lors de l'étape 2 ; **YourImagesFolder** est le conteneur du stockage d'objets blob où vous voulez stocker vos images. **VHDName** est l'étiquette affichée dans le portail de gestion pour identifier le disque dur virtuel. **PathToVHDFile** est le chemin d'accès complet et le nom du fichier .vhd.

Effectuez l'une des actions suivantes :

-   Depuis la fenêtre Azure PowerShell utilisée lors de l'étape précédente, tapez :

    `Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

    Pour plus d'informations, consultez la page [Add-AzureVhd][1].

-   Utilisez l'outil en commande de ligne Linux pour télécharger l'image. La commande suivante permet de télécharger une image :

        Azure vm image create <image name> --location <Location of the data center> --OS Linux <Sourcepath to the vhd>

## <span id="nonendorsed"></span> </a>Informations concernant les distributions non validées

Par essence, l'ensemble des distributions exécutées sur Azure doivent rassembler les conditions suivantes pour pouvoir fonctionner correctement sur la plate-forme.

Cette liste n'est pas exhaustive, car chaque distribution est différente. Il est également possible que, même en répondant à tous ces critères, il s'avère nécessaire de modifier votre image pour assurer son bon fonctionnement sur la plateforme.

C'est pour cette raison qu'il est recommandé de commencer avec une des [images validées par nos partenaires][images validées par nos partenaires].

La liste ci-dessous remplace l'étape 1 de la procédure pour créer votre propre disque dur virtuel :

1.  Vous devez vous assurer, au choix, que vous utilisez un noyau qui incorpore les derniers pilotes LIS pour Hyper V ou que les avez compilés correctement (ils sont en open source). Les pilotes sont disponibles [ici][ici]

2.  Votre noyau doit également comprendre la dernière version du pilote ATA PiiX, utilisé pour déployer les images. Il doit également comporter les correctifs validés pour le noyau sous la référence commit cd006086fa5d91414d8ff9ff2b78fbb593878e3c Date: Fri May 4 22:15:11 2012 +0100 ata\_piix: defer disks to the Hyper-V drivers by default

3.  Votre initrd compressé doit être inférieur à 40 Mo (\* nous travaillons continuellement à l'augmentation de cette valeur. Elle peut avoir évolué)

4.  Modifiez la ligne de démarrage du noyau dans grub ou grub2 afin d'y inclure les paramètres suivants. Ceci permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300

5.  Il est recommandé de modifier /etc/sysconfig/network/dhcp (ou son équivalent) de DHCLIENT\_SET\_HOSTNAME="yes" vers DHCLIENT\_SET\_HOSTNAME="no"

6.  Assurez-vous que tous les périphériques SCSI montés sur votre noyau incluent un délai d'expiration E/S de 300 secondes ou plus.

7.  Il sera nécessaire d'installer l'agent Linux Azure comme indiqué dans le [Guide de l'agent Linux][Guide de l'agent Linux]. L'agent est publié sous licence Apache 2. Toutes les informations sont disponibles à l'[emplacement GitHub de l'agent][emplacement GitHub de l'agent]

8.  Sous /etc/sudoers, placez la ligne suivante en commentaire (si elle est présente) :

        Defaults targetpw

9.  Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu.

10. Ne créez pas d'espace swap sur le disque du système d'exploitation.

    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11. Exécutez la commande suivante pour annuler le déploiement de la machine virtuelle :

        waagent -force -deprovision
        export HISTSIZE=0
        logout

12. Vous devez ensuite arrêter la machine virtuelle, puis poursuivre le téléchargement.

  [Création d’un compte Azure]: http://www.windowsazure.com/fr-fr/develop/php/tutorials/create-a-windows-azure-account/
  [Gestion des disques et des images]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj672979.aspx
  [Création d'une machine virtuelle personnalisée]: /fr-fr/manage/windows/how-to-guides/custom-create-a-vm/
  [cet article]: http://support.microsoft.com/kb/2805216
  [Création d'un certificat de gestion pour Azure]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
  [Installation du rôle Hyper-V et configuration d'une machine virtuelle]: http://technet.microsoft.com/library/hh846766.aspx
  [Linux dans les distributions approuvées par Azure]: ../linux-endorsed-distributions
  [Informations pour les distributions non approuvées]: #nonendorsed
  [Outils en ligne de commande Azure pour Linux et Mac]: http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409
  [Téléchargements Azure]: /fr-fr/develop/downloads/
  [Add-AzureVhd]: http://msdn.microsoft.com/library/windowsazure/dn495173.aspx
  [site Web de Microsoft]: http://go.microsoft.com/fwlink/?LinkID=253692&clcid=0x409
  [Étape 1 : préparation de l'image pour le téléchargement]: #prepimage
  [Étape 2 : création d'un compte de stockage dans Azure]: #createstorage
  [Étape 3 : préparation de la connexion à Azure]: #connect
  [Étape 4 : téléchargement de l'image sur Azure]: #upload
  [Centre de téléchargement]: http://www.microsoft.com/fr-fr/download/details.aspx?id=34603
  [Ouvrez les paramètres Hyper-V]: ./media/virtual-machines-linux-create-upload-vhd/settings.png
  [Ajoutez le lecteur DVD via le support d'installation]: ./media/virtual-machines-linux-create-upload-vhd/installiso.png
  [SUSE Studio]: http://www.susestudio.com
  [SLES 11 SP3 pour Azure dans SUSE Studio Gallery]: http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure
  [documentation SLES]: https://www.suse.com/documentation/sles11/
  [openSUSE 13.1 pour Azure dans SUSE Studio Gallery]: https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure
  [Créez un compte de stockage]: ./media/virtual-machines-linux-create-upload-vhd/create.png
  [Créer rapidement un compte de stockage]: ./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png
  [Entrer les détails du compte de stockage]: ./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png
  [Compte de stockage correctement créé]: ./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png
  [Prise en main des cmdlets Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj554332.aspx
  [1]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn205185.aspx
  [images validées par nos partenaires]: https://www.windowsazure.com/fr-fr/manage/linux/other-resources/endorsed-distributions/
  [ici]: http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409
  [Guide de l'agent Linux]: https://www.windowsazure.com/fr-fr/manage/linux/how-to-guides/linux-agent-guide/
  [emplacement GitHub de l'agent]: http://go.microsoft.com/fwlink/p/?LinkID=250998&clcid=0x409
