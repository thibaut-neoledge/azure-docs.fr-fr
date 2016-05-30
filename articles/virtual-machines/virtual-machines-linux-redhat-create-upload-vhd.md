<properties
	pageTitle="Création et téléchargement d’un disque dur virtuel Red Hat Enterprise Linux pour une utilisation dans Azure"
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Red Hat Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="mingzhan"/>


# Préparation d'une machine virtuelle Red Hat pour Azure

Dans cet article, vous allez apprendre à préparer une machine virtuelle Red Hat Enterprise Linux (RHEL) à utiliser dans Azure. Cet article couvre les versions de RHEL 6.7, 7.1 et 7.2. Les hyperviseurs de préparation abordés dans cet article sont Hyper-V, KVM (Machine virtuelle basée sur le noyau) et VMware. Pour plus d’informations sur les conditions d’éligibilité pour participer au programme d’accès au Cloud de Red Hat, consultez le [site Web d’accès au cloud de Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) et [Exécution RHEL sous Azure](https://access.redhat.com/articles/1989673).

[Préparer une machine virtuelle RHEL 6.7 à partir du Gestionnaire Hyper-V](#rhel67hyperv)

[Préparer une machine virtuelle RHEL 7.1/7.2 à partir du Gestionnaire Hyper-V](#rhel7xhyperv)

[Préparer une machine virtuelle RHEL 6.7 à partir de KMV](#rhel67kvm)

[Préparer une machine virtuelle RHEL 7.1/7.2 à partir de KMV](#rhel7xkvm)

[Préparer une machine virtuelle RHEL 6.7 à partir de VMWare](#rhel67vmware)

[Préparer une machine virtuelle RHEL 7.1/7.2 à partir de VMWare](#rhel7xvmware)

[Préparer une machine virtuelle RHEL 7.1/7.2 à partir d'un fichier Kickstart](#rhel7xkickstart)


## Préparer une machine virtuelle Red Hat à partir du Gestionnaire Hyper-V
### Composants requis
Cette section repose sur l’hypothèse que vous avez déjà installé une image RHEL (provenant d’un fichier ISO obtenu à partir du site web de Red Hat) sur un disque dur virtuel (VHD). Pour plus d’informations sur l’utilisation du Gestionnaire Hyper-V pour installer une image de système d’exploitation, voir [Installer le rôle Hyper-V et configurer une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d'installation de RHEL**

- Consultez également les [Notes générales d’installation sous Linux](virtual-machines-linux-create-upload-generic.md#general-linux-installation-notes) pour obtenir d’autres conseils sur la préparation de Linux pour Azure.

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide du Gestionnaire Hyper-V ou de l’applet de commande Powershell **convert-vhd**.

- Les VHD doivent être créés comme étant fixes (« fixed »), car les VHD dynamiques ne sont pas pris en charge.

- Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou [RAID](virtual-machines-linux-configure-raid.md) sont utilisables sur les disques de données si vous le souhaitez.

- Ne configurez pas une partition d’échange sur le disque du système d’exploitation. Vous pouvez configurer l’agent Linux pour la création d’un fichier d’échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.

- Lorsque vous utilisez **qemu-img** pour convertir des images de disque au format VHD, notez qu’un bogue connu touche la version 2.2.1 de qemu-img ou ses versions ultérieures. Celui-ci entraîne un formatage incorrect du VHD. Ce problème devrait être résolu dans une prochaine version de qemu-img. Pour l’instant, nous vous recommandons d’utiliser la version 2.2.0 de qemu-img ou une version antérieure.

### <a id="rhel67hyperv"> </a>Préparer une machine virtuelle RHEL 6.7 à partir du Gestionnaire Hyper-V###


1.	Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2.	Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3.	Exécutez la commande suivante pour désinstaller NetworkManager :

        # sudo rpm -e --nodeps NetworkManager

    Notez que si le package n’est pas encore installé, la commande échoue et un message d’erreur s’affiche. Ceci est normal.

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

6.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lorsque vous clonez une machine virtuelle dans Microsoft Azure ou Hyper-V :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

8.	Inscrivez votre abonnement Red Hat de façon à permettre l’installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Le package WALinuxAgent `WALinuxAgent-<version>` a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cette opération désactive NUMA en raison d’un bogue dans la version de noyau utilisée par RHEL 6.

    Outre l’action ci-dessus, nous vous recommandons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

    L’option crashkernel peut éventuellement être conservée. Notez cependant que ce paramètre réduira d’au moins 128 Mo la quantité de mémoire disponible dans la machine virtuelle. Cela peut se révéler problématique sur des machines virtuelles de petite taille.

11.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez /etc/ssh/sshd\_config pour inclure la ligne suivante :

        ClientAliveInterval 180

12.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    L'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s'ils n'avaient pas déjà été supprimés comme indiqué à l'étape 2.

13.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Une fois que vous avez installé l’agent Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14.	Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # sudo subscription-manager unregister

15.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

16.	Cliquez sur **Action > Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

### <a id="rhel7xhyperv"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 à partir du Gestionnaire Hyper-V###

1.  Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2.	Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

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

5.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

6.	Inscrivez votre abonnement Red Hat de façon à permettre l’installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre **GRUB\_CMDLINE\_LINUX**. Par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Outre l’action ci-dessus, nous vous recommandons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

	Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L’option crashkernel peut éventuellement être conservée. Notez cependant que ce paramètre réduira d’au moins 128 Mo la quantité de mémoire disponible dans la machine virtuelle. Cela peut se révéler problématique sur des machines virtuelles de petite taille.

8.	Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

10.	Le package WALinuxAgent `WALinuxAgent-<version>` a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

12.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Si vous souhaitez annuler l'inscription de l'abonnement, exécutez la commande suivante :

        # sudo subscription-manager unregister

14.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Cliquez sur **Action > Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


## Préparer une machine virtuelle Red Hat à partir de KVM

### <a id="rhel67kvm"> </a>Préparer une machine virtuelle RHEL 6.7 à partir de KMV###


1.	Téléchargez l'image KVM de RHEL 6.7 depuis le site web de Red Hat.

2.	Définissez un mot de passe racine.

    Générez un mot de passe chiffré et copiez la sortie de la commande :

        # openssl passwd -1 changeme

    Définissez un mot de passe racine avec guestfish :

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

	Modifiez le second champ de l’utilisateur racine en remplaçant « !! » par le mot de passe chiffré.

3.	Créez une machine virtuelle dans KVM à partir de l’image qcow2, définissez le type de disque sur **qcow2**, puis définissez le modèle d’appareil de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite la machine virtuelle, puis connectez-vous en tant que racine.

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

6.	Déplacez (ou supprimez) les règles udev afin d’éviter la génération de règles statiques pour l’interface Ethernet. Ces règles entraînent des problèmes lorsque vous clonez une machine virtuelle dans Microsoft Azure ou Hyper-V :

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # chkconfig network on

8.	Inscrivez votre abonnement Red Hat de façon à permettre l’installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cette opération désactive NUMA en raison d’un bogue dans la version de noyau utilisée par RHEL 6.

    Outre l’action ci-dessus, nous vous recommandons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L’option crashkernel peut éventuellement être conservée. Notez cependant que ce paramètre réduira d’au moins 128 Mo la quantité de mémoire disponible dans la machine virtuelle. Cela peut se révéler problématique sur des machines virtuelles de petite taille.

10. Ajoutez des modules de Hyper-V dans initramfs :

    Modifiez `/etc/dracut.conf` en y ajoutant le contenu suivant : add\_drivers+=”hv\_vmbus hv\_netvsc hv\_storvsc”

    Régénérez initramfs : # dracut –f -v

11.	Désinstallez Cloud-Init :

        # yum remove cloud-init

12.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu :

        # chkconfig sshd on

    Modifiez /etc/ssh/sshd\_config pour y inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

    Redémarrez sshd :

		# service sshd restart

13.	Le package WALinuxAgent `WALinuxAgent-<version>` a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14.	Installez l'agent linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent
        # chkconfig waagent on

15.	L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Une fois que vous avez installé l’agent Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans le fichier **/etc/waagent.conf** :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # subscription-manager unregister

17.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18.	Arrêtez la machine virtuelle dans KVM.

19.	Convertissez l’image qcow2 au format VHD. Convertissez tout d'abord l'image au format RAW :

         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Assurez-vous que la taille de l’image RAW est alignée sur 1 Mo. Dans le cas contraire, arrondissez la taille pour l’aligner sur 1 Mo : # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Convertissez le disque brut en VHD à taille fixe :

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xkvm"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 à partir de KMV###


1.	Téléchargez l’image KVM de RHEL 7.1 (ou 7.2) à partir du site web de Red Hat. Nous utiliserons RHEL 7.1, comme l’exemple présenté ici.

2.	Définissez un mot de passe racine.

    Générez un mot de passe chiffré et copiez la sortie de la commande :

        # openssl passwd -1 changeme

    Définissez un mot de passe racine avec guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Modifiez le second champ de l'utilisateur racine en remplaçant « !! » par le mot de passe chiffré.

3.	Créez une machine virtuelle dans KVM à partir de l’image qcow2, définissez le type de disque sur **qcow2**, puis définissez le modèle d’appareil de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite la machine virtuelle, puis connectez-vous en tant que racine.

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

6.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # chkconfig network on

7.	Inscrivez l'abonnement Red Hat pour permettre l'installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre **GRUB\_CMDLINE\_LINUX**. Par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Outre l’action ci-dessus, nous vous recommandons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L’option crashkernel peut éventuellement être conservée. Notez cependant que ce paramètre réduira d’au moins 128 Mo la quantité de mémoire disponible dans la machine virtuelle. Cela peut se révéler problématique sur des machines virtuelles de petite taille.

9.	Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Ajoutez des modules de Hyper-V dans initramfs :

    Modifiez `/etc/dracut.conf` en y ajoutant le contenu suivant :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Régénérez initramfs :

        # dracut –f -v

11.	Désinstallez Cloud-Init :

        # yum remove cloud-init

12.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu :

        # systemctl enable sshd

    Modifiez /etc/ssh/sshd\_config pour y inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

    Redémarrez sshd :

        systemctl restart sshd

13.	Le package WALinuxAgent `WALinuxAgent-<version>` a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14.	Installez l'agent linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent

    Activez le service waagent :

        # systemctl enable waagent.service

15.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16.	Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # subscription-manager unregister

17.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

18.	Arrêtez la machine virtuelle dans KVM.

19.	Convertissez l’image qcow2 au format VHD.

    Convertissez tout d'abord l'image au format RAW :

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Assurez-vous que la taille de l’image RAW est alignée sur 1 Mo. Dans le cas contraire, arrondissez la taille pour l’aligner sur 1 Mo :

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Convertissez le disque brut en VHD à taille fixe :

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## Préparer une machine virtuelle Red Hat à partir de VMware
### Configuration requise
Cette section suppose que vous avez déjà installé une machine virtuelle RHEL dans VMWare. Pour plus d’informations sur l’installation d’un système d’exploitation dans VMWare, voir le document [VMware Guest Operating System Installation Guide](http://partnerweb.vmware.com/GOSIG/home.html) (Guide d’installation de système d’exploitation invité VMWare).

- Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser les partitions standard plutôt que LVM (qui constitue souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Vous pouvez utiliser les techniques LVM ou RAID sur les disques de données si vous le souhaitez.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. Vous pouvez configurer l’agent Linux pour la création d’un fichier d’échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.

- Lorsque vous créez le disque dur virtuel, sélectionnez **Stocker le disque virtuel en un seul fichier**.



### <a id="rhel67vmware"> </a>Préparer une machine virtuelle RHEL 6.7 à partir de VMWare###

1.	Exécutez la commande suivante pour désinstaller NetworkManager :

         # sudo rpm -e --nodeps NetworkManager

    Notez que si le package n’est pas encore installé, la commande échoue et un message d’erreur s’affiche. Ceci est normal.

2.	Créez un fichier nommé **network** dans le répertoire /etc/sysconfig/ contenant le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3.	Créez un fichier nommé **ifcfg-eth0** dans le répertoire /etc/sysconfig/network-scripts/ contenant le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4.	Déplacez (ou supprimez) les règles udev afin d’éviter la génération de règles statiques pour l’interface Ethernet. Ces règles entraînent des problèmes lorsque vous clonez une machine virtuelle dans Microsoft Azure ou Hyper-V :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

6.	Inscrivez votre abonnement Red Hat de façon à permettre l’installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Le package WALinuxAgent `WALinuxAgent-<version>` a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0
        earlyprintk=ttyS0
        rootdelay=300
        numa=off

    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cette opération désactive NUMA en raison d’un bogue dans la version de noyau utilisée par RHEL 6. Outre l’action ci-dessus, nous vous recommandons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L’option crashkernel peut éventuellement être conservée. Notez cependant que ce paramètre réduira d’au moins 128 Mo la quantité de mémoire disponible dans la machine virtuelle. Cela peut se révéler problématique sur des machines virtuelles de petite taille.

9.	Ajoutez des modules de Hyper-V dans initramfs :

	    Edit `/etc/dracut.conf` and add content:

	        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

	    Rebuild initramfs:

	        # dracut –f -v

10.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

11.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

12.	Ne créez pas d'espace swap sur le disque du système d'exploitation :

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13.	Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # sudo subscription-manager unregister

14.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

15.	Arrêtez la machine virtuelle, puis convertissez le fichier VMDK en fichier .vhd.

    Convertissez tout d'abord l'image au format RAW :

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Assurez-vous que la taille de l’image RAW est alignée sur 1 Mo. Dans le cas contraire, arrondissez la taille pour l’aligner sur 1 Mo :

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Convertissez le disque brut en VHD à taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


### <a id="rhel7xvmware"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 à partir de VMWare###

1.	Créez un fichier nommé **network** dans le répertoire /etc/sysconfig/ contenant le texte suivant :

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2.	Créez un fichier nommé **ifcfg-eth0** dans le répertoire /etc/sysconfig/network-scripts/ contenant le texte suivant :

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

3.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

4.	Inscrivez votre abonnement Red Hat de façon à permettre l’installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre **GRUB\_CMDLINE\_LINUX**. Par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300
        console=ttyS0
        earlyprintk=ttyS0"

    Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Outre l’action ci-dessus, nous vous recommandons de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L’option crashkernel peut éventuellement être conservée. Notez cependant que ce paramètre réduira d’au moins 128 Mo la quantité de mémoire disponible dans la machine virtuelle. Cela peut se révéler problématique sur des machines virtuelles de petite taille.

6.	Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Ajoutez des modules de Hyper-V dans initramfs :

    Modifiez `/etc/dracut.conf`, ajoutez le contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Régénérez initramfs :

        # dracut –f -v

8.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

9.	Le package WALinuxAgent `WALinuxAgent-<version>` a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Si vous souhaitez annuler l'inscription de l'abonnement, exécutez la commande suivante :

        # sudo subscription-manager unregister

13.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14.	Arrêtez la machine virtuelle et convertissez le fichier VMDK au format VHD.

    Convertissez tout d'abord l'image au format RAW :

        # qemu-img convert -f vmdk –O raw rhel-7.1.vmdk rhel-7.1.raw

    Assurez-vous que la taille de l’image RAW est alignée sur 1 Mo. Dans le cas contraire, arrondissez la taille pour l’aligner sur 1 Mo :

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Convertissez le disque brut en VHD à taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


## Préparer une machine virtuelle Red Hat à partir d’un fichier ISO grâce à l’utilisation automatique d’un fichier Kickstart


### <a id="rhel7xkickstart"> </a>Préparer une machine virtuelle RHEL 7.1/7.2 à partir d’un fichier Kickstart###


1.	Créez un fichier Kickstart avec le contenu ci-dessous, puis enregistrez-le. Pour plus d’informations sur l’installation de Kickstart, voir le document [Kickstart Installation Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html) (Guide d’installation Kickstart).



        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
        auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff



        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^(ResourceDisk\.EnableSwap)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^(ResourceDisk\.SwapSizeMB)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^(GRUB_CMDLINE_LINUX)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#(ClientAliveInterval).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=yes
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2.	Placez le fichier Kickstart à un emplacement accessible à partir du système d’installation.

3.	Créez une machine virtuelle dans le Gestionnaire Hyper-V. Sur la page **Connecter un disque dur virtuel**, sélectionnez **Connecter un disque dur virtuel ultérieurement**, puis exécutez l’Assistant Nouvelle machine virtuelle.

4.	Ouvrez les paramètres de la machine virtuelle :

    a. Attachez un nouveau disque dur virtuel à la machine virtuelle. Veillez à sélectionner **Format VHD** et **Taille fixe**.

    b. Attachez l’ISO d’installation au lecteur de DVD.

    c. Configurez le BIOS de manière à exécuter le démarrage à partir d’un CD.

5.	Démarrez la machine virtuelle. Lorsque le guide d’installation s’affiche, appuyez sur la touche **Tab** pour configurer les options de démarrage.

6.	Entrez `inst.ks=<the location of the kickstart file>` à la fin des options de démarrage, puis appuyez sur **Entrée**.

7.	Attendez que l'installation se termine. À la fin de l’installation, la machine virtuelle s’arrête automatiquement. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## Problèmes connus
Il existe des problèmes connus liés à l'utilisation de RHEL 7.1 dans Hyper-V et Azure.

### Blocage des E/S du disque

Ce problème peut se produire au cours d’activités fréquentes des E/S du disque de stockage avec RHEL 7.1 dans Hyper-V et Azure.

Fréquence :

ce problème est intermittent. Toutefois, il se produit plus souvent lors d'opérations E/S du disque fréquentes dans Hyper-V et Azure.


[AZURE.NOTE] Ce problème a déjà été résolu par Red Hat. Pour installer les correctifs associés, exécutez la commande ci-dessous :

    # sudo yum update

### Impossible d’inclure le pilote Hyper-V dans le disque virtuel initial lors de l’utilisation d’un hyperviseur non-Hyper-V

Dans certains cas, les programmes d’installation de Linux n’incluent pas les pilotes pour Hyper-V dans le disque virtuel initial (initrd ou initramfs), sauf s’ils détectent que ce dernier s’exécute dans un environnement Hyper-V.

Lorsque vous utilisez un système de virtualisation différent (Virtualbox, Xen, etc.) pour préparer votre image Linux, vous pouvez avoir besoin de recréer initrd pour vous assurer que les modules noyau hv\_vmbus et hv\_storvsc figurent au moins parmi les modules disponibles sur le disque virtuel initial. Ceci est un problème connu, touchant au moins les systèmes basés sur la distribution Red Hat en amont.

Pour résoudre ce problème, ajoutez des modules Hyper-V dans initramfs, puis régénérez ce dernier :

Modifiez `/etc/dracut.conf` en y ajoutant le contenu suivant :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

Régénérez initramfs :

        # dracut –f -v

Pour plus d’informations, voir l’article concernant la [régénération d’initramfs](https://access.redhat.com/solutions/1958).

## Étapes suivantes
Vous êtes maintenant prêt à utiliser votre disque dur virtuel Red Hat Enterprise Linux pour créer des machines virtuelles dans Azure. S’il s’agit de la première fois que vous chargez le fichier .vhd sur Azure, consultez les étapes 2 et 3 dans [Création et chargement d’un disque dur virtuel contenant le système d’exploitation Linux](virtual-machines-linux-classic-create-upload-vhd.md).

Pour plus d’informations sur les hyperviseurs certifiés pour l’exécution de Red Hat Enterprise Linux, voir le [site web de Red Hat](https://access.redhat.com/certified-hypervisors).

<!---HONumber=AcomDC_0518_2016-->