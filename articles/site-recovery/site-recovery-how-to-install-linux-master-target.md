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
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: c7c50c539149a929b15f50e4b52dc48d92534640
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-install-a-linux-master-target-server"></a>Installation d’un serveur cible maître Linux
Après avoir basculé une machine virtuelle, vous pouvez la restaurer automatiquement sur le site local. L’opération de restauration vous oblige à reprotéger la machine virtuelle à partir d’Azure sur le site local. Pour ce faire, vous avez besoin d’un serveur cible maître, capable de recevoir le trafic. Si votre machine virtuelle protégée est de type Windows, vous avez besoin d’un serveur cible maître Windows. Si vous avez une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux. Pour savoir comment créer et installer un serveur cible maître Linux, lisez les étapes ci-dessous.

> [!IMPORTANT]
> À partir de la version de 9.10.0 du serveur cible maître, le serveur cible maître le plus récent ne peut être installé que sur un serveur Ubuntu 16.04. De nouvelles installations ne sont pas autorisées sur les serveurs CentOS6.6. Toutefois, vous pouvez continuer la mise à niveau de vos anciens serveurs cible maître via la version 9.10.0.

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

**Installez Ubuntu version 16.04.2 Minimal**

Suivez les étapes indiquées ci-dessous pour installer le système d’exploitation Ubuntu 16.04.2 64 bits.

**Étape 1 :** à l’aide du lien suivant, choisissez le serveur miroir le plus proche afin de télécharger un fichier ISO contenant Ubuntu 16.04.2 Minimal 64 bits

<https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64>

Conservez le fichier ISO Ubuntu 16.04.2 Minimal 64 bits dans le lecteur DVD et démarrez le système.

**Étape 2 :** sélectionnez **French** (Français) comme langue par défaut, puis appuyez sur Entrée.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image1.png)

**Étape 3 :** sélectionnez **Install Ubuntu Server** (Installer serveur Ubuntu), puis appuyez sur Entrée

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image2.png)

**Étape 4 :** sélectionnez **French** (Français) comme langue par défaut, puis appuyez sur Entrée

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image3.png)

**Étape 5 :** sélectionnez l’option appropriée dans la liste **Time Zone** (Fuseau horaire), puis appuyez sur Entrée

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image4.png)

**Étape 6 :** sélectionnez l’option par défaut **NO** (NON), puis appuyez sur Entrée

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image5.png)

**Étape 7 :** sélectionnez **French (France)** (Français (France)) comme pays d’origine pour le clavier, puis appuyez sur Entrée

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image6.png)

**Étape 8 :** sélectionnez **French (France)** (Français (France)) comme disposition du clavier, puis appuyez sur Entrée

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image7.png)

**Étape 9 :** entrez le nom d’hôte de votre serveur dans la **zone de texte Hostname** (Nom d’hôte), puis cliquez sur le bouton **Continue** (Continuer)

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image8.png)

**Étape 10 :** entrez un **User name** (Nom utilisateur) pour créer un compte utilisateur dans la **zone de texte**, puis cliquez sur le bouton **Continue** (Continuer)

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image9.png)

**Étape 11 :** entrez un **Password** (Mot de passe) pour votre compte utilisateur dans la **zone de texte**, puis cliquez sur le bouton **Continue** (Continuer)

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image10.png)

**Étape 12 :** confirmez votre **Password** (Mot de passe) pour votre compte utilisateur dans la **zone de texte**, puis cliquez sur le bouton **Continue** (Continuer)

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image11.png)

**Étape 13 :** sélectionnez l’option par défaut **NO,** (NON) puis appuyez sur **Entrée**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image12.png)

**Étape 14 :** sélectionnez l’option par défaut **YES** (OUI) si le fuseau horaire affiché est correct, puis appuyez sur **Entrée.**

Vous pouvez sélectionner l’option **No** (Non) pour reconfigurer votre fuseau horaire.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image13.png)

**Étape 15 :** sélectionnez l’option « **Guided -** (Guidée) **Use entire disk** (Utiliser ensemble disque) » depuis les options de méthode de partitionnement, puis appuyez sur **Entrée.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image14.png)

**Étape 16 :** sélectionnez le disque approprié depuis l’option **Select disk to partition** (Sélectionner disque pour partition), puis appuyez sur **Entrée.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image15.png)

**Étape 17 :** sélectionnez **YES** (OUI) pour écrire les modifications des disques, puis appuyez sur **Entrée.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image16.png)

**Étape 18 :** sélectionnez l’option par défaut NO (NON), puis appuyez sur Entrée.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image17.png)

**Étape 19 :** sélectionnez l’option appropriée pour gérer les mises à niveau de votre système et appuyez sur **Entrée**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image18.png)

> [!WARNING]
> Le serveur cible maître de Azure Site Recovery nécessite une version spécifique de Ubuntu. Vous devez donc veiller à ce que les mises à niveau du noyau de la machine virtuelle soient désactivées. Si elles sont activées, toute mise à niveau entraîne un dysfonctionnement du serveur cible maître. Veillez à sélectionner l’option « No automatic updates » (Aucune mise à jour automatique).


**Étape 20 :** vous pouvez commencer avec les options par défaut. Si vous souhaitez qu’openSSH pour SSH connect, sélectionnez l’option « OpenSSH server » (Serveur OpenSSH), puis Continue (Continuer).

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image19.png)

**Étape 21 :** sélectionnez l’option **YES** (OUI), puis appuyez sur **Entrée**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image20.png)

**Étape 22 :** sélectionnez le périphérique approprié (de préférence dev ou sda) pour l’installation du chargeur de démarrage et appuyez sur **Entrée.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image21.png)

**Étape 23 :** sélectionnez **Continue** (Continuer), puis appuyez sur **Entrée** pour **terminer l’installation.**

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image22.png)

Une fois l’installation terminée. Connectez-vous à la machine virtuelle à l’aide des nouvelles informations d’identification de l’utilisateur (voir **Étape 10**).

Suivez les étapes mentionnées dans la capture d’écran suivante pour définir le mot de passe de l’utilisateur racine, pour se connecter en tant qu’utilisateur racine pour d’autres opérations.

![](./media/site-recovery-how-to-install-linux-master-target/ubuntu/image23.png)


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

#### <a name="disable-kernel-upgrades"></a>**Désactiver les mises à niveau du noyau**

Le serveur cible maître de Azure Site Recovery nécessite une version spécifique de Ubuntu. Vous devez donc veiller à ce que les mises à niveau du noyau de la machine virtuelle soient désactivées. Si elles sont activées, toute mise à niveau entraîne un dysfonctionnement du serveur cible maître. Utilisez les étapes ci-dessous pour désactiver les mises à niveau du noyau.
> [!IMPORTANT]
> Nécessaire pour les étapes de script

#### <a name="download-and-install-additional-packages"></a>Télécharger et installer les packages supplémentaires

> [!NOTE]
> Vérifiez que vous avez accès à Internet pour télécharger et installer les packages supplémentaires. Si vous n’êtes pas connecté à Internet, vous devez rechercher ces packages RPM et les installer manuellement.

```
apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx
```

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
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemple : /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    Attendez la fin du script. Si le serveur cible maître est déjà inscrit, il figure dans la page Site Recovery Infrastructure (Infrastructure Site Recovery) du portail.


#### <a name="install-the-master-target-by-using-interactive-install"></a>Installer le serveur cible maître en mode interactif

1. Exécutez la commande suivante pour installer le service cible maître. Choisissez le rôle d’agent **Master Target** (Cible maître).

    ```
    ./install
    ```

2. Choisissez l’emplacement par défaut de l’installation, puis appuyez sur **Entrée** pour continuer.

    ![Choix d’un emplacement par défaut pour l’installation du serveur cible maître](./media/site-recovery-how-to-install-linux-master-target/image17.png)

Une fois l’installation terminée, vous devez enregistrer le serveur de configuration à l’aide de la ligne de commande.

1. Notez l’adresse IP du serveur de configuration. Vous en aurez besoin à l’étape suivante.

2. Exécutez la commande suivante pour installer le serveur cible maître et l’inscrire auprès du serveur de configuration.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exemple : /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt

    Attendez la fin du script. Si le serveur cible maître est déjà inscrit, il figure dans la page Site Recovery Infrastructure (Infrastructure Site Recovery) du portail.


### <a name="upgrade-the-master-target"></a>Mettez le serveur cible maître à niveau

Exécutez le programme d’installation. Il détecte automatiquement que l’agent est installé sur le serveur cible maître. Sélectionnez « Y » (O) pour mettre à niveau. Une fois la configuration terminée, vous pouvez vérifier la version du serveur cible maître installée à l’aide de la commande suivante.

    ```
        cat /usr/local/.vx_version
    ```

Vous pouvez constater que le champ VERSION indique le numéro de version du serveur cible maître.

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

