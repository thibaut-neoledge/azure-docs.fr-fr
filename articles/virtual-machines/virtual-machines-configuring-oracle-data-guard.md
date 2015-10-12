<properties
	pageTitle="Configuration d’Oracle Data Guard dans des machines virtuelles | Microsoft Azure"
	description="Suivez un didacticiel permettant de configurer et mettre en œuvre Oracle Data Guard sur des machines virtuelles Azure pour disposer d’une haute disponibilité et d’une récupération d’urgence."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Configuration d’Oracle Data Guard pour Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la gestion d’une ressource créée avec le modèle de déploiement classique.

Ce didacticiel explique comment configurer et mettre en œuvre Oracle Data Guard dans un environnement Azure Virtual Machines pour disposer d’une haute disponibilité et d’une récupération d’urgence. Il se concentre sur la réplication unidirectionnelle de bases de données Oracle non RAC.

Oracle Data Guard prend en charge la protection des données et la récupération d’urgence pour Oracle Database. Il s’agit d’une solution de dernière minute simple et aux performances élevées en matière de récupération d’urgence, de protection des données et de haute disponibilité pour l’ensemble de la base de données Oracle.

Ce didacticiel suppose que vous disposez déjà de connaissances théoriques et pratiques concernant les concepts de haute disponibilité et de récupération d’urgence pour Oracle Database. Pour plus d’informations, voir le [site web d’Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) ainsi que le guide [Oracle Data Guard Concepts and Administration Guide](http://docs.oracle.com/cd/E11882_01/server.112/e17022/create_ps.htm) (en anglais).

En outre, le didacticiel suppose que vous avez déjà mis en œuvre les conditions préalables suivantes :

- Vous avez déjà consulté la section Considérations relatives à la haute disponibilité et à la récupération d’urgence de la rubrique [Images de machine virtuelle Oracle - Considérations diverses](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md). Remarquez qu’Azure prend actuellement en charge des instances autonomes d’Oracle Database, mais pas Oracle Real Application Clusters (Oracle RAC).

- Vous avez créé deux machines virtuelles (VM, Virtual Machines) dans Azure à l’aide de la même image Oracle Enterprise Edition fournie par la plate-forme sur Windows Server. Pour plus d’informations, voir [Création d’une machine virtuelle Oracle Database 12c dans Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md) et [Machines virtuelles Azure](http://azure.microsoft.com/documentation/services/virtual-machines/). Assurez-vous que les machines virtuelles sont présentes au sein du [même service cloud](virtual-machines-load-balance.md) et du même [réseau virtuel](azure.microsoft.com/documentation/services/virtual-network/) pour garantir qu’elles peuvent accéder l’une à l’autre via l’adresse IP privée persistante. En outre, nous vous recommandons de placer les machines virtuelles au sein du même [groupe à haute disponibilité](virtual-machines-manage-availability.md) pour permettre à Azure de les positionner dans des domaines d’erreur ou de mise à niveau distincts. Remarquez qu’Oracle Data Guard est uniquement disponible avec Oracle Database Enterprise Edition. Chaque machine doit disposer d’au moins 2 Go de mémoire et de 5 Go d’espace disque. Pour obtenir les dernières informations sur les tailles des machines virtuelles fournies par la plateforme, voir [Tailles de machines virtuelles](http://msdn.microsoft.com/library/dn197896.aspx). Si vous avez besoin de volume de disque supplémentaire pour vos machines virtuelles, vous pouvez associer des disques supplémentaires. Pour plus d’informations, voir [Association d’un disque de données à une machine virtuelle Windows](storage-windows-attach-disk.md).

- Vous avez configuré les noms des machines virtuelles comme suit : « Machine1 » pour la machine virtuelle principale et « Machine2 » pour la machine virtuelle de secours sur le portail Azure.

- Vous avez défini la variable d’environnement **ORACLE\_HOME** pour qu’elle pointe vers le même chemin d’installation racine Oracle au sein des machines virtuelles principale et de secours, tel que `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Vous vous connectez à votre serveur Windows en tant que membre du groupe **Administrateurs** ou du groupe **ORA\_DBA**.

Ce didacticiel présente les procédures suivantes :

Mettre en œuvre l’environnement physique de la base de données de secours

1. Créer une base de données primaire

2. Préparer la base de données primaire pour la création de la base de données de secours

	1. Activer la journalisation forcée

	2. Créer un fichier de mot de passe

	3. Configurer un journal de rétablissement de secours

	4. Activer l'archivage

	5. Définir les paramètres d’initialisation de la base de données primaire

Créer une base de données de secours physique

1. Préparer un fichier de paramètres d’initialisation pour la base de données de secours

2. Configurer l’écouteur et le fichier tnsnames pour prendre en charge la base de données sur les machines principale et de secours

	1. Configurer listener.ora sur les deux serveurs pour contenir les entrées pour les deux bases de données

	2. Configurer tnsnames.ora sur les machines virtuelles principale et de secours pour contenir les entrées pour les bases de données primaire et de secours

	3. Démarrer l’écouteur et vérifier tnsping sur les deux machines virtuelles concernant les deux services

3. Démarrer l’instance de secours en état sans montage

4. Utiliser RMAN pour cloner la base de données et créer une base de données de secours

5. Démarrer la base de données de secours physique en mode de récupération géré

6. Vérifier la base de données de secours physique

> [AZURE.IMPORTANT]Ce didacticiel a été configuré et testé selon la configuration matérielle et logicielle suivante :
>
>| | **Base de données primaire** | **Base de données de secours** |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Version d’Oracle** | Oracle11g Enterprise version (11.2.0.4.0) | Oracle11g Enterprise version (11.2.0.4.0) |
>| **Nom de la machine** | Machine1 | Machine2 |
>| **Système d’exploitation** | Windows 2008 R2 | Windows 2008 R2 |
>| **SID Oracle** | TEST | TEST\_STBY |
>| **Mémoire** | Min. 2 Go | Min. 2 Go |
>| **Espace disque** | Min. 5 Go | Min. 5 Go |

Pour les versions ultérieures d’Oracle Database et d’Oracle Data Guard, il se peut que vous deviez procéder à certaines modifications supplémentaires. Pour obtenir les dernières informations concernant la version, voir la documentation relative à [Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et [Oracle Database](http://www.oracle.com/us/corporate/features/database-12c/index.html) sur le site web d’Oracle.

##Mettre en œuvre l’environnement physique de la base de données de secours
### 1\. Créer une base de données primaire

- Créez une base de données primaire « TEST » sur la machine virtuelle principale. Pour plus d’informations, consultez la section (en anglais) Creating and Configuring an Oracle Database.
- Connectez-vous à votre base de données en tant qu’utilisateur SYS avec le rôle SYSDBA dans l’invite de commandes SQL*Plus, puis exécutez l’instruction suivante pour afficher le nom de votre base de données :

		SQL> select name from v$database;

		The result will display like the following:

		NAME
		---------
		TEST
- Interrogez ensuite les noms des fichiers de base de données à partir de la vue système dba\_data\_files :

		SQL> select file_name from dba_data_files;
		FILE_NAME
		-------------------------------------------------------------------------------
		C:\ <YourLocalFolder>\TEST\USERS01.DBF
		C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
		C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
		C:<YourLocalFolder>\TEST\SYSTEM01.DBF
		C:<YourLocalFolder>\TEST\EXAMPLE01.DBF

### 2\. Préparer la base de données primaire pour la création de la base de données de secours

Avant de créer une base de données de secours, nous vous recommandons de vérifier que la base de données primaire est correctement configurée. Voici une liste des étapes que vous devez effectuer :

1. Activer la journalisation forcée
2. Créer un fichier de mot de passe
3. Configurer un journal de rétablissement de secours
4. Activer l'archivage
5. Définir les paramètres d’initialisation de la base de données primaire

#### Activer la journalisation forcée

Pour mettre en œuvre une base de données de secours, il faut activer la « Journalisation forcée » au sein de la base de données primaire. Cette option garantit que, même en cas d’opération sans journalisation, la journalisation est prioritaire et toutes les opérations sont journalisées dans les journaux de rétablissement. Par conséquent, vous vous assurez que tous les éléments présents au sein de la base de données primaire sont journalisés et que la base de données de secours comporte toutes les opérations de la base de données primaire. Exécutez l’instruction de modification de la base de données Alter pour activer la journalisation forcée :

	SQL> ALTER DATABASE FORCE LOGGING;

	Database altered.

#### Créer un fichier de mot de passe

Afin de pouvoir transmettre et appliquer des journaux archivés depuis le serveur principal vers le serveur de secours, le mot de passe sys doit être identique sur le serveur principal et de secours. C’est pourquoi vous créez un fichier de mot de passe sur la base de données primaire et le copiez sur le serveur de secours.

>[AZURE.IMPORTANT]Lorsque vous utilisez la base de données Oracle 12c, il existe un nouvel utilisateur, **SYSDG**, qui vous permet de gérer Oracle Data Guard. Pour plus d’informations, voir [Changes in Oracle Database 12c Release](http://docs.oracle.com/cd/E16655_01/server.121/e10638/release_changes.htm) (en anglais).

En outre, assurez-vous que l’environnement ORACLE\_HOME est déjà défini sur Machine1. Dans le cas contraire, définissez-le en tant que variable d’environnement à l’aide de la boîte de dialogue Variables d’environnement. Pour accéder à cette boîte de dialogue, démarrez l’utilitaire **Système** en double-cliquant sur l’icône Système dans le **Panneau de configuration**, puis cliquez sous l’onglet **Avancé** et sélectionnez **Variables d’environnement**. Cliquez sur le bouton **Nouveau** de la section **Variables système** pour configurer les variables d’environnement. Une fois les variables d’environnement configurées, fermez l’invite de commande Windows existante et ouvrez-en une nouvelle.

Exécutez l’instruction suivante pour basculer vers le répertoire Oracle\_Home, par exemple C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\database.

	cd %ORACLE_HOME%\database

Créez ensuite un fichier de mot de passe à l’aide de l’utilitaire de création de fichier de mot de passe [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). Dans la même invite de commandes Windows sur Machine1, exécutez la commande suivante en définissant la valeur de mot de passe en tant que mot de passe de **SYS** :

	ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Cette commande crée un fichier de mot de passe nommé PWDTEST.ora dans le répertoire ORACLE\_HOME\\database. Vous devez copier manuellement ce fichier dans le répertoire %ORACLE\_HOME%\\database sur Machine2.

#### Configurer un journal de rétablissement de secours

Vous devez ensuite configurer un journal de rétablissement de secours afin que le serveur principal puisse correctement recevoir le rétablissement lorsqu’il devient un serveur de secours. La création préalable de ces éléments à cet emplacement permet également la création automatique des journaux de rétablissement de secours sur le serveur de secours. Il est important de configurer les journaux de rétablissement de secours (SRL, Standby Redo Logs) d’une taille identique à celle des journaux de rétablissement en ligne. La taille des fichiers journaux de rétablissement de secours actuels doit correspondre exactement à celle des fichiers journaux de rétablissement en ligne de la base de données primaire actuelle.

Exécutez l’instruction suivante dans l’invite de commande SQL*PLUS sur Machine1. Le fichier journal v$logfile correspond à une vue système qui comporte des informations relatives aux fichiers journaux de rétablissement.

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE    MEMBER                                                       IS_
	---------- ------- ------- ------------------------------------------------------------ ---
	3         ONLINE  C:<YourLocalFolder>\TEST\REDO03.LOG               NO
	2         ONLINE  C:<YourLocalFolder>\TEST\REDO02.LOG               NO
	1         ONLINE  C:<YourLocalFolder>\TEST\REDO01.LOG               NO
	Next, query the v$log system view, displays log file information from the control file.
	SQL> select bytes from v$log;
	BYTES
	----------
	52428800
	52428800
	52428800


Remarquez que 52428800 correspond à 50 mégaoctets.

Dans la fenêtre SQL*Plus, exécutez ensuite les instructions suivantes pour ajouter un nouveau groupe de fichiers journaux de rétablissement de secours à une base de données de secours et spécifiez un nombre permettant d’identifier le groupe à l’aide de la clause GROUP. L’utilisation de numéros de groupe permet de simplifier l’administration de groupes de fichiers journaux de rétablissement de secours :

	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
	Database altered.
	SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
	Database altered.

Exécutez ensuite la vue système suivante pour afficher des informations relatives aux fichiers journaux de rétablissement. Cette opération vérifie également la bonne création des groupes de fichiers journaux de rétablissement de secours :

	SQL> select * from v$logfile;
	GROUP# STATUS  TYPE MEMBER IS_
	---------- ------- ------- --------------------------------------------- ---
	3         ONLINE C:<YourLocalFolder>\TEST\REDO03.LOG NO
	2         ONLINE C:<YourLocalFolder>\TEST\REDO02.LOG NO
	1         ONLINE C:<YourLocalFolder>\TEST\REDO01.LOG NO
	4         STANDBY C:<YourLocalFolder>\TEST\REDO04.LOG
	5         STANDBY C:<YourLocalFolder>\TEST\REDO05.LOG NO
	6         STANDBY C:<YourLocalFolder>\TEST\REDO06.LOG NO
	6 rows selected.

#### Activer l'archivage

Activez ensuite l’archivage en exécutant les instructions suivantes pour placer la base de données primaire en mode ARCHIVELOG et activer l’archivage automatique. Vous pouvez activer le mode ARCHIVELOG en montant la base de données, puis en exécutant la commande archivelog.

Tout d’abord, ouvrez une session en tant que sysdba. Dans l’invite de commandes Windows, exécutez la commande suivante :

	sqlplus /nolog

	connect / as sysdba

Arrêtez ensuite la base de données dans l’invite de commande SQL*Plus :

	SQL> shutdown immediate;
	Database closed.
	Database dismounted.
	ORACLE instance shut down.

Exécutez ensuite la commande de démarrage du montage pour monter la base de données. Vous vous assurez ainsi qu’Oracle associe l’instance à la base de données spécifiée.

	SQL> startup mount;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.

Exécutez ensuite la commande suivante :

	SQL> alter database archivelog;
	Database altered.

Exécutez ensuite l’instruction de modification de la base de données Alter avec la clause d’ouverture Open afin de rendre la base de données disponible pour une utilisation normale :

	SQL> alter database open;

	Database altered.

#### Définir les paramètres d’initialisation de la base de données primaire

Pour configurer Data Guard, vous devez d’abord créer et configurer les paramètres de secours sur un fichier pfile régulier (fichier texte de paramètres d’initialisation). Une fois le fichier pfile créé, vous devez le convertir en fichier de paramètre serveur (SPFILE).

Vous pouvez contrôler l’environnement de Data Guard à l’aide des paramètres présents dans le fichier INIT.ORA. Pour poursuivre ce didacticiel, vous devez mettre à jour le fichier INIT.ORA de la base de données primaire afin qu’elle puisse jouer les deux rôles suivants : primaire ou de secours.

	SQL> create pfile from spfile;
	File created.

Vous devez ensuite modifier le fichier pfile pour ajouter les paramètres de secours. Pour ce faire, ouvrez le fichier INITTEST.ORA, situé à l’emplacement suivant : %ORACLE\_HOME%\\database. Ajoutez ensuite les instructions suivantes au fichier INITTEST.ora. Remarquez que la convention d’affectation de noms concernant votre fichier INIT.ORA est la suivante : INIT <Nom\_de\_votre\_base\_de\_données >.ORA.

	db_name='TEST'
	db_unique_name='TEST'
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
	LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
	LOG_ARCHIVE_DEST_STATE_1=ENABLE
	LOG_ARCHIVE_DEST_STATE_2=ENABLE
	REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
	LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
	LOG_ARCHIVE_MAX_PROCESSES=30
	# Standby role parameters --------------------------------------------------------------------
	fal_server=TEST_STBY
	fal_client=TEST
	standby_file_management=auto
	db_file_name_convert='TEST_STBY','TEST'
	log_file_name_convert='TEST_STBY','TEST'
	# ---------------------------------------------------------------------------------------------


Le bloc d’instructions précédent présente trois éléments de configuration importants : - **LOG\_ARCHIVE\_CONFIG... :** qui vous permet de définir les ID uniques de base de données  - **LOG\_ARCHIVE\_DEST\_1... :** qui vous permet de définir l’emplacement du dossier d’archive local. Nous vous recommandons de créer un répertoire pour les besoins d’archivage de votre base de données et de spécifier l’emplacement d’archivage local explicitement à l’aide de cette instruction plutôt que d’utiliser le dossier par défaut %ORACLE\_HOME%\\database\\archive. - **LOG\_ARCHIVE\_DEST\_2.... LGWR ASYNC... :** qui vous permet de définir un processus d’écriture de journaux (LGWR, log writer) asynchrone afin de recueillir les données de rétablissement des transactions et de les transmettre aux destinations de secours. Le nom unique de la base de données DB\_UNIQUE\_NAME spécifie un nom unique concernant la base de données sur le serveur de secours.

Une fois le nouveau fichier de paramètres configuré, vous devez créer le fichier spfile à partir de celui-ci.

Commencez par fermer la base de données :

	SQL> shutdown immediate;

	Database closed.

	Database dismounted.

	ORACLE instance shut down.

Exécutez ensuite la commande de démarrage sans montage suivante :

	SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes

Créez à présent un fichier spfile :

	SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

	File created.

Fermez ensuite la base de données :

	SQL> shutdown immediate;

	ORA-01507: database not mounted

Utilisez ensuite la commande de démarrage pour démarrer une instance :

	SQL> startup;
	ORACLE instance started.
	Total System Global Area 1503199232 bytes
	Fixed Size                  2281416 bytes
	Variable Size             922746936 bytes
	Database Buffers          570425344 bytes
	Redo Buffers                7745536 bytes
	Database mounted.
	Database opened.

##Créer une base de données de secours physique
Cette section se concentre sur les étapes que vous devez effectuer sur Machine2 pour préparer la base de données de secours physique.

Vous devez tout d’abord vous connecter à distance à Machine2 via le portail Azure.

Sur le serveur de secours (Machine2), créez ensuite tous les dossiers nécessaires pour la base de données de secours, tel que C:\\ <Votre\_dossier\_local>\\TEST. Pour poursuivre ce didacticiel, assurez-vous que la structure du dossier correspond à celle de Machine1 pour conserver tous les fichiers nécessaires, tels que le fichier de contrôle (controlfile), les fichiers de données (datafiles), les fichiers journaux de rétablissement (redologfiles) et les fichiers udump, bdump et cdump. Définissez également les variables d’environnement ORACLE\_HOME et ORACLE\_BASE sur Machine2. Dans le cas contraire, définissez-les en tant que variables d’environnement à l’aide de la boîte de dialogue Variables d’environnement. Pour accéder à cette boîte de dialogue, démarrez l’utilitaire **Système** en double-cliquant sur l’icône Système dans le **Panneau de configuration**, puis cliquez sous l’onglet **Avancé** et sélectionnez **Variables d’environnement**. Cliquez sur le bouton **Nouveau** de la section **Variables système** pour configurer les variables d’environnement. Une fois les variables d’environnement configurées, vous devez fermer l’invite de commande Windows existante et en ouvrir une nouvelle pour afficher les modifications.

Procédez ensuite comme suit :

1. Préparer un fichier de paramètres d’initialisation pour la base de données de secours

2. Configurer l’écouteur et le fichier tnsnames pour prendre en charge la base de données sur les machines principale et de secours

	1. Configurer listener.ora sur les deux serveurs pour contenir les entrées pour les deux bases de données

	2. Configurer tnsnames.ora sur les machines virtuelles principale et de secours pour contenir les entrées pour les bases de données primaire et de secours

	3. Démarrer l’écouteur et vérifier tnsping sur les deux machines virtuelles concernant les deux services

3. Démarrer l’instance de secours en état sans montage

4. Utiliser RMAN pour cloner la base de données et créer une base de données de secours

5. Démarrer la base de données de secours physique en mode de récupération géré

6. Vérifier la base de données de secours physique

### 1\. Préparer un fichier de paramètres d’initialisation pour la base de données de secours

Cette section décrit comment préparer un fichier de paramètres d’initialisation pour la base de données de secours. Pour ce faire, copiez d’abord manuellement le fichier INITTEST.ORA à partir de Machine1 vers Machine2. Vous devez observer le fichier INITTEST.ORA dans le dossier %ORACLE\_HOME%\\database sur les deux machines. Modifiez ensuite le fichier INITTEST.ORA sur Machine2 afin de le configurer pour le rôle de secours, comme indiqué ci-dessous :

	db_name='TEST'
	db_unique_name='TEST_STBY'
	db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
	db_file_name_convert=’TEST’,’TEST_STBY’
	log_file_name_convert='TEST','TEST_STBY'


	job_queue_processes=10
	LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
	LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
	LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
	LOG_ARCHIVE_DEST_STATE_1='ENABLE'
	LOG_ARCHIVE_DEST_STATE_2='ENABLE'
	LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
	LOG_ARCHIVE_MAX_PROCESSES=30


Le bloc d’instructions précédent comprend deux éléments de configuration importants :

-	***.LOG\_ARCHIVE\_DEST\_1 :** vous devez créer manuellement le dossier C:\\OracleDatabase\\TEST\_STBY\\archives sur Machine2.
-	***.LOG\_ARCHIVE\_DEST\_2 :** cette étape est facultative. Vous définissez ce paramètre dans la mesure où il peut être nécessaire lorsque la machine principale est en cours de maintenance et que la machine de secours fait office de base de données primaire.

Vous devez ensuite démarrer l’instance de secours. Sur le serveur de la base de données de secours, entrez la commande suivante dans une invite de commandes Windows pour créer une instance Oracle en créant un nouveau service Windows :

	oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

Remarquez que la commande **Oradim** crée une instance Oracle, mais ne la démarre pas. Vous pouvez trouver cette commande dans le répertoire C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\BIN.

##Configurer l’écouteur et le fichier tnsnames pour prendre en charge la base de données sur les machines principale et de secours
Avant de créer une base de données de secours, vous devez vous assurer que les bases de données primaire et de secours présentes au sein de votre configuration peuvent communiquer entre elles. Pour ce faire, vous devez configurer l’écouteur et TNSNames manuellement ou à l’aide de l’utilitaire de configuration réseau NETCA. Il s’agit d’une tâche obligatoire lorsque vous utilisez l’utilitaire Recovery Manager (RMAN).

### Configurer listener.ora sur les deux serveurs pour contenir les entrées pour les deux bases de données

Connectez-vous à distance à Machine1 et modifiez le fichier listener.ora comme indiqué ci-dessous. Lorsque vous modifiez le fichier listener.ora, assurez-vous toujours que les parenthèses ouvrantes et fermantes s’affichent dans la même colonne. Le fichier listener.ora est disponible dans le dossier suivant : c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	# listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )

Connectez-vous ensuite à distance à Machine2 et modifiez le fichier listener.ora comme suit : n° de fichier de configuration réseau listener.ora : C:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\network\\admin\\listener.ora

	# Generated by Oracle configuration tools.

	SID_LIST_LISTENER =
	  (SID_LIST =
	    (SID_DESC =
	      (SID_NAME = test_stby)
	      (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
	      (PROGRAM = extproc)
	      (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
	    )
	  )

	LISTENER =
	  (DESCRIPTION_LIST =
	    (DESCRIPTION =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
	    )
	  )


### Configurer tnsnames.ora sur les machines virtuelles principale et de secours pour contenir les entrées pour les bases de données primaire et de secours

Connectez-vous à distance à Machine1 et modifiez le fichier tnsnames.ora comme indiqué ci-dessous. Le fichier tsnames.ora est disponible dans le dossier suivant : c:\\OracleDatabase\\product\\11.2.0\\dbhome\_1\\NETWORK\\ADMIN\\.

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )

Connectez-vous à distance à Machine2 et modifiez le fichier tnsnames.ora comme suit :

	TEST =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test)
	    )
	  )

	TEST_STBY =
	  (DESCRIPTION =
	    (ADDRESS_LIST =
	      (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
	    )
	    (CONNECT_DATA =
	      (SERVICE_NAME = test_stby)
	    )
	  )


### Démarrer l’écouteur et vérifier tnsping sur les deux machines virtuelles concernant les deux services

Ouvrez une nouvelle invite de commandes Windows sur les machines virtuelles principale et de secours et exécutez les instructions suivantes :

	C:\Users\DBAdmin>tnsping test

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test)))
	OK (0 msec)


	C:\Users\DBAdmin>tnsping test_stby

	TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
	Copyright (c) 1997, 2010, Oracle.  All rights reserved.
	Used parameter files:
	C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
	Used TNSNAMES adapter to resolve the alias
	Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
	VICE_NAME = test_stby)))
	OK (260 msec)


##Démarrer l’instance de secours en état sans montage
Vous devez configurer l’environnement pour prendre en charge de la base de données de secours sur la machine virtuelle de secours (Machine2).

Commencez par copier manuellement le fichier de mot de passe à partir de la machine principale (Machine1) vers la machine de secours (Machine2). Cette opération est nécessaire, car le mot de passe **sys** doit être identique sur les deux machines.

Ouvrez ensuite l’invite de commandes sur Machine2 et configurez les variables d’environnement pour qu’elles pointent vers la base de données de secours comme suit :

	SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
	SET ORACLE_SID=TEST_STBY

Démarrez ensuite la base de données de secours en état sans montage, puis générez un fichier spfile.

Démarrez la base de données :

	SQL>shutdown immediate;

	SQL>startup nomount
	ORACLE instance started.

	Total System Global Area  747417600 bytes
	Fixed Size                  2179496 bytes
	Variable Size             473960024 bytes
	Database Buffers          264241152 bytes
	Redo Buffers                7036928 bytes


##Utiliser RMAN pour cloner la base de données et créer une base de données de secours
Vous pouvez utiliser l’utilitaire Recovery Manager (RMAN) afin de sélectionner une copie de sauvegarde de la base de données primaire pour créer la base de données de secours physique.

Connectez-vous à distance à la machine virtuelle de secours (Machine2) et exécutez l’utilitaire RMAN en spécifiant une chaîne de connexion complète pour l’instance cible (base de données primaire, Machine1) et l’instance auxiliaire (base de données de secours, Machine2).

>[AZURE.IMPORTANT]N’utilisez pas l’authentification du système d’exploitation, car il n’existe pour le moment aucune base de données au sein de la machine serveur de secours.

	C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

	RMAN>DUPLICATE TARGET DATABASE
	  FOR STANDBY
	  FROM ACTIVE DATABASE
	  DORECOVER
	    NOFILENAMECHECK;

##Démarrer la base de données de secours physique en mode de récupération géré
Ce didacticiel décrit comment créer une base de données de secours physique. Pour plus d’informations sur la création d’une base de données de secours logique, consultez la documentation Oracle.

Ouvrez l’invite de commande SQL*Plus, puis activez Data Guard sur la machine virtuelle ou le serveur de secours (Machine2) en procédant comme suit :

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Lorsque vous ouvrez la base de données de secours en mode **MONTAGE**, l’envoi du journal d’archive continue et le processus de récupération géré poursuit l’application du journal sur la base de données de secours. Vous êtes ainsi certain que la base de données de secours reste à jour avec la base de données primaire. Remarquez que la base de données de secours n’est pas accessible à des fins de création de rapports pendant cette période.

Lorsque vous ouvrez la base de données de secours en mode **LECTURE SEULE**, l’envoi du journal d’archive se poursuit, mais le processus de récupération géré s’arrête. La base de données de secours est alors obsolète et le devient de plus en plus jusqu’à la reprise du processus de récupération géré. Vous pouvez accéder à la base de données de secours à des fins de création de rapports pendant cette période, mais il se peut que les données ne reflètent pas les dernières modifications.

De manière générale, nous vous recommandons de conserver la base de données de secours en mode **MONTAGE** afin de garder à jour les données de la base de données de secours en cas de défaillance de la base de données primaire. Vous pouvez toutefois conserver la base de données de secours en mode **LECTURE SEULE** à des fins de création de rapports, selon les besoins de votre application. Les étapes suivantes décrivent comment activer Data Guard en mode lecture seule à l’aide de SQL*Plus :

	SHUTDOWN IMMEDIATE;
	STARTUP MOUNT;
	ALTER DATABASE OPEN READ ONLY;


##Vérifier la base de données de secours physique
Cette section décrit comment vérifier la configuration de haute disponibilité en tant qu’administrateur.

Ouvrez la fenêtre d’invite de commande SQL*Plus , puis vérifiez le journal de rétablissement archivé sur la machine virtuelle de secours (Machine2) :

	SQL> show parameters db_unique_name;

	NAME                                TYPE       VALUE
	------------------------------------ ----------- ------------------------------
	db_unique_name                      string     TEST_STBY

	SQL> SELECT NAME FROM V$DATABASE

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	45                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   NO
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   NO
	47                    23-FEB-14   23-FEB-14   NO

Ouvrez la fenêtre d’invite de commande SQL*Plus et basculez les fichiers journaux (logfiles) sur la machine principale (Machine1) :

	SQL> alter system switch logfile;
	System altered.

	SQL> archive log list
	Database log mode              Archive Mode
	Automatic archival             Enabled
	Archive destination            C:\OracleDatabase\archive
	Oldest online log sequence     69
	Next log sequence to archive   71
	Current log sequence           71

Vérifiez le journal de rétablissement archivé sur la machine virtuelle de secours (Machine2) :

	SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

	SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
	----------------  ---------------  --------------- ------------
	45                    23-FEB-14   23-FEB-14   YES
	46                    23-FEB-14   23-FEB-14   YES
	47                    23-FEB-14   23-FEB-14   YES
	48                    23-FEB-14   23-FEB-14   YES

	49                    23-FEB-14   23-FEB-14   YES
	50                    23-FEB-14   23-FEB-14   IN-MEMORY

Identifiez tout écart sur la machine virtuelle de secours (Machine2) :

	SQL> SELECT * FROM V$ARCHIVE_GAP;
	no rows selected.

Une autre méthode de vérification consiste à basculer vers la base de données de secours, puis à tester s’il est possible de restaurer automatiquement la base de données primaire. Pour activer la base de données de secours en tant que base de données primaire, suivez les instructions suivantes :

	SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
	SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Si vous n’avez pas activé la fonction Flashback sur la base de données primaire d’origine, nous vous recommandons de supprimer la base de données primaire d’origine et de la recréer en guise de base de données de secours.

Nous vous recommandons d’activer la fonction Flashback sur les bases de données primaire et de secours. En cas de basculement, il est possible de restaurer la base de données primaire à une version antérieure au basculement et ainsi rapidement la convertir en base de données de secours.

##Ressources supplémentaires
[Liste des images de machine virtuelle Oracle](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=Oct15_HO1-->