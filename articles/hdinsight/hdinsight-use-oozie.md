<properties
	pageTitle="Utilisation de Hadoop Oozie dans HDInsight | Microsoft Azure"
	description="Utilisation de Hadoop Oozie dans HDInsight, un service pour les données volumineuses. Découvrez comment définir un workflow Oozie et envoyer une tâche Oozie."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="jgao"/>


# Utilisation d'Oozie avec Hadoop pour définir et exécuter un workflow dans HDInsight

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

##Vue d’ensemble
Apprenez à utiliser Apache Oozie pour définir un workflow et l'exécuter sur HDInsight. Pour apprendre à utiliser le coordinateur Oozie, consultez la page [Utilisation du coordinateur Hadoop Oozie basé sur le temps avec HDInsight][hdinsight-oozie-coordinator-time]. Pour en savoir plus sur Azure Data Factory, consultez la page [Utilisation de Pig et Hive avec Data Factory][azure-data-factory-pig-hive].

##Présentation d'Oozie

Apache Oozie est un système de workflow/coordination qui gère les tâches Hadoop. Il est intégré à la pile Hadoop et prend en charge les tâches Hadoop pour Apache MapReduce, Apache Pig, Apache Hive et Apache Sqoop. Il peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell.

Le workflow que vous allez implémenter en suivant les instructions de ce didacticiel contient deux actions :

![Diagramme du workflow][img-workflow-diagram]

1. Une action Hive exécute un script HiveQL pour compter les occurrences de chaque type de niveau de journalisation dans un fichier log4j. Chaque fichier log4j est constitué d’une ligne de champs qui contient un champ [LOG LEVEL] pour indiquer le type et la gravité, par exemple :

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

	La sortie du script Hive doit ressembler à ceci :

		[DEBUG] 434
		[ERROR] 3
		[FATAL] 1
		[INFO]  96
		[TRACE] 816
		[WARN]  4

	Pour plus d'informations sur Hive, consultez l'article [Utilisation de Hive avec HDInsight][hdinsight-use-hive].

2.  Une action Sqoop exporte la sortie de HiveQL vers une table dans la base de données SQL Azure. Pour plus d'informations sur Sqoop, consultez la rubrique [Utilisation de Hadoop Sqoop avec HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE]Pour obtenir la liste des versions Oozie prises en charge sur les clusters HDInsight, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions].

> [AZURE.NOTE]Ce didacticiel fonctionne sur les versions 3.0 et 2.1 de HDInsight. Ce didacticiel n'a pas été testé sur l'émulateur HDInsight.


##Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Pour exécuter des scripts Windows PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et définir la stratégie d’exécution sur *RemoteSigned*. Pour plus d’informations, consultez la rubrique [Exécution des scripts Windows PowerShell][powershell-script].
- **Un cluster HDInsight**. Pour plus d'informations sur la création d'un cluster HDInsight, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight à l'aide d'options personnalisées][hdinsight-provision] ou [Prise en main de Hadoop avec Hive dans HDInsight pour analyser l'utilisation des téléphones mobiles][hdinsight-get-started]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

	<table border = "1">
<tr><th>Propriété du cluster</th><th>Nom de la variable Windows&#160;PowerShell</th><th>Valeur</th><th>Description</th></tr>
<tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Cluster HDInsight sur lequel vous exécutez ce didacticiel.</td></tr>
<tr><td>Nom d'utilisateur du cluster HDInsight</td><td>$clusterUsername</td><td></td><td>Nom d'utilisateur de cluster HDInsight. </td></tr>
<tr><td>Mot de passe de l'utilisateur du cluster HDInsight </td><td>$clusterPassword</td><td></td><td>Mot de passe de l'utilisateur du cluster HDInsight.</td></tr>
<tr><td>Nom du compte de stockage Azure</td><td>$storageAccountName</td><td></td><td>Un compte de stockage Azure disponible sur le cluster HDInsight. Pour ce didacticiel, utilisez le compte de stockage par défaut indiqué au cours du processus d'approvisionnement du cluster.</td></tr>
<tr><td>Nom du conteneur d'objets blob Azure</td><td>$containerName</td><td></td><td>Dans cet exemple, utilisez le nom du conteneur d’objets blob qui est utilisé pour le système de fichiers du cluster HDInsight par défaut. Par défaut, il porte le même nom que le cluster HDInsight.</td></tr>
</table>

- **Une base de données SQL Azure**. Vous devez configurer une règle de pare-feu pour que la base de données SQL Azure autorise l'accès à partir de votre poste de travail. Pour obtenir des instructions sur la création d'une base de données SQL Azure et la configuration d'un pare-feu, consultez la rubrique [Prise en main de la base de données SQL Microsoft Azure][sqldatabase-get-started]. Cet article inclut un script Windows PowerShell pour la création de la table de base de données SQL Azure dont vous avez besoin pour ce didacticiel.

	<table border = "1">
<tr><th>Propriété de base de données&#160;SQL</th><th>Nom de la variable Windows&#160;PowerShell</th><th>Valeur</th><th>Description</th></tr>
<tr><td>Nom du serveur de base de données&#160;SQL</td><td>$sqlDatabaseServer</td><td></td><td>Base de données SQL&#160;Azure vers laquelle Sqoop exporte des données. </td></tr>
<tr><td>Nom de connexion à la base de données&#160;SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nom de connexion de la base de données&#160;SQL&#160;Azure.</td></tr>
<tr><td>Mot de passe de connexion à la base de données&#160;SQL</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Mot de passe de connexion à la base de données&#160;SQL&#160;Azure</td></tr>
<tr><td>Nom de la base de données&#160;SQL</td><td>$sqlDatabaseName</td><td></td><td>Base de données SQL&#160;Azure vers laquelle Sqoop exporte des données. </td></tr>
</table>> [AZURE.NOTE]Par défaut, une base de données SQL Azure autorise des connexions aux services Azure tels que Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l'activer depuis le portail de gestion Azure. Pour obtenir des instructions sur la création d'une base de données SQL et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d'une base de données SQL Azure][sqldatabase-create-configue].


> [AZURE.NOTE]Renseignez ces valeurs dans les tableaux. Cela vous sera utile pour ce didacticiel.


##Définition du workflow Oozie et du script HiveQL lié

Les définitions des workflows Oozie sont écrites en hPDL (un langage de définition du processus XML). Le nom du fichier de workflow par défaut est *workflow.xml*. Enregistrez le fichier de workflow en local et déployez-le ensuite sur le cluster HDInsight en utilisant Windows PowerShell plus loin dans ce didacticiel.

L'action Hive dans le workflow appelle un fichier de script HiveQL. Le fichier de script contient trois instructions HiveQL :

1. **L'instruction DROP TABLE** supprime la table Hive log4j si elle existe.
2. **L'instruction CREATE TABLE** crée une table externe Hive log4j pointant vers l'emplacement du fichier journal log4j. Le séparateur de champ est « , ». Le séparateur de ligne par défaut est « \\n ». Une table externe Hive est utilisée pour éviter que le fichier de données soit supprimé de son emplacement d'origine si vous souhaitez exécuter à plusieurs reprises le workflow Oozie.
3. **L'instruction INSERT OVERWRITE** compte les occurrences de chaque type de niveau de journalisation à partir de la table Hive log4j et enregistre la sortie vers un objet blob dans Azure Storage.

Il existe un problème connu de chemin d'accès à Hive. Vous le rencontrerez lors de l’envoi d’une tâche Oozie. Les instructions permettant d'y remédier sont disponibles dans le Wiki TechNet : [Erreur Hive HDInsight : impossible de renommer][technetwiki-hive-error].

**Définition du fichier de script HiveQL appelé par le workflow**

1. Créez un fichier texte avec le contenu suivant :

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

	Voici les trois variables utilisées dans le script :

	- ${hiveTableName}
	- ${hiveDataFolder}
	- ${hiveOutputFolder}

	Le fichier de définition du workflow (workflow.xml dans ce didacticiel) transmet ces valeurs à ce script HiveQL au moment de l'exécution.

2. Enregistrez le fichier sous **C:\\Tutorials\\UseOozie\\useooziewf.hql** en utilisant l’encodage **ANSI (ASCII)**. (Utilisez le Bloc-notes si votre éditeur de texte ne dispose pas de cette option.) Le fichier de script est déployé sur le cluster HDInsight plus loin dans ce didacticiel.



**Définition d'un workflow**

1. Créez un fichier texte avec le contenu suivant :

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		    <start to = "RunHiveScript"/>

		    <action name="RunHiveScript">
		        <hive xmlns="uri:oozie:hive-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.job.queue.name</name>
		                    <value>${queueName}</value>
		                </property>
		            </configuration>
		            <script>${hiveScript}</script>
			    	<param>hiveTableName=${hiveTableName}</param>
		            <param>hiveDataFolder=${hiveDataFolder}</param>
		            <param>hiveOutputFolder=${hiveOutputFolder}</param>
		        </hive>
		        <ok to="RunSqoopExport"/>
		        <error to="fail"/>
		    </action>

		    <action name="RunSqoopExport">
		        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		            <job-tracker>${jobTracker}</job-tracker>
		            <name-node>${nameNode}</name-node>
		            <configuration>
		                <property>
		                    <name>mapred.compress.map.output</name>
		                    <value>true</value>
		                </property>
		            </configuration>
			    <arg>export</arg>
			    <arg>--connect</arg>
			    <arg>${sqlDatabaseConnectionString}</arg>
			    <arg>--table</arg>
			    <arg>${sqlDatabaseTableName}</arg>
			    <arg>--export-dir</arg>
			    <arg>${hiveOutputFolder}</arg>
			    <arg>-m</arg>
			    <arg>1</arg>
			    <arg>--input-fields-terminated-by</arg>
			    <arg>"\001"</arg>
		        </sqoop>
		        <ok to="end"/>
		        <error to="fail"/>
		    </action>

		    <kill name="fail">
		        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		    </kill>

		   <end name="end"/>
		</workflow-app>

	Voici les deux actions définies dans le workflow : l'action de démarrage est *RunHiveScript*. Si cette action fonctionne correctement, l'action suivante est *RunSqoopExport*.

	RunHiveScript a plusieurs variables. Vous transmettez ces valeurs lors de l'envoi de la tâche Oozie à partir de votre poste de travail en utilisant Windows PowerShell.

	<table border = "1">
<tr><th>Variable de workflow</th><th>Description</th></tr>
<tr><td>${jobTracker}</td><td>Spécifie l'URL du suivi des tâches Hadoop. Utilisez <strong>jobtrackerhost: 9010</strong> dans les versions 3.0 et 2.1 de HDInsight .</td></tr>
<tr><td>${nameNode}</td><td>Spécifie l'URL du nœud de nom Hadoop. Utilisez l’adresse du système de fichiers par défaut, par exemple, <i>wasb://&lt;containerName>@&lt;storageAccountName>.blob.core.windows.net</i>.</td></tr>
<tr><td>${queueName}</td><td>Spécifie le nom de la file d’attente auquel est envoyée la tâche. Utilisez la <strong>valeur par défaut</strong>.</td></tr>
</table><table border = "1">
<tr><th>Variable d'action Hive</th><th>Description</th></tr>
<tr><td>${hiveDataFolder}</td><td>Spécifie le répertoire source pour la commande Hive de création d'une table.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Spécifie le dossier de sortie pour l'instruction INSERT OVERWRITE.</td></tr>
<tr><td>${hiveTableName}</td><td>Spécifie le nom de la table Hive référençant les fichiers de données log4j.</td></tr>
</table><table border = "1">
<tr><th>Variable d'action Sqoop</th><th>Description</th></tr>
<tr><td>${sqlDatabaseConnectionString}</td><td>Spécifie la chaîne de connexion à la base de données SQL</td></tr>
<tr><td>${sqlDatabaseTableName}</td><td>Spécifie la table de la base de données SQL&#160;Azure vers laquelle les données sont exportées.</td></tr>
<tr><td>${hiveOutputFolder}</td><td>Spécifie le dossier de sortie pour l'instruction INSERT OVERWRITE de Hive. Il s'agit du même dossier pour Sqoop Export (export-dir).</td></tr>
</table>Pour plus d'informations sur le workflow Oozie et l'utilisation des actions de workflow, consultez la rubrique [Documentation sur Apache Oozie 4.0][apache-oozie-400] (pour la version 3.0 de HDInsight) ou [Documentation sur Apache Oozie 3.3.2][apache-oozie-332] (pour la version 2.1 de HDInsight).

2. Enregistrez le fichier sous **C:\\Tutorials\\UseOozie\\workflow.xml** en utilisant l'encodage ANSI (ASCII). (Utilisez le Bloc-notes si votre éditeur de texte ne dispose pas de cette option.)

##Déploiement du projet Oozie et préparation du didacticiel

Exécutez un script Windows PowerShell pour effectuer les opérations suivantes :

- Copier le script HiveQL (useoozie.hql) dans Azure Storage (wasb:///tutorials/useoozie/useoozie.hql).
- Copier workflow.xml dans wasb:///tutorials/useoozie/workflow.xml.
- Copier le fichier de données (/ example/data/sample.log) dans wasb:///tutorials/useoozie/data/sample.log.
- Créer une table de base de données SQL Azure pour stocker les données d'exportation de Sqoop. Le nom de la table est *log4jLogCount*.

**Présentation du stockage HDInsight**

HDInsight utilise des objets blob dans Azure Storage pour stocker des données. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Lors de l'approvisionnement d'un cluster HDInsight, un compte Azure Storage et un conteneur d'objets blob spécifique de ce compte sont désignés en tant que système de fichiers par défaut, comme dans HDFS. En plus de ce compte de stockage, pendant le processus d’approvisionnement, vous pouvez ajouter des comptes de stockage supplémentaires à partir du même abonnement Azure ou à partir d'autres abonnements Azure. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight][hdinsight-provision]. Afin de simplifier le script Windows PowerShell utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur de stockage de fichiers par défaut, sous */tutorials/usesqoop*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight. La syntaxe est :

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]Seule la syntaxe *wasb://* est prise en charge dans les clusters HDInsight version 3.0. L'ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais elle n’est pas prise en charge dans les clusters HDInsight 3.0.

> [AZURE.NOTE]Le chemin d'accès wasb:// est un chemin d'accès virtuel. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Vous pouvez accéder à un fichier stocké dans le conteneur du système de fichiers par défaut à partir de HDInsight en utilisant l'un des URI suivants (le code suivant utilise workflow.xml comme exemple) :

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
	wasb:///tutorials/useoozie/workflow.xml
	/tutorials/useoozie/workflow.xml

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

	tutorials/useoozie/workflow.xml

**Présentation des tables interne et externe Hive**

Voici quelques éléments à connaître sur les tables interne et externe Hive :

- La commande CREATE TABLE crée une table interne, également nommée table gérée. Le fichier de données doit se trouver dans le conteneur par défaut.
- La commande CREATE TABLE déplace le fichier de données vers le dossier /hive/warehouse/<TableName> dans le conteneur par défaut.
- La commande CREATE EXTERNAL TABLE permet de créer une table externe. Le fichier de données peut se trouver à l'extérieur du conteneur par défaut.
- La commande CREATE EXTERNAL TABLE ne déplace pas le fichier de données.
- La commande CREATE EXTERNAL TABLE n'autorise aucun sous-dossier dans le dossier spécifié dans la clause LOCATION. C'est la raison pour laquelle le didacticiel réalise une copie du fichier sample.log.

Pour plus d'informations, consultez la rubrique [HDInsight : introduction aux tables interne et externe Hive.][cindygross-hive-tables].

**Préparation du didacticiel**

1. Ouvrez Windows PowerShell ISE. (Dans l'écran d'accueil Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Pour plus d'informations, consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		Add-AzureAccount

	Vous êtes invité à entrer les informations d'identification de votre compte Azure. Cette méthode d'ajout d'une connexion à un abonnement expire ; 12 heures plus tard, vous devez à nouveau exécuter la cmdlet.

	> [AZURE.NOTE]Si vous disposez de plusieurs abonnements Azure et que vous ne souhaitez pas utiliser l'abonnement défini par défaut, utilisez la cmdlet <strong>Select-AzureSubscription</strong> pour sélectionner l'abonnement actuel.

3. Copiez le script suivant dans le volet de script, puis définissez les six premières variables :

		# WASB variables
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<BlobStorageContainerName>"

		# SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"  
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  
		$sqlDatabaseTableName = "log4jLogsCount"

		# Oozie files for the tutorial
		$workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
		$hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"

		# WASB folder for storing the Oozie tutorial files.
		$destFolder = "tutorials/useoozie"  # Do NOT use the long path here


	Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

3. Ajoutez ce qui suit au script dans le volet de script :

		# Create a storage context object
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		function uploadOozieFiles()
		{
		    Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
			Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
			Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
		}

		function prepareHiveDataFile()
		{
			Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
			Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
		}

		function prepareSQLDatabase()
		{
			# SQL query string for creating log4jLogsCount table
			$cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
				    [Level] [nvarchar](10) NOT NULL,
				    [Total] float,
				CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
				(
				[Level] ASC
				)
				)"

			#Create the log4jLogsCount table
		    Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
			$conn = New-Object System.Data.SqlClient.SqlConnection
			$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
			$conn.open()
			$cmd = New-Object System.Data.SqlClient.SqlCommand
			$cmd.connection = $conn
			$cmd.commandtext = $cmdCreateLog4jCountTable
			$cmd.executenonquery()

			$conn.close()
		}

		# upload workflow.xml, coordinator.xml, and ooziewf.hql
		uploadOozieFiles;

		# make a copy of example/data/sample.log to example/data/log4j/sample.log
		prepareHiveDataFile;

		# create log4jlogsCount table on SQL database
		prepareSQLDatabase;

4. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script. La sortie doit ressembler à ceci :

	![Sortie de la préparation du didacticiel][img-preparation-output]

##Exécution du projet Oozie

Azure PowerShell ne fournit actuellement aucune cmdlet pour la définition de tâches Oozie. Vous pouvez utiliser la cmdlet **Invoke-RestMethod** pour appeler les services web Oozie. L'API des services web Oozie est une API JSON REST HTTP. Pour plus d'informations sur l'API des services web Oozie, consultez la page [Documentation sur Apache Oozie 4.0][apache-oozie-400] (pour la version 3.0 de HDInsight) ou [Documentation sur Apache Oozie 3.3.2][apache-oozie-332] (pour la version 2.1 de HDInsight).

**Envoi d'une tâche Oozie**

1. Ouvrez Windows PowerShell ISE. (Dans l'écran d'accueil Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Pour plus d'informations, consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).

3. Copiez le script suivant dans le volet de script, puis définissez les dix premières variables (ignorez la variable $storageUri).

		#HDInsight cluster variables
		$clusterName = "<HDInsightClusterName>"
		$clusterUsername = "<HDInsightClusterUsername>"
		$clusterPassword = "<HDInsightClusterUserPassword>"

		#Azure Blob storage variables
		$storageAccountName = "<StorageAccountName>"
		$storageContainerName = "<BlobContainerName>"
		$storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

		#Azure SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
		$sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"  

		#Oozie WF variables
		$oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
		$waitTimeBetweenOozieJobStatusCheck=10

		#Hive action variables
		$hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
		$hiveTableName = "log4jlogs"
		$hiveDataFolder = "$storageUri/tutorials/useoozie/data"
		$hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

		#Sqoop action variables
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
		$sqlDatabaseTableName = "log4jLogsCount"

		$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)


	Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

3. Ajoutez ce qui suit au script. Ce script définit la charge utile d'Oozie :

		#OoziePayload used for Oozie web service submission
		$OoziePayload =  @"
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>

		   <property>
		       <name>nameNode</name>
		       <value>$storageUrI</value>
		   </property>

		   <property>
		       <name>jobTracker</name>
		       <value>jobtrackerhost:9010</value>
		   </property>

		   <property>
		       <name>queueName</name>
		       <value>default</value>
		   </property>

		   <property>
		       <name>oozie.use.system.libpath</name>
		       <value>true</value>
		   </property>

		   <property>
		       <name>hiveScript</name>
		       <value>$hiveScript</value>
		   </property>

		   <property>
		       <name>hiveTableName</name>
		       <value>$hiveTableName</value>
		   </property>

		   <property>
		       <name>hiveDataFolder</name>
		       <value>$hiveDataFolder</value>
		   </property>

		   <property>
		       <name>hiveOutputFolder</name>
		       <value>$hiveOutputFolder</value>
		   </property>

		   <property>
		       <name>sqlDatabaseConnectionString</name>
		       <value>";$sqlDatabaseConnectionString";</value>
		   </property>

		   <property>
		       <name>sqlDatabaseTableName</name>
		       <value>$SQLDatabaseTableName</value>
		   </property>

		   <property>
		       <name>user.name</name>
		       <value>$clusterUsername</value>
		   </property>

		   <property>
		       <name>oozie.wf.application.path</name>
		       <value>$oozieWFPath</value>
		   </property>

		</configuration>
		"@

4. Ajoutez ce qui suit au script. Ce script vérifie l'état du service web Oozie :

	    Write-Host "Checking Oozie server status..." -ForegroundColor Green
	    $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieServerSatus = $jsonResponse[0].("systemMode")
	    Write-Host "Oozie server status is $oozieServerSatus..."

5. Ajoutez ce qui suit au script. Cette partie crée et démarre une tâche Oozie :

	    # create Oozie job
	    Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
	    Write-Host "`n--------`n$OoziePayload`n--------"
	    $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
	    $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $oozieJobId = $jsonResponse[0].("id")
	    Write-Host "Oozie job id is $oozieJobId..."

	    # start Oozie job
	    Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
	    $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=start"
	    $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6. Ajoutez ce qui suit au script. Ce script vérifie le statut de la tâche Oozie :

	    # get job status
	    Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
	    Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

	    Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
	    $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
	    $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
	    $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	    $JobStatus = $jsonResponse[0].("status")

	    while($JobStatus -notmatch "SUCCEEDED|KILLED")
	    {
	        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
	        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
	        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
	        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
	        $JobStatus = $jsonResponse[0].("status")
	    }

	    Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7. Si vous disposez du cluster HDInsight version 2.1, remplacez « https://$clusterName.azurehdinsight.net:443/oozie/v2/ » par « https://$clusterName.azurehdinsight.net:443/oozie/v1/ ». Le cluster HDInsight version 2.1 ne prend pas en charge la version 2 des services Web.

8. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script. La sortie doit ressembler à ceci :

	![Sortie du workflow exécuté par le didacticiel][img-runworkflow-output]

8. Connectez-vous à votre base de données SQL Azure pour voir les données exportées.

**Vérification du journal des erreurs de la tâche**

Pour résoudre les problèmes d'un workflow, le fichier journal Oozie se trouve à l'emplacement *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* ou *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* à partir du nœud principal du cluster. Pour plus d’informations, consultez la rubrique [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][hdinsight-admin-portal].

**Réexécution du didacticiel**

Pour réexécuter le workflow, vous devez supprimer les éléments suivants :

- Le fichier de sortie du script Hive
- Les données dans la table log4jLogsCount

Voici un exemple d'un script Windows PowerShell que vous pouvez utiliser :

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<ContainerName>"

	#SQL database variables
	$sqlDatabaseServer = "<SQLDatabaseServerName>"
	$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
	$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
	$sqlDatabaseName = "<SQLDatabaseName>"
	$sqlDatabaseTableName = "log4jLogsCount"

	Write-host "Delete the Hive script output file ..." -ForegroundColor Green
	$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
	Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

	Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
	$conn = New-Object System.Data.SqlClient.SqlConnection
	$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
	$conn.open()
	$cmd = New-Object System.Data.SqlClient.SqlCommand
	$cmd.connection = $conn
	$cmd.commandtext = "delete from $sqlDatabaseTableName"
	$cmd.executenonquery()

	$conn.close()


##Étapes suivantes
Dans ce didacticiel, vous avez appris à définir un workflow Oozie et à exécuter une tâche Oozie en utilisant Windows PowerShell. Pour en savoir plus, consultez les articles suivants :

- [Utilisation du coordinateur Oozie basé sur le temps avec HDInsight][hdinsight-oozie-coordinator-time]
- [Prise en main de Hadoop avec Hive dans HDInsight pour analyser l'utilisation des téléphones mobiles][hdinsight-get-started]
- [Prise en main de l'émulateur HDInsight][hdinsight-get-started-emulator]
- [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage]
- [Administration de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell]
- [Téléchargement de données pour les tâches Hadoop dans HDInsight][hdinsight-upload-data]
- [Utilisation de Sqoop avec Hadoop dans HDInsight][hdinsight-use-sqoop]
- [Utilisation de Hive avec Hadoop sur HDInsight][hdinsight-use-hive]
- [Utilisation de Pig avec Hadoop sur HDInsight][hdinsight-use-pig]
- [Développement de tâches de diffusion Hadoop en C# pour HDInsight][hdinsight-develop-streaming-jobs]
- [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: ../data-factory/data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: ../sql-database-create-configure.md
[sqldatabase-get-started]: ../sql-database-get-started.md

[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/fr-fr/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=July15_HO3-->