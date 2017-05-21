---
title: "Installation d’un serveur cible maître Linux pour le basculement à partir d’Azure vers un site local | Microsoft Docs"
description: "Pour reprotéger une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux. Découvrez comment en installer."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 60102ebb43efc8710f102450df5b98edcb1d4b39
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Installation d’un serveur cible maître Linux
Après avoir basculé une machine virtuelle, vous pouvez la restaurer automatiquement sur le site local. L’opération de restauration vous oblige à reprotéger la machine virtuelle à partir d’Azure sur le site local. Pour ce faire, vous avez besoin d’un serveur cible maître, capable de recevoir le trafic. Si votre machine virtuelle protégée est de type Windows, vous avez besoin d’un serveur cible maître Windows. Si vous avez une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux. Pour savoir comment créer et installer un serveur cible maître Linux, lisez les étapes ci-dessous.

## <a name="overview"></a>Vue d'ensemble
Cet article fournit des informations utiles et la marche à suivre pour installer un serveur cible maître Linux.

Publiez vos commentaires ou vos questions en bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Composants requis

* Pour choisir correctement l’hôte sur lequel vous devez déployer le serveur cible maître, déterminez si la restauration automatique s’effectuera sur une machine virtuelle locale existante ou sur une nouvelle machine virtuelle (après la suppression de la machine virtuelle locale).
    * Dans le cas d’une machine virtuelle existante, l’hôte du serveur cible maître doit avoir accès aux magasins de données de celle-ci.
    * Si la machine virtuelle locale n’existe pas, la machine virtuelle restaurée est créée sur le même hôte que le serveur cible maître. Vous pouvez choisir n’importe quel hôte ESXi pour installer le serveur cible maître.
* Le serveur maître cible doit appartenir à un réseau capable de communiquer avec le serveur de processus et le serveur de configuration.
* La version du serveur cible maître doit être inférieure ou égale à celle du serveur de processus et du serveur de configuration. Par exemple, si la version du serveur de configuration est 9.4, celle du serveur cible maître peut être 9.4 ou 9.3 mais pas 9.5.
* Le serveur cible maître ne peut être qu’une machine virtuelle VMware, et pas un serveur physique.

## <a name="master-target-sizing-guideline"></a>Contraintes de dimensionnement du serveur cible maître

Le serveur cible maître doit être créé selon les contraintes de dimensionnement suivantes :
    * RAM - 6 Go ou plus
    * Taille du disque de système d’exploitation - 100 Go ou plus (pour installer CentOS 6.6)
    * Taille du disque supplémentaire pour le lecteur de conservation - 1 To
    * Cœurs d’UC - 4 cœurs ou plus


## <a name="steps-to-deploy-the-master-target-server"></a>Procédure de déploiement du serveur maître cible

### <a name="install-centos-66-minimal"></a>Installer CentOS 6.6 Minimal

Pour installer le système d’exploitation CentOS 6.6 64 bits, procédez comme suit :

1. Parmi les liens suivants, choisissez le serveur miroir le plus proche afin de télécharger un fichier ISO de CentOS 6.6 Minimal 64 bits.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Placez le fichier ISO de CentOS 6.6 Minimal 64 bits dans le lecteur DVD et démarrez le système.

    ![Boîte de dialogue Welcome (Bienvenue) de CentoOS 6.6](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Cliquez sur **Skip** (Ignorer) pour ignorer le processus de test du support.

    ![Cliquer sur Skip (Ignorer) pour ignorer le processus de test du support](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. Dans l’écran d’accueil, cliquez sur le bouton **Next** (Suivant).

    ![Bouton Next (Suivant) dans l’écran de bienvenue de l’installation](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Sélectionnez **French** (Français) comme langue par défaut, puis cliquez sur **Next** (Suivant).

    ![Sélectionner une langue](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Sélectionnez **French** (Français) comme disposition de clavier, puis cliquez sur **Next** (Suivant).

    ![Sélectionner la disposition de clavier française](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Sélectionnez **Basic Storage Devices** (Appareils de stockage de base), puis cliquez sur **Next** (Suivant).

    ![Sélectionner un appareil de stockage](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. Un message d’avertissement indique que les données existant sur le disque dur vont être supprimées. Vérifiez que le disque dur ne contient pas de données importantes, puis cliquez sur **Yes, discard any data** (Oui, rejeter les données).

    ![Avertissement concernant la suppression des données si vous continuez](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Entrez le nom d’hôte de votre serveur dans la zone **Hostname** (Nom d’hôte), puis cliquez sur **Configure Network** (Configurer le réseau). Dans la boîte de dialogue **Network Connection** (Connexion réseau), sélectionnez l’interface réseau, puis cliquez sur le bouton **Edit** (Modifier) pour configurer les paramètres IPV4.

    ![Sélectionner un nom d’hôte et configurer IPV4](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. Dans la boîte de dialogue **Editing System eth0** (Modifier le système eth0), activez la case à cocher **Connect automatically** (Se connecter automatiquement). Dans l’onglet **IPv4 Settings** (Paramètres IPv4), choisissez **Manual** (Manuel) dans **Method** (Méthode), puis cliquez sur le bouton **Add** (Ajouter). Renseignez les champs **Static IP** (Adresse IP statique), **Netmask** (Masque de réseau), **Gateway** (Passerelle) et **DNS Server** (Serveur DNS). Cliquez sur **Apply** (Appliquer) pour enregistrer les informations.

    ![Paramètres de configuration du réseau](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Sélectionnez votre fuseau horaire, puis cliquez sur **Next** (Suivant).

    ![Sélectionner un fuseau horaire](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Renseignez le champ **Root password** (Mot de passe racine), confirmez le mot de passe, puis cliquez sur **Next** (Suivant).

    ![Ajouter un mot de passe](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Sélectionnez **Create Custom Layout** (Créer une mise en page personnalisée), puis cliquez sur **Next** (Suivant).

    ![Sélectionner un type d’installation](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Sélectionnez la partition **Free** (Libre), puis cliquez sur **Create** (Créer) pour créer les partitions **/**, **/var/crash** et **/home** avec le type de système de fichiers **ext4**. Créez une **Swap Partition** (Partition d’échange) avec **swap** (Échange) comme type de système de fichiers. Pour allouer une taille de partition, suivez la formule d’allocation de taille, indiquée dans le tableau.

    > [!NOTE]
    > Le serveur cible maître Linux ne doit pas utiliser LVM (Logical Volume Manager) pour les espaces de stockage racine ou de rétention. Par défaut, le serveur cible maître Linux est configuré pour ne pas rechercher les disques/partitions LVM.

    ![Tableau des noms de partition, des tailles de partition et des types de système de fichiers](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Après avoir créé la partition, cliquez sur **Next** (Suivant).

    ![Boîte de dialogue affichant les valeurs sélectionnées pour les partitions](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Si des appareils sont détectés, un message d’avertissement relatif au formatage apparaît. Cliquez sur **Format** (Formater) pour formater le disque dur avec la dernière table de partition.

    ![Cliquer sur le bouton Format (Formater) pour formater le disque](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Cliquez sur **Write changes to disk** (Enregistrer les modifications sur le disque) pour appliquer les modifications de partition au disque.

    ![Cliquer sur Enregistrer les modifications sur le disque](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Cochez l’option **Install boot loader** (Installer le chargeur de démarrage), puis cliquez sur **Next** (Suivant) pour installer le chargeur de démarrage sur la partition racine.

    ![Installer le chargeur de démarrage sur la partition racine](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. Le processus d’installation démarre. Vous pouvez en surveiller l’état d’avancement.

    ![Boîte de dialogue affichant l’état d’avancement de l’installation](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. L’écran suivant apparaît à la fin de l’installation. Cliquez sur **Reboot** (Redémarrer).

    ![Écran d’installation réussie](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Étapes de post-installation
Ensuite, préparez l’ordinateur à configurer comme serveur cible maître.

Pour obtenir l’ID de chaque disque dur SCSI d’une machine virtuelle Linux, activez le paramètre **disk.EnableUUID = TRUE**.

Pour ce faire, procédez comme suit :

1. Arrêtez votre machine virtuelle.

2. Cliquez avec le bouton droit de la souris sur l’entrée de la machine virtuelle dans le volet gauche, puis sélectionnez **Edit Settings** (Modifier les paramètres).

3. Cliquez sur l’onglet **Options** .

4. Sélectionnez **Advanced (Avancé) &gt; General (Général)** dans le volet gauche, puis cliquez sur le bouton **Configuration Parameters** (Paramètres de configuration) à droite.

    ![Onglets Options](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

    L’option **Paramètres de configuration** n’est pas disponible lorsque la machine est arrêtée. Pour activer cet onglet, arrêtez la machine virtuelle.

5. Vérifiez si une ligne comportant **disk.EnableUUID** existe.

    - Si le paramètre est présent et a pour valeur **False**, remplacez-la par **True** (les valeurs ne respectent pas la casse).

    - Si le paramètre existe et a pour valeur **True**, cliquez sur **Cancel** (Annuler).

    - Si le paramètre n’existe pas, cliquez sur **Add Row** (Ajouter une ligne).

    - Ajoutez **disk.EnableUUID** dans la colonne **Name** (Nom) et définissez sa valeur sur **TRUE**.

    ![Vérification de la présence de disk.EnableUUID](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-additional-packages"></a>Télécharger et installer les packages supplémentaires

> [!NOTE]
> Vérifiez que vous avez accès à Internet pour télécharger et installer les packages supplémentaires. Si vous n’êtes pas connecté à Internet, vous devez rechercher ces packages RPM et les installer manuellement.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

La commande précédente télécharge les 15 packages suivants à partir du référentiel CentOS 6.6 et les installe. Si vous n’avez pas accès à Internet, vous devrez télécharger les packages RPM suivant :


bc-1.06.95-1.el6.x86\_64.rpm

busybox-1.15.1-20.el6.x86\_64.rpm

elfutils-libs-0.158-3.2.el6.x86\_64.rpm

kexec-tools-2.0.0-280.el6.x86\_64.rpm

lsscsi-0.23-2.el6.x86\_64.rpm

lzo-2.03-3.1.el6\_5.1.x86\_64.rpm

perl-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-Module-Pluggable-3.90-136.el6\_6.1.x86\_64.rpm

perl-Pod-Escapes-1.04-136.el6\_6.1.x86\_64.rpm

perl-Pod-Simple-3.13-136.el6\_6.1.x86\_64.rpm

perl-libs-5.10.1-136.el6\_6.1.x86\_64.rpm

perl-version-0.77-136.el6\_6.1.x86\_64.rpm

rsync-3.0.6-12.el6.x86\_64.rpm

snappy-1.1.0-1.el6.x86\_64.rpm

wget-1.12-5.el6\_6.1.x86\_64.rpm


#### <a name="install-additional-packages-for-specific-operating-systems"></a>Installer les packages supplémentaires pour des systèmes d’exploitation spécifiques

> [!NOTE]
> Si les machines sources protégées utilisent un système de fichiers Reiser ou XFS pour la racine ou l’appareil de démarrage, téléchargez et installez les packages supplémentaires suivants sur le serveur cible maître Linux avant la protection.


***ReiserFS (s’il est utilisé dans Suse11SP3. ReiserFS n’est pas le système de fichiers par défaut dans Suse11SP3.)***

```
cd /usr/local

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 et versions ultérieures)***

```
cd /usr/local

wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

yum install device-mapper-multipath
```
Requis pour activer les packages multichemins sur le serveur maître cible.

### <a name="get-the-installer-for-setup"></a>Obtenir le programme d’installation

Si votre serveur cible maître est connecté à Internet, vous pouvez utiliser la procédure suivante pour télécharger le programme d’installation. Sinon, vous pouvez copier le programme d’installation à partir du serveur de traitement et l’installer.

#### <a name="download-the-master-target-installation-packages"></a>Télécharger les packages d’installation du serveur maître cible

[Téléchargez les éléments d’installation du serveur maître cible Linux](https://aka.ms/latestlinuxmobsvc).

Pour le télécharger avec Linux, tapez :

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Veillez à télécharger et décompresser le programme d’installation dans le répertoire d’accueil. Si vous le décompressez dans /usr/Local, l’installation échoue.


#### <a name="access-the-installer-from-the-process-server"></a>Accéder au programme d’installation à partir du serveur de traitement

1. Accédez au répertoire C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository sur le serveur de processus.

2. Copiez le fichier d’installation requis à partir du serveur de processus et enregistrez-le sous latestlinuxmobsvc.tar.gz dans votre répertoire d’accueil.


### <a name="apply-custom-configuration-changes"></a>Appliquer des modifications de configuration personnalisées

Pour appliquer les modifications de configuration personnalisées, procédez comme suit :


1. Exécutez la commande suivante pour décompresser le fichier binaire.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```
    ![Capture d’écran de la commande exécutée](./media/site-recovery-how-to-install-linux-master-target/image16.png)

2. Exécutez la commande suivante pour accorder l’autorisation.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Exécutez la commande suivante pour exécuter le script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> Exécutez le script une seule fois sur le serveur. Arrêtez le serveur. Redémarrez le serveur après avoir ajouté un disque, comme indiqué dans la procédure ci-dessous.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Ajouter un disque de rétention à la machine virtuelle du serveur cible maître Linux

Pour créer un disque de rétention, procédez comme suit :

1. Connectez un nouveau disque de **1 To** à la machine virtuelle du serveur cible maître Linux et **démarrez** la machine.

2. Utilisez la commande **multipath -ll** pour obtenir l’ID multichemin du disque de rétention.

    ```
    multipath -ll
    ```

    ![ID multichemin du disque de rétention](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formatez le lecteur et créez un système de fichiers dessus.

    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![Création d’un système de fichiers sur le lecteur](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Après avoir créé le système de fichiers, montez le disque de rétention.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![Montage du disque de rétention](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Enfin, créez l’entrée **fstab** pour monter le lecteur de rétention à chaque démarrage.
    ```
    vi /etc/fstab
    ```
    Appuyez sur **Inser** pour commencer à modifier le fichier. Créez une ligne et insérez-y le texte suivant. Modifiez l’ID multichemin du disque en fonction de l’ID multichemin de la commande précédente.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Appuyez sur la touche **Echap** et tapez **:wq** (écrire et quitter) pour fermer la fenêtre de l’éditeur.

### <a name="install-the-master-target"></a>Installer le serveur cible maître

> [!IMPORTANT]
> La version du serveur cible maître doit être supérieure ou égale à celle du serveur de processus et du serveur de configuration. Si cette condition n’est pas remplie, la reprotection aboutit mais la réplication échoue.


> [!NOTE]
> Avant d’installer le serveur cible maître, vérifiez que le fichier /etc/hosts sur la machine virtuelle contient des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau.

1. Copiez la phrase secrète à partir de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de configuration, puis enregistrez-la dans le fichier passphrase.txt dans le même répertoire local en exécutant la commande suivante.

    ```
    echo <passphrase> >passphrase.txt
    ```
    Exemple : echo itUx70I47uxDuUVY >passphrase.txt

2. Notez l’adresse IP du serveur de configuration. Vous en aurez besoin à l’étape suivante.

3. Exécutez la commande suivante pour installer le serveur cible maître et l’inscrire auprès du serveur de configuration.

    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    Exemple : ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    Attendez la fin du script. Si le serveur cible maître est déjà inscrit, il figure dans la page Site Recovery Infrastructure (Infrastructure Site Recovery) du portail.

#### <a name="install-the-master-target-by-using-interactive-install"></a>Installer le serveur cible maître en mode interactif

1. Exécutez la commande suivante pour installer le service cible maître. Choisissez le rôle d’agent **Master Target** (Cible maître).

    ```
    ./install
    ```

2. Choisissez l’emplacement par défaut de l’installation, puis appuyez sur **Entrée** pour continuer.

    ![Choix d’un emplacement par défaut pour l’installation du serveur cible maître](./media/site-recovery-how-to-install-linux-master-target/image17.png)


3. Choisissez les paramètres **globaux** à configurer.

    ![Configuration des paramètres globaux](./media/site-recovery-how-to-install-linux-master-target/image18.png)

4. Spécifiez les adresses IP du serveur de configuration.

5. Spécifiez le port 443 pour le serveur de configuration.

    ![Définition de l’adresse IP et du port du serveur de configuration](./media/site-recovery-how-to-install-linux-master-target/image19.png)

6. Copiez la phrase secrète du serveur de configuration à partir de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de configuration et collez-la dans la zone **Passphrase** (Phrase secrète). La zone reste vide, même lorsque vous y collez le texte.

7. Sélectionnez **Quit** (Quitter) dans le menu.

8. Attendez la fin de l’installation et de l’inscription.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Installer les outils VMware sur le serveur maître cible

Vous devez installer les outils VMware sur le serveur maître cible pour qu’ils puissent détecter les magasins de données. Si les outils ne sont pas installés, l’écran de reprotection ne répertorie pas les magasins de données. Vous devrez redémarrer après l’installation des outils VMware.

## <a name="next-steps"></a>Étapes suivantes
Une fois installé et inscrit, le serveur cible maître apparaît dans la section **Master Target** (Cible maître) de la page **Site Recovery Infrastructure** (Infrastructure Site Recovery), sous la vue d’ensemble du serveur de configuration.

Vous pouvez maintenant procéder à la [reprotection](site-recovery-how-to-reprotect.md), puis à la restauration automatique.

## <a name="common-issues"></a>Problèmes courants

* Veillez à ne pas activer Storage vMotion sur des composants de gestion tels qu’un serveur cible maître. Si le serveur cible maître est déplacé après une reprotection, les disques de machine virtuelle (VMDK) ne peuvent pas être détachés et la restauration échouera.
* Le serveur cible maître ne doit pas présenter d’instantanés sur la machine virtuelle. Si des instantanés sont présents, la restauration automatique échouera.
* En raison de configurations de carte réseau personnalisées chez certains clients, l’interface réseau est désactivée au démarrage et l’agent du serveur cible maître ne s’initialise pas. Vérifiez que les propriétés suivantes sont configurées correctement. Vérifiez ces propriétés dans le fichier /etc/sysconfig/network-scripts/ifcfg-eth de la carte Ethernet.
    * BOOTPROTO=dhcp
    * ONBOOT=yes

