---
title: "Implémenter Oracle Data Guard sur une machine virtuelle Linux Azure | Documents Microsoft"
description: "Configurez et exécutez rapidement Oracle Data Guard dans votre environnement Azure."
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
ms.date: 05/10/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: e87485fc2e5adce1ed8d85dc424ef95102c849c3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/16/2017

---

# <a name="implement-oracle-data-guard-on-azure-linux-vm"></a>Implémenter Oracle Data Guard sur une machine virtuelle Linux Azure 

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide explique comment utiliser l’interface de ligne de commande Azure pour déployer une base de données Oracle 12c depuis une image de la galerie du Marketplace. Une fois la base de données Oracle créée, ce document montre étape par étape comment installer et configurer Data Guard sur une machine virtuelle Azure.

Avant de commencer, assurez-vous que l’interface de ligne de commande Azure est installée. Pour plus d’informations, consultez le [Guide d’installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>Préparer l’environnement
### <a name="assumptions"></a>Hypothèses

Pour installer Oracle Data Guard, vous devez créer deux machines virtuelles Azure sur le même groupe à haute disponibilité. L’image Place de marché que vous utilisez pour créer la machine virtuelle est « Oracle:Oracle-Database-Ee:12.1.0.2:latest ».

La machine virtuelle principale (myVM1) a une instance Oracle active.

Le machine virtuelle de secours (myVM2) dispose uniquement du logiciel Oracle installé.

### <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-availability-set"></a>Créer un groupe à haute disponibilité

Cette étape est facultative mais recommandée. Pour plus d’informations, voir [Instructions pour les groupes à haute disponibilité Azure pour les machines virtuelles Windows](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-virtual-machine"></a>Create virtual machine

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). 

L’exemple ci-après crée deux machines virtuelles nommées `myVM1` et `myVM2`. Crée les clés SSH si elles n’existent pas déjà dans un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.

Créer myVM1 (machine virtuelle principale)
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Une fois la machine virtuelle créée, Azure CLI affiche des informations similaires à l’exemple suivant : prenez note de l’adresse IP publique `publicIpAddress`. Cette adresse est utilisée pour accéder à la machine virtuelle.

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

Créer myVM2 (machine virtuelle secours)
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Prenez note également de l’adresse IP publique `publicIpAddress` une fois celle-ci créée.

### <a name="open-the-tcp-port-for-connectivity"></a>Ouvrir le port TCP pour la connectivité

L’étape consiste à configurer des points de terminaison externes qui permettent l’accès à distance à la base de données Oracle. Pour ce faire, exécutez la commande suivante.

Ouvrir un port pour myVM1

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmN1SG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Le résultat doit être semblable à la réponse suivante :

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Ouvrir un port pour myVM2

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2N1SG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle. Remplacez l’adresse IP par la valeur `publicIpAddress` de votre machine virtuelle.

```bash 
ssh azureuser@<publicIpAddress>
```

### <a name="create-database-on-myvm1-primary"></a>Créer une base de données sur myVM1 (machine virtuelle principale)

Le logiciel Oracle est déjà installé sur l’image Place de marché, l’étape suivante consiste donc à installer la base de données. La première étape s’exécute en tant que superutilisateur « oracle ».

```bash
sudo su - oracle
```

Créer la base de données :

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Les résultats doivent ressembler à la réponse suivante :

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

Définir les variables ORACLE_SID et ORACLE_HOME

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Si vous le souhaitez, vous pouvez ajouter ORACLE_HOME et ORACLE_SID au fichier .bashrc, de façon à ce que ces paramètres soient enregistrés pour des connexions futures.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="data-guard-configurations"></a>Configurations de Data Guard

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Activer le mode de journalisation Archive sur myVM1 (machine virtuelle principale)

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
```
Activez la journalisation forcée et assurez-vous qu’au moins un fichier journal est présent.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Créez des journaux de rétablissement de secours.

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Activez Flashback (qui effectuait la récupération beaucoup plus tôt) et définissez STANDBY_FILE_MANAGEMENT sur auto.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
```

### <a name="service-setup-on-myvm1-primary"></a>Configuration du service sur myVM1 (machine virtuelle principale)

Modifiez ou créez le fichier tnsnames.ora qui se trouve dans le dossier $ORACLE_HOME\network\admin.

Ajoutez les entrées suivantes.

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Modifiez ou créez le fichier listener.ora qui se trouve dans le dossier $ORACLE_HOME\network\admin.

Ajoutez les entrées suivantes.

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Démarrez l’écouteur.

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="service-setup-on-myvm2-standby"></a>Configuration du service sur myVM2 (machine virtuelle de secours)

Modifiez ou créez le fichier tnsnames.ora qui se trouve dans le dossier $ORACLE_HOME\network\admin.

Ajoutez les entrées suivantes.

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Modifiez ou créez le fichier listener.ora qui se trouve dans le dossier $ORACLE_HOME\network\admin.

Ajoutez les entrées suivantes.

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Démarrez l’écouteur.

```bash
$ lsnrctl stop
$ lsnrctl start
```

Activez Data Guard Broker.
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="restore-database-to-myvm2-standby"></a>Restaurer la base de données sur myVM2 (machine virtuelle de secours)

Créez un fichier de paramètres « /tmp/initcdb1_stby.ora » dont le contenu est le suivant.
```bash
*.db_name='cdb1'
```

Créez les dossiers.

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Créez le fichier de mot de passe.

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0.2/db_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```
Démarrez la base de données sur myVM2.

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Restaurez la base de données avec l’utilitaire RMAN.

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Exécutez les commandes suivantes dans RMAN.
```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

Activez Data Guard Broker.
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Configurer Data Guard Broker sur myVM1 (machine virtuelle principale)

Démarrez le gestionnaire Data Guard et connectez-vous à l’aide de SYS et d’un mot de passe (sans utiliser d’authentification du système d’exploitation). Procédez comme suit :

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Examinez la configuration.
```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Cela achève la configuration d’Oracle Data Guard. La section suivante montre comment tester la connectivité et basculement.

### <a name="connect-database-from-client-machine"></a>Se connecter à la base de données à partir d’un ordinateur client

Mettez à jour ou créez le fichier tnsnames.ora sur votre ordinateur client qui est généralement situé dans $ORACLE_HOME\network\admin.

Remplacez l’adresse IP avec votre adresse IP publique `publicIpAddress` pour myVM1 et myVM2.

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Démarrez sqlplus.

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```
## <a name="test-data-guard-configuration"></a>Tester la configuration de Data Guard

### <a name="database-switchover-on-myvm1-primary"></a>Basculement de base de données vers myVM1 (machine virtuelle principale)

Pour basculer de la base de données primaire vers la base de données de secours (de cdb1 à cdb1_stby)

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

Vous devriez à présent être en mesure de vous connecter à la base de données de secours.

Démarrez sqlplus.

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="database-switch-back-on-myvm2-standby"></a>Re-basculement de base de données vers myVM2 (machine virtuelle de secours)

Pour re-basculer, exécutez ce qui suit sur myVM2.
```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Une fois encore, vous devriez à présent pouvoir vous connecter à la base de données primaire.

Démarrez sqlplus.

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Cela achève l’installation et la configuration de Data Guard sur Oracle linux.


## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, la commande suivante permet de supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel de création de machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Découvrir des exemples de commande de déploiement de machine virtuelle](../../linux/cli-samples.md)

