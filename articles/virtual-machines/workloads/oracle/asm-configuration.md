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
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: e3c98a3d4d7012f6cb6c7e1a1b8263e6ecdee57b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---

# <a name="set-up-oracle-asm-on-an-azue-linux-virtual-machine"></a>Configurer Oracle ASM sur une machine virtuelle Linux Azure 

Dans cet article, découvrez comment installer et configurer Oracle Automatic Storage Management (Oracle ASM) sur une machine de virtuelle Linux Oracle dans Azure.

Avant de commencer, vérifiez qu’Azure CLI est installé. Pour plus d’informations, consultez le [Guide d’installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="sign-in-to-azure"></a>Connexion à Azure 

Dans Azure CLI, pour vous connecter à votre abonnement Azure, utilisez la commande [az login](/cli/azure/#login). Ensuite, suivez les instructions à l’écran.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Pour créer un groupe de ressources, utilisez la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé myResourceGroup à l’emplacement westus.

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>Créer une machine virtuelle

Pour créer une machine virtuelle à utiliser avec Oracle ASM, procédez comme suit :

1.  Pour créer une machine virtuelle, utilisez la commande [az vm create](/cli/azure/vm#create). 

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

2.  Ajoutez des disques à utiliser pour votre configuration d’Oracle ASM :

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

## <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle

Pour créer une session SSH avec la machine virtuelle, utilisez la commande suivante. Remplacez l’adresse IP par la valeur de `publicIpAddress` pour votre machine virtuelle.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installer Oracle ASM

Pour installer Oracle ASM, procédez comme suit. 

Pour plus d’informations sur l’installation d’Oracle ASM, consultez [Oracle ASMLib Downloads for Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Exécutez `yum list` :

  ```bash
  $ sudo su -
  # yum list
  ```
  Plusieurs minutes peuvent être nécessaires pour charger `yum list` la première fois que vous l’exécutez.

2.  Exécutez ces commandes supplémentaires :

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

3.  Vérifiez qu’Oracle ASM est installé :

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

4.  Ajoutez des utilisateurs et des groupes :

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

5.  Vérifiez les utilisateurs et les groupes :

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

6.  Créez un dossier et changez son propriétaire :

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>Configurer Oracle ASM

Pour ce didacticiel, l’utilisateur par défaut est *grid* et le groupe par défaut est *asmadmin*. Vérifiez que l’utilisateur *oracle* fait partie du groupe asmadmin. Pour configurer votre installation Oracle ASM, procédez comme suit :

1.  Définissez le pilote de la bibliothèque Oracle ASM :

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

2.  Affichez la configuration du disque :
  ```bash
  # cat /proc/partitions
  ```

3.  Formatez le disque :

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

4.  Répétez l’étape précédente pour /dev/sdd, /dev/sde et /dev/sdf.

5.  Vérifiez la configuration du disque :

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

6.  Vérifiez l’état du service Oracle ASM :

  ```bash
  # service oracleasm status
  Checking if ASM is loaded: no
  Checking if /dev/oracleasm is mounted: no
  ```

7.  Démarrez le service Oracle ASM :

  ```bash
  # service oracleasm start
  Initializing the Oracle ASMLib driver:                     [  OK  ]
  Scanning the system for Oracle ASMLib disks:               [  OK  ]
  ```

8.  Créez les disques Oracle ASM :

  ```bash
  # service oracleasm createdisk ASMSP /dev/sdc1
  Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

  # service oracleasm createdisk DATA /dev/sdd1
  Marking disk "DATA" as an ASM disk:                        [  OK  ]

  # service oracleasm createdisk DATA1 /dev/sde1
  Marking disk "DATA1" as an ASM disk:                       [  OK  ]

  [root@myVM ~]# service oracleasm createdisk FRA /dev/sdf1
  Marking disk "FRA" as an ASM disk:                         [  OK  ]
  ```

9.  Répertoriez les disques Oracle ASM :

  ```bash
  # service oracleasm listdisks
  ASMSP
  DATA
  DATA1
  FRA
  ```

10. Changez les mots de passe pour les utilisateurs root, oracle et grid (vous utilisez les mots de passe plus tard, lors de l’installation) :

  ```bash
  # passwd oracle
  # passwd grid
  # passwd root
  ```

11. Changez l’autorisation du dossier :

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

1.  Téléchargez Oracle Grid Infrastructure à partir de la [page de téléchargement d’Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

  Sous le titre du téléchargement **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64**, il doit y avoir deux fichiers .zip à télécharger.

2.  Après avoir téléchargé les fichiers .zip sur votre ordinateur client, vous pouvez utiliser SCP (Secure Copy Protocol) pour copier les fichiers sur votre machine virtuelle.

    ```bash
    scp *.zip <publicIpAddress>:<folder>
    ```

3.  Déplacez les fichiers .zip dans le dossier /opt. Ensuite, changez le propriétaire des fichiers :

    ```bash
    # mv <folder>/*.zip /opt
    # cd /opt
    # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
    # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
    ```

4.  Décompressez les fichiers (installez l’utilitaire unzip de Linux s’il n’est pas déjà installé) :

    ```bash
    # yum install unzip
    # unzip linuxamd64_12102_grid_1of2.zip
    # unzip linuxamd64_12102_grid_2of2.zip
    ```

5.  Changez l’autorisation :

    ```bash
    # chown -R grid:oinstall /opt/grid
    ```

6.  Désactivez le pare-feu :

    ```bash
    # service iptables status
    # service iptables stop
    ```

7.  Vérifiez l’espace d’échange disponible. Un espace d’échange d’au moins 6 Go est nécessaire pour installer Oracle Grid Infrastructure :

    ```bash
    # swapon -s
    ```

    Si vous avez moins de 6 Go d’espace d’échange, vous pouvez ajouter plus d’espace en exécutant les commandes suivantes :

    ```bash
    # dd if=/dev/zero of=/extraswap bs=1M count=6144
    6144+0 records in
    6144+0 records out
    6442450944 bytes (6.4 GB) copied, 141.245 s, 45.6 MB/s

    # mkswap /mnt/resource/extraswap
    mkswap: /mnt/resource/extraswap: warning: don't erase bootbits sectors
            on whole disk. Use -f to force.
    Setting up swapspace version 1, size = 6291452 KiB
    no label, UUID=80bd7816-b3a2-4eec-a824-733209644fc5
    # swapon /mnt/resource/extraswap

    ```

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Préparer le client et la machine virtuelle à exécuter X11 (pour les clients Windows uniquement)

Pour préparer le client et la machine virtuelle à exécuter X11, procédez comme suit : 

1.  Connectez-vous en tant que root, puis éditez le fichier /etc/ssh/ssh_config. Changez la valeur pour Forwardx11 en `yes` :

  ```
  #   ForwardX11 no
  ForwardX11 yes

  ```

2.  Téléchargez PuTTY et Xming sur votre ordinateur Windows :

  * [Télécharger PuTTY](http://www.putty.org/)
  * [Télécharger Xming](https://xming.en.softonic.com/)

3.  Après avoir installé PuTTY, dans le dossier de PuTTY (par exemple C:\Program Files\PuTTY), exécutez puttygen.exe (PuTTY Key Generator).

4.  Dans PuTTY Key Generator :

  1.  Pour générer une clé, sélectionnez le bouton **Generate** (Générer). 
  2.  Copiez le contenu de la clé (Ctrl+C).
  3.  Sélectionnez le bouton **Save private key** (Enregistrer la clé privée). 
  4.  Ignorer l’avertissement qui s’affiche, puis sélectionnez **OK**.

  ![Capture d’écran de la page du générateur de clé PuTTY](./media/asm-configuration/puttykeygen.png)

5.  Dans votre machine virtuelle, exécutez ces commandes :

  ```bash
  # sudo su - grid
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

6.  Créez un fichier nommé authorized_keys. Collez le contenu de la clé dans ce fichier, puis enregistrez-le.

  > [!NOTE]
  > La clé doit contenir la chaîne `ssh-rsa`. En outre, le contenu de la clé doit être constitué d’une seule ligne de texte.
  >  

7.  Démarrez PuTTY. Dans le volet **Category** (Catégorie), accédez à **Connection** > **SSH** > **Auth** (Connexion > SSH > Authentification). Dans la zone **Private key file for authentication** (Fichier de clé privée pour l’authentification), accédez à la clé que vous avez générée précédemment.

  ![Capture d’écran de la page Set Private Key (Définir la clé privée)](./media/asm-configuration/setprivatekey.png)

9.  Dans le volet **Category** (Catégorie), accédez à **Connection** > **SSH** > **X11** (Connexion > SSH > X11). Sélectionnez **Enable X11 forwarding** (Activer le transfert X11).

  ![Capture d’écran de la page Enable X11 (Activer X11)](./media/asm-configuration/enablex11.png)

10. Dans le volet **Category** (Catégorie), accédez à **Session**. Entrez les informations de l’hôte, puis sélectionnez **Open** (Ouvrir).

  ![Capture d’écran de la page Session](./media/asm-configuration/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installer Oracle Grid Infrastructure

Pour installer Oracle Grid Infrastructure, procédez comme suit :

1. Connectez-vous en tant que grid. (Vous devez pouvoir vous connecter sans avoir à saisir un mot de passe.) 

  > [!NOTE]
  > Vérifiez que Xming est en cours d’exécution avant de commencer l’installation.

    ```bash
    $ cd /opt/grid
    $ ./runInstaller
    ```

  Le programme d’installation d’Oracle Grid Infrastructure 12c Release 1 s’ouvre. (Plusieurs minutes peuvent être nécessaires au démarrage du programme d’installation.)

2. Dans la page **Select Installation Option** (Sélectionner l’option d’installation), sélectionnez **Install and Configure Oracle Grid Infrastructure for a Standalone Server** (Installer et configurer Oracle Grid Infrastructure pour un serveur autonome).

  ![Capture d’écran de la page Select Installation Option (Sélectionner l’option d’installation)](./media/asm-configuration/install01.png)

3. Dans la page **Select Product Languages** (Sélectionner les langues du produit), sélectionnez **English** (Anglais) ou la langue que vous souhaitez.

  ![Capture d’écran de la page Select Product Languages (Sélectionner les langues du produit)](./media/asm-configuration/install02.png)

4. Dans la page **Create ASM Disk Group** (Créer un groupe de disques ASM) :
  1.  Entrez un nom pour le groupe de disques.
  2.  Sous **Redundancy** (Redondance), sélectionnez **External** (Externe).
  3.  Sous **Allocation Unit Size** (Taille d’unité d’allocation), sélectionnez **4**.
  4.  Sous **Add Disks** (Ajouter des disques), sélectionnez **ORCLASMSP**.

  ![Capture d’écran de la page Create ASM Disk Group (Créer un groupe de disques ASM) du programme d’installation](./media/asm-configuration/install03.png)

5. Dans la page **Specify ASM Password** (Spécifier le mot de passe ASM), sélectionnez l’option **Use same passwords for these accounts** (Utiliser les mêmes mots de passe pour ces comptes) et entrez un mot de passe.

  ![Capture d’écran de la page Specify ASM Password (Spécifier le mot de passe ASM) du programme d’installation](./media/asm-configuration/install04.png)

6. (Facultatif) Dans la page **Specify Management Options** (Spécifier les options de gestion), sélectionnez **Register with Enterprise Manager (EM) Cloud Control** (S’inscrire auprès d’ Enterprise Manager (EM) Cloud Control).

  ![Capture d’écran de la page Specify Management Options (Spécifier les options de gestion) du programme d’installation](./media/asm-configuration/install05.png)

7. Dans la page **Privileged Operating System Groups** (Groupes privilégiés du système d’exploitation), utilisez les paramètres par défaut.

  ![Capture d’écran Privileged Operating System Groups (Groupes privilégiés du système d’exploitation) du programme d’installation](./media/asm-configuration/install06.png)

8. Dans la page **Specify Installation Location** (Spécifier l’emplacement d’installation), utilisez les paramètres par défaut.

  ![Capture d’écran de la page Specify Installation Location (Spécifier l’emplacement d’installation) du programme d’installation](./media/asm-configuration/install07.png)

9. Dans la page **Create Inventory** (Créer un inventaire), entrez ou accédez à l’emplacement du dossier.

  ![Capture d’écran de la page Create Inventory (Créer un inventaire) du programme d’installation](./media/asm-configuration/install08.png)

10. Dans la page **Root script execution configuration** (Configuration de l’exécution des script root), sélectionnez **Automatically run configuration scripts** (Exécuter automatiquement les scipts de configuration). Ensuite, sélectionnez l’option **Use "root" user credential** (Utiliser les informations d’identification de l’utilisateur « root »), puis entrez le mot de passe de l’utilisateur root.

  ![Capture d’écran de la page Use "root" user credential (Utiliser les informations d’identification de l’utilisateur « root ») du programme d’installation](./media/asm-configuration/install09.png)

11. Dans la page **Perform Prerequisite Checks** (Effectuer les vérifications préalables), sélectionnez **Fix & Check Again** (Corriger et revérifier).

  ![Capture d’écran de la page Perform Prerequisite Checks (Effectuer les vérifications préalables) du programme d’installation](./media/asm-configuration/install10.png)

12. Dans la page **Fixup Script** (Script de réparation), sélectionnez **OK**.

  ![Capture d’écran de la page Fixup Script (Script de réparation) du programme d’installation](./media/asm-configuration/install11.png)

13. Dans la page **Summary** (Récapitulatif), passez en revue vos sélections pour les paramètres puis sélectionnez **Install** (Installer).

  ![Capture d’écran de la page Summary (Récapitulatif) du programme d’installation](./media/asm-configuration/install12.png)

14. Une boîte de dialogue d’avertissement apparaît. Cliquez sur **Yes** (Oui) pour continuer.

  ![Capture d’écran de la boîte de dialogue d’avertissement](./media/asm-configuration/install14.png)

15. Dans la page **Finish** (Terminer), sélectionnez **Close** (Fermer) pour terminer l’installation.

  ![Capture d’écran de la page Finish (Terminer) du programme d’installation](./media/asm-configuration/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>Configurer votre installation Oracle ASM

Pour configurer votre installation Oracle ASM, procédez comme suit :

1.  Connectez-vous en tant que grid, à partir de votre session X11 :

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  L’Assistant de configuration d’Oracle ASM s’ouvre.

2.  Dans la page **Configure ASM: Disk Groups** (Configurer ASM : Groupes de disques), sélectionnez le bouton **Create** (Créer) puis sélectionnez **Show Advanced Options** (Afficher les options avancées).

  ![Capture d’écran de la page Configure ASM: Disk Groups (Configurer ASM : Groupes de disques)](./media/asm-configuration/asm01.png)

3.  Dans la page **Create Disk Group** (Créer un groupe de disques) :

  1.  Entrez le nom du groupe de disques.
  2.  Sous **Select Member Disks** (Sélectionner les disques membres), sélectionnez **ORCL_DATA** et **ORCL_DATA1**.
  3.  Sous **Allocation Unit Size** (Taille d’unité d’allocation), sélectionnez **4**. 

  ![Capture d’écran de la page Create Disk Group (Créer un groupe de disques)](./media/asm-configuration/asm02.png)

4.  Dans la page **Configure ASM: Disk Groups** (Configurer ASM : Groupes de disques), sélectionnez le bouton **Create** (Créer) puis sélectionnez **Show Advanced Options** (Afficher les options avancées).

  ![Capture d’écran de la page Configure ASM: Disk Groups (Configurer ASM : Groupes de disques)](./media/asm-configuration/asm03.png)

5.  Dans la page **Create Disk Group** (Créer un groupe de disques) :

  1.  Entrez le nom du groupe de disques.
  2.  Sous **Redundancy** (Redondance), sélectionnez **External** (Externe).
  3.  Sous **Select Member Disks** (Sélectionner les disques membres), sélectionnez **ORCL_FRA**.
  4.  Sous **Allocation Unit Size** (Taille d’unité d’allocation), sélectionnez **4**.

  ![Capture d’écran de la page Create Disk Group (Créer un groupe de disques)](./media/asm-configuration/asm04.png)

6.  Sélectionnez **Exit** (Quitter) pour fermer l’Assistant de configuration d’ASM.

  ![Capture d’écran de la page Configure ASM: Disk Groups (Configurer ASM : Groupes de disques) avec le bouton Exit (Quitter)](./media/asm-configuration/asm05.png)

## <a name="create-the-database"></a>Création de la base de données

Le logiciel Oracle est déjà installé sur l’image de la Place de marché Azure. Pour installer la base de données, procédez comme suit :

1.  Changez l’utilisateur et passez au superutilisateur d’Oracle, puis initialisez l’écouteur pour la journalisation :

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   L’Assistant de configuration de base de données s’ouvre.

2.  Dans la page **Database Operation** (Opération de base de données), sélectionnez **Create Database** (Créer une base de données).

  ![Capture d’écran de la page Database Operation (Opération de base de données)](./media/asm-configuration/createdb01.png)
  
3. Dans la page **Creation Mode** (Mode de création ) :

    1.  Entrez un nom pour la base de données. 
    2.  Pour **Storage Type** (Type de stockage), sélectionnez **Automatic Storage Management (ASM)**.
    3.  Pour **Type de stockage** (Emplacement des fichiers de base de données), accédez au dossier que vous voulez utiliser.
    4.  Pour **Fast Recovery Area** (Zone de récupération rapide), accédez au dossier que vous voulez utiliser.

  ![Capture d’écran de la page Creation Mode (Mode de création)](./media/asm-configuration/createdb02.png)

4.  Dans la page **Summary** (Récapitulatif), passez en revue vos sélections pour les paramètres puis sélectionnez **Finish** (Terminer) pour créer la base de données.

  ![Capture d’écran de la page Summary (Récapitulatif)](./media/asm-configuration/createdb03.png)

5.   (Facultatif) Dans la page **Finish** (Terminer), pour changer les mots de passe, sélectionnez **Password Management** (Gestion des mots de passe).

  ![Capture d’écran de la page Finish (Terminer)](./media/asm-configuration/createdb04.png)


## <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Quand vous n’avez plus besoin de la machine virtuelle, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel : Créer des machines virtuelles à haute disponibilité](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](../../linux/cli-samples.md)

