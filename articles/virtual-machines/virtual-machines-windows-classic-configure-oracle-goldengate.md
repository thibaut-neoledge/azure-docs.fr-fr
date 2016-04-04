<properties
	pageTitle="Configuration d’Oracle GoldenGate dans des machines virtuelles | Microsoft Azure"
	description="Suivez un didacticiel vous permettant de configurer et mettre en œuvre Oracle GoldenGate sur des machines virtuelles Azure Windows Server pour assurer une haute disponibilité et une récupération d’urgence."
	services="virtual-machines-windows"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />


#Configuration de GoldenGate Oracle pour Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Ce didacticiel décrit comment configurer et mettre en œuvre Oracle GoldenGate dans un environnement Azure Virtual Machines pour disposer d’une haute disponibilité et d’une récupération d’urgence. Il se concentre sur la [réplication bidirectionnelle](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) pour les bases de données Oracle non-RAC et requiert que les deux sites soient actifs.

Oracle GoldenGate prend en charge la distribution et l’intégration des données. Il permet de configurer une solution de distribution et de synchronisation des données via la configuration de réplication Oracle à Oracle, et offre une solution flexible de haute disponibilité. Oracle GoldenGate complète Oracle Data Guard avec ses fonctionnalités de réplication pour permettre la distribution d’informations à l’ensemble de l’entreprise ainsi que des migrations et mises à niveau sans interruption de service. Pour plus d’informations, voir [Using Oracle GoldenGate with Oracle Data Guard](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm) (en anglais).

Oracle GoldenGate présente les principaux composants suivants : Extract, Data pump, Replicat, Trails ou Extract Files, Checkpoints, Manager et Collector. Pour établir une réplication bidirectionnelle entre deux sites, vous devez créer et démarrer tous les composants sur les deux sites. Pour obtenir des informations plus détaillées sur l’architecture d’Oracle GoldenGate, consultez le guide (en anglais) [Oracle GoldenGate Guide](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

Ce didacticiel suppose que vous disposez déjà de connaissances théoriques et pratiques concernant les concepts de haute disponibilité et de récupération d’urgence pour Oracle Database, ainsi que concernant [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Pour plus d’informations, voir le [site web d’Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

En outre, le didacticiel suppose que vous avez déjà mis en œuvre les conditions préalables suivantes :

- Vous avez déjà consulté la section Considérations relatives à la haute disponibilité et à la récupération d’urgence de la rubrique [Images de machine virtuelle Oracle - Considérations diverses](virtual-machines-windows-classic-oracle-considerations.md). Remarquez qu’Azure prend actuellement en charge des instances autonomes d’Oracle Database, mais pas Oracle Real Application Clusters (Oracle RAC).

- Vous avez téléchargé le logiciel Oracle GoldenGate à partir du site web [Téléchargements Oracle](http://www.oracle.com/us/downloads/index.html). Vous avez sélectionné Product Pack Oracle Fusion Middleware – Data Integration. Vous avez ensuite sélectionné Oracle GoldenGate sur Oracle v11.2.1 Media Pack pour Microsoft Windows x64 (64 bits) pour une base de données Oracle 11g. Téléchargez ensuite Oracle GoldenGate v11.2.1.0.3 pour Oracle 11g 64 bits sur Windows 2008 (64 bits).

- Vous avez créé deux machines virtuelles (VM, Virtual Machines) dans Azure à l’aide de l’image Oracle Enterprise Edition fournie par la plate-forme sur Windows Server. Pour plus d’informations, voir [Création d’une machine virtuelle Oracle Database 12c dans Azure](#z3dc8d3c097cf414e9048f7a89c026f80) et [Machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/). Assurez-vous que les machines virtuelles sont présentes au sein du [même service cloud](virtual-machines-linux-load-balance.md) et du même [réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour garantir qu’elles peuvent accéder l’une à l’autre via l’adresse IP privée persistante.

- Vous avez nommé les machines virtuelles « MachineGG1 » pour le site A et « MachineGG2 » pour le site B sur le portail Azure Classic.

- Vous avez créé les bases de données de test « TestGG1 » sur le site A et « TestGG2 » sur le site B.

- Vous vous connectez à votre serveur Windows en tant que membre du groupe Administrateurs ou du groupe **ORA\_DBA**.

Ce didacticiel présente les procédures suivantes :

1. Configurer la base de données sur les sites A et B  

	1. Charger les données initiales

2. Préparer les sites A et B pour la réplication de base de données

3. Créer tous les objets nécessaires pour prendre en charge la réplication DDL

4. Configurer GoldenGate Manager sur les sites A et B

5. Créer un groupe Extract et des processus Data Pump sur les sites A et B

	1. Créer des processus Extract et Data Pump sur le site A

	2. Créer une table de points de contrôle GoldenGate sur le site B

	3. Commande ADD REPLICAT sur le site B

	4. Créer des processus Extract et Data Pump sur le site B

	5. Créer une table de points de contrôle GoldenGate sur le site A

	6. Commande ADD REPLICAT sur le site A

	7. Commande ADD TRANSDATA sur les sites A et B

	8. Démarrer les processus Extract et Data Pump sur le site A

	9. Démarrer les processus Extract et Data Pump sur le site B

	10. Démarrer le processus REPLICAT sur le site A

	11. Démarrer le processus REPLICAT sur le site B

6. Vérifier le processus de réplication bidirectionnelle

>[AZURE.IMPORTANT] Ce didacticiel a été configuré et testé selon la configuration matérielle et logicielle suivante :
>
>| | **Base de données du site A** | **Base de données du site B** |
>|------------------------|----------------------------------|----------------------------------|
>| **Version d’Oracle** | Oracle11g Release 2 – (11.2.0.1) | Oracle11g Release 2 – (11.2.0.1) |
>| **Nom de la machine** | MachineGG1 | MachineGG2 |
>| **Système d’exploitation** | Windows 2008 R2 | Windows 2008 R2 |
>| **SID Oracle** | TESTGG1 | TESTGG2 |
>| **Schéma de réplication** | SCOTT | SCOTT |

Pour les versions ultérieures d’Oracle Database et d’Oracle GoldenGate, il se peut que vous deviez procéder à certaines modifications supplémentaires. Pour obtenir les dernières informations concernant la version, consultez la documentation relative à [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) et [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) sur le site web d’Oracle. Par exemple, pour une base de données source version 11.2.0.4 et versions ultérieures, la capture de DDL est exécutée par le serveur logmining de façon asynchrone et nécessite l’installation de déclencheurs, tables ou autres objets de base de données spéciaux. Les mises à niveau d’Oracle GoldenGate peuvent être effectuées sans arrêter les applications utilisateur. L’utilisation d’un déclencheur DDL et des objets de prise en charge est requise lorsqu’Extract est en mode intégré avec une base de données source Oracle 11g antérieure à la version 11.2.0.4. Pour obtenir des instructions détaillées, voir le guide [Installing and Configuring Oracle GoldenGate for Oracle Database](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf) (en anglais).

##1\. Configurer la base de données sur les sites A et B
Cette section explique comment configurer la base de données sur les sites A et B. Vous devez effectuer toutes les étapes décrites sur les deux sites.

Tout d’abord, connectez-vous à distance aux sites A et B via le portail Azure Classic. Ouvrez une invite de commandes Windows et créez un répertoire de base pour les fichiers de configuration d’Oracle GoldenGate :

	mkdir C:\OracleGG

Ensuite, décompressez et installez le logiciel Oracle GoldenGate dans ce dossier. Après cette étape, vous pouvez démarrer GoldenGate Software Command Interpreter (GGSCI) en exécutant la commande suivante :

	C:\OracleGG\.\ggsci

Vous pouvez utiliser [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) pour exécuter plusieurs commandes permettant de configurer, contrôler et surveiller GoldenGate Oracle.

Exécutez ensuite la commande suivante pour créer tous les sous-dossiers du package d’installation :

	GGSCI (Hostname) 1> CREATE SUBDIRS

Exécutez la commande suivante pour quitter l’invite de commandes de GGSCI :

	GGSCI (Hostname) 1> EXIT

Vous devez ensuite créer un utilisateur de base de données, lequel sera utilisé par les processus Oracle GoldenGate Manager, Extract et Replication. Remarquez que vous pouvez créer des utilisateurs individuels pour chaque processus ou ne configurer qu’un seul utilisateur commun. Dans ce didacticiel, nous allons créer un seul utilisateur, nommé ggate. Nous accorderons ensuite à cet utilisateur les privilèges nécessaires. Remarquez que vous devez effectuer les opérations suivantes sur les sites A et B.

Ouvrez la fenêtre de commande SQL*Plus sur les sites A et B avec des privilèges d’administrateur de base de données à l’aide de **SYSDBA**, comme suit :

	Enter username: / as sysdba

Exécutez ensuite la commande suivante :

	SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata<DBNAME><DBNAME>ggs_data01.dbf' size 200m;
	SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
	      grant connect, resource to ggate;
	      grant select any dictionary, select any table to ggate;
	      grant create table to ggate;
	      grant flashback any table to ggate;
	      grant execute on dbms_flashback to ggate;
	      grant execute on utl_file to ggate;
	      grant create any table to ggate;
	      grant insert any table to ggate;
	      grant update any table to ggate;
	      grant delete any table to ggate;
	      grant drop any table to ggate;

Localisez ensuite le fichier INIT<SID\_de\_la\_base\_de\_données>.ORA dans le dossier %ORACLE\_HOME%\\database sur les sites A et B, puis ajoutez les paramètres de base de données suivants au fichier INITTEST.ora :

	UNDO\_MANAGEMENT=AUTO
	UNDO\_RETENTION=86400

Pour obtenir une liste complète de toutes les commandes Oracle GoldenGate GGSCI, voir [Reference for Oracle GoldenGate for Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm) (en anglais).

### Charger les données initiales

Vous pouvez procéder au chargement initial des données dans la base de données selon plusieurs méthodes. Par exemple, vous pouvez utiliser [Oracle GoldenGate Direct Load](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) ou un utilitaire d’importation et d’exportation classique pour exporter la table de données du site A vers le site B.

Pour illustrer le processus de réplication d’Oracle GoldenGate, ce didacticiel décrit la création d’une table sur les sites A et B à l’aide des commandes suivantes.

Tout d’abord, ouvrez la fenêtre de commande SQL*Plus, puis exécutez la commande suivante pour créer une table d’inventaire sur les bases de données des sites A et B :

	create table scott.inventory
	(prod_id number,
	prod_category varchar2(20),
	qty_in_stock number,
	last_dml timestamp default systimestamp);

Ajoutez ensuite une contrainte à la nouvelle table sur les bases de données des sites A et B.

	alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Accordez ensuite tous les privilèges sur la nouvelle table d’inventaire à l’utilisateur ggate sur les sites A et B :

	grant all on scott.inventory to ggate;

Ensuite, créez et activez un déclencheur de base de données INVENTORY\_CDR\_TRG sur la table pour vous assurer que toutes les transactions vers la nouvelle table sont enregistrées si l’utilisateur ne correspond pas à ggate. Effectuez cette opération sur les sites A et B.

	CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
	BEFORE UPDATE
	ON SCOTT.INVENTORY
	REFERENCING NEW AS New OLD AS Old
	FOR EACH ROW
	BEGIN
	IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
	THEN
	:NEW.LAST_DML := SYSTIMESTAMP;
	END IF;
	END;
	/


##2\. Préparer les sites A et B pour la réplication de base de données
Cette section explique comment préparer les sites A et B à la réplication de base de données. Vous devez effectuer toutes les étapes décrites dans cette section sur les deux sites.

Tout d’abord, connectez-vous à distance aux sites A et B via le portail Azure Classic. Basculez la base de données en mode archivelog à l’aide de la fenêtre de commande SQL*Plus :

	sql>shutdown immediate
	sql>startup mount
	sql>alter database archivelog;
	sql>alter database open;


Activez ensuite la [journalisation supplémentaire](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) minimale en procédant comme suit :

	SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Préparez ensuite la base de données pour qu’elle prenne en charge la réplication DDL (Data Definition Language) :

	SQL> alter system set recyclebin=off scope=spfile;

Puis, arrêtez et redémarrez la base de données :

	sql>shutdown immediate
	sql>startup


##3\. Créer tous les objets nécessaires pour prendre en charge la réplication DDL
Cette section répertorie les scripts que vous devez utiliser pour créer tous les objets nécessaires à la prise en charge de la réplication DDL. Vous devez exécuter les scripts spécifiés dans cette section sur les sites A et B.

Ouvrez une invite de commandes Windows et accédez au dossier GoldenGate Oracle, par exemple C:\\OracleGG. Démarrez l’invite de commandes SQL*Plus avec des privilèges d’administrateur de base de données, en utilisant par exemple **SYSDBA** sur les sites A et B.

Exécutez ensuite les scripts suivants :

	SQL> @marker_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @ddl_setup.sql  
	Enter GoldenGate schema name: ggate
	SQL> @role_setup.sql
	Enter GoldenGate schema name: ggate
	SQL> grant ggs_ggsuser_role to ggate;
	 Grant succeeded.
	 SQL> @ddl_enable
	 Trigger altered.
	 SQL> @ddl_pin ggate


L’outil Oracle GoldenGate nécessite une connexion de niveau table pour prendre en charge les données DDL (Data Definition Language). C’est pourquoi vous devez activer la journalisation supplémentaire au niveau de la table à l’aide de la commande ADD TRANDATA. Ouvrez la fenêtre de l’interpréteur de commande GGSCI, connectez-vous à la base de données et exécutez la commande ADD TRANDATA :

	GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

	GGSCI(Hostname) 6> add trandata scott.inventory

##4\. Configurer GoldenGate Manager sur les sites A et B
Oracle GoldenGate Manager procède à certaines opérations, par exemple, le démarrage des autres processus GoldenGate, le suivi de la gestion des fichiers journaux et la création de rapports.

Vous devez configurer le processus Oracle GoldenGate Manager sur les sites A et B. Pour ce faire, effectuez les opérations suivantes sur les sites A et B.

Ouvrez une fenêtre de commande Windows et démarrez GGSCI :

	cd C:\OracleGG\
	c:\OracleGG>ggsci
	Oracle GoldenGate Command Interpreter for Oracle
	Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
	Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
	Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Vous enregistrez ainsi la session GGSCI dans une base de données afin de pouvoir exécuter des commandes qui affectent la base de données :

	GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

Affichez l’état et le retard (le cas échéant) pour les processus Manager, Extract et Replicat sur un système :

	GGSCI (HostName) 2> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Ouvrez le fichier de paramètres à l’aide de la commande EDIT PARAMS, puis ajoutez les informations suivantes :

	GGSCI (HostName) 3> edit params mgr
	PORT 7809
	USERID ggate, PASSWORD ggate
	PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Affichez l’état et le retard (le cas échéant) pour les processus Manager, Extract et Replicat sur un système :

	GGSCI (HostName) 46> info all
	Program     Status      Group       Lag           Time Since Chkpt
	MANAGER     STOPPED

Vous enregistrez ainsi la session GGSCI dans une base de données afin de pouvoir exécuter des commandes qui affectent la base de données :

	GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

	Successfully logged into database.

Démarrez le processus de Manager :

	GGSCI (HostName) 48> start manager
	Manager started.

##5\. Créer un groupe Extract et des processus Data Pump sur les sites A et B

###Créer des processus Extract et Data Pump sur le site A

Vous devez créer les processus d'extraction et Data Pump sur les sites A et B. Connectez-vous à distance aux sites A et B via le portail Azure Classic. Ouvrez la fenêtre de l’interpréteur de commande GGSCI. Exécutez les commandes suivantes sur le site A :

	GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
	EXTRACT added.
	GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
	EXTTRAIL added.
	GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
	EXTRACT added.
	GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
	RMTTRAIL added.

Ouvrez le fichier de paramètres à l’aide de la commande EDIT PARAMS, puis ajoutez les informations suivantes : GGSCI (MachineGG1) 18> edit params ext1 EXTRACT ext1 USERID ggate, PASSWORD ggate EXTTRAIL C:\\OracleGG\\dirdat\\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS ( ON UPDATE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml), ON DELETE KEYINCLUDING (prod\_category,qty\_in\_stock, last\_dml));

Ouvrez le fichier de paramètres à l’aide de la commande EDIT PARAMS, puis ajoutez les informations suivantes :

	GGSCI (MachineGG1) 15> edit params dpump1
	EXTRACT dpump1
	 USERID ggate, PASSWORD ggate
	 RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
	 RMTTRAIL C:\OracleGG\dirdat\ab
	 PASSTHRU
	 TABLE scott.inventory;

###Créer une table de points de contrôle GoldenGate sur le site B

Vous devez ensuite ajouter une table de points de contrôle sur le site B. Pour ce faire, vous devez ouvrir une fenêtre d’interpréteur de commandes GoldenGate et exécutez la commande suivante :

	C:\OracleGG\ggsci
	GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

Ajoutez ensuite la table de points de contrôle à la base de données, où ggate correspond au propriétaire :

	GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
	Successfully created checkpoint table ggate.checkpointtable.

Ajoutez le nom de la table de points de contrôle au fichier GLOBALS sur le serveur cible, qui correspond au site B dans cette étape. Modifiez le fichier GLOBALS sur le Site b :

	GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Puis, ajoutez le paramètre CHECKPOINTTABLE au fichier GLOBALS existant :

	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Dans la dernière étape, enregistrez et fermez le fichier de paramètres GLOBALS.


###Commande ADD REPLICAT sur le site B
Cette section décrit comment ajouter un processus REPLICAT « REP2 » au site B.

Utilisez la commande ADD REPLICAT pour créer un groupe Replicat sur le site B :

	GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Ouvrez le fichier de paramètres à l’aide de la commande EDIT PARAMS, puis ajoutez les informations suivantes :

	GGSCI (MachineGG2) 10> edit params rep2
	REPLICAT rep2
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

###Créer des processus Extract et Data Pump sur le site B

Cette section décrit comment créer un nouveau processus d’extraction « EXT2 » et un nouveau processus DATA PUMP « DPUMP2 » sur le site B :

	GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
	 EXTRACT added.
	GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
	 EXTTRAIL added.
	GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
	 EXTRACT added.
	GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
	 RMTTRAIL added.

Ouvrez le fichier de paramètres à l’aide de la commande EDIT PARAMS, puis ajoutez les informations suivantes :

	GGSCI (MachineGG2) 31> edit params ext2
	EXTRACT ext2
	USERID ggate, PASSWORD ggate
	EXTTRAIL C:\OracleGG\dirdat\ac
	TRANLOGOPTIONS EXCLUDEUSER ggate
	TABLE scott.inventory,
	GETBEFORECOLS (
	ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
	ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Ouvrez le fichier de paramètres à l’aide de la commande EDIT PARAMS, puis ajoutez les informations suivantes :

	GGSCI (MachineGG2) 32> edit params dpump2
	EXTRACT dpump2
	USERID ggate, PASSWORD ggate
	RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
	RMTTRAIL C:\OracleGG\dirdat\ad
	PASSTHRU
	TABLE scott.inventory;

###Créer une table de points de contrôle GoldenGate sur le site A

Ouvrez la fenêtre d’interpréteur de commandes Oracle GoldenGate et créez une table de points de contrôle :

	GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
	Successfully logged into database.

	GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

	Successfully created checkpoint table ggate.checkpointtable.

Vous devez également ajouter le nom de la table de points de contrôle au fichier GLOBALS sur le site A.

Ouvrez la fenêtre d’interpréteur de commandes Oracle GoldenGate et modifiez le fichier GLOBALS sur le site A :

	GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
	Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
	GGSCHEMA ggate
	CHECKPOINTTABLE ggate.checkpointtable

Enregistrez et fermez le fichier de paramètres GLOBALS.

###Commande ADD REPLICAT sur le site A

Cette section décrit comment ajouter un processus REPLICAT « REP1 » au site A.

La commande suivante crée un groupe Replicat « rep1 » avec le nom d’une piste et la table de points de contrôle associée.

	GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
	 REPLICAT added.

Ouvrez le fichier de paramètres à l’aide de la commande EDIT PARAMS, puis ajoutez les informations suivantes :

	GGSCI (MachineGG1) 10> edit params rep1
	REPLICAT rep1
	ASSUMETARGETDEFS
	USERID ggate, PASSWORD ggate
	DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
	MAP scott.inventory, TARGET scott.inventory;

### Commande ADD TRANSDATA sur les sites A et B

Activez la journalisation supplémentaire au niveau de la table à l’aide de la commande ADD TRANDATA. Ouvrez la fenêtre d’interpréteur de commandes Oracle GoldenGate, connectez-vous à la base de données et exécutez la commande ADD TRANDATA.

Connectez-vous à distance à MachineGG1, ouvrez l’interpréteur de commandes Oracle GoldenGate et exécutez la commande suivante :

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Connectez-vous à distance à MachineGG2, ouvrez l’interpréteur de commandes Oracle GoldenGate et exécutez la commande suivante :

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Affichez les informations relatives à l’état de la journalisation supplémentaire de niveau table :

	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Commande ADD TRANSDATA sur les sites A et B

Activez la journalisation supplémentaire au niveau de la table à l’aide de la commande ADD TRANDATA. Ouvrez la fenêtre d’interpréteur de commandes Oracle GoldenGate, connectez-vous à la base de données et exécutez la commande ADD TRANDATA.

Connectez-vous à distance à MachineGG1, ouvrez l’interpréteur de commandes Oracle GoldenGate et exécutez la commande suivante :

	GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	GGSCI (MachineGG1) 13> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Connectez-vous à distance à MachineGG2, ouvrez l’interpréteur de commandes Oracle GoldenGate et exécutez la commande suivante :

	GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
	 Successfully logged into database.
	GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
	Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
	Display information about the state of table-level supplemental logging:
	GGSCI (MachineGG2) 15> info trandata scott.inventory
	Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
	Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###Démarrer les processus Extract et Data Pump sur le site A

Démarrez le processus Extract « ext1 » sur le site A :

	GGSCI (MachineGG1) 31> start extract ext1
	Sending START request to MANAGER …
	EXTRACT EXT1 starting

Démarrez le processus Data Pump « dpump1 » sur le site A :

	GGSCI (MachineGG1) 23> start extract dpump1
	Sending START request to MANAGER …
	EXTRACT DPUMP1 starting
Affichez les informations relatives au groupe Extract « ext1 » : GGSCI (MachineGG1) 32> info extract ext1 EXTRACT EXT1 Last Started 2013-11-25 08:03 Status RUNNING Checkpoint Lag 00:00:00 (updated 00:00:02 ago) Log Read Checkpoint Oracle Redo Logs 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Affichez l’état et le retard (le cas échéant) pour les processus Manager, Extract et Replicat sur un système :

	GGSCI (MachineGG1) 16> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
	EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###Démarrer les processus Extract et Data Pump sur le site B

Démarrez le processus Extract « ext2 » sur le site B :

	GGSCI (MachineGG2) 22> start extract ext2
	Sending START request to MANAGER …
	EXTRACT EXT2 starting

Démarrez le processus Data Pump « dpump2 » sur le site B :

	GGSCI (MachineGG2) 23> start extract dpump2
	Sending START request to MANAGER …
	EXTRACT DPUMP2 starting

Affichez l’état et le retard (le cas échéant) pour les processus Manager, Extract et Replicat sur un système :

	GGSCI (ActiveGG2orcldb) 6> info all
	Program     Status      Group       Lag at Chkpt  Time Since Chkpt

	MANAGER     RUNNING
	EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
	EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### Démarrer le processus REPLICAT sur le site A

Cette section décrit comment démarrer le processus REPLICAT « REP1 » sur le site A.

Démarrez le processus Replicat sur le site A :

	GGSCI (MachineGG1) 38> start replicat rep1
	Sending START request to MANAGER …
	REPLICAT REP1 starting

Affichez l’état d’un groupe Replicat :

	GGSCI (MachineGG1) 39> status replicat rep1
	 REPLICAT REP1: RUNNING

###Démarrer le processus REPLICAT sur le site B

Cette section décrit comment démarrer le processus REPLICAT « REP2 » sur le site B.

Démarrez le processus Replicat sur le site B :

	GGSCI (MachineGG2) 26> start replicat rep2
	Sending START request to MANAGER …
	REPLICAT REP2 starting

Affichez l’état d’un groupe Replicat :

	GGSCI (MachineGG2) 27> status replicat rep2
	REPLICAT REP2: RUNNING

##6\. Vérifier le processus de réplication bidirectionnelle

Pour vérifier la configuration d’Oracle GoldenGate, insérez une ligne dans la base de données du site A. Connectez-vous à distance au site A. Ouvrez la fenêtre de commande SQL*Plus, puis exécutez la commande suivante : SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> insert into inventory values  (100,’TV’,100,sysdate);

	1 row created.

	SQL> commit;

	Commit complete.

Vérifiez ensuite que cette ligne est bien répliquée sur le site B. Pour ce faire, connectez-vous à distance au site B, puis ouvrez la fenêtre de commande SQL*Plus et exécutez la commande suivante :

	SQL> select name from v$database;

	NAME
	———
	TESTGG

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13

Insérez un nouvel enregistrement sur le site B :

	SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
	1 row created.

	SQL> commit;

	Commit complete.

Connectez-vous à distance au site A et vérifiez la bonne réplication :

	SQL> select * from inventory;

	PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
	———- ——————– ———— ———
	100 TV 100 22-MAR-13
	101 DVD 10 22-MAR-13

##Ressources supplémentaires
[Liste des images de machine virtuelle Oracle](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0323_2016-->