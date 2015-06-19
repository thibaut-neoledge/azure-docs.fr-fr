<properties 
	pageTitle="Téléchargement de données pour les tâches Hadoop dans HDInsight | Microsoft Azure" 
	description="Apprenez à télécharger des données et à y accéder dans HDInsight avec l'Explorateur du stockage Azure, Azure PowerShell, la ligne de commande Hadoop ou Sqoop." 
	services="storage, hdinsight" 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#Téléchargement de données pour les tâches Hadoop dans HDInsight

Azure HDInsight fournit un système HDFS (Hadoo Distributed File System) complet pour le stockage d'objets blob Azure. Il est conçu en tant qu’extension HDFS pour fournir une expérience transparente aux clients. Il permet à l'ensemble des composants de l'écosystème Hadoop de fonctionner directement sur les données qu'il gère. Le stockage d'objets blob Azure et HDFS sont des systèmes de fichiers distincts qui sont optimisés pour le stockage de données et pour les calculs réalisés à partir de ces données. Pour connaître les avantages que constitue l'utilisation du stockage d'objets blob Azure, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Les clusters Azure HDInsight sont généralement déployés pour exécuter des tâches MapReduce et sont supprimés une fois ces tâches terminées. Conserver les données dans les clusters HDFS une fois les calculs terminés serait une façon onéreuse de stocker ces données. Le stockage d'objets blob Azure constitue une option de stockage à long terme économique, partageable, hautement évolutive et disponible pour les données qui doivent être traitées à l'aide de HDInsight. Le stockage de données dans un objet blob permet de libérer en toute sécurité les clusters HDInsight utilisés pour les calculs, sans perte de données.

Vous pouvez accéder au stockage d'objets blob Azure via [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], la [bibliothèque du client de stockage Windows Azure pour .NET][azure-storage-client-library], l’[interface de ligne de commande Azure pour Mac, Linux et Windows][xplatcli] ou des outils d'explorateur. Voici certains des outils disponibles :

* [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

##Configuration requise

Notez la configuration requise avant de démarrer :

* Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d'Azure HDInsight][hdinsight-get-started] ou la rubrique [Mise en service de clusters HDInsight][hdinsight-provision].


##<a id="azcopy"></a>Téléchargement de données vers le stockage d'objets blob Azure avec AzCopy##

AzCopy est un utilitaire de ligne de commande conçu pour simplifier la tâche de transfert de données à destination et en provenance d'un compte Azure Storage. Vous pouvez l'utiliser comme un outil indépendant ou l'incorporer dans une application existante. [Téléchargement d'AzCopy][azure-azcopy-download].

La syntaxe d'AzCopy est :

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Pour plus d'informations, consultez la page [AzCopy : téléchargement de fichiers pour les objets blob Azure][azure-azcopy].

##<a id="powershell"></a>Téléchargement de données vers le stockage d'objets blob avec Azure PowerShell##

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Vous pouvez utiliser Azure PowerShell pour télécharger des données vers le stockage d'objets blob Azure, pour traiter les données avec des tâches MapReduce. Pour plus d'informations sur la configuration de votre poste de travail pour exécuter Azure PowerShell, consultez l'article [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

**Téléchargement d'un fichier local vers un stockage d'objets blob Azure**

1. Ouvrez la fenêtre de la console Azure PowerShell, comme indiqué dans la section [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).
2. Définissez les valeurs des cinq premières variables dans le script suivant :

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. Collez le script dans la console Azure PowerShell pour l'exécuter.

Les conteneurs de stockage d'objets blob Azure stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser le caractère « / » dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. Par exemple, une clé d'objet blob peut être *input/log1.txt*. Il n'existe pas de répertoire « input », mais le caractère « / » figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier. Dans le script précédent, vous pouvez donner une structure de dossiers au fichier, en configurant la variable **$blobname**, par exemple, *$blobname=»myfolder/myfile.txt»*.

Lorsque vous utilisez les outils d'Azure Explorer, vous pouvez remarquer la présence de certains fichiers de 0 octet. Ces fichiers ont deux utilités :

- S'il existe des dossiers vides, ils servent à indiquer l'existence du dossier. Le stockage d'objets blob Azure sait que si un objet blob est nommé foo/bar, c'est qu'il y a un dossier nommé **foo**. Mais la seule façon d'indiquer un dossier vide nommé **foo** est de disposer de ces fichiers de 0 octet.
- Ils contiennent des métadonnées spécifiques requises par le système de fichiers Hadoop, notamment les autorisations et les propriétaires des dossiers.


##<a id="xplatcli"></a>Téléchargement de données vers le stockage d'objets blob Azure avec l'interface de ligne de commande Azure

L’interface de ligne de commande Azure pour Mac, Linux et Windows est un outil interplateforme qui vous permet de gérer les services Azure. Pour télécharger des données vers le stockage d'objets blob Azure, procédez comme suit :

1. [Installation et configuration de l'interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli.md).

2. Ouvrez une invite de commandes, un bash ou un autre shell et procédez comme suit pour vous authentifier auprès de votre abonnement Azure.

		azure login

	Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe de votre abonnement.

3. Entrez la commande suivante pour répertorier les comptes de stockage pour votre abonnement :

		azure storage account list

4. Sélectionnez le compte de stockage qui contient l'objet blob que vous souhaitez utiliser, puis entrez la commande suivante pour récupérer la clé de ce compte :

		azure storage account keys list <storage-account-name>

	Cette commande doit renvoyer les clés **primaires** et **secondaires** clés. Copiez la valeur de la clé **primaire**, car vous devrez l’utiliser lors des étapes suivantes.

5. Utilisez la commande suivante pour récupérer une liste de conteneurs d'objets blob dans le compte de stockage :

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Utilisez les commandes suivantes pour charger et télécharger des fichiers vers l'objet blob :

	* Pour charger un fichier :

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* Pour télécharger un fichier :

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Si vous utilisez toujours le même compte de stockage, vous pouvez définir les variables d'environnement suivantes au lieu de spécifier le compte et la clé de chaque commande :
> 
> * **AZURE_STORAGE_ACCOUNT**  le nom du compte de stockage
> 
> * **AZURE_STORAGE_ACCESS_KEY** : la clé du compte de stockage

##<a id="storageexplorer"></a>Téléchargement de données vers le stockage d'objets blob avec Azure Storage Explorer

*Azure Storage Explorer* est un outil utile pour examiner et modifier les données du compte Azure Storage. Il s'agit d'un outil gratuit que vous pouvez télécharger depuis CodePlex : [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Avant de l'utiliser, vous devez connaître le nom et la clé de votre compte Azure Storage. Pour obtenir des instructions permettant d'obtenir ces informations, consultez la section « Affichage, copie et régénération des clés d'accès de stockage » de l'article [Création, gestion et suppression d’un compte de stockage][azure-create-storage-account].

1. Exécutez Azure Storage Explorer.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Cliquez sur **Ajouter un compte**. Lorsque vous aurez ajouté un compte à Azure Storage Explorer, vous n'aurez plus besoin de repasser par cette étape.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Entrez le **nom de votre compte de stockage** et la **clé de votre compte de stockage**, puis cliquez sur **Ajouter le compte de stockage**. Vous pouvez ajouter plusieurs comptes de stockage, et chaque compte sera affiché sous un onglet.
4. Sous **Type de stockage**, cliquez sur **Objets blob**.

	![HDI.ASEBlob][image-ase-blob]

5. ﻿Dans **Conteneur**, cliquez sur le nom du conteneur associé à votre cluster HDInsight. Lorsque vous créez un cluster HDInsight, vous devez indiquer un conteneur. Sinon, le processus de création de cluster en crée un pour vous.
6. Sous **Objets blob**, cliquez sur **Téléchargement**.
7. Indiquez un fichier à télécharger, puis cliquez sur **Ouvrir**.








































































##<a id="commandline"></a>Téléchargement de données vers le stockage d'objets blob Azure avec la ligne de commande Hadoop

Pour utiliser la ligne de commande Hadoop, vous devez d'abord vous connecter au cluster avec le Bureau à distance :



1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **HDINSIGHT**. Une liste de clusters Hadoop déployés s'affiche.
3. Cliquez sur le cluster HDInsight sur lequel vous voulez télécharger des données.
4. En haut de la page, cliquez sur **CONFIGURATION**.
5. Cliquez sur **ENABLE REMOTE** si vous n'avez pas activé le Bureau à distance, puis suivez les instructions. Sinon, passez à l'étape suivante.
4. En bas de la page, cliquez sur **CONNECT**.
7. Cliquez sur **Ouvrir**.
8. Entrez vos informations d'identification, puis cliquez sur **OK**.
9. Cliquez sur **Oui**.
10. Depuis le Bureau, cliquez sur **Ligne de commande Hadoop**.
12. L'exemple de code suivant montre comment copier le fichier davinci.txt à partir du système de fichiers local du nœud principal HDInsight vers le répertoire /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Comme le système de fichiers par défaut est dans le stockage d'objets blob Azure, /example/datadavinci.txt s'y trouve également. Vous pouvez également faire référence au fichier comme ceci :

		wasb:///example/data/davinci.txt 

	ou

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	Le nom de domaine complet est requis lorsque vous utilisez *wasbs*.

13. Utilisez la commande suivante pour répertorier les fichiers téléchargés :

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Importation de données vers HDFS à partir d'une base de données SQL Azure ou d'un serveur SQL avec Sqoop

Sqoop est un outil conçu pour transférer des données entre Hadoop et des bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQ Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR. Pour plus d'informations, consultez le [guide d'utilisation de Sqoop][apache-sqoop-guide].

Avant d'importer les données, vous devez connaître le nom du serveur qui héberge la base de données SQL Azure, le nom du compte de la base de données,le mot de passe du compte et le nom de la base de données.

Par défaut, une base de données SQL Azure autorise des connexions aux services Azure tels que Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l'activer depuis le portail de gestion Azure. Pour obtenir des instructions sur la création d'une base de données SQL Azure et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d'une base de données SQL][sqldatabase-create-configure].

La procédure suivante utilise Azure PowerShell pour envoyer une tâche Sqoop.

**Importation de données vers HDInsight avec Sqoop et Azure PowerShell**

1. Ouvrez la fenêtre de la console Azure PowerShell, comme indiqué dans la section [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).
2. Définissez les valeurs des huit premières variables dans le script suivant :

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. Collez le script dans la console Azure PowerShell pour l'exécuter. Pour obtenir un exemple permettant de récupérer le fichier de données à partir du stockage d'objets blob Azure, consultez le didacticiel [Prise en main de HDInsight][hdinsight-get-started].

Pour plus d'informations sur l’utilisation de Sqoop, consultez la rubrique [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop].

## Étapes suivantes
Maintenant que vous savez comment obtenir des données avec HDInsight, consultez les articles suivants pour apprendre à effectuer des analyses :

* [Prise en main d'Azure HDInsight][hdinsight-get-started]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-azcopy-download]: ../storage-use-azcopy.md
[azure-azcopy]: ../storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight/hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight/hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight/hdinsight-use-pig.md
[hdinsight-provision]: hdinsight/hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database/sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[xplatcli]: ../xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54--> 