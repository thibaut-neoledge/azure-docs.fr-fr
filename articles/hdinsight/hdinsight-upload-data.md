<properties
	pageTitle="Téléchargement de données pour les tâches Hadoop dans HDInsight | Microsoft Azure"
	description="Découvrez comment télécharger des données pour les tâches Hadoop et y accéder dans HDInsight avec l'interface CLI Azure, Azure Storage Explorer, Azure PowerShell, la ligne de commande Hadoop ou Sqoop."
	services="hdinsight,storage"
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
	ms.date="06/10/2015"
	ms.author="jgao"/>



#Téléchargement de données pour les tâches Hadoop dans HDInsight

Azure HDInsight fournit un système HDFS (Hadoo Distributed File System) complet pour le stockage d'objets blob Azure. Il est conçu en tant qu’extension HDFS pour fournir une expérience transparente aux clients. Il permet à l'ensemble des composants de l'écosystème Hadoop de fonctionner directement sur les données qu'il gère. Le stockage d'objets blob Azure et HDFS sont des systèmes de fichiers distincts qui sont optimisés pour le stockage de données et pour les calculs réalisés à partir de ces données. Pour connaître les avantages que constitue l'utilisation du stockage d'objets blob Azure, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

##Composants requis

Notez la configuration requise avant de démarrer :

* Un cluster Azure HDInsight. Pour obtenir des instructions, consultez le didacticiel [Prise en main d'Azure HDInsight][hdinsight-get-started] ou la rubrique [Mise en service de clusters HDInsight][hdinsight-provision].

##Qu'est-ce que le stockage d'objets blob ?

Les clusters Azure HDInsight sont généralement déployés pour exécuter des tâches MapReduce et sont supprimés une fois ces tâches terminées. Conserver les données dans les clusters HDFS une fois les calculs terminés serait une façon onéreuse de stocker ces données. Le stockage d'objets blob Azure constitue une option de stockage à long terme économique, partageable, hautement évolutive et disponible pour les données qui doivent être traitées à l'aide de HDInsight. Le stockage de données dans un objet blob permet de libérer en toute sécurité les clusters HDInsight utilisés pour les calculs, sans perte de données.

###Répertoires

Les conteneurs de stockage d'objets blob Azure stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser le caractère « / » dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. HDInsight les interprète comme des répertoires réels.

Par exemple, une clé d'objet blob peut être *input/log1.txt*. Il n'existe pas de répertoire « input », mais le caractère « / » figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier.

Pour cette raison, quand vous utilisez les outils d'Azure Explorer, vous pouvez remarquer la présence de fichiers de 0 octet. Ces fichiers ont deux utilités :

- S'il existe des dossiers vides, ils servent à indiquer l'existence du dossier. Le stockage d'objets blob Azure sait que si un objet blob est nommé foo/bar, c'est qu'il y a un dossier nommé **foo**. Mais la seule façon d'indiquer un dossier vide nommé **foo** est de disposer de ces fichiers de 0 octet.

- Ils contiennent des métadonnées spécifiques requises par le système de fichiers Hadoop, notamment les autorisations et les propriétaires des dossiers.

##Utilitaires de ligne de commande

Microsoft fournit les utilitaires suivants pour utiliser le stockage d'objets blob Azure :

| Outil | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interface de ligne de commande Azure][azurecli] | ✔ | ✔ | ✔ |
| [Azure PowerShell][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Commande Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE]Alors que l'interface CLI Azure, Azure PowerShell et AzCopy peuvent tous être utilisés en dehors d'Azure, la commande Hadoop est disponible uniquement sur le cluster HDInsight et autorise uniquement le chargement de données du système de fichiers local dans le stockage d'objets blob Azure.

###<a id="xplatcli"></a>Interface CLI Azure

L'interface CLI Azure est un outil interplateforme qui vous permet de gérer les services Azure. Pour télécharger des données vers le stockage d'objets blob Azure, procédez comme suit :

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

###<a id="powershell"></a>Azure PowerShell

Azure PowerShell est un environnement de création de scripts qui vous permet de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Pour plus d'informations sur la configuration de votre poste de travail pour exécuter Azure PowerShell, consultez l'article [Installation et configuration d'Azure PowerShell](../powershell-install-configure.md).

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

3. Collez le script dans la console Azure PowerShell pour l'exécuter afin de copier le fichier.

Pour des exemples de scripts PowerShell créés pour fonctionner avec HDInsight, consultez les [outils HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande conçu pour simplifier la tâche de transfert de données à destination et en provenance d'un compte Azure Storage. Vous pouvez l'utiliser comme un outil indépendant ou l'incorporer dans une application existante. [Téléchargement d'AzCopy][azure-azcopy-download].

La syntaxe d'AzCopy est :

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Pour plus d'informations, consultez la page [AzCopy : téléchargement de fichiers pour les objets blob Azure][azure-azcopy].


###<a id="commandline"></a>Ligne de commande Hadoop

La ligne de commande Hadoop est utile uniquement pour stocker les données dans le stockage d'objets blob quand celles-ci sont déjà présentes sur le nœud principal du cluster.

Pour utiliser la commande Hadoop, vous devez d'abord vous connecter au nœud principal à l'aide de l'une des méthodes suivantes :

* **HDInsight Windows** : [connexion à l'aide du Bureau à distance](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight Linux** : connexion à l'aide de SSH ([commande SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) ou [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Une fois connecté, vous pouvez utiliser la syntaxe suivante pour télécharger un fichier dans le stockage.

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

Par exemple, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Comme le système de fichiers par défaut pour HDInsight se trouve dans le stockage d'objets blob Azure, /example/data.txt s'y trouve également. Vous pouvez également faire référence au fichier comme ceci :

	wasb:///example/data/data.txt

ou

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Pour obtenir la liste des autres commandes Hadoop qui fonctionnent avec des fichiers, consultez [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

##Clients graphiques

Plusieurs applications fournissent également une interface graphique pour utiliser Azure Storage. Voici une liste de quelques-unes de ces applications :

| Client | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/) | | | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Zudio](https://zudio.co/) | ✔ | ✔ | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###<a id="storageexplorer"></a>Azure Storage Explorer

*Azure Storage Explorer* est un outil utile pour examiner et modifier les données du compte Azure Storage. Il s'agit d'un outil gratuit que vous pouvez télécharger depuis CodePlex : [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Avant de l'utiliser, vous devez connaître le nom et la clé de votre compte Azure Storage. Pour obtenir des instructions permettant d'obtenir ces informations, consultez la section « Affichage, copie et régénération des clés d'accès de stockage » de l'article [Création, gestion et suppression d’un compte de stockage][azure-create-storage-account].

1. Exécutez Azure Storage Explorer.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Cliquez sur **Ajouter un compte**. Lorsque vous aurez ajouté un compte à Azure Storage Explorer, vous n'aurez plus besoin de repasser par cette étape.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Entrez le **nom de votre compte de stockage** et la **clé de votre compte de stockage**, puis cliquez sur **Ajouter le compte de stockage**. Vous pouvez ajouter plusieurs comptes de stockage, et chaque compte sera affiché sous un onglet.

4. Sous **Type de stockage**, cliquez sur **Objets blob**.

	![HDI.ASEBlob][image-ase-blob]

5. Dans **Conteneur**, cliquez sur le nom du conteneur associé à votre cluster HDInsight. Lorsque vous créez un cluster HDInsight, vous devez indiquer un conteneur. Sinon, le processus de création de cluster en crée un pour vous.

6. Sous **Objets blob**, cliquez sur **Téléchargement**.

7. Indiquez un fichier à télécharger, puis cliquez sur **Ouvrir**.


##Services

###Azure Data Factory

Le service Azure Data Factory est un service entièrement géré pour composer des services de stockage de données, de traitement de données et de déplacement de données dans des pipelines de production rationalisés, évolutifs et fiables.

Azure Data Factory permet de déplacer des données dans le stockage d'objets blob Azure ou de créer des pipelines de données qui utilisent directement des fonctionnalités HDInsight telles que Hive et Pig.

Pour plus d'informations, consultez la [Documentation Azure Data Factory](http://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

Sqoop est un outil conçu pour transférer des données entre Hadoop et des bases de données relationnelles. Vous pouvez l’utiliser pour importer des données depuis un système de gestion de base de données relationnelle (SGBDR) tel que SQ Server, MySQL ou Oracle dans un système de fichiers distribués Hadoop (HDFS), transformer des données dans Hadoop avec MapReduce ou Hive et exporter à nouveau les données dans un SGBDR.

Pour plus d'informations, consultez [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop].

##Kits de développement logiciel (SDK) de développement

Le stockage d'objets blob Azure est également accessible à l'aide d'un kit de développement logiciel (SDK) Azure dans les langages de programmation suivants :

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Pour plus d'informations sur l'installation des kits de développement logiciel (SDK) Azure, consultez [Téléchargements Azure](http://azure.microsoft.com/downloads/)


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

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
 

<!---HONumber=58_postMigration-->