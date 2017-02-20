---
title: "Installation d’un serveur cible maître Linux | Microsoft Docs"
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
ms.date: 12/20/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: 4133e98289bf22bccb71b9522627a645e92c5223
ms.openlocfilehash: 175972f0522db292180a56330307017a25f735b1


---
# <a name="how-to-install-linux-master-target-server"></a>Installation d’un serveur cible maître Linux
Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour avoir un rapide aperçu, consultez la section [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d'ensemble
Cet article fournit des informations et des instructions relatives à la récupération (basculement ou restauration automatique) des machines virtuelles et des serveurs physiques protégés par Microsoft Azure Site Recovery.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="pre-requisites"></a>Conditions préalables

1. Pour choisir correctement l’hôte sur lequel vous devez déployer le serveur cible maître, déterminez si la restauration automatique s’effectuera sur une machine virtuelle locale existante ou sur une nouvelle machine virtuelle (puisque la machine virtuelle locale a été supprimée). 
    * Pour une machine virtuelle existante, l’hôte du serveur maître cible doit avoir accès aux magasins de données de la machine virtuelle.
    * Si la machine virtuelle n’existe pas en local, la machine virtuelle de restauration automatique sera créée sur le même hôte que le serveur maître cible.
2. Le serveur maître cible doit se trouver sur un réseau capable de communiquer avec le serveur de processus et le serveur de configuration.
3. La version du serveur maître cible doit être inférieure ou égale à celles du serveur de processus et du serveur de configuration. (par exemple, si la version du serveur de configuration est 9.4, celle du serveur maître cible peut être 9.4 et 9.3, mais pas 9.5)
4. Le serveur maître cible peut uniquement être une machine virtuelle VMware, et pas une machine virtuelle physique.


## <a name="steps-to-deploy-the-master-target-server"></a>Procédure de déploiement du serveur maître cible

### <a name="install-centos-66-minimal"></a>Installer CentOS 6.6 Minimal

Suivez les étapes indiquées ci-dessous pour installer le système d’exploitation CentOS 6.6 64 bits.

**Étape 1 :** à l’aide des liens suivants, choisissez le serveur miroir le plus proche afin de télécharger un fichier ISO contenant CentOS 6.6 Minimal 64 bits.

<http://archive.kernel.org/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.symnds.com/distributions/CentOS-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://bay.uchicago.edu/centos-vault/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

<http://mirror.nsc.liu.se/centos-store/6.6/isos/x86_64/CentOS-6.6-x86_64-minimal.iso>

Conservez le fichier ISO CentOS 6.6 Minimal 64 bits dans le lecteur DVD et démarrez le système.

![](./media/site-recovery-how-to-install-linux-master-target/media/image1.png)

**Étape 2 :** sélectionnez **Skip** (Ignorer) pour ignorer le processus de test de support.

![](./media/site-recovery-how-to-install-linux-master-target/media/image2.png)

**Étape 3 :** vous pouvez maintenant voir l’écran d’accueil de l’installation. Ici, cliquez sur le bouton **Next** (Suivant).

![](./media/site-recovery-how-to-install-linux-master-target/media/image3.png)

**Étape 4 :** sélectionnez **French** (Français) comme langue par défaut puis cliquez sur **Next** (Suivant) pour continuer.

![](./media/site-recovery-how-to-install-linux-master-target/media/image4.png)

**Étape 5 :** sélectionnez **French** (Français) comme disposition de clavier. Cliquez sur **Next** (Suivant) pour continuer l'installation.

![](./media/site-recovery-how-to-install-linux-master-target/media/image5.png)

**Étape 6 :** sélectionnez le type de périphériques où sera effectuera l’installation. Sélectionnez **Basic storage Devices** (Périphériques de stockage de base).

Cliquez sur **Next** (Suivant) pour continuer l'installation.

![](./media/site-recovery-how-to-install-linux-master-target/media/image6.png)

**Étape 7 :** un message d’avertissement s’affiche, indiquant que les données existantes sur le disque dur seront supprimées. Assurez-vous que le disque dur ne contient pas de données importantes puis cliquez sur **Yes, discard any data** (Oui, supprimer le données).

![](./media/site-recovery-how-to-install-linux-master-target/media/image7.png)

**Étape 8 :** entrez le nom d’hôte de votre serveur dans la **zone de texte Hostname** (Nom d’hôte).
Cliquez sur **Configure Network** (Configurer le réseau).

Dans la fenêtre **Network Connection** (Connexion réseau), sélectionnez votre interface réseau. Cliquez sur le bouton **Edit** (Modifier) pour configurer les paramètres IPV4.

![](./media/site-recovery-how-to-install-linux-master-target/media/image8.png)

**Étape 9 :** l’écran **Editing System etho** s’affiche. Cochez la case **Connect automatically** (Se connecter automatiquement). Sous l’onglet « Paramètres IPv4 », choisissez la méthode **Manual** (Manuelle), puis cliquez sur le bouton **Add** (Ajouter). Spécifiez l’adresse IP statique, le masque réseau, la passerelle et le serveur DNS. Cliquez sur **Apply** (Appliquer) pour enregistrer les informations.

![](./media/site-recovery-how-to-install-linux-master-target/media/image9.png)

**Étape 10 :** Sélectionnez votre fuseau horaire dans la zone de liste déroulante Time Zone puis cliquez sur **Next** (Suivant) pour continuer.

![](./media/site-recovery-how-to-install-linux-master-target/media/image10.png)

**Étape 11 :** entrez le **mot de passe racine** (Root password), confirmez le mot de passe, puis cliquez sur **Next** (Suivant) pour continuer.

![](./media/site-recovery-how-to-install-linux-master-target/media/image11.png)

**Étape 12 :** sélectionnez **Create Custom Layout** (Créer une disposition personnalisée) comme mode de partition puis cliquez sur **Next** (Suivant) pour continuer.

![](./media/site-recovery-how-to-install-linux-master-target/media/image12.png)

**Étape 13 :** sélectionnez le type de partition **Free** (Libre), puis cliquez sur **Create** (Créer) pour créer des partitions **/**, **/var/crash** et **/home** avec **ext4** comme type de système de fichiers. Créez une **partition d’échange** avec **swap** comme type de système de fichiers. Pour allouer une taille de partition, suivez la formule d’allocation de taille, comme indiqué dans le tableau.

REMARQUE : le système maître cible Linux ne doit pas utiliser LVM pour les espaces de stockages racines ou de rétention. Le serveur maître cible Linux est configuré pour éviter la découverte des disques/partitions LVM par défaut.

![](./media/site-recovery-how-to-install-linux-master-target/media/image13.png)

**Étape 14 :** après la création de la partition, cliquez sur **Next** (Suivant) pour continuer.

![](./media/site-recovery-how-to-install-linux-master-target/media/image14.png)

**Étape 15 :** si des périphériques existants sont détectés, un message d’avertissement de formatage apparaît.

Cliquez sur **Format** (Formater) pour formater le lecteur avec la dernière table de partition.

![](./media/site-recovery-how-to-install-linux-master-target/media/image15.png)



**Étape 16 :** cliquez sur **Write changes to disk** (Enregistrer les modifications sur le disque) pour appliquer les modifications de partition sur le disque.

![](./media/site-recovery-how-to-install-linux-master-target/media/image16.png)

**Étape 17 :** cochez l’option **Install boot loader** (Installer le chargeur de démarrage), puis cliquez sur **Next** (Suivant) pour installer le chargeur de démarrage sur la partition racine.

![](./media/site-recovery-how-to-install-linux-master-target/media/image17.png)



**Étape 18 :** le processus d’installation démarre. Vous pouvez suivre la progression de l’installation.

![](./media/site-recovery-how-to-install-linux-master-target/media/image18.png)

**Étape 19 :** l’écran suivant apparaît si l’installation a réussi. Cliquez sur **Reboot** (Redémarrer)

![](./media/site-recovery-how-to-install-linux-master-target/media/image19.png)


### <a name="post-installation-steps"></a>Procédure de post-installation

Pour obtenir l’ID SCSI de chacun des disques durs SCSI d’une machine virtuelle Linux, activez le paramètre disk.EnableUUID = TRUE.

Pour activer ce paramètre, suivez la procédure ci-dessous :

a. Arrêtez votre machine virtuelle.

b. Cliquez avec le bouton droit sur l’entrée de machine virtuelle du panneau de gauche, puis sélectionnez **Modifier les paramètres.**

c. Cliquez sur l’onglet **Options** .

d. Sélectionnez l’élément **Avancé&gt;Général** sur la gauche, puis cliquez sur les **Paramètres de configuration** affichés sur la droite.

![](./media/site-recovery-how-to-install-linux-master-target/media/image20.png)

L’option Paramètres de configuration est inactive lorsque l’ordinateur est en cours d’exécution. Pour rendre cet onglet actif, arrêtez l’ordinateur.

e. Vérifiez si une ligne comportant **disk.EnableUUID** existe.

Si cette valeur existe et qu’elle est définie sur False, remplacez ce paramétrage par True (ces éléments sont sensibles à la casse).

Si cette valeur existe et qu’elle est définie sur true, cliquez sur Annuler et testez la commande SCSI du système d’exploitation invité après le démarrage.

f. Si elle n’existe pas, cliquez sur **Ajouter des lignes.**

Dans la colonne Nom, ajoutez disk.EnableUUID.

Définissez la valeur sur TRUE

Remarque : n’ajoutez pas les valeurs ci-dessus avec des guillemets doubles.

![](./media/site-recovery-how-to-install-linux-master-target/media/image21.png)

#### <a name="download-and-install-the-additional-packages"></a>Télécharger et installer les packages supplémentaires

Remarque : assurez-vous que le système dispose d’une connectivité Internet avant de télécharger et d’installer les packages supplémentaires.

```
# yum install -y xfsprogs perl lsscsi rsync wget kexec-tools
```

La commande ci-dessus téléchargera les 15 packages mentionnés ci-dessous à partir du référentiel CentOS 6 et procèdera à leur installation.


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

REMARQUE : si les ordinateurs source protégés utilisent un système de fichiers Reiser ou XFS pour l’appareil racine ou de démarrage, les packages supplémentaires suivants doivent être téléchargés et installés sur le système maître cible Linux avant la protection.

***ReiserFS (Suse11SP3. ReiserFS n’est cependant pas le système de fichiers par défaut dans Suse11SP3)***

```
# cd /usr/local

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

# wget
<http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

# rpm -ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm
reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
```

***XFS (RHEL, CentOS 7 et versions ultérieures)***

```
# cd /usr/local

# wget
<http://archive.kernel.org/centos-vault/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

# rpm -ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

# yum install device-mapper-multipath 
```
Requis pour activer les packages à plusieurs chemins sur le serveur maître cible.

### <a name="download-the-mt-installation-packages"></a>Téléchargez les packages d’installation du serveur maître cible

Téléchargez les derniers bits d’installation du serveur maître cible Linux ici : [https://aka.ms/latestlinuxmobsvc](https://aka.ms/latestlinuxmobsvc).

Pour les télécharger avec Linux, tapez : 

```
    # wget https://aka.ms/latestlinuxmobsvc
```

Veillez à télécharger et à décompresser le programme d’installation uniquement dans le répertoire de base. Si vous le décompressez dans /usr/Local, l’installation échouera.

### <a name="apply-custom-configuration-changes"></a>Appliquer les modifications de configuration personnalisée

Avant d’appliquer les modifications de configuration personnalisée, assurez-vous d’avoir exécuté la procédure de post-installation.

Pour appliquer les modifications de configuration personnalisée, procédez comme suit :

1. Copiez le binaire de l’agent unifié RHEL6-64 (Red Hat Enterprise Linux 64 bits) sur le SE nouvellement créé.

2. Exécutez la commande tar ci-dessous pour désarchiver le fichier binaire.
    ```
    tar -zxvf <File name>
    ```

    ![](./media/site-recovery-how-to-install-linux-master-target/image16.png)
    
3. Exécutez la commande ci-dessous pour attribuer l’autorisation.
    ```
    # chmod 755 ./ApplyCustomChanges.sh
    ```

4. Lancez la commande ci-dessous, puis exécutez le script.
    ```
    # ./ApplyCustomChanges.sh
    ```
REMARQUE : exécutez le script une seule fois sur le serveur. **Redémarrez** le serveur une fois le script ci-dessus exécuté.

### <a name="add-retention-disk-to-linux-mt-vm"></a>Ajouter le disque de rétention à la machine virtuelle du serveur maître cible Linux 

Suivez les étapes indiquées ci-dessous pour créer un disque de rétention.

**Étape 1 :** connectez un nouveau disque de **1 To** à la machine virtuelle du serveur maître cible Linux et identifiez son ID multi-chemin

Appelez la commande **multipath -ll** pour obtenir l’ID multi-chemin du disque de rétention.

![](./media/site-recovery-how-to-install-linux-master-target/media/image22.png)

**Étape 2 :** appelez la commande **mkfs.ext4 /dev/mapper/<Retention disk's multipath id>** pour créer un système de fichiers sur le disque de rétention.

![](./media/site-recovery-how-to-install-linux-master-target/media/image23.png)

**Étape 3 :** une fois le système de fichiers créé, appelez la ou les commandes ci-dessous pour monter le disque de rétention.

![](./media/site-recovery-how-to-install-linux-master-target/media/image24.png)

**Étape 4 :** créez l’entrée fstab :
```
vi /etc/fstab 
```
et ajoutez la ligne

** /dev/mapper/36000c2989daa2fe6dddcde67f2079afe /mnt/retention ext4 rw 0 0 **

### <a name="install-master-target"></a>Installer le serveur cible maître


> [!NOTE]
> La version du serveur maître cible doit être inférieure ou égale à celles du serveur de processus et du serveur de configuration. Si la version du serveur maître cible est supérieure, la reprotection réussira, mais la réplication échouera.
> 

> [!NOTE]
> Avant d’installer le serveur maître principal, vérifiez que les fichiers /etc/hosts sur la machine virtuelle contiennent des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
> 


1. Exécutez la commande ci-dessous pour installer le serveur maître. Choisissez le rôle d’agent « Master Target » (Maître cible).
```
# ./install
```

2. Choisissez l’emplacement par défaut de l’installation
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image17.png)
    

3. Choisissez les paramètres globaux à configurer

    ![](./media/site-recovery-how-to-install-linux-master-target/image18.png)
    
4. Spécifiez les adresses IP du serveur CS

5. Spécifiez le port du serveur CX, généralement 9443.
    
    ![](./media/site-recovery-how-to-install-linux-master-target/image19.png)
    
6. Copiez la phrase secrète CS à partir de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur de configuration.

7. Attendez la fin de l’installation et de l’inscription.
### <a name="install-vmware-tools-on-the-master-target-server"></a>Installer les outils VMware sur le serveur maître cible

Les outils VMware doivent être installés sur le serveur maître cible afin qu’ils puissent détecter les magasins de données. Si les outils ne sont pas installés, l’écran de reprotection ne répertorie pas les magasins de données.

## <a name="next-steps"></a>Étapes suivantes
 

## <a name="common-issues"></a>Problèmes courants


<!--HONumber=Feb17_HO2-->


