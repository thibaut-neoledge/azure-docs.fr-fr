---
title: Configurer Oracle ASM sur une machine virtuelle Linux Azure | Microsoft Docs
description: "Rendez Oracle ASM opérationnel rapidement dans votre environnement Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 39f6acd0def9fa5c2de470268cda6666aa572e5d
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Configurer Oracle ASM sur une machine virtuelle Linux Azure  

Dans cet article, découvrez comment installer et configurer Oracle Automatic Storage Management (Oracle ASM) sur une machine de virtuelle Linux Oracle dans Azure.

Avant de commencer, vérifiez qu’Azure CLI est installé. Pour plus d’informations, consultez le [Guide d’installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Préparer l’environnement

### <a name="sign-in-to-azure"></a>Connexion à Azure 

Dans Azure CLI, pour vous connecter à votre abonnement Azure, utilisez la commande [az login](/cli/azure/#login). Ensuite, suivez les instructions à l’écran.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Pour créer un groupe de ressources, utilisez la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé myResourceGroup à l’emplacement westus.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-a-vm"></a>Créer une machine virtuelle

Pour créer une machine virtuelle à utiliser avec Oracle ASM, procédez comme suit :

#### <a name="1--to-create-a-virtual-machine-use-the-az-vm-createcliazurevmcreate-command"></a>1.  Pour créer une machine virtuelle, utilisez la commande [az vm create](/cli/azure/vm#create). 

  L’exemple suivant crée une machine virtuelle nommée myVM. Il crée également des clés SSH si elles n’existent pas déjà à un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  

  ```azurecli
  az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
  ```

  Une fois que vous avez créé la machine virtuelle, Azure CLI affiche des informations similaires à l’exemple suivant. Notez la valeur pour `publicIpAddress`. Vous utilisez cette adresse pour accéder à la machine virtuelle.

  ```azurecli
  {
    "fqdns": "",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
    "location": "westus",
    "macAddress": "00-0D-3A-36-2F-56",
    "powerState": "VM running",
    "privateIpAddress": "10.0.0.4",
    "publicIpAddress": "13.64.104.241",
    "resourceGroup": "myResourceGroup"
  }
  ```

#### <a name="2--add-disks-to-use-for-your-oracle-asm-configuration"></a>2.  Ajoutez des disques à utiliser pour votre configuration d’Oracle ASM :

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

### <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Pour créer une session SSH avec la machine virtuelle, utilisez la commande suivante. Remplacez l’adresse IP par la valeur de `publicIpAddress` pour votre machine virtuelle.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installer Oracle ASM

Pour installer Oracle ASM, procédez comme suit. 

Pour plus d’informations sur l’installation d’Oracle ASM, consultez [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

### <a name="1-run-yum-list"></a>1. Exécutez `yum list` :

  ```bash
  $ sudo su -
  # yum list
  ```
  Plusieurs minutes peuvent être nécessaires pour charger `yum list` la première fois que vous l’exécutez.

### <a name="2--run-these-additional-commands"></a>2.  Exécutez ces commandes supplémentaires :

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

### <a name="3--verify-that-oracle-asm-is-installed"></a>3.  Vérifiez qu’Oracle ASM est installé :

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

### <a name="4--add-users-and-groups"></a>4.  Ajoutez des utilisateurs et des groupes :

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

### <a name="5--verify-users-and-groups"></a>5.  Vérifiez les utilisateurs et les groupes :

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

### <a name="6--create-a-folder-and-change-owner"></a>6.  Créez un dossier et changez son propriétaire :

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>Configurer Oracle ASM

Pour ce didacticiel, l’utilisateur par défaut est *grid* et le groupe par défaut est *asmadmin*. Vérifiez que l’utilisateur *oracle* fait partie du groupe asmadmin. Pour configurer votre installation Oracle ASM, procédez comme suit :

### <a name="1--set-the-oracle-asm-library-driver"></a>1.  Définissez le pilote de la bibliothèque Oracle ASM :

  ```bash
  # /usr/sbin/oracleasm configure -i

  Configuring the Oracle ASM library driver.

  This will configure the on-boot properties of the Oracle ASM library
  driver. The following questions will determine whether the driver is
  loaded on boot and what permissions it will have. The current values
  will be shown in brackets ('[]'). Hitting <ENTER> without typing an
  answer will keep that current value. Ctrl-C will abort.

  Default user to own the driver interface []: grid
  Default group to own the driver interface []: asmadmin
  Start Oracle ASM library driver on boot (y/n) [n]: y
  Scan for Oracle ASM disks on boot (y/n) [y]: y
  Writing Oracle ASM library driver configuration: done
  ```

### <a name="2--view-the-disk-configuration"></a>2.  Affichez la configuration du disque :
  ```bash
  # cat /proc/partitions
  ```

### <a name="3--format-the-disk"></a>3.  Formatez le disque :

  ```bash
  # fdisk /dev/sdc
  Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
  Building a new DOS disklabel with disk identifier 0xf865c6ca.
  Changes will remain in memory only, until you decide to write them.
  After that, of course, the previous content won't be recoverable.

  Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

  The device presents a logical sector size that is smaller than
  the physical sector size. Aligning to a physical sector (or optimal
  I/O) size boundary is recommended, or performance may be impacted.

  WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
          switch off the mode (command 'c') and change display units to
          sectors (command 'u').

  Command (m for help): n
  Command action
    e   extended
    p   primary partition (1-4)
  p
  Partition number (1-4): 1
  First cylinder (1-6527, default 1):
  Using default value 1
  Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
  Using default value 6527

  Command (m for help): w
  The partition table has been altered!

  Calling ioctl() to re-read partition table.
  Syncing disks.
  ```

### <a name="4--repeat-the-preceding-step-for-devsdd-devsde-and-devsdf"></a>4.  Répétez l’étape précédente pour /dev/sdd, /dev/sde et /dev/sdf.

### <a name="5--check-the-disk-configuration"></a>5.  Vérifiez la configuration du disque :

```bash
# cat /proc/partitions
major minor  #blocks  name

  8       16   14680064 sdb
  8       17   14678976 sdb1
  8       32   52428800 sdc
  8       33   52428096 sdc1
  8       48   52428800 sdd
  8       49   52428096 sdd1
  8       64   52428800 sde
  8       65   52428096 sde1
  8       80   52428800 sdf
  8       81   52428096 sdf1
  8        0   52428800 sda
  8        1     512000 sda1
  8        2   51915776 sda2
  11        0    1048575 sr0
```

### <a name="6--check-the-oracle-asm-service-status"></a>6.  Vérifiez l’état du service Oracle ASM :

```bash
# service oracleasm status
Checking if ASM is loaded: no
Checking if /dev/oracleasm is mounted: no
```

### <a name="7--start-the-oracle-asm-service"></a>7.  Démarrez le service Oracle ASM :

```bash
# service oracleasm start
Initializing the Oracle ASMLib driver:                     [  OK  ]
Scanning the system for Oracle ASMLib disks:               [  OK  ]
```

### <a name="8--create-oracle-asm-disks"></a>8.  Créez les disques Oracle ASM :

```bash
# service oracleasm createdisk ASMSP /dev/sdc1
Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk DATA /dev/sdd1
Marking disk "DATA" as an ASM disk:                        [  OK  ]

# service oracleasm createdisk DATA1 /dev/sde1
Marking disk "DATA1" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk FRA /dev/sdf1
Marking disk "FRA" as an ASM disk:                         [  OK  ]
```

### <a name="9--list-oracle-asm-disks"></a>9.  Répertoriez les disques Oracle ASM :

```bash
# service oracleasm listdisks
ASMSP
DATA
DATA1
FRA
```

### <a name="10-change-the-passwords-for-the-root-oracle-and-grid-users-you-use-the-passwords-later-during-installation"></a>10. Changez les mots de passe pour les utilisateurs root, oracle et grid (vous utilisez les mots de passe plus tard, lors de l’installation) :

```bash
# passwd oracle
# passwd grid
# passwd root
```

### <a name="11-change-the-folder-permission"></a>11. Changez l’autorisation du dossier :

```bash
# chmod -R 775 /opt
# chown grid:oinstall /opt
# chown oracle:oinstall /dev/sdc1
# chown oracle:oinstall /dev/sdd1
# chown oracle:oinstall /dev/sde1
# chown oracle:oinstall /dev/sdf1
# chmod 600 /dev/sdc1
# chmod 600 /dev/sdd1
# chmod 600 /dev/sde1
# chmod 600 /dev/sdf1
```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Télécharger et préparer Oracle Grid Infrastructure

Pour télécharger et préparer Oracle Grid Infrastructure, procédez comme suit :

### <a name="1--download-oracle-grid-infrastructure-from-the-oracle-asm-download-pagehttpwwworaclecomtechnetworkdatabaseenterprise-editiondownloadsdatabase12c-linux-download-2240591html"></a>1.  Téléchargez Oracle Grid Infrastructure à partir de la [page de téléchargement d’Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

  Sous le titre du téléchargement **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64**, il doit y avoir deux fichiers .zip à télécharger.

### <a name="2--after-you-download-the-zip-files-to-your-client-computer-you-can-use-secure-copy-protocol-scp-to-copy-the-files-to-your-vm"></a>2.  Après avoir téléchargé les fichiers .zip sur votre ordinateur client, vous pouvez utiliser SCP (Secure Copy Protocol) pour copier les fichiers sur votre machine virtuelle.

```bash
scp *.zip <publicIpAddress>:<folder>
```

### <a name="3--move-the-zip-files-to-the-opt-folder-then-change-the-owner-of-the-files"></a>3.  Déplacez les fichiers .zip dans le dossier /opt. Ensuite, changez le propriétaire des fichiers :

```bash
# mv <folder>/*.zip /opt
# cd /opt
# chown grid:oinstall linuxamd64_12102_grid_1of2.zip
# chown grid:oinstall linuxamd64_12102_grid_2of2.zip
```
### <a name="4--unzip-the-files-install-the-linux-unzip-utility-if-its-not-already-installed"></a>4.  Décompressez les fichiers (installez l’utilitaire unzip de Linux s’il n’est pas déjà installé) :
```bash
# yum install unzip
# unzip linuxamd64_12102_grid_1of2.zip
# unzip linuxamd64_12102_grid_2of2.zip
```
### <a name="5--change-permission"></a>5.  Changez l’autorisation :
```bash
# chown -R grid:oinstall /opt/grid
```
### <a name="6--turn-off-the-firewall"></a>6.  Désactivez le pare-feu :
```bash
# service iptables status
# service iptables stop
```

### <a name="7--check-available-swap-space-you-need-at-lease-68-gb-of-swap-space-to-install-grid-by-default-linux-azure-vms-will-not-have-swap-enabled-and-configured-by-default"></a>7.  Vérifiez l’espace d’échange disponible. Vous devez disposer d’au moins 6,8 Go d’espace d’échange pour installer Grid. Par défaut, l’espace d’échange n’est pas activé ni configuré sur les machines virtuelles Linux Azure.

Il est vivement recommandé d’utiliser waagent pour configurer l’espace d’échange de sorte qu’il soit toujours créé sur le disque éphémère (disque temporaire). Pour plus d’informations sur les étapes à appliquer, consultez le lien ci-dessous : 

* [Comment faire pour ajouter un fichier d’échange dans des machines virtuelles Linux Azure](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Préparer le client et la machine virtuelle à exécuter X11 (pour les clients Windows uniquement)
Il s’agit d’une étape facultative. Vous pouvez l’ignorer si vous utilisez un client Linux ou si x11 est déjà configuré.

### <a name="1--download-putty-and-xming-to-your-windows-computer"></a>1.  Téléchargez PuTTY et Xming sur votre ordinateur Windows :

  * [Télécharger PuTTY](http://www.putty.org/)
  * [Télécharger Xming](https://xming.en.softonic.com/)

### <a name="2--after-you-install-putty-in-the-putty-folder-for-example-cprogram-filesputty-run-puttygenexe-putty-key-generator"></a>2.  Après avoir installé PuTTY, dans le dossier de PuTTY (par exemple C:\Program Files\PuTTY), exécutez puttygen.exe (PuTTY Key Generator).

### <a name="3--in-putty-key-generator"></a>3.  Dans PuTTY Key Generator :

- Pour générer une clé, sélectionnez le bouton **Generate** (Générer).
- Copiez le contenu de la clé (Ctrl+C).
- Sélectionnez le bouton **Save private key** (Enregistrer la clé privée).
- Ignorer l’avertissement qui s’affiche, puis sélectionnez **OK**.

  ![Capture d’écran de la page du générateur de clé PuTTY](./media/oracle-asm/puttykeygen.png)

### <a name="4--in-your-vm-run-these-commands"></a>4.  Dans votre machine virtuelle, exécutez ces commandes :

```bash
# sudo su - grid
$ mkdir .ssh (if not already created)
$ cd .ssh
```

### <a name="5--create-a-file-named-authorizedkeys-paste-the-contents-of-the-key-in-this-file-and-then-save-the-file"></a>5.  Créez un fichier nommé authorized_keys. Collez le contenu de la clé dans ce fichier, puis enregistrez-le.

> [!NOTE]
> La clé doit contenir la chaîne `ssh-rsa`. En outre, le contenu de la clé doit être constitué d’une seule ligne de texte.
>  

### <a name="6--start-putty-in-the-category-pane-go-to-connection--ssh--auth-in-the-private-key-file-for-authentication-box-browse-to-the-key-that-you-generated-earlier"></a>6.  Démarrez PuTTY. Dans le volet **Category** (Catégorie), accédez à **Connection** > **SSH** > **Auth** (Connexion > SSH > Authentification). Dans la zone **Private key file for authentication** (Fichier de clé privée pour l’authentification), accédez à la clé que vous avez générée précédemment.

  ![Capture d’écran de la page Set Private Key (Définir la clé privée)](./media/oracle-asm/setprivatekey.png)

### <a name="7--in-the-category-pane-go-to-connection--ssh--x11-select-the-enable-x11-forwarding-box"></a>7.  Dans le volet **Category** (Catégorie), accédez à **Connection** > **SSH** > **X11** (Connexion > SSH > X11). Sélectionnez **Enable X11 forwarding** (Activer le transfert X11).

  ![Capture d’écran de la page Enable X11 (Activer X11)](./media/oracle-asm/enablex11.png)

### <a name="8-in-the-category-pane-go-to-session-enter-the-host-information-and-then-select-open"></a>8. Dans le volet **Category** (Catégorie), accédez à **Session**. Entrez les informations de l’hôte, puis sélectionnez **Open** (Ouvrir).

  ![Capture d’écran de la page Session](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installer Oracle Grid Infrastructure

Pour installer Oracle Grid Infrastructure, procédez comme suit :

### <a name="1-sign-in-as-grid-you-should-be-able-to-sign-in-without-being-prompted-for-a-password"></a>1. Connectez-vous en tant que grid. (Vous devez pouvoir vous connecter sans avoir à saisir un mot de passe.) 

> [!NOTE]
> Vérifiez que Xming est en cours d’exécution avant de commencer l’installation.

```bash
$ cd /opt/grid
$ ./runInstaller
```

  Le programme d’installation d’Oracle Grid Infrastructure 12c Release 1 s’ouvre. (Plusieurs minutes peuvent être nécessaires au démarrage du programme d’installation.)

### <a name="2-on-the-select-installation-option-page-select-install-and-configure-oracle-grid-infrastructure-for-a-standalone-server"></a>2. Dans la page **Select Installation Option** (Sélectionner l’option d’installation), sélectionnez **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Installer et configurer Oracle Grid Infrastructure pour un serveur autonome).

  ![Capture d’écran de la page Select Installation Option (Sélectionner l’option d’installation)](./media/oracle-asm/install01.png)

### <a name="3-on-the-select-product-languages-page-select-english-or-the-language-that-you-want"></a>3. Dans la page **Select Product Languages** (Sélectionner les langues du produit), sélectionnez **English** (Anglais) ou la langue que vous souhaitez.

  ![Capture d’écran de la page Select Product Languages (Sélectionner les langues du produit)](./media/oracle-asm/install02.png)

### <a name="4-on-the-create-asm-disk-group-page"></a>4. Dans la page **Create ASM Disk Group** (Créer un groupe de disques ASM) :
- Entrez un nom pour le groupe de disques.
- Sous **Redundancy** (Redondance), sélectionnez **External** (Externe).
- Sous **Allocation Unit Size** (Taille d’unité d’allocation), sélectionnez **4**.
- Sous **Add Disks** (Ajouter des disques), sélectionnez **ORCLASMSP**.

  ![Capture d’écran de la page Create ASM Disk Group (Créer un groupe de disques ASM) du programme d’installation](./media/oracle-asm/install03.png)

### <a name="5-on-the-specify-asm-password-page-select-the-use-same-passwords-for-these-accounts-option-and-enter-a-password"></a>5. Dans la page **Specify ASM Password** (Spécifier le mot de passe ASM), sélectionnez l’option **Use same passwords for these accounts** (Utiliser les mêmes mots de passe pour ces comptes) et entrez un mot de passe.

  ![Capture d’écran de la page Specify ASM Password (Spécifier le mot de passe ASM) du programme d’installation](./media/oracle-asm/install04.png)

### <a name="6-optional-on-the-specify-management-options-page-select-the-register-with-enterprise-manager-em-cloud-control-box"></a>6. (Facultatif) Dans la page **Specify Management Options** (Spécifier les options de gestion), sélectionnez **Register with Enterprise Manager (EM) Cloud Control** (S’inscrire auprès d’ Enterprise Manager (EM) Cloud Control).

  ![Capture d’écran de la page Specify Management Options (Spécifier les options de gestion) du programme d’installation](./media/oracle-asm/install05.png)

### <a name="7-on-the-privileged-operating-system-groups-page-use-the-default-settings"></a>7. Dans la page **Privileged Operating System Groups** (Groupes privilégiés du système d’exploitation), utilisez les paramètres par défaut.

  ![Capture d’écran Privileged Operating System Groups (Groupes privilégiés du système d’exploitation) du programme d’installation](./media/oracle-asm/install06.png)

### <a name="8-on-the-specify-installation-location-page-use-default-settings"></a>8. Dans la page **Specify Installation Location** (Spécifier l’emplacement d’installation), utilisez les paramètres par défaut.

  ![Capture d’écran de la page Specify Installation Location (Spécifier l’emplacement d’installation) du programme d’installation](./media/oracle-asm/install07.png)

### <a name="9-on-the-create-inventory-page-enter-or-browse-to-the-folder-location"></a>9. Dans la page **Create Inventory** (Créer un inventaire), entrez ou accédez à l’emplacement du dossier.

  ![Capture d’écran de la page Create Inventory (Créer un inventaire) du programme d’installation](./media/oracle-asm/install08.png)

### <a name="10-on-the-root-script-execution-configuration-page-select-the-automatically-run-configuration-scripts-box-then-select-the-use-root-user-credential-option-and-enter-the-root-user-password"></a>10. Dans la page **Root script execution configuration** (Configuration de l’exécution des script root), sélectionnez **Automatically run configuration scripts** (Exécuter automatiquement les scipts de configuration). Ensuite, sélectionnez l’option **Use "root" user credential** (Utiliser les informations d’identification de l’utilisateur « root »), puis entrez le mot de passe de l’utilisateur root.

  ![Capture d’écran de la page Use "root" user credential (Utiliser les informations d’identification de l’utilisateur « root ») du programme d’installation](./media/oracle-asm/install09.png)

### <a name="11-on-the-perform-prerequisite-checks-page-select-fix--check-again"></a>11. Dans la page **Perform Prerequisite Checks** (Effectuer les vérifications préalables), sélectionnez **Fix & Check Again** (Corriger et revérifier).

  ![Capture d’écran de la page Perform Prerequisite Checks (Effectuer les vérifications préalables) du programme d’installation](./media/oracle-asm/install10.png)

### <a name="12-on-the-fixup-script-page-select-ok"></a>12. Dans la page **Fixup Script** (Script de réparation), sélectionnez **OK**.

  ![Capture d’écran de la page Fixup Script (Script de réparation) du programme d’installation](./media/oracle-asm/install11.png)

### <a name="13-on-the-summary-page-review-your-settings-selections-and-then-select-install"></a>13. Dans la page **Summary** (Récapitulatif), passez en revue vos sélections pour les paramètres puis sélectionnez **Install** (Installer).

  ![Capture d’écran de la page Summary (Récapitulatif) du programme d’installation](./media/oracle-asm/install12.png)

### <a name="14-a-warning-dialog-box-appears-select-yes-to-continue"></a>14. Une boîte de dialogue d’avertissement apparaît. Cliquez sur **Yes** (Oui) pour continuer.

  ![Capture d’écran de la boîte de dialogue d’avertissement](./media/oracle-asm/install14.png)

### <a name="15-on-the-finish-page-select-close-to-finish-the-installation"></a>15. Dans la page **Finish** (Terminer), sélectionnez **Close** (Fermer) pour terminer l’installation.

  ![Capture d’écran de la page Finish (Terminer) du programme d’installation](./media/oracle-asm/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Configurer votre installation Oracle ASM

Pour configurer votre installation Oracle ASM, procédez comme suit :

### <a name="1--sign-in-as-grid-from-your-x11-session"></a>1.  Connectez-vous en tant que grid, à partir de votre session X11 :

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  L’Assistant de configuration d’Oracle ASM s’ouvre.

### <a name="2--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>2.  Dans la page **Configure ASM: Disk Groups** (Configurer ASM : Groupes de disques), sélectionnez le bouton **Create** (Créer) puis sélectionnez **Show Advanced Options** (Afficher les options avancées).

  ![Capture d’écran de la page Configure ASM: Disk Groups (Configurer ASM : Groupes de disques)](./media/oracle-asm/asm01.png)

### <a name="3--on-the-create-disk-group-page"></a>3.  Dans la page **Create Disk Group** (Créer un groupe de disques) :

- Entrez le nom du groupe de disques.
- Sous **Select Member Disks** (Sélectionner les disques membres), sélectionnez **ORCL_DATA** et **ORCL_DATA1**.
- Sous **Allocation Unit Size** (Taille d’unité d’allocation), sélectionnez **4**.

  ![Capture d’écran de la page Create Disk Group (Créer un groupe de disques)](./media/oracle-asm/asm02.png)

### <a name="4--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>4.  Dans la page **Configure ASM: Disk Groups** (Configurer ASM : Groupes de disques), sélectionnez le bouton **Create** (Créer) puis sélectionnez **Show Advanced Options** (Afficher les options avancées).

  ![Capture d’écran de la page Configure ASM: Disk Groups (Configurer ASM : Groupes de disques)](./media/oracle-asm/asm03.png)

### <a name="5--on-the-create-disk-group-page"></a>5.  Dans la page **Create Disk Group** (Créer un groupe de disques) :

- Entrez le nom du groupe de disques.
- Sous **Redundancy** (Redondance), sélectionnez **External** (Externe).
- Sous **Select Member Disks** (Sélectionner les disques membres), sélectionnez **ORCL_FRA**.
- Sous **Allocation Unit Size** (Taille d’unité d’allocation), sélectionnez **4**.

  ![Capture d’écran de la page Create Disk Group (Créer un groupe de disques)](./media/oracle-asm/asm04.png)

### <a name="6--select-exit-to-close-asm-configuration-assistant"></a>6.  Sélectionnez **Exit** (Quitter) pour fermer l’Assistant de configuration d’ASM.

  ![Capture d’écran de la page Configure ASM: Disk Groups (Configurer ASM : Groupes de disques) avec le bouton Exit (Quitter)](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Création de la base de données

Le logiciel Oracle est déjà installé sur l’image de la Place de marché Azure. Pour installer la base de données, procédez comme suit :

### <a name="1--switch-users-to-the-oracle-superuser-and-then-initialize-the-listener-for-logging"></a>1.  Changez l’utilisateur et passez au superutilisateur d’Oracle, puis initialisez l’écouteur pour la journalisation :

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   L’Assistant de configuration de base de données s’ouvre.

### <a name="2--on-the-database-operation-page-select-create-database"></a>2.  Dans la page **Database Operation** (Opération de base de données), sélectionnez **Create Database** (Créer une base de données).

  ![Capture d’écran de la page Database Operation (Opération de base de données)](./media/oracle-asm/createdb01.png)
  
### <a name="3-on-the-creation-mode-page"></a>3. Dans la page **Creation Mode** (Mode de création ) :

- Entrez un nom pour la base de données.
- Pour **Storage Type** (Type de stockage), sélectionnez **Automatic Storage Management (ASM)**.
- Pour **Type de stockage** (Emplacement des fichiers de base de données), accédez au dossier que vous voulez utiliser.
- Pour **Fast Recovery Area** (Zone de récupération rapide), accédez au dossier que vous voulez utiliser.

  ![Capture d’écran de la page Creation Mode (Mode de création)](./media/oracle-asm/createdb02.png)

### <a name="4--on-the-summary-page-review-your-settings-selections-and-then-select-finish-to-create-the-database"></a>4.  Dans la page **Summary** (Récapitulatif), passez en revue vos sélections pour les paramètres puis sélectionnez **Finish** (Terminer) pour créer la base de données.

  ![Capture d’écran de la page Summary (Récapitulatif)](./media/oracle-asm/createdb03.png)

### <a name="5---optional-on-the-finish-page-to-change-the-passwords-select-password-management"></a>5.   (Facultatif) Dans la page **Finish** (Terminer), pour changer les mots de passe, sélectionnez **Password Management** (Gestion des mots de passe).

  ![Capture d’écran de la page Finish (Terminer)](./media/oracle-asm/createdb04.png)


## <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Quand vous n’avez plus besoin de la machine virtuelle, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel : Créer des machines virtuelles à haute disponibilité](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](../../linux/cli-samples.md)

