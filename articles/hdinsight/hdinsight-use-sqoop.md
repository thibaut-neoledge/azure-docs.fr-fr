<properties
	pageTitle="Utilisation de Hadoop Sqoop dans HDInsight | Microsoft Azure"
	description="Découvrez comment utiliser Azure PowerShell à partir d'un poste de travail pour exécuter des commandes Sqoop import et export entre un cluster HDInsight et une base de données SQL Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="jgao"/>

#Utilisation de Sqoop avec Hadoop dans HDInsight (Windows)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Découvrez comment utiliser Azure PowerShell et le kit de développement logiciel (SDK) HDInsight .NET depuis une station de travail pour exécuter Sqoop afin d’importer et d’exporter entre un cluster HDInsight et une base de données SQL Azure ou une base de données SQL Server.

> [AZURE.NOTE]Les étapes décrites dans cet article peuvent être utilisées avec un cluster HDInsight Windows ou Linux. Toutefois, ces étapes fonctionnent uniquement à partir d’un client Windows.
>
> Si vous utilisez un client Linux, OS X ou Unix, et un serveur HDInsight Linux, consultez [Utilisation de Sqoop avec Hadoop dans HDInsight (SSH)](hdinsight-use-sqoop-mac-linux.md)

##Qu’est-ce que Sqoop ?

Bien que Hadoop soit préférable pour traiter des données non structurées et semi-structurées, telles que des journaux et des fichiers, il peut être également nécessaire de traiter les données structurées stockées dans des bases de données relationnelles.

[Sqoop][sqoop-user-guide-1.4.4] est un outil conçu pour transférer des données entre les clusters Hadoop et les bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQL Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR. Dans ce didacticiel, vous allez utiliser une base de données SQL Server comme base de données relationnelle.

Pour obtenir la liste des versions Sqoop prises en charge par les clusters HDInsight, consultez la rubrique [Nouveautés sur les versions de cluster fournies par HDInsight][hdinsight-versions].

##Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Pour exécuter des scripts Azure PowerShell, vous devez exécuter Azure PowerShell en tant qu’administrateur et définir la stratégie d’exécution sur *RemoteSigned*. Consultez la page [Exécution de scripts Windows PowerShell][powershell-script].

- **Cluster Azure HDInsight** : pour obtenir des instructions sur l'approvisionnement des clusters, consultez les rubriques [Prise en main de HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight][hdinsight-provision]. Vous aurez besoin des données suivantes pour suivre ce didacticiel :

	<table border="1">
<tr><th>Propriété du cluster</th><th>Nom de la variable Azure&#160;PowerShell</th><th>Valeur</th><th>Description</th></tr>
<tr><td>Nom du cluster HDInsight</td><td>$clusterName</td><td></td><td>Nom de votre cluster HDInsight</td></tr>
<tr><td>Nom du compte de stockage Azure</td><td>$storageAccountName</td><td></td><td>Un compte de stockage Azure disponible sur le cluster HDInsight. Pour ce didacticiel, utilisez le compte de stockage par défaut que vous avez indiqué au cours du processus d'approvisionnement du cluster.</td></tr>
<tr><td>Nom du conteneur de blob Azure</td><td>$containerName</td><td></td><td>Dans cet exemple, utilisez le nom du blob qui est utilisé pour le système de fichiers du cluster HDInsight par défaut. Par défaut, il porte le même nom que le cluster HDInsight.</td></tr>
</table>

- ****Base de données SQL Azure ou Microsoft SQL Server

	- **Base de données SQL Azure** : vous devez configurer une règle de pare-feu pour le serveur de base de données SQL Azure afin d’autoriser l'accès depuis votre station de travail. Pour des instructions sur la création d'une base de données SQL Azure et la configuration d'un pare-feu, consultez la rubrique [Prise en main de la base de données SQL Azure][sqldatabase-get-started]. Cet article inclut un script Windows PowerShell pour la création de la table de base de données SQL Azure dont vous avez besoin pour ce didacticiel.
	
		<table border="1">
<tr><th>Propriété de la base de données&#160;SQL&#160;Azure</th><th>Nom de la variable Azure&#160;PowerShell</th><th>Valeur</th><th>Description</th></tr>
<tr><td>Nom du serveur de base de données SQL Azure.</td><td>$sqlDatabaseServer</td><td></td><td>Le serveur de base de données&#160;SQL&#160;Azure vers lequel Sqoop exporte des données et depuis lequel il en importe. </td></tr>
<tr><td>Nom de connexion de la base de données&#160;SQL&#160;Azure.</td><td>$sqlDatabaseLogin</td><td></td><td>Votre nom de connexion pour la base de données SQL&#160;Azure.</td></tr>
<tr><td>Le mot de passe de connexion pour la base de données&#160;SQL&#160;Azure</td><td>$sqlDatabasePassword</td><td></td><td>Votre mot de passe de connexion pour la base de données SQL&#160;Azure.</td></tr>
<tr><td>Nom de la base de données SQL&#160;Azure</td><td>$sqlDatabaseName</td><td></td><td>La base de données&#160;SQL&#160;Azure vers laquelle Sqoop va exporter des données et depuis laquelle il va en importer. </td></tr>
</table>> [AZURE.NOTE]Par défaut, une base de données SQL Azure autorise des connexions aux services Azure tels que Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l'activer depuis la version préliminaire du portail Azure. Pour obtenir des instructions sur la création d'une base de données SQL Azure et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d'une base de données SQL][sqldatabase-create-configue].
	
	* **SQL Server** : si votre cluster HDInsight se trouve sur le même réseau virtuel que SQL Server dans Azure, vous pouvez utiliser les étapes décrites dans cet article pour importer et exporter des données vers une base de données SQL Server.
	
		> [AZURE.NOTE]HDInsight prend en charge uniquement les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d'affinités.
	
		* Pour créer et configurer un réseau virtuel, consultez la rubrique [Tâches de configuration du réseau virtuel](../services/virtual-machines/).
	
			* Lorsque vous utilisez SQL Server dans votre centre de données, vous devez configurer le réseau virtuel comme étant *de site à site* ou *de point à site*.
	
				> [AZURE.NOTE]Pour les réseaux virtuels de **point à site**, SQL Server doit exécuter l'application de configuration du client VPN, qui est disponible depuis le **tableau de bord** de la configuration de votre réseau virtuel Azure.
	
			* Lorsque vous utilisez SQL Server sur une machine virtuelle Azure, toute configuration du réseau virtuel peut être utilisée si la machine virtuelle qui héberge SQL Server est membre du même réseau virtuel que HDInsight.
	
		* Pour l’approvisionnement d’un cluster HDInsight sur un réseau virtuel, consultez la rubrique [Approvisionnement des clusters Hadoop dans HDInsight à l’aide des options personnalisées](hdinsight-provision-clusters.md)
	
		> [AZURE.NOTE]SQL Server doit également autoriser l'authentification. Vous devez utiliser une connexion SQL Server pour compléter les étapes décrites dans cet article.
	
		<table border="1">
<tr><th>Propriété de la base de données&#160;SQL&#160;Server</th><th>Nom de la variable Azure&#160;PowerShell</th><th>Valeur</th><th>Description</th></tr>
<tr><td>Nom SQL Server</td><td>$sqlDatabaseServer</td><td></td><td>Serveur SQL Server depuis ou vers lequel Sqoop importe et exporte des données. </td></tr>
<tr><td>Nom de connexion SQL Server</td><td>$sqlDatabaseLogin</td><td></td><td>Votre nom de connexion pour SQL&#160;Server.</td></tr>
<tr><td>Mot de passe de connexion SQL&#160;Server</td><td>$sqlDatabasePassword</td><td></td><td>Votre mot de passe de connexion pour SQL&#160;Server.</td></tr>
<tr><td>Nom de la base de données&#160;SQL Server</td><td>$sqlDatabaseName</td><td></td><td>La base de données SQL&#160;Server vers laquelle Sqoop exportera des données et depuis laquelle il en importera. </td></tr>
</table>


> [AZURE.NOTE]Remplissez les valeurs dans les tables précédentes. Cela vous sera utile pour ce didacticiel.

##Présentation du scénario
Le cluster HDInsight inclut des exemples de données. Vous devrez utiliser les deux éléments suivants :

- Un fichier journal log4j situé sous */example/data/sample.log*. Ce fichier inclut les journaux suivants :

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- Une table Hive appelée *hivesampletable*, qui référence le fichier de données situé sous */hive/warehouse/hivesampletable*. Cette table contient des données relatives aux appareils mobiles. Le schéma de la table Hive est le suivant :

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

Vous allez d'abord exporter *sample.log* et *hivesampletable* vers la base de données SQL Azure ou vers SQL Server, puis importer à nouveau la table contenant les données de l’appareil mobile dans HDInsight en utilisant la procédure suivante :

	/tutorials/usesqoop/importeddata

###Présentation du stockage HDInsight

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Lors de l'approvisionnement d'un cluster HDInsight, un compte de stockage Azure et un conteneur de stockage d'objets blob spécifique de ce compte sont indiqués en tant que système de fichiers par défaut, comme dans HDFS. En plus de ce compte de stockage, pendant le processus d’approvisionnement, vous pouvez ajouter des comptes de stockage supplémentaires à partir du même abonnement Azure ou à partir d'autres abonnements Azure.

Pour plus d'instructions sur l’ajout des comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision]. Afin de simplifier le script Windows PowerShell utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur du système de fichiers par défaut, sous */tutorials/usesqoop*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight. La syntaxe est :

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]Seule la syntaxe **wasb://* est prise en charge dans la version 3.0 du cluster HDInsight. L’ancienne syntaxe **asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais elle n’est pas prise en charge dans les clusters HDInsight 3.0.

> [AZURE.NOTE]Le chemin d’accès **wasb://* est un chemin d’accès virtuel. Pour plus d’informations, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage].

Un fichier stocké dans le blob du système de fichiers par défaut est accessible depuis HDInsight à l’aide de n’importe lequel des URI suivants (les exemples utilisent le fichier sample.log) :

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

	example/data/sample.log


##Préparation du didacticiel

Vous allez créer deux tables dans la base de données SQL Azure ou dans SQL Server. Elles seront utilisées pour Sqoop pour être exportées plus tard dans ce didacticiel. Vous devrez également traiter les fichiers sample.log afin qu'ils puissent être traités par Sqoop.

###Création d'une table SQL

**Pour une base de données SQL Azure**

1. Ouvrez Windows PowerShell ISE (dans l'écran d'accueil Windows 8, tapez **PowerShell\_ISE**, puis cliquez sur **Windows PowerShell ISE**. Consultez la page [Démarrage de Windows PowerShell sur Windows 8 et Windows][powershell-start]).

2. Copiez le script suivant dans le volet de script, puis définissez les quatre premières variables.

		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

3. Ajoutez le script suivant dans le volet de script. Il s'agit des instructions SQL qui définissent les deux tables et leurs index cluster. La base de données SQL Azure requiert un index cluster.

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

4. Ajoutez le script suivant dans le volet de script afin d'exécuter les commandes SQL :

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
6. Contrôlez les tables et les index de cluster dans la [version préliminaire du portail][azure-management-portal].

**Pour SQL Server**

1. Ouvrez **SQL Server Management Studio** et connectez-vous à SQL Server.

2. Créez une base de données nommée **sqoopdb**.

3. Sélectionnez la base de données **sqoopdb**, puis sélectionnez **Nouvelle requête** dans le ruban supérieur de SQL Server Management Studio.

4. Saisissez les informations suivantes dans la fenêtre Requête.

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

5. Cliquez sur **F5**, ou sélectionnez ** ! Exécuter** sur le ruban pour exécuter la requête. Le message suivant s'affiche normalement sous la requête :

		Command(s) completed successfully.

6. Fermez SQL Server Management Studio.

###Génération de données

Dans ce didacticiel, vous allez exporter un fichier journal log4j (fichier délimité) et une table Hive vers une base de données SQL Azure. Le fichier délimité s’appelle */example/data/sample.log*. Vous trouverez plus haut dans ce didacticiel quelques exemples de journaux log4j. Certaines lignes du fichier journal sont vides et d'autres ressemblent à ce qui suit :

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

Cela convient pour d'autres exemples qui utilisent ces données, mais nous devons supprimer ces exceptions pour pouvoir importer le fichier dans la base de données SQL Azure ou SQL Server. Notez que la présence de lignes vides ou contenant un nombre d'éléments inférieur au nombre de champs définis dans la table de la base de données SQL Azure entraînera l'échec de la commande Sqoop export. La table log4jlogs contient 7 champs de type chaîne.

**Pour traiter le fichier sample.log**

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		Add-AzureAccount

	Vous êtes invité à entrer les informations d'identification de votre compte Azure. Cette méthode d'ajout de connexion à un abonnement inclut un délai d'expiration, et vous devrez vous reconnecter au bout de 12 heures.

	> [AZURE.NOTE]Si vous disposez de plusieurs abonnements Azure et que vous ne souhaitez pas utiliser l'abonnement défini par défaut, utilisez la cmdlet <strong>Select-AzureSubscription</strong> pour sélectionner l'abonnement actuel.

3. Copiez le script suivant dans le volet de script, puis définissez les deux premières variables.

		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

4. Ajoutez le script suivant dans le volet de script :

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
6. Pour examiner le fichier de données modifiées, vous pouvez utiliser la version préliminaire du portail Azure, un outil d'exploration Azure Storage, ou Azure PowerShell. Le didacticiel [Prise en main de HDInsight][hdinsight-get-started] inclut un exemple de code présentant l’utilisation de Azure PowerShell pour télécharger un fichier et afficher son contenu.


##Exécution d'une commande Sqoop export à l'aide de PowerShell

Dans cette section, vous allez utiliser Azure PowerShell pour exécuter la commande Sqoop export en vue d'exporter une table Hive et un fichier de données vers une base de données SQL Azure ou vers SQL Server. Un exemple HDInsight .NET est fourni à la section suivante.

> [AZURE.NOTE]En dehors des informations de la chaîne de connexion, les étapes décrites dans cette section doivent fonctionner pour une base de données SQL Azure ou pour SQL Server. Elles ont été testées avec la configuration suivante :
>
> * **Configuration de point à site du réseau virtuel Azure** : un réseau virtuel connectant le cluster HDInsight à un serveur SQL Server dans un centre de données privé. Pour plus d'informations, consultez la page [Configuration d'un réseau privé virtuel (VPN) de point à site dans le portail de gestion](../vpn-gateway/vpn-gateway-point-to-site-create.md).
> * **Azure HDInsight 3.1** : pour plus d'informations sur la création d'un cluster sur un réseau virtuel, consultez la rubrique [Approvisionnement de clusters HDInsight à l’aide d'options personnalisées](hdinsight-provision-clusters.md).
> * **SQL Server 2014** : configuré de manière à autoriser l'authentification et à exécuter le package de configuration du client VPN pour établir une connexion sécurisée au réseau virtuel.

**Pour exporter le fichier journal log4j**

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

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

	Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

	Notez que $exportDir\_log4j n’a pas le nom de fichier sample.log spécifié. Sqoop exportera les données de l'ensemble des fichiers dans ce dossier.

4. Ajoutez le script suivant dans le volet de script :

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	Notez que le délimiteur de champ est **\\0x20**, qui est un espace. Le délimiteur est défini dans le fichier sample.log du script Azure PowerShell. Pour plus d'informations sur **-m 1**, consultez le [Guide d'utilisation de Sqoop][sqoop-user-guide-1.4.4].

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script.
6. Examinez les données exportées dans la [version préliminaire du portail][azure-management-portal].

**Pour exporter la table Hive hivesampletable**

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

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

	Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

4. Ajoutez le script suivant dans le volet de script :

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"


		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script.
6. Examinez les données exportées dans la [version préliminaire du portail][azure-management-portal].

##Exécutez la commande Sqoop export à l'aide du kit de développement logiciel (SDK) HDInsight .NET

Dans cette section, vous allez créer une application console C# pour exporter la table hivesampletable vers la table de base de données SQL créée précédemment dans ce didacticiel.

**Pour envoyer une tâche Sqoop**

1. Dans la console du gestionnaire de package Visual Studio, exécutez la commande Nuget suivante pour importer le package.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
2. Utilisez les instructions using suivantes dans le fichier Program.cs :

		using System;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
3. Dans la fonction Main(), ajoutez le code suivant. Pour plus d'informations sur l'utilisation du kit de développement logiciel (SDK) HDInsight .NET, consultez la rubrique [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs].

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
		
		var sqlDatabaseServerName = "<AzureSQLDatabaseServerName>";
		var sqlDatabaseLogin = "<AzureSQLDatabaseLogin>";
		var sqlDatabaseLoginPassword = "<AzureSQLDatabaseLoginPassword>";
		var sqlDatabaseDatabaseName = "<AzureSQLDatabaseDatabaseName>";
		
		var sqlDatabaseTableName = "log4jlogs";
		var exportDir = "/hive/warehouse/hivesampletable";

		var cmdExport = @"export";
		// Connection string for using Azure SQL Database.
		// Comment if using SQL Server
		cmdExport = cmdExport + @" --connect 'jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName +"'"; 
		// Connection string for using SQL Server.
		// Uncomment if using SQL Server
		//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
		cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
		cmdExport = cmdExport + @" --export-dir " + exportDir;
		cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";
		
		HDInsightJobManagementClient _hdiJobManagementClient;
		var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		_hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

		var parameters = new SqoopJobSubmissionParameters
		{
		    UserName = ExistingClusterUsername,
		    Command = cmdExport
		};
		
		System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
		var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
		System.Console.WriteLine("Validating that the response is as expected...");
		System.Console.WriteLine("Response status code is " + response.StatusCode);
		System.Console.WriteLine("Validating the response object...");
		System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		Console.WriteLine("Press ENTER to continue ...");
		Console.ReadLine();
4. Appuyez sur **F5** pour exécuter le programme. 

##Exécutez la commande Sqoop import à l'aide d’Azure PowerShell

Dans cette section, vous allez importer à nouveau les journaux log4j (que vous aviez exporté vers la base de données SQL Azure) dans HDInsight.

1. Ouvrez Windows PowerShell ISE.
2. Dans le volet inférieur, exécutez la commande suivante pour vous connecter à votre abonnement Azure :

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

	Pour plus d'informations sur les variables, consultez la section [Conditions préalables](#prerequisites) de ce didacticiel.

4. Ajoutez le script suivant dans le volet de script :

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. Cliquez sur **Exécuter le script** ou appuyez sur **F5** pour exécuter le script.
6. Pour examiner le fichier de données modifiées, vous pouvez utiliser la version préliminaire du portail Azure, un outil d'exploration Azure Storage, ou Azure PowerShell. Le didacticiel [Prise en main de HDInsight][hdinsight-get-started] inclut un exemple de code présentant l'utilisation de Azure PowerShell pour télécharger un fichier et afficher son contenu.

##Étapes suivantes

Vous maîtrisez à présent l'utilisation de Sqoop. Pour plus d'informations, consultez les rubriques suivantes :

- [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie] : utilisez l’action Sqoop dans un flux de travail Oozie.
- [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data] : utilisez Hive pour analyser des données sur les retards de vol, puis utilisez Sqoop pour exporter ces données vers une base de données SQL Azure.
- [Téléchargement de données vers HDInsight][hdinsight-upload-data] : découvrez d'autres méthodes pour télécharger des données vers HDInsight ou le stockage d'objets blob Azure.




[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=Oct15_HO3-->