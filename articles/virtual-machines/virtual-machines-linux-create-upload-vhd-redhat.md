<properties 
	pageTitle="Création et téléchargement d’un disque dur virtuel Red Hat Enterprise Linux pour une utilisation dans Azure" 
	description="Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Red Hat Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="SuperScottz" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="mingzhan"/>


# Préparation d'une machine virtuelle Red Hat pour Azure
Dans cet article, vous allez apprendre à préparer une Machine virtuelle Red Hat Enterprise Linux (RHEL) à utiliser dans Azure. Les versions de RHEL couvertes dans cet article sont 6.7 et 7.1, et les hyperviseurs de préparation abordés dans cet article sont Hyper-V, KVM et VMWare. Pour plus d’informations sur les conditions d’éligibilité pour participer au programme d’accès au Cloud de Red Hat, consultez le [site Web d’accès au cloud de Red Hat](http://www.redhat.com/en/technologies/cloud-computing/cloud-access).




##Préparation d'une image à partir du Gestionnaire Hyper-V 
###Composants requis
Cette section suppose que vous avez déjà installé une image RHEL à partir d'un fichier ISO obtenu depuis le site web Red Hat sur un disque dur virtuel (VHD). Pour plus d’informations sur l’utilisation du Gestionnaire Hyper-V pour installer une image de système d’exploitation, consultez [Installer le rôle Hyper-V et configurer une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes d'installation de RHEL**

- Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l'aide du Gestionnaire Hyper-V ou de l'applet de commande Powershell convert-vhd.

- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou RAID peuvent être utilisées sur des disques de données si vous préférez.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- La taille des disques durs virtuels doit être un multiple de 1 Mo.

- Lorsque vous utilisez qemu-img pour convertir les images de disque au format VHD, notez qu'un bogue connu touche la version 2.2.1 et versions supérieures de qemu-img. Celui-ci entraîne un formatage incorrect du disque dur virtuel. Le problème sera résolu dans la prochaine version de qemu-img. Pour l’instant, nous vous recommandons d’utiliser la version 2.2.0 de qemu-img ou ses versions antérieures.


###RHEL 6.7

1.	Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2.	Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

3.	Exécutez la commande suivante pour désinstaller NetworkManager :

        # sudo rpm -e --nodeps NetworkManager

    **Remarque :** si le package n’est pas déjà installé, la commande échoue et un message d’erreur s’affiche. Ceci est normal.

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

6.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Microsoft Azure ou Hyper-V :
            
        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

8.	Inscrivez l'abonnement Red Hat pour permettre l'installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9.	Le package WALinuxAgent `WALinuxAgent-<version>` a été transmis au référentiel Fedora EPEL 6. Activez le référentiel epel en exécutant la commande suivante :

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

10.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Cela désactive NUMA en raison d'une erreur dans la version de noyau utilisée par RHEL 6.

    Outre les précautions ci-dessus, il est recommandé de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.

    L'option crashkernel peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.

11.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez /etc/ssh/sshd\_config pour inclure la ligne suivante :

        ClientAliveInterval 180

12.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

    **Remarque :** l’installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s’ils n’ont pas déjà été supprimés comme indiqué à l’étape 2.

13.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après un approvisionnement sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l'agent Linux Azure (voir l'étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :

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

16.	Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

###RHEL 7.1

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

2.	Cliquez sur Connecter pour ouvrir une fenêtre de console de la machine virtuelle.

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

6.	Inscrivez l'abonnement Red Hat pour permettre l'installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre **GRUB\_CMDLINE\_LINUX**, par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Outre les précautions ci-dessus, il est recommandé de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto
    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L'option crashkernel peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.

8.	Lorsque vous avez fini de modifier `/etc/default/grub` comme indiqué ci-dessus, exécutez la commande suivante pour régénérer la configuration grub :

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

10.	Le package WALinuxAgent `WALinuxAgent-<version>` a été transmis au référentiel Fedora EPEL 6. Activez le référentiel epel en exécutant la commande suivante :

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

11.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service 

12.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

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

15.	Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.


##Préparation d'une image à partir de KVM 
###RHEL 6.7

1.	Téléchargez l'image KVM de RHEL 6.7 depuis le site web de Red Hat.

2.	Définissez un mot de passe racine

    Générez un mot de passe chiffré et copiez la sortie de la commande :

        # openssl passwd -1 changeme
    Définissez un mot de passe racine avec guestfish :
       
        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit
    Modifiez le second champ de l'utilisateur racine en remplaçant « !! » par le mot de passe chiffré.

3.	Créez une machine virtuelle dans KVM à partir de l’image qcow2, définissez le type de disque sur **qcow2**, définissez le modèle de périphérique de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite la machine virtuelle, puis connectez-vous en tant que racine.

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

6.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Microsoft Azure ou Hyper-V :

        # mkdir -m 0700 /var/lib/waagent
        # mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

7.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # chkconfig network on

8.	Inscrivez l'abonnement Red Hat pour permettre l'installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # subscription-manager register –auto-attach --username=XXX --password=XXX

9.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier `/boot/grub/menu.lst` dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Cela désactive NUMA en raison d'une erreur dans la version de noyau utilisée par RHEL 6.

    Outre les précautions ci-dessus, il est recommandé de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L'option crashkernel peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.

10.	Désinstallez Cloud-Init :

        # yum remove cloud-init

11.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu :
 
        # chkconfig sshd on

    Modifiez /etc/ssh/sshd\_config pour inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

    Redémarrez sshd :

		# service sshd restart

12.	Le package WALinuxAgent `WALinuxAgent-<version>` a été transmis au référentiel Fedora EPEL 6. Activez le référentiel epel en exécutant la commande suivante :

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

13.	Installez l'agent linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent
        # chkconfig waagent on

14.	L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans le fichier **/etc/waagent.conf** :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :
        
        # subscription-manager unregister

16.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Arrêtez la machine virtuelle dans KVM.

18.	Convertissez l'image qcow2 au format vhd : convertissez tout d'abord l'image au format RAW :
         
         # qemu-img convert -f qcow2 –O raw rhel-6.7.qcow2 rhel-6.7.raw
    Assurez-vous que la taille des images RAW est alignée sur 1 Mo. Sinon, arrondissez la taille pour l'aligner sur 1 Mo : # MB=$((1024*1024)) # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \\ gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}') # rounded\_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-6.7.raw $rounded_size

    Convertissez le disque brut sur disque dur virtuel à taille fixe :

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd


###RHEL 7.1

1.	Téléchargez l'image KVM de RHEL 7.1 depuis le site web Red Hat.

2.	Définissez un mot de passe racine

    Générez un mot de passe chiffré et copiez la sortie de la commande.

        # openssl passwd -1 changeme

    Définissez un mot de passe racine avec guestfish.

        # guestfish --rw -a <image-name>
        ><fs> run
        ><fs> list-filesystems
        ><fs> mount /dev/sda1 /
        ><fs> vi /etc/shadow
        ><fs> exit

    Modifiez le second champ de l'utilisateur racine en remplaçant « !! » par le mot de passe chiffré.

3.	Créez une machine virtuelle dans KVM à partir de l’image qcow2, définissez le type de disque sur **qcow2**, définissez le modèle de périphérique de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite la machine virtuelle, puis connectez-vous en tant que racine.

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

        # subscription-manager register –auto-attach --username=XXX --password=XXX

8.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre **GRUB\_CMDLINE\_LINUX**, par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Outre les précautions ci-dessus, il est recommandé de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L'option crashkernel peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.

9.	Lorsque vous avez fini de modifier `/etc/default/grub` comme indiqué ci-dessus, exécutez la commande suivante pour régénérer la configuration grub :

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10.	Désinstallez Cloud-Init :

        # yum remove cloud-init

11.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu :

        # systemctl enable sshd

    Modifiez /etc/ssh/sshd\_config pour inclure les lignes suivantes :

        PasswordAuthentication yes
        ClientAliveInterval 180

    Redémarrez sshd :

        systemctl restart sshd	

12.	Le package WALinuxAgent `WALinuxAgent-<version>` a été transmis au référentiel Fedora EPEL 6. Activez le référentiel epel en exécutant la commande suivante :

        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

13.	Installez l'agent linux Azure en exécutant la commande suivante :

        # yum install WALinuxAgent

    Activez le service waagent :

        # systemctl enable waagent.service

14.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # subscription-manager unregister

16.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17.	Arrêtez la machine virtuelle dans KVM.

18.	Convertissez l'image qcow2 au format vhd :

    Convertissez tout d'abord l'image au format RAW :

         # qemu-img convert -f qcow2 –O raw rhel-7.1.qcow2 rhel-7.1.raw

    Assurez-vous que la taille des images RAW est alignée sur 1 Mo. Sinon, arrondissez la taille pour l'aligner sur 1 Mo :

         # MB=$((1024*1024))
         # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                  gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
         # rounded_size=$((($size/$MB + 1)*$MB))

         # qemu-img resize rhel-7.1.raw $rounded_size

    Convertissez le disque brut sur disque dur virtuel à taille fixe :

         # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##Préparation d'une image à partir de VMWare
###Composants requis
Cette section suppose que vous avez déjà installé une machine virtuelle RHEL dans VMWare. Pour plus d’informations sur la manière d’installer un système d’exploitation dans VMWare, consultez le [Guide d’installation de système d’exploitation invité VMWare](http://partnerweb.vmware.com/GOSIG/home.html).
 
- Lors de l'installation du système Linux, il est recommandé d'utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou RAID peuvent être utilisées sur des disques de données si vous préférez.

- Ne configurez pas une partition d'échange sur le disque du système d'exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d'informations à ce sujet.

- Lors de la création du disque dur virtuel, sélectionnez **Stocker le disque virtuel en un seul fichier**.

###RHEL 6.7
1.	Exécutez la commande suivante pour désinstaller NetworkManager :

         # sudo rpm -e --nodeps NetworkManager

    **Remarque :** si le package n’est pas déjà installé, la commande échoue et un message d’erreur s’affiche. Ceci est normal.

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

4.	Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Microsoft Azure ou Hyper-V :

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/

5.	Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

        # sudo chkconfig network on

6.	Inscrivez l'abonnement Red Hat pour permettre l'installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7.	Le package WALinuxAgent `WALinuxAgent-<version>` a été transmis au référentiel Fedora EPEL 6. Activez le référentiel epel en exécutant la commande suivante :

        # wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
        # rpm -ivh epel-release-6-8.noarch.rpm

8.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :

        console=ttyS0 
        earlyprintk=ttyS0 
        rootdelay=300 
        numa=off

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Cela désactive NUMA en raison d'une erreur dans la version de noyau utilisée par RHEL 6. Outre les précautions ci-dessus, il est recommandé de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L'option crashkernel peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.

9.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

10.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo chkconfig waagent on

11.	Ne créez pas d'espace swap sur le disque du système d'exploitation :
    
    L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12.	Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

        # sudo subscription-manager unregister

13.	Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

        # sudo waagent -force -deprovision 
        # export HISTSIZE=0
        # logout

14.	Arrêtez la machine virtuelle et convertissez le fichier VMDK en fichier VHD.

    Convertissez tout d'abord l'image au format RAW :

        # qemu-img convert -f vmdk –O raw rhel-6.7.vmdk rhel-6.7.raw

    Assurez-vous que la taille des images RAW est alignée sur 1 Mo. Sinon, arrondissez la taille pour l'aligner sur 1 Mo :

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.7.raw" | \
                gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.7.raw $rounded_size

    Convertissez le disque brut sur disque dur virtuel à taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.7.raw rhel-6.7.vhd

###RHEL 7.1

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

4.	Inscrivez l'abonnement Red Hat pour permettre l'installation des packages à partir du référentiel RHEL. Pour ce faire, exécutez la commande suivante :

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5.	Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre **GRUB\_CMDLINE\_LINUX**, par exemple :

        GRUB_CMDLINE_LINUX="rootdelay=300 
        console=ttyS0 
        earlyprintk=ttyS0"

    Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Outre les précautions ci-dessus, il est recommandé de supprimer les paramètres suivants :

        rhgb quiet crashkernel=auto

    Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série. L'option crashkernel peut rester configurée le cas échéant, mais notez que ce paramètre réduit d'au moins 128 Mo la mémoire disponible dans la machine virtuelle, ce qui peut poser un problème sur les plus petites machines virtuelles.

6.	Lorsque vous avez fini de modifier `/etc/default/grub` comme indiqué ci-dessus, exécutez la commande suivante pour régénérer la configuration grub :

         # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7.	Ajoutez des modules de Hyper-V dans initramfs :

    Modifiez `/etc/dracut.conf`, ajoutez le contenu :

        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”

    Générez de nouveau initramfs :

        # dracut –f -v

8.	Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

        ClientAliveInterval 180

9.	Le package WALinuxAgent `WALinuxAgent-<version>` a été transmis au référentiel Fedora EPEL 6. Activez le référentiel epel en exécutant la commande suivante :


        # wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
        # rpm -ivh epel-release-7-5.noarch.rpm

10.	Installez l'agent linux Azure en exécutant la commande suivante :

        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent.service

11.	Ne créez pas d'espace swap sur le disque du système d'exploitation. L'agent Linux Azure peut configurer automatiquement un espace swap à l'aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque temporaire et qu'il peut être vidé lors de l'annulation de l'approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure (voir l’étape précédente), modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

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

    Assurez-vous que la taille des images RAW est alignée sur 1 Mo. Sinon, arrondissez la taille pour l'aligner sur 1 Mo :

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.1.raw" | \
                 gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.1.raw $rounded_size

    Convertissez le disque brut sur disque dur virtuel à taille fixe :

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.1.raw rhel-7.1.vhd


##Préparation automatique à partir d'un fichier ISO Kickstart
###RHEL 7.1

1.	Créez le fichier Kickstart avec le contenu ci-dessous et enregistrez le fichier. Pour plus d’informations sur l’installation du fichier de démarrage, reportez-vous au [Guide d’installation Kickstart](https://access.redhat.com/documentation/fr-FR/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).


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

        # Primary Fedora repo
        repo --name="epel7" --baseurl="http://dl.fedoraproject.org/pub/epel/7/x86_64/"

        %packages
        @base
        @console-internet
        chrony
        sudo
        python-pyasn1
        parted
        ntfsprogs
        WALinuxAgent
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

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

 

2.	Placez le fichier Kickstart vers un emplacement accessible à partir du système d'installation.
 
3.	Créez une machine virtuelle dans le Gestionnaire Hyper-V. Sur la page **Connecter un disque dur virtuel**, sélectionnez **Connecter un disque dur virtuel ultérieurement**, et complétez l’Assistant Nouvelle machine virtuelle.

4.	Ouvrez les paramètres de la machine virtuelle :

    a. Connectez un disque dur virtuel à la machine virtuelle. Veillez à sélectionner **Format VHD** et **Taille fixe**.
    
    b. Connectez l'ISO d'installation au lecteur DVD.

    c. Configurez le BIOS de manière à exécuter le démarrage depuis un CD.

5.	Démarrez la machine virtuelle. Lorsque le guide d’installation s’affiche, appuyez sur **Onglet** pour configurer les options de démarrage.

6.	Entrez `inst.ks=<the location of the Kickstart file>` à la fin des options de démarrage, puis appuyez sur **Entrée**.

7.	Attendez que l'installation se termine. Lorsqu'elle est terminée, la machine virtuelle sera automatiquement arrêtée. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

##Problèmes connus
Il existe des problèmes connus liés à l'utilisation de RHEL 7.1 dans Hyper-V et Azure.

###Problème : blocage des E/S du disque 

Ce problème peut se produire au cours d'activités des E/S du disque de stockage fréquentes avec RHEL 7.1 dans Hyper-V et Azure.

Fréquence :

le problème est aléatoire. Toutefois, il se produit plus souvent lors d'opérations E/S du disque fréquentes dans Hyper-V et Azure.

    
[AZURE.NOTE]Ce problème a déjà été résolu par Red Hat. Pour installer les correctifs associés, exécutez la commande ci-dessous :

    # sudo yum update

<!---HONumber=AcomDC_1203_2015-->