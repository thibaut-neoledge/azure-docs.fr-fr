<properties urlDisplayName="Use Hadoo Sqoop in HDInsight" pageTitle="Utilisation de Hadoop Sqoop dans HDInsight | Azure" metaKeywords="" description="Learn how to use Azure PowerShell from a workstation to run Sqoop import and export between an Hadoop cluster and an Azure SQL database." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Use Hadoop Sqoop in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# Utilisation de Sqoop avec Hadoop dans HDInsight
 
Découvrez comment utiliser Azure PowerShell et le Kit de développement logiciel (SDK) HDInsight .NET à partir d'un poste de travail, afin d'exécuter des commandes Sqoop import et export entre un cluster HDInsight et une base de données SQL Azure ou SQL Server.

##Dans cet article

- [Qu'est-ce que Sqoop ?](#whatissqoop)
- [Configuration requise](#prerequisites)
- [Présentation du scénario du didacticiel](#scenario)
- [Préparation du didacticiel](#prepare)
- [Exécution d'une commande Sqoop export à l'aide de PowerShell](#export)
- [Exécution d'une commande Sqoop export à l'aide du Kit de développement logiciel (SDK) HDInsight](#export-sdk)
- [Exécution d'une commande Sqoop import à l'aide de PowerShell](#import)
- [Étapes suivantes](#nextsteps)


## <a id="whatissqoop"></a> Qu'est-ce que Sqoop ?

Alors qu'il semble naturel de choisir Hadoop pour traiter des données non structurées et semi-structurées, telles que des journaux et des fichiers, il peut également s'avérer nécessaire de traiter les données structurées stockées dans des bases de données relationnelles.

[Sqoop][sqoop-user-guide-1.4.4] est un outil conçu pour transférer des données entre les clusters Hadoop et les bases de données relationnelles. Il permet d'importer des données depuis un système de gestion de base de données relationnelle (ou SGBDR) tel que SQL, MySQL ou Oracle vers HDFS (Hadoop Distributed File System), de transformer les données dans Hadoop avec MapReduce ou Hive, puis de les réexporter dans un SGBDR. Dans ce didacticiel, vous allez utiliser une base de données SQL comme base de données relationnelle.

Pour obtenir la liste des versions Sqoop prises en charge par les clusters HDInsight, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight][hdinsight-versions].




##<a id="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Poste de travail** - Ordinateur sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install]. Pour exécuter des scripts PowerShell, vous devez exécuter Azure PowerShell en tant qu'administrateur et définir la stratégie d'exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][powershell-script].

- **Cluster Azure HDInsight**. Pour obtenir des instructions sur l'approvisionnement des clusters, consultez les rubriques [Prise en main de HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight][hdinsight-provision]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

	<table border="1">
	<tr><th>Propriété du cluster</th><th>Nom de la variable PowerShell</th><th>Valeur</th><th>Description</th></tr>
	<tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Nom du cluster HDInsight.</td></tr>
	<tr><td>Nom du compte de stockage Azure</td><td>$storageAccountName</td><td></td><td>Il s'agit du compte de stockage Azure disponible sur le cluster HDInsight. Pour ce didacticiel, utilisez le compte de stockage par défaut indiqué au cours du processus d'approvisionnement du cluster.</td></tr>
	<tr><td>Nom du conteneur d'objets blob Azure</td><td>$containerName</td><td></td><td>Dans cet exemple, utilisez le conteneur de stockage d'objets blob Azure utilisé pour le système de fichiers de cluster HDInsight par défaut. Par défaut, il porte le même nom que le cluster HDInsight.</td></tr>
	</table>

- **Base de données SQL Azure**. Vous devez configurer une règle de pare-feu pour que le serveur de base de données SQL autorise l'accès à partir de votre poste de travail. Pour des instructions sur la création d'une base de données SQL et la configuration d'un pare-feu, consultez la rubrique [Prise en main de la base de données SQL Azure][sqldatabase-get-started]. Cet article inclut un script PowerShell permettant de créer la table de base de données SQL nécessaire pour ce didacticiel. 

	<table border="1">
	<tr><th>Propriété de base de données SQL</th><th>Nom de la variable PowerShell</th><th>Valeur</th><th>Description</th></tr>
	<tr><td>Nom du serveur de base de données SQL</td><td>$sqlDatabaseServer</td><td></td><td>Serveur de base de données SQL depuis ou vers lequel Sqoop importe et exporte des données. </td></tr>
	<tr><td>Nom de connexion à la base de données SQL</td><td>$sqlDatabaseLogin</td><td></td><td>Nom de connexion à la base de données SQL.</td></tr>
	<tr><td>Mot de passe de connexion à la base de données SQL</td><td>$sqlDatabasePassword</td><td></td><td>Mot de passe de connexion à la base de données SQL.</td></tr>
	<tr><td>Nom de la base de données SQL</td><td>$sqlDatabaseName</td><td></td><td>Base de données SQL Azure depuis ou vers laquelle Sqoop importe et exporte des données. </td></tr>
	</table>

	> [WACOM.NOTE] Une base de données SQL Azure autorise par défaut les connexions aux services Azure tels que Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l'activer à partir du portail de gestion Azure. Pour obtenir des instructions sur la création d'une base de données SQL et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d'une base de données SQL][sqldatabase-create-configue]. 

* **SQL Server**. Si votre cluster HDInsight se trouve sur le même réseau virtuel Azure qu'un serveur SQL Server, vous pouvez utiliser les étapes décrites dans cet article pour importer et exporter des données vers une base de données SQL Server. Pour plus d'informations, consultez les articles suivants.

	> [WACOM.NOTE] > Azure HDInsight prend uniquement en charge les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec les réseaux virtuels basés sur des groupes d'affinités.

	* Pour **créer et configurer un réseau virtuel**, consultez la page [Tâches de configuration du réseau virtuel](http://msdn.microsoft.com/fr-fr/library/azure/jj156206.aspx).

		* Lorsque vous utilisez SQL Server **dans votre centre de données**, vous devez configurer le réseau virtuel comme étant *de site à site* ou de *point à site*.

			> [WACOM.NOTE] Pour les réseaux virtuels **de point à site** Virtual Networks, le serveur SQL Server doit exécuter l'application de configuration du client VPN, qui est disponible sur le **tableau de bord** de votre configuration Azure Virtual Network.

		* Lorsque vous utilisez SQL Server dans une **machine virtuelle Azure**, toute configuration de réseau virtuel peut être utilisée dès lors que la machine virtuelle qui héberge SQL Server est membre du même réseau virtuel que HDInsight.

	* Pour **approvisionner un cluster HDInsight sur un réseau virtuel**, consultez la page [Approvisionnement de clusters HDInsight au moyen d'options personnalisées](/fr-fr/documentation/articles/hdinsight-provision-clusters/)

	> [WACOM.NOTE] Le serveur SQL Server doit également autoriser l'authentification SQL. Vous devez utiliser une connexion SQL pour les étapes décrites dans cet article.

	<table border="1">
	<tr><th>Propriété de base de données SQL</th><th>Nom de la variable PowerShell</th><th>Valeur</th><th>Description</th></tr>
	<tr><td>Nom SQL Server</td><td>$sqlDatabaseServer</td><td></td><td>Serveur SQL Server depuis ou vers lequel Sqoop importe et exporte des données. </td></tr>
	<tr><td>Nom de connexion SQL Server</td><td>$sqlDatabaseLogin</td><td></td><td>Un nom de connexion SQL.</td></tr>
	<tr><td>Mot de passe de connexion SQL</td><td>$sqlDatabasePassword</td><td></td><td>Le mot de passe de connexion SQL.</td></tr>
	<tr><td>Nom de la base de données SQL Server</td><td>$sqlDatabaseName</td><td></td><td>La base de données depuis ou vers laquelle Sqoop importe et exporte des données. </td></tr>
	</table>


> [WACOM.NOTE] Renseignez les tableaux ci-dessus.  Cela vous sera utile pour ce didacticiel.

## <a id="scenario"></a>Présentation du scénario
Le cluster HDInsight inclut des exemples de données. Vous devrez utiliser les deux éléments suivants :

- Fichier journal log4j situé sous */example/data/sample.log*. Ce fichier inclut les journaux suivants :

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Table Hive *hivesampletable*, qui référence le fichier de données situé sous */hive/warehouse/hivesampletable*. Cette table contient des données relatives aux appareils mobiles. Le schéma de la table Hive est le suivant :

	<table border="1">
	<tr><th>Champ</th><th>Type de données</th></tr>
	<tr><td>clientid</td><td>string</td></tr>
	<tr><td>querytime</td><td>string</td></tr>
	<tr><td>market</td><td>string</td></tr>
	<tr><td>deviceplatform</td><td>string</td></tr>
	<tr><td>devicemake</td><td>string</td></tr>
	<tr><td>devicemodel</td><td>string</td></tr>
	<tr><td>state</td><td>string</td></tr>
	<tr><td>country</td><td>string</td></tr>
	<tr><td>querydwelltime</td><td>double</td></tr>
	<tr><td>sessionid</td><td>bigint</td></tr>
	<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
	</table>

Vous allez d'abord exporter les fichiers sample.log et hivesampletable vers la base de données SQL ou SQL Server, puis réimporter la table contenant les données relatives aux appareils mobiles dans HDInsight en utilisant la procédure suivante :

	/tutorials/usesqoop/importeddata

###Présentation du stockage HDInsight

HDInsight utilise le stockage d'objets blob Azure pour stocker des données.  Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage]. 

Lors de l'approvisionnement d'un cluster HDInsight, un compte Azure Storage et un conteneur de stockage d'objets blob spécifique de ce compte sont désignés en tant que système de fichiers par défaut, comme dans HDFS. En plus de ce compte de stockage, pendant la configuration, vous pouvez ajouter des comptes de stockage à partir du même abonnement Azure ou depuis d'autres abonnements Azure. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision]. Afin de simplifier le script PowerShell utilisé dans le cadre de ce didacticiel, tous les fichiers sont conservés dans le conteneur de système de fichiers par défaut, sous */tutorials/usesqoop*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight. 
La syntaxe WASB est :

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Seule la syntaxe *wasb://* est prise en charge dans le cluster HDInsight version 3.0. L'ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais pas dans les clusters HDInsight 3.0, et elle ne sera pas prise en charge dans les versions ultérieures.

> [WACOM.NOTE] Le chemin d'accès WASB est un chemin d'accès virtuel.  Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage]. 

HDInsight peut accéder aux fichiers stockés dans le conteneur de système de fichiers par défaut via n'importe lequel des URI suivants (cet exemple utilise le fichier sample.log) :

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

	example/data/sample.log


##<a id="prepare"></a>Préparation du didacticiel

Vous allez créer deux tables dans la base de données SQL ou SQL Server. Elles seront utilisées pour la commande Sqoop export plus loin dans ce didacticiel. et prétraiter le fichier sample.log afin qu'il puisse être traité par Sqoop.

###Création d'une table SQL

**Pour une base de données SQL Azure**

1. Ouvrez Windows PowerShell ISE (dans l'écran d'accueil Windows 8, tapez **PowerShell_ISE**, puis cliquez sur **Windows PowerShell ISE**. Consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).

2. Copiez le script suivant dans le volet de script, puis définissez les quatre premières variables.
		
		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>" 
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>" 

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	Pour accéder à d'autres descriptions des variables, consultez la section [Configuration requise](#prerequisites) de ce didacticiel. 

3. Ajoutez le script suivant dans le volet de script. Il s'agit des instructions SQL définissant les deux tables ainsi que leurs index cluster,  puisque la base de données SQL requiert un index cluster.

		# SQL query strings for creating tables and clustered indexes
		$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		    [t1] [nvarchar](50), 
		    [t2] [nvarchar](50), 
		    [t3] [nvarchar](50), 
		    [t4] [nvarchar](50), 
		    [t5] [nvarchar](50), 
		    [t6] [nvarchar](50), 
		    [t7] [nvarchar](50))"
		
		$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
		
		$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
		[clientid] [nvarchar](50), 
		[querytime] [nvarchar](50), 
		[market] [nvarchar](50), 
		[deviceplatform] [nvarchar](50), 
		[devicemake] [nvarchar](50), 
		[devicemodel] [nvarchar](50), 
		[state] [nvarchar](50), 
		[country] [nvarchar](50), 
		[querydwelltime] [float],
		[sessionid] [bigint],
		[sessionpagevieworder][bigint])"
		
		$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

4. Ajoutez le script suivant dans le volet de script afin d'exécuter les commandes SQL.

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()
		
		Write-Host "Close connection ..." -ForegroundColor Green		
		$conn.close()
		
		Write-Host "Done" -ForegroundColor Green
	
5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script. 
6. Utilisez le [portail de gestion Azure][azure-management-portal] pour examiner les tables et les index cluster.

**Pour SQL Server**

1. Ouvrez **SQL Server Management Studio** et connectez-vous au serveur SQL Server.

2. Créez une base de données nommée **sqoopdb**.

3. Sélectionnez la base de données **sqoopdb**, puis **Nouvelle requête** dans le ruban situé en haut de SQL Server Management Studio.

4. Entrez les informations suivantes dans la fenêtre de requête.

		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50), 
		 [t2] [nvarchar](50), 
		 [t3] [nvarchar](50), 
		 [t4] [nvarchar](50), 
		 [t5] [nvarchar](50), 
		 [t6] [nvarchar](50), 
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50), 
		 [querytime] [nvarchar](50), 
		 [market] [nvarchar](50), 
		 [deviceplatform] [nvarchar](50), 
		 [devicemake] [nvarchar](50), 
		 [devicemodel] [nvarchar](50), 
		 [state] [nvarchar](50), 
		 [country] [nvarchar](50), 
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

5. Utilisez la touche de fonction **F5** ou sélectionnez **! Exécuter** sur le ruban pour exécuter la requête. Le message suivant s'affiche normalement au-dessous de la requête.

		Command(s) completed successfully.

6. Fermez SQL Server Management Studio.

###Génération de données

Dans ce didacticiel, vous allez exporter un fichier journal log4j (fichier délimité) et une table Hive vers une base de données SQL.  Le fichier délimité est situé sous */example/data/sample.log*. Vous trouverez plus haut dans ce didacticiel quelques exemples de journaux log4j. Certaines lignes du fichier journal sont vides et d'autres similaires à ce qui suit :

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Cela convient pour d'autres exemples qui utilisent ces données, mais nous devons supprimer ces exceptions pour pouvoir importer le fichier dans la base de données SQL ou SQL Server. Notez que la présence de lignes vides ou contenant un nombre d'éléments inférieur au nombre de champs définis dans la table de base de données SQL entraînera l'échec de la commande Sqoop export. La table log4jlogs contient 7 champs de type chaîne.

**Pour prétraiter le fichier sample.log**

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		Add-AzureAccount

	Vous êtes invité à entrer les informations d'identification de votre compte Azure. Cette méthode d'ajout de connexion à un abonnement inclut un délai d'expiration, et vous devrez vous reconnecter au bout de 12 heures. 

	> [WACOM.NOTE] Si vous disposez de plusieurs abonnements Azure et que vous ne souhaitez pas utiliser l'abonnement défini par défaut, utilisez la cmdlet <strong>Select-AzureSubscription</strong> pour sélectionner l'abonnement actuel.

3. Copiez le script suivant dans le volet de script, puis définissez les deux premières variables.
		
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"
		
		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	Pour accéder à d'autres descriptions des variables, consultez la section [Configuration requise](#prerequisites) de ce didacticiel. 
 
4. Ajoutez le script suivant dans le volet de script.

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
		
		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
		
		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)
		
		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream
		
		# process the source blob
		$exString = "java.lang.Exception:"
		while(-Not $sReader.EndOfStream){
		    $line = $sReader.ReadLine()
		    $split = $line.Split(" ")
		
		    # remove the "java.lang.Exception" from the first element of the array
		    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		    if ($split[0] -eq $exString){
		        #create a new ArrayList to remove $split[0]
		        $newArray = [System.Collections.ArrayList] $split
		        $newArray.Remove($exString)
		
		        # update $split and $line
		        $split = $newArray
		        $line = $newArray -join(" ")
		    }
		
		    # remove the lines that has less than 7 elements
		    if ($split.count -ge 7){
		        write-host $line
		        $writeStream.WriteLine($line)
		    }
		}
		
		# Write to the destination blob
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream)

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script.  
6. Utilisez le portail de gestion, l'Explorateur de stockage Azure ou Azure PowerShell pour examiner le fichier de données modifié.  Le didacticiel [Prise en main de HDInsight][hdinsight-get-started] inclut un exemple de code présentant l'utilisation de PowerShell pour télécharger un fichier et afficher son contenu.


##<a id="export"></a>Exécution d'une commande Sqoop export à l'aide de PowerShell

Dans cette section, vous allez utiliser Azure PowerShell pour exécuter la commande Sqoop export en vue d'exporter une table Hive et un fichier de données vers une base de données SQL Azure ou SQL Server. Un exemple HDInsight .NET est fourni à la section suivante.

> [WACOM.NOTE] En dehors des informations de chaîne de connexion, les étapes décrites dans cette section doivent fonctionner pour une base de données SQL Azure ou SQL Server. Elles ont été testées avec la configuration suivante :
> 
> * **Configuration " de point à site " d'Azure Virtual Network** - Réseau virtuel connectant le cluster HDInsight à un serveur SQL Server dans un centre de données privé. Consultez la page [Configuration d'un VPN de site à site dans le portail de gestion](http://msdn.microsoft.com/fr-fr/library/azure/dn133792.aspx) pour plus d'informations.
> * **Azure HDInsight 3.1** - Consultez la page [Approvisionnement de clusters Hadoop dans HDInsight à l'aide d'options personnalisées](/fr-fr/documentation/articles/hdinsight-provision-clusters/) pour plus d'informations sur la création d'un cluster dans un réseau virtuel
> * **SQL Server 2014** - Configuré de manière à autoriser l'authentification SQL et à exécuter le package de configuration du client VPN pour établir une connexion sécurisée au réseau virtuel

**Pour exporter le fichier journal log4j**

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		Add-AzureAccount

	Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3. Copiez le script suivant dans le volet de script, puis définissez les sept premières variables.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_log4j = "/tutorials/usesqoop/data"
	
	Pour accéder à d'autres descriptions des variables, consultez la section [Configuration requise](#prerequisites) de ce didacticiel. 

	Notez que le fichier sample.log n'est pas spécifié dans la section $exportDir_log4j. Sqoop exportera les données de l'ensemble des fichiers dans ce dossier.

4. Ajoutez le script suivant dans le volet de script.

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	Notez que le délimiteur de champ est **\0x20**, qui est un espace. Le délimiteur est défini dans le script PowerShell de prétraitement du fichier sample.log. Pour plus d'informations sur **-m 1**, consultez le [guide d'utilisation de Sqoop][sqoop-user-guide-1.4.4].

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script.  
6. Passez en revue les données exportées dans le  [portail de gestion Azure][azure-management-portal].

**Pour exporter la table Hive hivesampletable**

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		Add-AzureAccount

	Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3. Copiez le script suivant dans le volet de script, puis définissez les sept premières variables.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"
		
		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$exportDir_mobile = "/hive/warehouse/hivesampletable"
	
	Pour accéder à d'autres descriptions des variables, consultez la section [Configuration requise](#prerequisites) de ce didacticiel. 

4. Ajoutez le script suivant dans le volet de script.
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
		
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script.   
6. Passez en revue les données exportées dans le  [portail de gestion Azure][azure-management-portal].



##<a id="export-sdk"></a>Exécution d'une commande Sqoop export à l'aide du Kit de développement logiciel (SDK) HDInsight .NET

Voici un exemple C# utilisant le Kit de développement logiciel (SDK) HDInsight .NET pour exécuter une commande Sqoop export. Pour plus d'informations sur l'utilisation du Kit de développement logiciel (SDK) HDInsight .NET, consultez la rubrique [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs].


	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace sqoopSDKSample
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Set the variables
	            string subscriptionID = "<AzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<AzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>";
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";
	
	            cmdExport = @"export";
				// Connection string for using Azure SQL Database.
				// Comment if using SQL Server
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
				// Connection string for using SQL Server.
				// Uncomment if using SQL Server
				//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
	
	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
	            {
	                Command = cmdExport,
	                StatusFolder = "/tutorials/usesqoop/jobStatus"
	            };
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);
	
	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);
	
	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);
	
	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());
	
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

Pour exécuter un fichier de script, remplacez :

	Command = cmdExport,

 par :

	File = "/tutorials/usesqoop/sqoopexport.txt",

Le fichier de script doit se trouver sur WASB.




##<a id="import"></a>Exécution d'une commande Sqoop import à l'aide de PowerShell

Dans cette section, vous allez réimporter la table log4j logs (précédemment exportée vers la base de données SQL) dans HDInsight.

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		Add-AzureAccount

	Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3. Copiez le script suivant dans le volet de script, puis définissez les sept premières variables.

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"
		
		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"
		
		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"
		
		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"
	
	Pour accéder à d'autres descriptions des variables, consultez la section [Configuration requise](#prerequisites) de ce didacticiel. 

4. Ajoutez le script suivant dans le volet de script.
	
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
		
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script. 
6. Utilisez le portail de gestion, l'Explorateur de stockage Azure ou Azure PowerShell pour examiner le fichier de données modifié.  Le didacticiel [Prise en main de HDInsight][hdinsight-get-started] inclut un exemple de code présentant l'utilisation de PowerShell pour télécharger un fichier et afficher son contenu.

##<a id="nextsteps"></a>Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

- [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie] : utilisez l'action Sqoop dans le flux de travail Oozie.
- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data] : Utilisez Hive pour analyser les données sur les retards de vol, puis Sqoop pour les exporter vers la base de données SQL.
- [Téléchargement de données vers HDInsight][hdinsight-upload-data] : découvrez d'autres méthodes pour télécharger des données vers HDInsight/le stockage d'objets blob Azure.


 

[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[sqldatabase-get-started]: ../sql-database-get-started/
[sqldatabase-create-configue]: ../sql-database-create-configure/

[powershell-start]: http://technet.microsoft.com/fr-fr/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/fr-fr/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!--HONumber=35_1-->
