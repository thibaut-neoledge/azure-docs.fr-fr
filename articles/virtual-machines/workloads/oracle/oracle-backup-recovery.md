---
title: "Sauvegarder et récupérer une base de données de la base de données Oracle Database 12c sur une machine virtuelle Linux Azure | Microsoft Docs"
description: "Découvrez comment sauvegarder et récupérer une base de données Oracle Database 12c dans votre environnement Azure."
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
ms.date: 5/17/2017
ms.author: rclaus
ms.openlocfilehash: 9a2293f13b90e9a4cb11b4169fad969dd622a9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Sauvegarder et récupérer une base de données de la base de données Oracle Database 12c sur une machine virtuelle Linux Azure

Vous pouvez utiliser Azure CLI pour créer et gérer des ressources Azure à partir d’une invite de commandes, ou utiliser des scripts. Dans cet article, nous utilisons des scripts Azure CLI pour déployer une base de données Oracle Database 12c à partir d’une image de la galerie de la Place de marché Microsoft Azure.

Avant de commencer, vérifiez qu’Azure CLI est installé. Pour plus d’informations, consultez le [Guide d’installation d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Préparer l’environnement

### <a name="step-1-prerequisites"></a>Étape 1 : Conditions préalables

*   Pour effectuer le processus de sauvegarde et de récupération, vous devez créer une machine virtuelle Linux avec une instance installée d’Oracle Database 12c. L’image de la Place de marché que vous utilisez pour créer la machine virtuelle se nomme *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Pour savoir comment créer une base de données Oracle, consultez le [Guide de création rapide d’une base de données Oracle](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>Étape 2 : connexion à la machine virtuelle

*   Pour créer une session Secure Shell (SSH) avec la machine virtuelle, utilisez la commande suivante. Remplacez la combinaison d’adresse IP et de nom d’hôte par la valeur `publicIpAddress` pour votre machine virtuelle.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>Étape 3 : préparation des données

1.  Cette étape suppose que vous disposiez d’une instance Oracle (cdb1) s’exécutant sur une machine virtuelle nommée *myVM*.

    Exécutez la racine de super utilisateur *oracle*, puis initialisez l’écouteur :

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  (Facultatif) Assurez-vous que la base de données est en mode journal d’archivage :

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  (Facultatif) Créez une table pour tester la validation :

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  Vérifiez ou modifiez l’emplacement et la taille du fichier de sauvegarde :

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Utilisez Oracle Recovery Manager (RMAN) pour sauvegarder la base de données :

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>Étape 4 : Sauvegarde cohérente avec les applications pour les machines virtuelles Linux

La sauvegarde cohérente avec les applications est une nouvelle fonctionnalité de Sauvegarde Azure. Vous pouvez créer et sélectionner des scripts à exécuter avant et après l’instantané de la machine virtuelle (pré-instantané et post-instantané).

1. Téléchargez le fichier JSON.

    Téléchargez VMSnapshotScriptPluginConfig.json à partir de https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. Le fichier doit comporter un contenu du type suivant :

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Créez le dossier /etc/azure sur la machine virtuelle :

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Copiez le fichier JSON.

    Copiez le fichier VMSnapshotScriptPluginConfig.json dans le dossier /etc/azure.

4. Modifiez le fichier JSON.

    Modifiez le fichier VMSnapshotScriptPluginConfig.json pour inclure les paramètres `PreScriptLocation` et `PostScriptlocation`. Par exemple :

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Créez les fichiers de script de pré-instantané et post-instantané.

    Voici un exemple de script de pré-instantané et de post-instantané pour une « sauvegarde à froid » (sauvegarde hors connexion, avec arrêt et redémarrage) :

    Pour /etc/azure/pre_script.sh :

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Pour /etc/azure/post_script.sh :

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Voici un exemple de script de pré-instantané et de post-instantané pour une « sauvegarde à chaud » (sauvegarde en ligne) :

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Pour /etc/azure/post_script.sh :

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Pour /etc/azure/pre_script.sql, modifiez le contenu du fichier selon vos besoins :

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Pour /etc/azure/post_script.sql, modifiez le contenu du fichier selon vos besoins :

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Modifiez les autorisations des fichiers :

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Testez les scripts.

    Pour tester les scripts, connectez-vous en tant qu’utilisateur racine. Assurez-vous ensuite qu’il n’y a aucune erreur :

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

Pour plus d’informations, consultez [Sauvegarde cohérente avec les applications pour les machines virtuelles Linux](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>Étape 5 : utilisation d’archivages Azure Recovery Services pour sauvegarder la machine virtuelle

1.  Dans le portail Azure, recherchez des **coffres Recovery Services**.

    ![Page Coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Dans le panneau **Coffres Recovery Services**, cliquez sur **Ajouter** pour ajouter un coffre.

    ![Page d’ajout de coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_02.png)

3.  Pour continuer, cliquez sur **myVault**.

    ![Page de détails de coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Dans le panneau **myVault**, cliquez sur **Sauvegarde**.

    ![Page de sauvegarde des coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Dans le panneau **Objectif de sauvegarde**, utilisez les valeurs par défaut **Azure** et **Machine virtuelle**. Cliquez sur **OK**.

    ![Page de détails de coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_05.png)

6.  Pour **Stratégie de sauvegarde**, utilisez **DefaultPolicy** ou sélectionnez **Créer une stratégie**. Cliquez sur **OK**.

    ![Page de détails de la stratégie de sauvegarde des coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Dans le panneau **Sélectionner les machines virtuelles**, cochez la case **myVM1**, puis cliquez sur **OK**. Cliquez sur le bouton **Activer la sauvegarde**.

    ![Page de détails des éléments à sauvegarder dans les coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Après que vous avez cliqué sur **Activer la sauvegarde**, le processus de sauvegarde ne démarre pas avant l’expiration de l’heure planifiée. Pour configurer une sauvegarde immédiate, procédez comme suit.

8.  Dans le panneau **myVault - Éléments de sauvegarde**, sous **NOMBRE D’ÉLÉMENTS DE SAUVEGARDE**, sélectionnez le nombre d’éléments de sauvegarde.

    ![Page de détails myVault d’Archivages de Recovery Services](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Sur le côté droit du panneau **Éléments de sauvegarde (Machine virtuelle Azure)**, cliquez sur le bouton de sélection (**…**), puis sur **Sauvegarder maintenant**.

    ![Commande Sauvegarder maintenant des coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_09.png)

10. Cliquez sur le bouton **Sauvegarder**. Attendez que le processus de sauvegarde se termine. Puis allez à l’[étape 6 : suppression des fichiers de base de données](#step-6-remove-the-database-files).

    Pour afficher l’état du travail de sauvegarde, cliquez sur **Travaux**.

    ![Page de travail des coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_10.png)

    L’état du travail de sauvegarde s’affiche dans l’image suivante :

    ![Page de travail d’Archivages de Recovery Services avec état](./media/oracle-backup-recovery/recovery_service_11.png)

11. Pour une sauvegarde cohérente avec les applications, résolvez les éventuelles erreurs présentes dans le fichier journal. Le fichier journal se trouve sous /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>Étape 6 : suppression des fichiers de base de données 
Plus loin dans cet article, vous allez apprendre à tester le processus de récupération. Avant de tester le processus de récupération, vous devez supprimer les fichiers de base de données.

1.  Supprimez les fichiers d’espace de table et de sauvegarde :

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Facultatif) Arrêtez l’instance Oracle :

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Restaurer les fichiers supprimés à partir des coffres Recovery Services
Pour restaurer les fichiers supprimés, procédez comme suit :

1. Dans le portail Azure, recherchez l’élément *myVault* dans les coffres Recovery Services. Dans le panneau **Vue d’ensemble**, sous **Éléments de sauvegarde**, sélectionnez le nombre d’éléments.

    ![Éléments de sauvegarde myVault des coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_12.png)

2. Sous **NOMBRE D’ÉLÉMENTS DE SAUVEGARDE**, sélectionnez le nombre d’éléments.

    ![Nombre d’éléments de sauvegarde de machine virtuelle Azure des coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_13.png)

3. Dans le panneau **myvm1**, cliquez sur **Récupération de fichier (aperçu)**.

    ![Capture d’écran de la page de récupération de fichiers des coffres Recovery Services](./media/oracle-backup-recovery/recovery_service_14.png)

4. Dans le volet **Récupération de fichier (aperçu)**, cliquez sur **Télécharger le script**. Ensuite, enregistrez le fichier de téléchargement (.sh) dans un dossier sur l’ordinateur client.

    ![Options d’enregistrement de fichier de script téléchargé](./media/oracle-backup-recovery/recovery_service_15.png)

5. Copiez le fichier .sh vers la machine virtuelle.

    L’exemple suivant montre comment utiliser une commande de copie sécurisée (scp) pour déplacer le fichier vers la machine virtuelle. Vous pouvez également copier le contenu vers le Presse-papiers, puis le coller dans un nouveau fichier configuré sur la machine virtuelle.

    > [!IMPORTANT]
    > Dans l’exemple suivant, veillez à mettre à jour les valeurs d’adresse IP et de dossier. Les valeurs doivent mapper au dossier d’enregistrement du fichier.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Modifiez le fichier afin qu’il appartienne à la racine.

    Dans l’exemple suivant, modifiez le fichier afin qu’il appartient à la racine. Modifiez ensuite les autorisations.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    L’exemple suivant montre ce que vous devriez voir après avoir exécuté le script précédent. Lorsque vous êtes invité à continuer, entrez **Y**.

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. L’accès aux volumes montés est confirmé.

    Pour quitter, entrez **q**, puis recherchez les volumes montés. Pour créer la liste des volumes ajoutés, à l’invite de commandes, entrez **df -k**.

    ![Commande df-k](./media/oracle-backup-recovery/recovery_service_16.png)

8. Utilisez le script suivant pour copier de nouveau les fichiers manquants dans les dossiers :

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. Dans le script suivant, utilisez RMAN pour récupérer la base de données :

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. Démontez le disque.

    Dans le panneau **Récupération de fichier (aperçu)** du portail Azure, cliquez sur **Démonter les disques**.

    ![Commande Démonter les disques](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Restaurer la machine virtuelle entière

Au lieu de restaurer les fichiers supprimés des coffres Recovery Services, vous pouvez restaurer la machine virtuelle dans son intégralité.

### <a name="step-1-delete-myvm"></a>Étape 1 : Supprimer myVM

*   Dans le portail Azure, accédez au coffre **myVM1**, puis sélectionnez **Supprimer**.

    ![Commande Supprimer du coffre](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>Étape 2 : restauration de la machine virtuelle

1.  Accédez à **Archivages de Recovery Services**, puis sélectionnez **myVault**.

    ![Entrée myVault](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Dans le panneau **Vue d’ensemble**, sous **Éléments de sauvegarde**, sélectionnez le nombre d’éléments.

    ![Éléments de sauvegarde myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Dans le panneau **Éléments de sauvegarde (Machine virtuelle Azure)**, sélectionnez **myvm1**.

    ![Page de récupération de machine virtuelle](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Dans le panneau **myvm1**, cliquez sur le bouton de sélection (**…**), puis sur **Restaurer la machine virtuelle**.

    ![Commande Restaurer la machine virtuelle](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Dans le panneau **Sélectionner un point de restauration**, sélectionnez l’élément à restaurer, puis cliquez sur **OK**.

    ![Sélectionner le point de restauration](./media/oracle-backup-recovery/recover_vm_06.png)

    Si vous avez activé la sauvegarde cohérente avec les applications, une barre bleue verticale apparaît.

6.  Dans le panneau **Restaurer la configuration**, sélectionnez le nom de la machine virtuelle ainsi que le groupe de ressources, puis cliquez sur **OK**.

    ![Restaurer les valeurs de configuration](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Pour restaurer la machine virtuelle, cliquez sur le bouton **Restaurer**.

8.  Pour afficher l’état du processus de restauration, cliquez sur **Travaux**, puis cliquez sur **Travaux de sauvegarde**.

    ![Commande d’état des travaux de sauvegarde](./media/oracle-backup-recovery/recover_vm_08.png)

    L’image suivante montre l’état du processus de restauration :

    ![État du processus de restauration](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>Étape 3 : création d’une adresse IP publique
Une fois la machine virtuelle restaurée, définissez l’adresse IP publique.

1.  Dans la zone de recherche, tapez **adresse IP publique**.

    ![Liste d’adresses IP publiques](./media/oracle-backup-recovery/create_ip_00.png)

2.  Dans le panneau **Adresses IP publiques**, cliquez sur **Ajouter**. Dans le panneau **Créer une adresse IP publique**, dans le champ **Nom**, sélectionnez le nom de l’adresse IP publique. Pour **Groupe de ressources**, sélectionnez **Use existing** (Utiliser existant). Cliquez sur **Créer**.

    ![Créer une adresse IP](./media/oracle-backup-recovery/create_ip_01.png)

3.  Pour associer l’adresse IP publique à l’interface réseau de la machine virtuelle, recherchez et sélectionnez **myVMip**. Cliquez ensuite sur **Associer**.

    ![Associer une adresse IP](./media/oracle-backup-recovery/create_ip_02.png)

4.  Pour **Type de ressource**, sélectionnez **Interface réseau**. Sélectionnez la carte réseau utilisée par l’instance myVM, puis cliquez sur **OK**.

    ![Sélectionner le type de ressource et les valeurs de carte réseau](./media/oracle-backup-recovery/create_ip_03.png)

5.  Recherchez et ouvrez l’instance de myVM qui est déplacée à partir du portail. L’adresse IP associée à la machine virtuelle s’affiche dans le panneau **Vue d’ensemble** de myVM.

    ![Valeur d’adresse IP](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>Étape 4 : connexion à la machine virtuelle

*   Pour vous connecter à la machine virtuelle, utilisez le script suivant :

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>Étape 5 : vérification de l’accessibilité de la base de données
*   Pour tester l’accessibilité, utilisez le script suivant :

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Si la commande **startup** de la base de données génère une erreur, pour récupérer la base de données, consultez [Étape 6 : Utiliser RMAN pour récupérer la base de données](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>Étape 6 : (Facultatif) Utiliser RMAN pour récupérer la base de données
*   Pour récupérer la base de données, utilisez le script suivant :

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

La sauvegarde et la récupération de la base de données Oracle Database 12c sur une machine virtuelle Linux Azure est maintenant terminée.

## <a name="delete-the-vm"></a>Supprimer la machine virtuelle

Quand vous n’avez plus besoin de la machine virtuelle, vous pouvez utiliser la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées :

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel : créer des machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Explorer des exemples Azure CLI de déploiement de machines virtuelles](../../linux/cli-samples.md)



