<properties 
	pageTitle="Interrogation de données volumineuses (Big Data) à partir d'un stockage d'objets blob compatible Hadoop | Azure" 
	description="HDInsight utilise le stockage d'objets blob Hadoop comme magasin de données volumineuses pour HDFS. Apprenez à interroger depuis le stockage d'objets blob et à stocker les résultats de votre analyse." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="mollybos"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>


# Interrogation de données volumineuses (" Big Data ") à partir d'un stockage d'objets blob compatible Hadoop en vue d'une analyse dans HDInsight

Le stockage d'objets blob est une solution de stockage Azure compatible avec Hadoop qui est à la fois robuste, polyvalente et économique. Cette solution s'intègre en toute transparence à HDInsight. Grâce à une interface HDFS (Hadoop Distributed File System), l'ensemble des composants de HDInsight peut fonctionner directement sur les données dans le stockage d'objets blob. Dans ce didacticiel, vous apprendrez comment configurer un conteneur pour le stockage d'objets blob, puis traiter les données qu'il contient.

le stockage de données dans le stockage d'objets blob permet de supprimer les clusters HDInsight ayant servi aux calculs, sans perte de données utilisateur. 

> [AZURE.NOTE]	La syntaxe *asv://* n'est pas prise en charge dans les clusters HDInsight version 3.0 et ne sera pas prise en charge dans les versions ultérieures. Cela signifie que toutes les tâches envoyées vers un cluster HDInsight 3.0 utilisant explicitement la syntaxe " asv:// " échoueront. Vous devez plutôt utiliser la syntaxe *wasb://*. De même, les tâches créées avec un metastore existant contenant des références explicites aux ressources utilisant la syntaxe asv:// et envoyées vers des clusters HDInsight version 3.0 échoueront également. Vous devrez recréer ces metastores en utilisant la syntaxe wasb:// pour adresser les ressources.

> HDInsight prend uniquement en charge les objets blob de blocs pour le moment.

> La plupart des commandes HDFS telles que <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b>, etc., fonctionnent toujours comme prévu. Seules les commandes propres à l'implémentation HDFS native (nommée DFS), telles que <b>fschk</b> et <b>dfsadmin</b> se comporteront différemment sur le stockage d'objets blob Azure.

Pour plus d'informations sur l'approvisionnement d'un cluster HDInsight, consultez le didacticiel [Prise en main de HDInsight][hdinsight-get-started] ou la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].

## Dans cet article

* [Architecture de stockage HDInsight](#architecture)
* [Avantages du stockage d'objets blob Azure](#benefits)
* [Préparation d'un conteneur au stockage d'objets blob](#preparingblobstorage)
* [Adressage des fichiers dans le stockage d'objets blob](#addressing)
* [Accès aux objets blob avec PowerShell](#powershell)
* [Étapes suivantes](#nextsteps)

## <a id="architecture"></a>Architecture de stockage HDInsight
Le schéma suivant résume l'architecture de stockage HDInsight :

![Hadoop clusters in HDInsight access and store big data in cost-effective, scalable Hadoop-compatible Azure Blob storage in the cloud.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Vous pouvez accéder à ce système de fichiers en utilisant l'URI complet. Par exemple : 

	hdfs://<namenodehost>/<path>

De plus, HDInsight permet d'accéder aux données du stockage d'objets blob. La syntaxe pour accéder au stockage d'objets blob est la suivante :

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop prend en charge une notion de système de fichiers par défaut. Le système de fichiers par défaut comprend un schéma et une autorité par défaut ; il peut également être utilisé pour résoudre des chemins d'accès relatifs. Durant la configuration de HDInsight, un compte Azure Storage et un conteneur de stockage d'objets blob spécifique de ce compte sont désignés en tant que système de fichiers par défaut.

En plus de ce compte de stockage, pendant la configuration, vous pouvez ajouter des comptes de stockage à partir du même abonnement Azure ou depuis d'autres abonnements Azure. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision]. 

- **Conteneurs des comptes de stockage connectés à un  cluster :** comme le nom et la clé du compte sont stockés dans le fichier *core-site.xml*, vous disposez d'un accès complet aux objets blob de ces conteneurs.
- **Conteneurs publics ou objets blob publics dans les comptes de stockage qui ne sont PAS connectés à un cluster :** vous avez l'autorisation en lecture seule pour les objets blob dans les conteneurs.

	> [AZURE.NOTE]
        > Un conteneur public vous permet d'obtenir une liste de tous ses objets blob disponibles, ainsi que ses métadonnées. Vous pouvez accéder aux  objets blob d'un objet blob public uniquement si vous connaissez leur URL exacte. Pour plus d'informations, consultez la rubrique <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/dd179354.aspx">Limitation de l'accès aux conteneurs et aux objets blob</a>.

- **Conteneurs privés dans les comptes de stockage qui ne sont PAS connectés à un cluster :** non accessibles dans les conteneurs d'objets blob, sauf si vous définissez le compte de stockage lorsque vous envoyez des tâches WebHCat. Ceci est expliqué plus loin dans cet article.


Les comptes de stockage définis dans le processus d'approvisionnement et leurs clés sont stockés dans %HADOOP_HOME%/conf/core-site.xml.  Le comportement par défaut de HDInsight consiste à utiliser les comptes de stockage définis dans le fichier core-site.xml. Il est déconseillé de modifier le fichier core-site.xml, car le nœud principal du cluster peut à tout moment être recréé ou migrer, et toutes les modifications apportées à ces fichiers seront perdues.

Plusieurs tâches WebHCat, notamment Hive, MapReduce, la diffusion en continu Hadoop et Pig, peuvent véhiculer avec elles une description des comptes de stockage et des métadonnées (cela fonctionne actuellement pour les comptes de stockage, mais pas pour les métadonnées.) Vous trouverez un exemple de cette fonctionnalité dans la section [Accès aux objets blob avec PowerShell](#powershell) . Pour plus d'informations, consultez la page [Utilisation d'un cluster HDInsight avec des comptes de stockage et des metastores secondaires](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Les conteneurs de stockage d'objets blob stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser le caractère " / " dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. Par exemple, une clé d'objet blob peut être *input/log1.txt*. Il n'existe pas de répertoire  *input*, mais le caractère " / " figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier.










## <a id="benefits"></a>Avantages du stockage d'objets blob Azure
La réduction des performances entraînée par la séparation des emplacements de stockage et de calcul est compensée par le fait que les clusters de calcul sont configurés à proximité des ressources du compte de stockage dans le centre de données Azure, où le réseau à haut débit permet aux nœuds de calcul d'accéder plus efficacement aux données dans le stockage d'objets blob.

Voici les avantages offerts par le stockage de données dans un stockage d'objets blob au lieu d'un système HDFS :

* **Réutilisation et partage des données :** les données du système HDFS sont situées dans le cluster de calcul. Seules les applications pouvant accéder au cluster de calcul peuvent utiliser les données avec l'API HDFS. Vous pouvez accéder aux données du stockage d'objets blob via les API HDFS ou les [API REST de stockage d'objets blob][blob-storage-restAPI]. Vous pouvez donc utiliser un plus grand nombre d'applications (notamment d'autres clusters HDInsight) et d'outils pour produire et consommer des données.
* **Archivage des données :** le stockage de données dans le stockage d'objets blob permet de supprimer les clusters HDInsight ayant servi aux calculs, sans perte de données utilisateur. 
* **Coût de stockage de données :** le stockage à long terme des données dans DFS est plus coûteux que le stockage des données dans un stockage d'objets blob, car le coût d'un cluster de calcul est plus élevé que celui d'un conteneur de stockage d'objets blob. De plus, comme vous n'avez pas à recharger les données pour chaque génération de cluster de calcul, vous faites également des économies sur les chargements de données.
* **Évolution flexible :** même si le système HDFS offre un système de fichiers monté en charge, cette capacité est déterminée par le nombre de nœuds que vous configurez pour votre cluster. Au lieu de procéder ainsi, il est parfois plus simple de profiter des capacités d'évolution flexible du stockage d'objets blob, que vous obtenez automatiquement.
* **Géo-réplication :** vous pouvez géo-répliquer vos conteneurs de stockage d'objets blob via le portail Azure. Si cette fonctionnalité permet la récupération géographique et la redondance des données, un basculement vers un emplacement géo-répliqué affecte sérieusement les performances et peut entraîner des frais supplémentaires. Nous vous recommandons donc de peser sérieusement le pour et le contre avant de choisir la géo-réplication.

Certains packages et tâches MapReduce peuvent créer des résultats intermédiaires que vous ne voulez pas stocker dans un conteneur de stockage d'objets blob. Dans ce cas, vous pouvez toujours choisir de stocker les données dans un système HDFS local. En fait, HDInsight utilise DFS pour plusieurs de ces résultats intermédiaires dans les tâches Hive et d'autres processus. 





## <a id="preparingblobstorage"></a>Préparation d'un conteneur au stockage d'objets blob
Pour utiliser des objets blob, commencez par créer un [compte de stockage Azure][azure-storage-create]. Durant cette opération, vous devez indiquer un centre de données Azure qui stockera les objets que vous créez en utilisant ce compte. Le cluster et le compte de stockage doivent être hébergés dans le même centre de données (les bases de données SQL des metastores Hive et Oozie doivent également se trouver dans le même centre de données). Où qu'il réside, chaque objet blob que vous créez appartient à un conteneur de votre compte de stockage. Ce conteneur peut être un conteneur de stockage d'objets blob existant créé hors de HDInsight ou un conteneur créé pour un cluster HDInsight. 



### Création d'un conteneur d'objets blob pour HDInsight avec le portail de gestion

Lorsque vous approvisionnez un cluster HDInsight à partir du portail de gestion Azure, vous disposez de deux options : *quick create* et *custom create*. L'option quick create requiert un compte Azure Storage.  Pour obtenir des instructions, consultez le guide [Création d'un compte de stockage][azure-storage-create]. 

En utilisant l'option quick create, vous pouvez sélectionner un compte de stockage existant. Le processus de configuration crée un conteneur ayant le même nom que le cluster HDInsight. S'il existe déjà un conteneur de même nom, <clusterName>-<x> sera utilisé. Par exemple, myHDIcluster-1. Ce conteneur sert de système de fichiers par défaut.

![Using Quick Create for a new Hadoop cluster in HDInsight in the Azure portal.][img-hdi-quick-create]
 
Avec la création personnalisée, vous avez le choix entre les options suivantes pour le compte de stockage par défaut :

- utiliser un stockage existant ;
- créer un stockage ;
- utiliser un stockage d'un autre abonnement.

Vous avez également la possibilité de créer votre propre conteneur d'objets blob ou d'utiliser un conteneur existant.
 
![Option to use an existing storage account for your HDInsight cluster.][img-hdi-custom-create-storage-account]
  




### Création d'un conteneur avec Azure PowerShell
Vous pouvez utiliser [Azure PowerShell][powershell-install] pour créer des conteneurs d'objets blob. Voici un exemple de script PowerShell :

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


## <a id="addressing"></a>Adressage des fichiers dans le stockage d'objets blob

Le modèle d'URI pour accéder aux fichiers du stockage d'objets blob est le suivant : 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] La syntaxe pour l'adressage des fichiers sur un émulateur de stockage (HDInsight) est <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



Le modèle d'URI offre à la fois un accès non chiffré avec le préfixe *wasb:* et un accès chiffré SSL avec *wasbs*. Dans la mesure du possible, nous vous recommandons d'utiliser *wasbs*, même lorsqu'il s'agit d'accéder à des données qui résident dans le même centre de données Azure.
	
&lt;BlobStorageContainerName&gt; identifie le nom du conteneur de stockage d'objets blob.
&lt;StorageAccountName&gt; identifie le nom de compte de stockage Azure. Un nom de domaine complet (FQDN) est requis.
	
Si ni &lt;BlobStorageContainerName&gt; ni &lt;StorageAccountName&gt; n'a été spécifié, le système de fichiers par défaut est utilisé. Pour les fichiers du système de fichiers par défaut, vous pouvez utiliser un chemin d'accès relatif ou absolu. Par exemple, le fichier hadoop-mapreduce-examples.jar fourni avec les clusters HDInsight peut être désigné pour l'une des utilisations suivantes :

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [AZURE.NOTE] Le nom de fichier est <i>hadoop-examples.jar</i> sur les clusters HDInsight versions 1.6 et 2.1.


&lt;path&gt; correspond au nom du chemin d'accès du fichier ou du répertoire HDFS. Comme les conteneurs de stockage d'objets blob constituent simplement un magasin de clé-valeur, il n'y a pas de système de fichiers hiérarchique. Un signe " / " à l'intérieur d'une clé d'objet blob est interprété comme un séparateur de répertoire. Par exemple, le nom d'objet blob pour *hadoop-mapreduce-examples.jar* :

	example/jars/hadoop-mapreduce-examples.jar
	

## <a id="powershell"></a>Accès aux objets blob avec Azure PowerShell

Pour plus d'informations sur l'installation et la configuration d'Azure PowerShell sur votre poste de travail, consultez la page [Installation et configuration d'Azure PowerShell][powershell-install]. La fenêtre de console Azure PowerShell ou PowerShell_ISE permet d'exécuter les cmdlets PowerShell. 

Utilisez la commande suivante pour répertorier les cmdlets relatives aux objets blob :

	Get-Command *blob*

![List of Blob-related PowerShell cmdlets.][img-hdi-powershell-blobcommands]


**Exemple PowerShell pour le chargement d'un fichier**

Consultez la page [Téléchargement de données vers HDInsight][hdinsight-upload-data].

**Exemple PowerShell pour le téléchargement d'un fichier**

Le script suivant télécharge un objet blob de blocs vers le dossier actuel. Avant d'exécuter le script, remplacez le répertoire par un dossier sur lequel vous disposez d'un accès en écriture. 


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**Exemple PowerShell pour la suppression d'un fichier**

Le script suivant indique comment supprimer un fichier.
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**Exemple PowerShell pour la création d'une liste des fichiers d'un dossier**

Le script suivant indique comment créer une liste des fichiers à l'intérieur d'un " dossier ". L'exemple suivant montre comment utiliser la cmdlet Invoke-Hive pour exécuter la commande dfs ls afin de lister le contenu d'un dossier.

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**Exemple PowerShell pour accéder à un compte de stockage non défini**
	
Cet exemple montre comment lister le contenu d'un dossier d'un compte de stockage non défini pendant le processus d'approvisionnement.
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
## <a id="nextsteps"></a>Étapes suivantes

Dans cet article, vous avez appris comment utiliser le stockage d'objets blob avec HDInsight, mais aussi, que le stockage d'objets blob est un élément essentiel de HDInsight. Ceci vous permet de créer des solutions à long terme et évolutives pour acquérir des données d'archivage avec le stockage d'objets blob Azure et d'utiliser HDInsight pour déverrouiller les informations des données stockées.

Pour en savoir plus, consultez les articles suivants :

* [Prise en main d'Azure HDInsight][hdinsight-get-started]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  

<!--HONumber=42-->
