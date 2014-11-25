<properties linkid="manage-services-hdinsight-upload-data-for-hadoop-jobs-in-hdinsight" urlDisplayName="Upload Data" pageTitle="Upload data for Hadoop jobs in HDInsight | Azure" metaKeywords="" description="Learn how to upload and access data in HDInsight using Azure Storage Explorer, Azure PowerShell, the Hadoop command line, or Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data for Hadoop jobs in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Téléchargement de données pour des tâches Hadoop dans HDInsight

Azure HDInsight fournit un système HDFS (Hadoop Distributed File System) complet pour le stockage d'objets blob Azure. Il a été conçu en tant qu'extension HDFS pour offrir aux clients une expérience transparente en permettant à l'ensemble complet de composants de l'écosystème Hadoop de fonctionner directement sur les données qu'il gère. Le stockage d'objets blob Azure et HDFS sont des systèmes de fichiers distincts qui sont optimisés pour le stockage de données et pour les calculs réalisés à partir de ces données. Pour connaître les avantages que constitue l'utilisation du stockage d'objets blob Azure, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Les clusters Azure HDInsight sont généralement déployés pour exécuter des tâches MapReduce et sont supprimés une fois ces tâches terminées. Conserver les données dans les clusters HDFS une fois les calculs terminés serait une façon onéreuse de stocker ces données. Le stockage d'objets blob Azure constitue une option de stockage à long terme économique, partageable, hautement évolutive et disponible pour les données qui doivent être traitées à l'aide de HDInsight. Le stockage de données dans un objet blob permet de libérer en toute sécurité les clusters HDInsight utilisés pour les calculs, sans perte de données.

Vous pouvez accéder au stockage d'objets blob Azure via [AzCopy][AzCopy], [Azure PowerShell][Azure PowerShell], la [bibliothèque du client de stockage Windows Azure pour .NET][bibliothèque du client de stockage Windows Azure pour .NET] ou des outils d'explorateur. Voici certains des outils disponibles :

-   [Azure Storage Explorer][Azure Storage Explorer]
-   [Cloud Storage Studio 2][Cloud Storage Studio 2]
-   [CloudXplorer][CloudXplorer]
-   [Azure Explorer][Azure Explorer]
-   [Azure Explorer PRO][Azure Explorer PRO]

**Configuration requise**

Notez la configuration requise avant de commencer la lecture de cet article :

-   Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d'Azure HDInsight][Prise en main d'Azure HDInsight] ou la rubrique [Mise en service de clusters HDInsight][Mise en service de clusters HDInsight].

## Dans cet article

-   [Téléchargement de données vers le stockage d'objets blob avec AzCopy][Téléchargement de données vers le stockage d'objets blob avec AzCopy]
-   [Téléchargement de données vers le stockage d'objets blob avec Azure PowerShell][Téléchargement de données vers le stockage d'objets blob avec Azure PowerShell]
-   [Téléchargement de données vers le stockage d'objets blob avec Azure Storage Explorer][Téléchargement de données vers le stockage d'objets blob avec Azure Storage Explorer]
-   [Téléchargement de données vers le stockage d'objets blob avec la ligne de commande Hadoop][Téléchargement de données vers le stockage d'objets blob avec la ligne de commande Hadoop]
-   [Importation de données à partir d'une base de données SQL Azure vers le stockage d'objets blob avec Sqoop][Importation de données à partir d'une base de données SQL Azure vers le stockage d'objets blob avec Sqoop]

## <span id="azcopy"></span></a>Téléchargement de données vers le stockage d'objets blob avec AzCopy

AzCopy est un utilitaire de ligne de commande conçu pour simplifier le transfert de données vers et depuis un compte Azure Storage. Vous pouvez l'utiliser comme un outil indépendant ou l'incorporer dans une application existante. [Téléchargement d'AzCopy][Téléchargement d'AzCopy].

La syntaxe d'AzCopy est :

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Pour plus d'informations, consultez la page [AzCopy : téléchargement de fichiers pour les objets blob Azure][AzCopy]

## <span id="powershell"></span></a>Téléchargement de données vers le stockage d'objets blob avec Azure PowerShell

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Vous pouvez utiliser Azure PowerShell pour télécharger des données vers le stockage d'objets blob, pour traiter les données avec des tâches MapReduce. Pour plus d'informations sur la configuration de votre poste de travail pour exécuter Azure PowerShell, consultez l'article [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].

**Téléchargement d'un fichier local vers un stockage d'objets blob**

1.  Ouvrez la fenêtre de la console Azure PowerShell, comme indiqué dans la section [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].
2.  Définissez les valeurs des cinq premières variables dans le script suivant :

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

3.  Collez le script dans la fenêtre de la console Azure PowerShell pour l'exécuter.

Les conteneurs de stockage d'objets blob stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser le caractère « / » dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. Par exemple, une clé d'objet blob peut être *input/log1.txt*. Il n'existe pas de répertoire « input », mais le caractère « / » figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier. Dans le script précédent, vous pouvez donner une structure de dossiers au fichier, en configurant la variable $blobname. Par exemple *$blobname="myfolder/myfile.txt"*.

Lorsque vous utilisez les outils d'Azure Explorer, vous pouvez remarquer la présence de certains fichiers de 0 octet. Ces fichiers ont deux utilités :

-   Ils servent à indiquer la présence de dossiers vides. Le stockage d'objets blob sait que si un objet blob est nommé foo/bar, c'est qu'il y a un dossier nommé foo. Mais si vous voulez avoir un dossier vide nommé foo, la seule façon de l'indiquer est d'avoir ces fichiers de 0 octet.
-   Ils contiennent des métadonnées spécifiques requises par le système de fichiers Hadoop, notamment les autorisations et les propriétaires des dossiers.

## <span id="storageexplorer"></span></a>Téléchargement de données vers le stockage d'objets blob avec Azure Storage Explorer

*Azure Storage Explorer* est un outil utile pour examiner et modifier les données de votre compte Azure Storage. Il est gratuit et vous pouvez le télécharger depuis la page [][]<http://azurestorageexplorer.codeplex.com/></a>.

Avant de l'utiliser, vous devez connaître le nom et la clé de votre compte Azure Storage. Pour obtenir des instructions sur l'obtention de ces informations, consultez la section «  Affichage, copie et régénération de clés d'accès de stockage » de la rubrique [Gestion des comptes de stockage][Gestion des comptes de stockage].

1.  Exécutez Azure Storage Explorer.

    ![HDI.AzureStorageExplorer][HDI.AzureStorageExplorer]

2.  Cliquez sur **Ajouter un compte**. Lorsque vous aurez ajouté un compte à Azure Storage Explorer, vous n'aurez plus besoin de repasser par cette étape.

    ![HDI.ASEAddAccount][HDI.ASEAddAccount]

3.  Entrez le **nom du compte de stockage** et la **clé du compte de stockage**, puis cliquez sur **Ajouter le compte de stockage**. Vous pouvez ajouter plusieurs comptes de stockage, et chaque compte sera affiché sous un onglet.
4.  Sous **Type de stockage**, cliquez sur **Objets blob**.

    ![HDI.ASEBlob][HDI.ASEBlob]

5.  ﻿Dans **Conteneur**, cliquez sur le conteneur associé à votre cluster HDInsight. Lorsque vous créez un cluster HDInsight, vous devez indiquer un conteneur. Sinon, le processus de création de cluster en crée un pour vous.
6.  Sous **Objets blob**, cliquez sur **Téléchargement**.
7.  Indiquez un fichier à télécharger, puis cliquez sur **Ouvrir**.

## <span id="commandline"></span></a> Téléchargement de données vers le stockage d'objets blob avec la ligne de commande Hadoop

Pour utiliser la ligne de commande Hadoop, vous devez d'abord vous connecter au cluster avec le Bureau à distance.

1.  Connectez-vous au [portail de gestion][portail de gestion].
2.  Cliquez sur **HDINSIGHT**. Une liste de clusters Hadoop déployés s'affiche.
3.  Cliquez sur le cluster HDInsight sur lequel vous voulez télécharger des données.
4.  En haut de la page, cliquez sur **CONFIGURATION**.
5.  Cliquez sur **ENABLE REMOTE** si vous n'avez pas activé le Bureau à distance, puis suivez les instructions. Sinon, passez à l'étape suivante.
6.  En bas de la page, cliquez sur **CONNECTER**.
7.  Cliquez sur **Ouvrir**.
8.  Entrez vos informations d'identification, puis cliquez sur **OK**.
9.  Cliquez sur **Oui**.
10. Depuis le Bureau, cliquez sur **Ligne de commande Hadoop**.
11. L'exemple suivant montre comment copier le fichier davinci.txt à partir du système de fichiers local du nœud principal HDInsight vers le répertoire /example/data.

        hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

    Comme le système de fichiers par défaut est sur le stockage d'objets blob Azure, /example/datadavinci.txt s'y trouve également. Vous pouvez également faire référence au fichier comme ceci :

        wasb:///example/data/davinci.txt 

    ou

        wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

    Le nom de domaine complet est requis lorsque vous utilisez *wasbs*.

12. Utilisez la commande suivante pour répertorier les fichiers téléchargés :

        hadoop dfs -lsr /example/data

## <span id="sqoop"></span></a> Importation de données vers HDFS à partir d'une base de données ou d'un serveur SQL avec Sqoop

Sqoop est un outil conçu pour transférer des données entre Hadoop et des bases de données relationnelles. Il permet d'importer des données depuis un système de gestion de base de données relationnelle (ou SGBDR) tel que SQL, MySQL ou Oracle vers HDFS, de transformer les données dans Hadoop avec MapReduce ou Hive, puis de les réexporter dans un SGBDR. Pour plus d'informations, consultez le [guide d'utilisation de Sqoop][guide d'utilisation de Sqoop].

Avant d'importer les données, vous devez connaître le nom du serveur de base de données SQL Azure, le mot de passe du compte et le nom de la base de données.

Une base de données SQL Azure autorise par défaut les connexions aux services Azure tels que Azure HDinsight. Si ce paramètre de pare-feu est désactivé, vous devez l'activer à partir du portail de gestion Azure. Pour obtenir des instructions sur la création d'une base de données SQL et la configuration des règles de pare-feu, consultez la rubrique [Création et configuration d'une base de données SQL][Création et configuration d'une base de données SQL].

La procédure suivante utilise PowerShell pour envoyer une tâche Sqoop.

**Importation de données vers HDInsight avec Sqoop et PowerShell**

1.  Ouvrez la fenêtre de la console Azure PowerShell, comme indiqué dans la section [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].
2.  Définissez les valeurs des huit premières variables dans le script suivant :

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

3.  Collez le script dans la fenêtre de la console Azure PowerShell pour l'exécuter. Pour obtenir un exemple PowerShell permettant de récupérer le fichier de données à partir du stockage d'objets blob Azure, consultez le didacticiel [Prise en main de HDInsight][Prise en main d'Azure HDInsight].

Pour plus d'informations sur l'utilisation de Sqoop, consultez la rubrique [Utilisation de Sqoop avec HDInsight][Utilisation de Sqoop avec HDInsight].

## Étapes suivantes

Maintenant que vous savez comment obtenir des données avec HDInsight, utilisez les articles suivants pour apprendre à effectuer des analyses :

-   [Prise en main d'Azure HDInsight][Prise en main d'Azure HDInsight]
-   [Envoi de tâches Hadoop par programme][Envoi de tâches Hadoop par programme]
-   [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight]
-   [Utilisation de Pig avec HDInsight][Utilisation de Pig avec HDInsight]

  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [AzCopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
  [Azure PowerShell]: http://msdn.microsoft.com/fr-fr/library/windowsazure/jj152841.aspx
  [bibliothèque du client de stockage Windows Azure pour .NET]: /fr-fr/develop/net/how-to-guides/blob-storage/
  [Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
  [Cloud Storage Studio 2]: http://www.cerebrata.com/Products/CloudStorageStudio/
  [CloudXplorer]: http://clumsyleaf.com/products/cloudxplorer
  [Azure Explorer]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
  [Azure Explorer PRO]: http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [Mise en service de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Téléchargement de données vers le stockage d'objets blob avec AzCopy]: #azcopy
  [Téléchargement de données vers le stockage d'objets blob avec Azure PowerShell]: #powershell
  [Téléchargement de données vers le stockage d'objets blob avec Azure Storage Explorer]: #storageexplorer
  [Téléchargement de données vers le stockage d'objets blob avec la ligne de commande Hadoop]: #commandline
  [Importation de données à partir d'une base de données SQL Azure vers le stockage d'objets blob avec Sqoop]: #sqoop
  [Téléchargement d'AzCopy]: http://aka.ms/WaCopy
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  []: http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer"
  [Gestion des comptes de stockage]: ../storage-manage-storage-account/
  [HDI.AzureStorageExplorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
  [HDI.ASEAddAccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
  [HDI.ASEBlob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
  [portail de gestion]: https://manage.windowsazure.com
  [guide d'utilisation de Sqoop]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
  [Création et configuration d'une base de données SQL]: ../sql-database-create-configure/
  [Utilisation de Sqoop avec HDInsight]: ../hdinsight-use-sqoop/
  [Envoi de tâches Hadoop par programme]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig
