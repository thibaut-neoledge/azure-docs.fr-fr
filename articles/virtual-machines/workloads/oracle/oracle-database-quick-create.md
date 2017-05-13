---
title: "Créer une base de données Oracle 12c sur une machine virtuelle Azure | Microsoft Docs"
description: "Configurez et exécutez rapidement une base de données Oracle 12C dans votre environnement Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/26/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: ba55e2e4449737c4b759211cf0c429d42b961a38
ms.contentlocale: fr-fr
ms.lasthandoff: 04/29/2017

---

# <a name="create-an-oracle-12c-database-on-azure-vm"></a>Créer une base de données Oracle 12c sur une machine virtuelle Azure

Ce script crée une base de données Oracle 12c avec l’interface de ligne de commande Azure.

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide explique comment utiliser l’interface de ligne de commande Azure pour déployer une base de données Oracle 12c depuis une image de la galerie du Marketplace.

Avant de commencer, assurez-vous que l’interface de ligne de commande Azure est installée. Pour plus d’informations, consultez le [Guide d’installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westus`.

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-virtual-machine"></a>Create virtual machine

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). 

L’exemple suivant crée une machine virtuelle nommée `myVM` et crée des clés SSH si elles n’existent pas déjà dans un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

Lorsque la machine virtuelle a été créée, l’interface de ligne de commande Azure affiche des informations similaires à l’exemple suivant : prenez note de `publicIpAddress`. Cette adresse est utilisée pour accéder à la machine virtuelle.

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

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle. Remplacez l’adresse IP par la valeur `publicIpAddress` de votre machine virtuelle.

```bash 
ssh <publicIpAddress>
```

## <a name="create-database"></a>Créer une base de données

Le logiciel Oracle est déjà installé sur l’image Marketplace, l’étape suivante consiste donc à installer la base de données. La première étape s’exécute en tant que superutilisateur « oracle » et initialise l’écouteur pour la journalisation :

```bash
sudo su - oracle
[oracle@myVM /]$ lsnrctl start
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

L’étape suivante consiste à créer la base de données :

```bash
[oracle@myVM /]$ dbca -silent \
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

## <a name="preparing-for-connectivity"></a>Préparation pour la connectivité 
Pour vérifier que la base de données a été initialisée correctement, nous allons tester la connectivité locale. Le moyen le plus simple consiste à se connecter avec `sqlplus`.  Avant de se connecter, nous devons définir certaines variables d’environnement, en particulier les variables d’environnement *ORACLE_HOME* et *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

Si vous le souhaitez, vous pouvez ajouter ORACLE_HOME et ORACLE_SID au fichier .bashrc, de façon à ce que ces paramètres soient enregistrés pour des connexions futures.

```
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# add oracle sid
export ORACLE_SID=cdb1

```

## <a name="setup-connectivity-to-oracle-em-express"></a>Configurer la connectivité à Oracle EM Express

Nous allons activer Oracle EM Express pour disposer d’un outil de gestion graphique permettant d’explorer la base de données.  Pour vous connecter à Oracle EM Express, le port doit tout d’abord être configuré dans Oracle.

```bash
$ sudo su - oracle

sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> select con_id, name, open_mode from v$pdbs;

    CON_ID NAME                           OPEN_MODE
---------- ------------------------------ ----------
         2 PDB$SEED                       READ ONLY
         3 PDB1                           MOUNT

SQL> alter session set container=pdb1;

Session altered.

SQL> alter database open;

database opened.

SQL> alter session set container=pdb1;

Session altered.

SQL> exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);

PL/SQL procedure successfully completed.
```
## <a name="automating-database-startup-and-shutdown"></a>Automatisation du démarrage et de l’arrêt de la base de données

Une fois que l’instance Oracle est créée, elle n’est pas configurée pour démarrer automatiquement au démarrage de l’ordinateur.  Pour accomplir ces tâches, vous devez vous connecter en tant que root et créer/mettre à jour certains fichiers système.

```bash
# sudo su -
```

Mettez à jour le fichier « /etc/oratab » de la valeur par défaut « N » vers « Y ».

```
cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
```

Ensuite, créez le fichier « /etc/init.d/dbora ».

```bash
#!/bin/sh
# chkconfig: 345 99 10
# description: Oracle auto start-stop script.
#
# Set ORA_HOME to be equivalent to the $ORACLE_HOME
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle

case "$1" in
'start')
    # Start the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    # Remove "&" if you don't want startup as a background process.
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
    touch /var/lock/subsys/dbora
    ;;

'stop')
    # Stop the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
    rm -f /var/lock/subsys/dbora
    ;;
esac
```

Modifier les autorisations

```bash
# chgrp dba /etc/init.d/dbora
# chmod 750 /etc/init.d/dbora
```
Créez des liens symboliques pour le démarrage et l’arrêt.

```bash
# ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
```

Redémarrez la machine virtuelle à tester.
```bash
# reboot
```

## <a name="opening-the-ports-for-connectivity"></a>Ouverture des ports pour la connectivité

La dernière étape consiste à configurer quelques points de terminaison externes. Quittez votre session SSH sur la machine virtuelle pour pouvoir configurer l’instance d’Azure Network Security Group qui protège la machine virtuelle. Pour ouvrir le point de terminaison afin d’accéder à distance à la base de données Oracle, vous devez exécuter la commande suivante. 

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Le résultat doit être semblable à la réponse suivante :

```
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

Pour ouvrir le point de terminaison afin d’accéder à distance à Oracle EM Express , vous devez exécuter la commande suivante.

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle-EM\
    --protocol tcp --direction inbound --priority 1001 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 5502 --access allow
```

Le résultat doit être semblable à la réponse suivante :

```azurecli
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "5502",
  "direction": "Inbound",
  "etag": "W/\"06c68b5e-1b3f-4ae0-bcf6-59b3b981d685\"",
  "id": "/subscriptions/2dad32d6-b188-49e6-9437-ca1d51cec4dd/resourceGroups/kennyRG/providers/Microsoft.Network/networkSecurityGroups/kennyVM1NSG/securityRules/allow-oracle-EM",
  "name": "allow-oracle-EM",
  "priority": 1001,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Se connecter à EM Express à partir de votre navigateur
```
https://<VM hostname>:5502/em
```
Vous pouvez vous connecter à l’aide du compte SYS et du mot de passe que vous avez spécifié lors de l’installation


## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, la commande suivante permet de supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel de création de machines virtuelles hautement disponibles](../../linux/create-cli-complete.md)

[Découvrir des exemples de commande de déploiement de machine virtuelle](../../linux/cli-samples.md)

