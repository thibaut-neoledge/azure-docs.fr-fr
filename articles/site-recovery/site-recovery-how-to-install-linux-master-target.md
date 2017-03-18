---
title: "Installation d’un serveur cible maître Linux pour le basculement à partir d’Azure vers un site local | Microsoft Docs"
description: "Avant de reprotéger une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux. Découvrez comment en installer."
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
translationtype: Human Translation
ms.sourcegitcommit: c4fb25880584add0832464d9049dc6c78059c48b
ms.openlocfilehash: 8ac18526e6781f189444233a191aa1d6c5b863ff
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-install-linux-master-target-server"></a>Installation d’un serveur cible maître Linux
Une fois que vous avez basculé vos machines virtuelles, vous pouvez les restaurer automatiquement en local. Pour effectuer la restauration automatique, vous devez d’abord faire passer la machine virtuelle à l’état protégé en la reprotégeant à partir d’Azure vers un site local. Pour ce faire, vous avez besoin d’un serveur cible maître pour recevoir le trafic en local. Si votre machine virtuelle protégée est une machine virtuelle Windows, vous avez besoin d’un serveur cible maître Windows. Dans le cas d’une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux pour la reprotection. Lisez les étapes ci-dessous pour savoir comment créer et installer un serveur cible maître Linux.

## <a name="overview"></a>Vue d'ensemble
Cet article fournit des informations utiles et la procédure à suivre pour installer un serveur cible maître Linux.

Publiez des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Conditions préalables

* Pour choisir correctement l’hôte sur lequel vous devez déployer le serveur cible maître, déterminez si la restauration automatique s’effectuera sur une machine virtuelle locale existante ou sur une nouvelle machine virtuelle (en raison de la suppression de la machine virtuelle locale). 
    * Pour une machine virtuelle existante, l’hôte du serveur maître cible doit avoir accès aux magasins de données de la machine virtuelle.
    * Si la machine virtuelle n’existe pas en local, la machine virtuelle de restauration automatique est créée sur le même hôte que le serveur cible maître. Vous pouvez choisir n’importe quel hôte ESXi pour installer le serveur cible maître.
* Le serveur maître cible doit se trouver sur un réseau capable de communiquer avec le serveur de processus et le serveur de configuration.
* La version du serveur maître cible doit être inférieure ou égale à celles du serveur de processus et du serveur de configuration. (par exemple, si la version du serveur de configuration est 9.4, celle du serveur maître cible peut être 9.4 et 9.3, mais pas 9.5)
* Le serveur maître cible peut uniquement être une machine virtuelle VMware, et pas une machine virtuelle physique.


## <a name="steps-to-deploy-the-master-target-server"></a>Procédure de déploiement du serveur cible maître

### <a name="install-centos-66-minimal"></a>Installer CentOS 6.6 Minimal

Suivez les étapes indiquées ci-dessous pour installer le système d’exploitation CentOS 6.6 64 bits.

1. Parmi les liens suivants, choisissez le serveur miroir le plus proche afin de télécharger un fichier ISO contenant CentOS 6.6 Minimal 64 bits.

    <http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    <http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

    Conservez le fichier ISO CentOS 6.6 Minimal 64 bits dans le lecteur DVD et démarrez le système.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

2. Sélectionnez **Skip** (Ignorer) pour ignorer le processus de test de support.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

3. Vous pouvez maintenant voir l’écran d’accueil de l’installation. Ici, cliquez sur le bouton **Next** (Suivant).

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

4. Sélectionnez **French (Français)** comme langue par défaut, puis cliquez sur **Next** (Suivant) pour continuer.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

5. Sélectionnez **French** (Français) comme disposition de clavier. Cliquez sur **Next** (Suivant) pour continuer l'installation.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

6. Sélectionnez le type de périphériques où l’installation sera effectuée. Sélectionnez **Basic storage Devices** (Périphériques de stockage de base). Cliquez sur **Next** (Suivant) pour continuer l'installation.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

7. Un message d’avertissement s’affiche, indiquant que les données existantes sur le disque dur seront supprimées. Assurez-vous que le disque dur ne contient pas de données importantes puis cliquez sur **Yes, discard any data** (Oui, supprimer le données).

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

8. Entrez le nom d’hôte de votre serveur dans la **zone de texte Hostname** (Nom d’hôte). Cliquez sur **Configure Network** (Configurer le réseau), puis dans la fenêtre **Network Connection** (Connexion réseau), sélectionnez votre interface réseau. Cliquez sur le bouton **Edit** (Modifier) pour configurer les paramètres IPV4.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

9. La fenêtre **Editing System eth0** (Modification du système eth0) suivante s’affiche. Cochez la case **Connect automatically** (Se connecter automatiquement). Sous l’onglet « Paramètres IPv4 », choisissez la méthode **Manual** (Manuelle), puis cliquez sur le bouton **Add** (Ajouter). Spécifiez l’adresse IP statique, le masque réseau, la passerelle et le serveur DNS. Cliquez sur **Apply** (Appliquer) pour enregistrer les informations.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

10. Sélectionnez votre fuseau horaire dans la zone de liste modifiable, puis cliquez sur **Next** (Suivant) pour continuer.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

11. Entrez le mot de passe racine (**Root password**), confirmez le mot de passe, puis cliquez sur **Next** (Suivant) pour continuer.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

12. Sélectionnez **Create Custom Layout** (Créer une disposition personnalisée) comme mode de partition, puis cliquez sur **Next** (Suivant) pour continuer.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

13. Sélectionnez le type de partition **Free** (Libre), puis cliquez sur **Create** (Créer) pour créer des partitions **/**, **/var/crash** et **/home** avec **ext4** comme type de système de fichiers. Créez une **partition d’échange** avec **swap** comme type de système de fichiers. Pour allouer une taille de partition, suivez la formule d’allocation de taille, comme indiqué dans le tableau.

    > [!NOTE]
    > Le système cible maître Linux ne doit pas utiliser LVM pour les espaces de stockage racine ou de rétention. Le serveur maître cible Linux est configuré pour éviter la découverte des disques/partitions LVM par défaut.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

14. Une fois la partition créée, cliquez sur **Next** (Suivant) pour continuer.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

15. Si des périphériques existants sont détectés, un message d’avertissement relatif au formatage apparaît. Cliquez sur **Format** (Formater) pour formater le lecteur avec la dernière table de partition.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)

16. Cliquez sur **Write changes to disk** (Enregistrer les modifications sur le disque) pour appliquer les modifications de partition sur le disque.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

17. Cochez l’option **Install boot loader** (Installer le chargeur de démarrage), puis cliquez sur **Next** (Suivant) pour installer le chargeur de démarrage sur la partition racine.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)


18. Le processus d’installation démarre. Vous pouvez suivre la progression de l’installation.

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

19. L’écran suivant apparaît à la fin de l’installation. Cliquez sur **Reboot** (Redémarrer)

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Procédure de post-installation
Nous allons maintenant préparer la machine à configurer en tant que serveur cible maître.

Pour obtenir l’ID SCSI de chacun des disques durs SCSI d’une machine virtuelle Linux, activez le paramètre disk.EnableUUID = TRUE.

Pour activer ce paramètre, suivez la procédure ci-dessous :

a. Arrêtez votre machine virtuelle.

b. Cliquez avec le bouton droit sur l’entrée de machine virtuelle du panneau de gauche, puis sélectionnez **Modifier les paramètres.**

c. Cliquez sur l’onglet **Options** .

d. Sélectionnez l’élément **Avancé&gt;Général** sur la gauche, puis cliquez sur les **Paramètres de configuration** affichés sur la droite.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

L’option Paramètres de configuration est inactive lorsque l’ordinateur est en cours d’exécution. Pour rendre cet onglet actif, arrêtez la machine.

e. Vérifiez si une ligne comportant **disk.EnableUUID** existe.

f. Si ce paramètre existe et qu’il présente la valeur FALSE, remplacez cette valeur par TRUE (les valeurs TRUE et FALSE ne sont pas sensibles à la casse).

g. Si ce paramètre existe et qu’il présente la valeur TRUE, cliquez sur Cancel (Annuler).
    
h. Si ce paramètre n’existe pas, cliquez sur **Add Row** (Ajouter une ligne).

i. Ajoutez disk.EnableUUID dans la colonne Name (Nom) et définissez sa valeur sur TRUE.

   * Si cette valeur existe et qu’elle est définie sur False, remplacez ce paramétrage par True (ces éléments sont sensibles à la casse).

   * Si cette valeur existe et qu’elle est définie sur true, cliquez sur Annuler et testez la commande SCSI du système d’exploitation invité après le démarrage.

f. Si elle n’existe pas, cliquez sur **Ajouter des lignes.**

  Dans la colonne Nom, ajoutez disk.EnableUUID.

  Affectez-lui la valeur TRUE.



![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Télécharger et installer les packages supplémentaires

> [!NOTE]
> Assurez-vous que système dispose d’une connectivité Internet avant de télécharger et d’installer les packages supplémentaires. Si ce n’est pas le cas, vous devrez rechercher et installer manuellement ces packages RPM.

```
yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

La commande ci-dessus téléchargera les 15 packages mentionnés ci-dessous à partir du référentiel CentOS 6 et procèdera à leur installation. Si vous n’avez pas accès à Internet, vous devrez télécharger les packages RPM suivant.


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
> Si les ordinateurs source protégés utilisent un système de fichiers Reiser ou XFS pour le périphérique racine ou de démarrage, les packages supplémentaires suivants doivent être téléchargés et installés sur le serveur cible maître Linux avant la protection.
 

***ReiserFS (s’il est utilisé dans Suse11SP3. Cependant, ReiserFS n’est pas le système de fichiers par défaut dans Suse11SP3)***

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
Requis pour activer les packages à plusieurs chemins sur le serveur maître cible.

### <a name="get-the-installer-for-setup"></a>Obtenir le programme d’installation

Si vous avez accès à internet à partir de votre serveur cible maître, vous pouvez télécharger le programme d’installation en suivant les étapes ci-dessous. Si ce n’est pas le cas, vous pouvez copier le programme d’installation à partir du serveur de traitement et l’installer.

#### <a name="download-the-mt-installation-packages"></a>Téléchargez les packages d’installation du serveur maître cible

Téléchargez les derniers bits d’installation du serveur maître cible Linux ici : [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Pour les télécharger avec Linux, tapez : 

```
wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz
```

Veillez à télécharger et à décompresser le programme d’installation uniquement dans le répertoire de base. Si vous le décompressez dans /usr/Local, l’installation échouera.


#### <a name="access-the-installer-from-the-process-server"></a>Accéder au programme d’installation à partir du serveur de traitement

Accédez au serveur de traitement, puis au répertoire - C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

Copiez le fichier d’installation requis à partir du serveur de traitement et enregistrez-le sous le nom latestlinuxmobsvc.tar.gz dans votre répertoire de base.


### <a name="apply-custom-configuration-changes"></a>Appliquer les modifications de configuration personnalisée

Pour appliquer les modifications de configuration personnalisée, procédez comme suit :


1. Exécutez la commande tar ci-dessous pour désarchiver le fichier binaire.
    ```
    tar -zxvf latestlinuxmobsvc.tar.gz
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
2. Exécutez la commande ci-dessous pour attribuer l’autorisation.
    ```
    chmod 755 ./ApplyCustomChanges.sh
    ```

3. Lancez la commande ci-dessous, puis exécutez le script.
    ```
    ./ApplyCustomChanges.sh
    ```
> [!NOTE]
> N’exécutez le script qu’une fois sur le serveur. Arrêtez le serveur. Redémarrez le serveur après avoir ajouté un disque à l’aide de la procédure ci-dessous.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Ajouter un disque de rétention à la machine virtuelle du serveur cible maître Linux 

Suivez les étapes indiquées ci-dessous pour créer un disque de rétention.

1. Attachez un nouveau disque de **1 To** à la machine virtuelle du serveur cible maître Linux et **démarrez** la machine.

2. Appelez la commande **multipath -ll** pour obtenir l’ID multi-chemin du disque de rétention.

    ```
    multipath -ll
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

3. Formatez le lecteur et créez un système de fichiers sur le nouveau lecteur. 
    
    ```
    mkfs.ext4 /dev/mapper/<Retention disk's multipath id>
    ```
    ![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

4. Une fois le système de fichiers créé, montez le disque de rétention.
    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

5. Enfin, créez l’entrée fstab pour monter le lecteur de rétention à chaque démarrage.
    ```
    vi /etc/fstab 
    ```
    Appuyez sur la touche [Insertion] pour commencer à modifier le fichier. Créez une ligne et insérez-y le texte suivant. Modifiez l’ID multi-chemin du disque en fonction de l’ID multi-chemin surligné obtenu avec la commande précédente.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    Appuyez sur la touche [Échap] et tapez :wq (écrire et quitter) pour fermer la fenêtre de l’éditeur.

### <a name="install-master-target"></a>Installer le serveur cible maître

> [!NOTE]
> La version du serveur maître cible doit être inférieure ou égale à celles du serveur de processus et du serveur de configuration. Si la version du serveur maître cible est supérieure, la reprotection réussira, mais la réplication échouera.
 

> [!NOTE]
> Avant d’installer le serveur maître principal, vérifiez que les fichiers /etc/hosts sur la machine virtuelle contiennent des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
 
1. Copiez la phrase secrète à partir de **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** sur le serveur de configuration et enregistrez-la dans le fichier passphrase.txt dans le même répertoire local en exécutant la commande suivante.

    ```
    echo <passphrase> >passphrase.txt
    ```
    Exemple : echo itUx70I47uxDuUVY >passphrase.txt

2. Notez l’adresse IP du serveur de configuration. Vous en aurez besoin à l’étape suivante.

3. Exécutez la commande suivante pour installer le serveur cible maître et inscrire le serveur auprès du serveur de configuration.
    
    ```
    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i <Configuration Server IP Address> -p 443 -s y -c https -P passphrase.txt
    ```

    Exemple : ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

    Attendez la fin de l’exécution du script. Si l’inscription du serveur cible maître réussit, ce dernier est répertorié dans la page Infrastructure Site Recovery du portail.

#### <a name="installing-master-target-using-interactive-install"></a>Installation du serveur cible maître à l’aide de l’installation interactive

1. Exécutez la commande suivante pour installer le serveur cible maître. Choisissez le rôle d’agent « Master Target » (Maître cible).

    ```
    ./install
    ```

2. Choisissez l’emplacement par défaut de l’installation, puis appuyez sur la touche [Entrée] pour continuer.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Choisissez les paramètres globaux à configurer

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Spécifiez les adresses IP du serveur CS

5. Spécifiez le port 443 pour le serveur CS.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copiez la phrase secrète CS à partir de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de configuration et collez-la dans la zone prévue à cet effet. Celle-ci apparaîtra vide même après l’opération de copier-coller.

7. Dans le menu, accédez à [Quit] (Quitter).

8. Attendez la fin de l’installation et de l’inscription.

### <a name="install-vmware-tools-on-the-master-target-server"></a>Installer les outils VMware sur le serveur cible maître

Les outils VMware doivent être installés sur le serveur maître cible afin qu’ils puissent détecter les magasins de données. Si les outils ne sont pas installés, l’écran de reprotection ne répertorie pas les magasins de données. Vous devrez redémarrer après l’installation des outils VMware.

## <a name="next-steps"></a>Étapes suivantes
Une fois l’installation et l’inscription du serveur cible maître terminées, ce dernier apparaît dans la section Maître cible de la page Infrastructure Site Recovery, sous la vue d’ensemble du serveur de configuration.

Vous pouvez maintenant procéder à la [reprotection](site-recovery-how-to-reprotect.md), puis à la restauration automatique.

## <a name="common-issues"></a>Problèmes courants

* Veillez à ne pas activer Storage vMotion sur des composants de gestion tels qu’un serveur cible maître. Si le serveur cible maître passe outre la reprotection, le disque de la machine virtuelle ne pourra pas être détaché et la restauration automatique échouera.
* La machine virtuelle du serveur cible maître ne doit pas présenter d’instantanés. S’il existe des instantanés, la restauration automatique échouera.
* En raison de configurations de carte réseau personnalisées chez certains clients, l’interface réseau est désactivée au cours du démarrage et l’agent du serveur cible maître ne peut pas être initialisé. Assurez-vous que les propriétés suivantes sont configurées correctement.
    * Vérifiez les deux propriétés dans les fichiers de la carte Ethernet /etc/sysconfig/network-scripts/ifcfg-eth* :
        * BOOTPROTO=dhcp 
        * ONBOOT=yes


