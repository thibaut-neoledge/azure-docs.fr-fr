<properties 
   pageTitle="Approvisionnement personnalisé de clusters Hadoop dans HDInsight | Microsoft Azure" 
   description="Apprenez à approvisionner et à personnaliser les clusters pour Azure HDInsight à l’aide du portail Azure Classic, d’Azure PowerShell, d’une ligne de commande ou du Kit de développement (SDK) .NET HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/25/2016"
   ms.author="jgao"/>

#Approvisionnement de clusters dans HDInsight

Apprenez à planifier l’approvisionnement de clusters HDInsight.

> [AZURE.IMPORTANT] Les étapes de ce document utilisent le portail Azure Classic. Microsoft ne recommande pas l’utilisation du portail Classic lors de la création de services. Pour obtenir une explication des avantages du portail Azure, consultez la rubrique [Portail Microsoft Azure](https://azure.microsoft.com/features/azure-portal/).
>
> Ce document inclut également des informations sur l’utilisation d’Azure PowerShell, de l’interface de ligne de commande Azure et du Kit de développement logiciel (SDK) .NET SDK pour HDInsight. Les extraits de code fournis sont basés sur des commandes qui utilisent Azure Service Management (ASM) pour travailler avec HDInsight et qui sont __déconseillées__. Ces commandes seront supprimées d’ici au 1er janvier 2017.
>
>Pour obtenir une version de ce document qui utilise le portail Azure, ainsi que des extraits de code PowerShell, de l’interface de ligne de commande (CLI) Azure et du Kit de développement logiciel (SDK) .NET SDK pour HDInsight qui utilisent Azure Resource Manager (ARM), consultez [Approvisionnement de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md).

**Configuration requise :**

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Options de configuration de base


- **Nom du cluster**

	Le nom du cluster permet d’identifier un cluster. Le nom du cluster doit respecter les instructions suivantes :
	
	- Le champ doit être une chaîne comportant entre 3 et 63 caractères.
	- Le champ ne peut contenir que des lettres, des chiffres et des traits d'union.

- **Nom d’abonnement**

	Un cluster HDInsight est lié à un abonnement Azure.
 
- **Système d’exploitation**

	Vous pouvez configurer des clusters HDInsight sur l’un des deux systèmes d’exploitation suivants :
	- **HDInsight sous Windows (Windows Server 2012 R2 Datacenter)** :
	- **HDInsight sous Linux** : HDInsight offre la possibilité de configurer des clusters Linux sur Azure. Configurez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour plus d’informations, consultez [Prise en main de Hadoop sur Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).


- **Version de HDInsight**

	Elle permet de déterminer la version de HDInsight à utiliser pour ce cluster. Pour plus d'informations, consultez la rubrique [Versions et composants de cluster Hadoop dans HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Type de cluster** et **taille du cluster (également appelé nœuds de données)**

	HDInsight permet aux clients de déployer de nombreux types de cluster pour différentes charges de travail d’analyse des données. Les types de cluster proposés actuellement sont :
	
	- Les clusters Hadoop : pour les charges de travail de requête et d’analyse
	- Les clusters HBase : pour les charges de travail NoSQL
	- Les clusters Storm : pour les charges de travail de traitement des événements en temps réel
	- Les clusters Spark : pour les charges de travail de traitement en mémoire, de requêtes interactives, de diffusion en continu et d’apprentissage automatique.

	![Clusters HDInsight](./media/hdinsight-provision-clusters-v1/hdinsight.clusters.png)
 
	> [AZURE.NOTE] Le *cluster Azure HDInsight* est également appelé *Cluster Hadoop dans HDInsight* ou *Cluster HDInsight*. Parfois, il est utilisé de manière interchangeable avec le *cluster Hadoop*. Ils font tous référence aux clusters Hadoop hébergés dans l'environnement Microsoft Azure.

	Un type de cluster donné comprend différents rôles correspondant aux différents nœuds, qui permettent à un client de dimensionner ces nœuds dans un rôle donné, approprié aux détails de leur charge de travail. Les nœuds de travail d’un cluster Hadoop peuvent, par exemple, être dotés d'une grande capacité de mémoire si les types d'analyses en cours d'exécution utilisent beaucoup de mémoire.

	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png)

	Les clusters Hadoop pour HDInsight sont déployés avec deux rôles :

	- Nœud principal (2 nœuds)
	- Nœud de données (au moins 1 nœud)

	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png)

	Les clusters HBase pour HDInsight sont déployés avec trois rôles :
	- Serveurs principaux (2 nœuds)
	- Serveurs Region (au moins 1 nœud)
	- Nœuds Master/Zookeeper (3 nœuds)
	
	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)

	Les clusters Storm pour HDInsight sont déployés avec trois rôles :
	- Nœuds Nimbus (2 nœuds)
	- Serveurs Supervisor (au moins 1 nœud)
	- Nœuds Zookeeper (3 nœuds)
	

	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)

	Les clusters Spark pour HDInsight sont déployés avec trois rôles :
	- Nœud principal (2 nœuds)
	- Nœud de travail (au moins 1 nœud)
	- Nœuds Zookeeper (3 nœuds) (Gratuit pour les nœuds Zookeeper de taille A1)

	L’utilisation de ces nœuds est facturée aux clients pendant toute la durée de vie du cluster. Cette facturation démarre une fois qu'un cluster est créé et s'arrête lorsque le cluster est supprimé (les clusters ne peuvent pas être désalloués ou mis en suspens). La taille du cluster a une incidence sur le prix du cluster. À des fins d’apprentissage, il est recommandé d'utiliser le nœud de données 1. Pour plus d'informations sur la tarification de HDInsight, consultez la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] La limite de taille du cluster varie selon les abonnements Azure. Contactez le support de facturation pour augmenter la limite.
	
- **Région/réseau virtuel (également appelé emplacement)**

	![Régions Azure](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

	Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Taille du nœud**

	![tailles de nœud de machine virtuelle HDInsight](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

	Sélectionnez une taille de machine virtuelle pour les nœuds. Pour en savoir plus, consultez la rubrique [Tailles pour Cloud Services](cloud-services-sizes-specs.md).

	Selon votre choix de machines virtuelles, les coûts peuvent varier. HDInsight utilise toutes les machines virtuelles de niveau standard pour les nœuds de cluster. Pour plus d’informations sur les répercussions de la taille des machines virtuelles sur les prix, consultez <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Tarification HDInsight</a>.


- **Utilisateurs HDInsight**

	Les clusters HDInsight vous permettent de configurer deux comptes d'utilisateur lors de la configuration :

	- Utilisateur HTTP. Si vous utilisez la configuration de base à partir du portail Azure Classic, le nom d’utilisateur par défaut est admin.
	- Utilisateur RDP (clusters Windows) : il est utilisé pour se connecter au cluster à l'aide de RDP. Lorsque vous créez le compte, vous devez définir une date d'expiration qui est de 90 jours à compter d'aujourd'hui.
	- Utilisateur SSH (clusters Linux) : il est utilisé pour se connecter au cluster à l'aide de SSH. Vous pouvez créer des comptes d’utilisateur SSH supplémentaires une fois le cluster créé, en suivant la procédure décrite dans la rubrique [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
  
 

- **Un compte Azure Storage**


	Le HDFS d'origine utilise de nombreux disques locaux sur le cluster. HDInsight utilise à la place le stockage d'objets blob Azure pour stocker des données. Le stockage d’objets blob Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. Grâce à une interface HDFS (Hadoop Distributed File System), l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées dans le stockage d’objets blob. Le stockage de données dans le stockage d’objets blob vous permet de supprimer les clusters HDInsight servant aux calculs, sans perte de données utilisateur.
	
	Durant la configuration, vous devez spécifier un compte de stockage Azure, ainsi qu’un conteneur de stockage d’objets blob Azure sur le compte de stockage Azure. Certains processus de configuration requièrent le compte de stockage Azure et le conteneur de stockage d'objets blob qui a été créé au préalable. Le conteneur de stockage d’objets blob est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des comptes Azure Storage supplémentaires (stockage lié) qui seront accessibles via le cluster. Le cluster peut également accéder aux conteneurs d’objets blob qui sont configurés avec un accès public en lecture complet ou un accès public en lecture pour les objets blob uniquement. Pour en savoir plus sur la restriction d’accès, consultez la rubrique [Gérer l’accès aux ressources d’Azure Storage](storage-manage-access-to-resources.md).

	![Stockage HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)
	
	>[AZURE.NOTE] Un conteneur de stockage d'objets blob regroupe un ensemble d'objets blob, comme illustré dans l'image :
	
	![Stockage des objets blob](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)
	  
	
	>[AZURE.WARNING] Ne partagez pas un conteneur de stockage d’objets blob sur plusieurs clusters. Ce n’est pas pris en charge.
	
	Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

- **Metastore Hive/Oozie**

	Le metastore contient les métadonnées Hive et Oozie, telles que les tables, les partitions, les schémas et les colonnes Hive. L’utilisation du metastore vous permet de conserver vos métadonnées Hive et Oozie, afin de ne pas devoir recréer des tables Hive ou des tâches Oozie lorsque vous configurez un nouveau cluster. Hive utilise par défaut une base de données intégrée Azure SQL pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé. Par exemple, vous disposez d’un cluster qui a été configuré avec un metastore Hive. Vous avez créé des tables Hive. Après avoir supprimé et recréé le cluster à l'aide du même metastore Hive, vous ne pourrez pas afficher les tables Hive que vous avez créées dans le cluster d'origine.

## Options de configuration avancées

>[AZURE.NOTE] Cette section ne s'applique actuellement qu’aux clusters HDInsight Windows.

### Personnalisation de clusters à l'aide de la personnalisation de cluster HDInsight

Vous souhaitez parfois configurer des fichiers de configuration. En voici quelques-uns :

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Les clusters ne peuvent pas conserver les modifications lorsqu’ils ont été réimagés. Pour plus d'informations, consultez la page [Redémarrages d'instances de rôles pour cause de mise à jour du système d'exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Pour conserver les modifications apportées pendant la durée de vie des clusters, vous pouvez utiliser la personnalisation de cluster HDInsight au cours du processus d'approvisionnement.

Voici un exemple de script Azure PowerShell de personnalisation d'une configuration Hive :

	# hive-site.xml configuration 
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60
	
	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues 
	
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

Et d’autres exemples sur la personnalisation d’autres fichiers de configuration :

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1
	
	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50
	
	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000
	
	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
	
Pour plus d’informations, consultez le blog d’Azim Uddin, intitulé[Personnalisation de l’approvisionnement d’un cluster HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Personnalisation de clusters HDInsight à l'aide d'une action de script

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la configuration. Ces scripts sont appelés à l’aide de l’option **ACTION DE SCRIPT**, une option de configuration qui peut être utilisée à partir du portail Azure Classic, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).


### Utilisation des réseaux virtuels Azure

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png)

Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Étendre les capacités de HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

## Outils d'approvisionnement

- Portail Azure Classic
- Azure PowerShell
- Kit de développement logiciel (SDK) .NET
- Interface de ligne de commande

### Utiliser le portail Azure Classic

Vous pouvez vous référer aux [options de configuration de base] et aux [options de configuration avancées] pour obtenir une description des champs.

**Pour créer un cluster HDInsight en utilisant l’option Création personnalisée**

1. Connectez-vous au [portail Azure Classic][azure-management-portal].
2. Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.
3. Sur la page **Détails du cluster**, entrez ou sélectionnez les valeurs suivantes :

	- Nom du cluster
	- Nom d’abonnement
	- Type du cluster
	- Système d’exploitation
	- Version de HDInsight

4. Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	- Nœuds de données
	- Région/Réseau virtuel
	- Taille du nœud principal
	- Taille du nœud de données

5. Sur la page **Configuration de l'utilisateur du cluster**, entrez les valeurs suivantes :

	- Nom d’utilisateur HTTP
	- Mot de passe HTTP/Confirmer le mot de passe
	- Activation du Bureau à distance pour le cluster
	- Sélection du metastore Hive/Oozie

6. Si vous choisissez d’entrer le metastore Hive/Oozie dans l’écran précédent, dans la page **Configuration du metastore Hive/Oozie**, indiquez les valeurs suivantes :

	- Base de données de metastore Hive
	- Utilisateur de la base de données
	- Mot de passe utilisateur de la base de données
	- Base de données de metastore Oozie
	- Utilisateur de la base de données
	- Mot de passe utilisateur de la base de données

7. Sur la page **Compte de stockage**, entrez les valeurs suivantes :

	- Compte de stockage
	- Nom du compte
	- Conteneur par défaut
	- Comptes de stockage supplémentaires

8. Dans la page **Actions de Script**, cliquez sur **Ajouter une action de script** pour fournir des détails sur le script personnalisé que vous souhaitez exécuter pour personnaliser un cluster, pendant la création du cluster. Pour plus d’informations, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).

	- Nom : indiquez un nom pour l'action de script.
	- URI du script : spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster.
	- Type de nœud : spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de données uniquement</b>.
	- Paramètres : spécifiez les paramètres, si le script le demande.</td></tr>

	Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.

### Utilisation d'Azure PowerShell
Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur la configuration d’un cluster HDInsight à l’aide d’Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des cmdlets HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des cmdlets Windows PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

> [AZURE.NOTE] Bien que vous puissiez utiliser les scripts de cette section pour configurer un cluster HDInsight sur un réseau virtuel Azure, ceux-ci ne permettent pas de créer un réseau de ce type. Pour plus d’informations sur la création d’un réseau Azure Virtual Network, consultez la rubrique [Tâches de configuration d’un réseau virtuel](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant Azure PowerShell :

- Création d'un compte Azure Storage
- Création d'un conteneur d'objets blob Azure
- Création d'un cluster HDInsight

Vous pouvez exécuter les scripts depuis la console Windows PowerShell ou depuis l’environnement de développement intégré (ISE) de Windows PowerShell.

HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. Ce compte de stockage doit se situer dans le même centre de données que le cluster HDInsight.

**Pour vous connecter à votre compte Azure**

	Add-AzureAccount

Vous êtes invité à entrer les informations d'identification de votre compte Azure.

**Pour créer un compte Azure Storage**

	$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
	$location = "<MicrosoftDataCenter>"				# For example, "West US"

	# Create an Azure Storage account
	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Si vous disposez déjà d’un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes Windows PowerShell suivantes pour récupérer ces informations :

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount

	# List the keys for a Storage account
	Get-AzureStorageKey "<StorageAccountName>"

**Pour créer un conteneur de stockage d’objets blob Azure**

	$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
	$containerName="<ContainerName>"				# Provide a container name

	# Create a storage context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
	                                       -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour mettre en service un cluster HDInsight**

> [AZURE.NOTE] Les cmdlets Azure PowerShell constituent la seule méthode recommandée pour modifier les variables de configuration d’un cluster HDInsight. Les modifications apportées aux fichiers de configuration Hadoop pendant une connexion au cluster via le Bureau distant peuvent être remplacées en cas de mise à jour corrective du cluster. Dans ce cas, les valeurs de configuration définies via Azure PowerShell seront conservées.

- Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
	
		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system
	
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
		$hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
		$hadoopUserPassword = "<HadoopUserPassword>"
	
		$secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)
	
		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster
	
		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}
	
		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE] Les commandes $hadoopUserName et $hadoopUserPassword sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Vous utiliserez ce compte pour vous connecter au cluster et exécuter des tâches. Si vous utilisez l’option Création rapide à partir du portail Azure Classic pour approvisionner un cluster, le nom d’utilisateur Hadoop par défaut est « admin ». Ne confondez pas ce compte avec le compte d’utilisateur de protocole RDP (Remote Desktop Protocol) Le compte d’utilisateur RDP doit être différent du compte d’utilisateur Hadoop. Pour plus d’informations, consultez la rubrique [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure Classic][hdinsight-admin-portal].

	L’approvisionnement du cluster peut prendre plusieurs minutes.

	![HDI.CLI.Provision][image-hdi-ps-provision]

**Approvisionnement d’un cluster HDInsight au moyen d’options de configuration personnalisées**

Tout en approvisionnant un cluster, vous pouvez utiliser d’autres options de configuration, comme une connexion à plusieurs conteneurs de stockage d’objets blob Azure en utilisant un réseau virtuel ou en utilisant une base de données SQL Azure pour les metastores Hive et Oozie. Ceci vous permet de séparer la durée de vie de vos données et métadonnées de celle du cluster.

> [AZURE.NOTE] Les cmdlets Windows PowerShell constituent la seule méthode recommandée pour modifier les variables de configuration d’un cluster HDInsight. Les modifications apportées aux fichiers de configuration Hadoop pendant une connexion au cluster via le Bureau distant peuvent être remplacées en cas de mise à jour corrective du cluster. Dans ce cas, les valeurs de configuration définies via Azure PowerShell seront conservées.

- Exécutez les commandes suivantes à partir d'une fenêtre PowerShell Windows :

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

**Pour afficher la liste des clusters HDInsight**

- Exécutez la commande suivante à partir de la fenêtre de console Azure PowerShell pour répertorier le cluster HDInsight et vérifier qu’il est correctement approvisionné :

		Get-AzureHDInsightCluster -Name <ClusterName>


### Utilisation de l’interface de ligne de commande Microsoft Azure

> [AZURE.NOTE] Depuis le 29 août 2014, l’interface de ligne de commande Azure ne permet plus d’associer un cluster à un réseau virtuel Azure.

Une autre possibilité pour l’approvisionnement d’un cluster HDInsight est l’interface de ligne de commande Azure. L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux.

Pour une aide générale sur l’utilisation de l’interface de ligne de commande Azure, consultez la rubrique [Interface de ligne de commande Azure](../xplat-cli-install.md).

Les instructions ci-dessous expliquent comment installer l’interface de ligne de commande Azure sur Linux et Windows et comment l’utiliser pour approvisionner un cluster.

- [Configuration de l’interface de ligne de commande Azure pour Linux](#clilin)
- [Configuration de l’interface de ligne de commande Azure pour Windows](#cliwin)
- [Configuration de clusters HDInsight à l’aide de l’interface de ligne de commande Azure](#cliprovision)

#### <a id="clilin"></a>Configuration de l’interface de ligne de commande Azure pour Linux

Pour configurer votre ordinateur Linux à l’aide de l’interface de ligne de commande Azure, procédez comme suit :

- Installation de l’interface de ligne de commande Azure à l’aide du gestionnaire de package Node.js (NPM)
- Connectez-vous à un abonnement Azure

**Installation de l’interface de ligne de commande Azure à l’aide de NPM**

1.	Ouvrez une fenêtre de terminal sur votre ordinateur Linux et exécutez la commande suivante :

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur *-h* à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Pour vous connecter à votre abonnement Azure**

Avant d’utiliser l’interface de ligne de commande Azure, vous devez configurer la connectivité entre votre poste de travail et Azure. L’interface de ligne de commande Azure se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d’Azure dans un fichier de paramètres de publication. Le fichier de paramètres de publication peut ensuite être importé en tant que paramètre de configuration local persistant dont l’interface de ligne de commande Azure se servira pour les opérations ultérieures. Vous n’avez besoin d’importer vos paramètres de publication qu’une seule fois.

> [AZURE.NOTE] Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés du dossier ou utilisez le chiffrement du lecteur BitLocker.


1.	Ouvrez une fenêtre de terminal.
2.	Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	La commande ouvre la page web sur laquelle télécharger le fichier de paramètres de publication. Si la page web ne s’ouvre pas, cliquez sur le lien de la fenêtre de terminal pour ouvrir la page du navigateur et connectez-vous au portail.

3.	Téléchargez le fichier de paramètres de publication sur votre ordinateur.
5.	À partir de la fenêtre d’invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configuration de l’interface de ligne de commande Azure pour Windows

Pour configurer votre ordinateur Windows à l’aide de l’interface de ligne de commande Azure, procédez comme suit :

- Installation de l’interface de ligne de commande (à l’aide de NPM ou de Windows Installer)
- Téléchargement et importation de paramètres de publication de compte Azure


L’interface de ligne de commande Azure peut être installée à l’aide de NPM ou de Windows Installer. Microsoft vous recommande de procéder à l’installation en utilisant uniquement l’une des deux options suivantes.

**Installation de l’interface de ligne de commande Azure à l’aide de NPM**

1.	Accédez à **www.nodejs.org**.
2.	Cliquez sur **INSTALLER** et suivez les instructions en conservant les paramètres par défaut.
3.	Ouvrez **Invite de commandes** (ou *Invite de commandes Azure* ou *Invite de commandes développeur pour VS2012*) à partir de votre poste de travail.
4.	Exécutez la commande suivante dans la fenêtre d’invite de commandes :

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE] Si vous obtenez une erreur indiquant que la commande du NPM est introuvable, vérifiez que les chemins d’accès suivants figurent dans la variable d’environnement PATH : <i>C:\\Program Files (x86)\\nodejs;C:\\Users[nomutilisateur]\\AppData\\Roaming\\npm</i> ou <i>C:\\Program Files\\nodejs;C:\\Users[nomutilisateur]\\AppData\\Roaming\\npm</i>

5.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur *-h* à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Installation de l’interface de ligne de commande à l’aide de Windows Installer**

1.	Accédez à **http://azure.microsoft.com/downloads/**.
2.	Faites défiler l’écran pour accéder à la section **Outils en ligne de commande**, puis cliquez sur **Interface de ligne de commande Azure** et suivez les étapes de l’Assistant Web Platform Installer.

**Pour télécharger et importer des paramètres de publication**

Avant d’utiliser l’interface de ligne de commande Azure, vous devez configurer la connectivité entre votre poste de travail et Azure. L’interface de ligne de commande Azure se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d’Azure dans un fichier de paramètres de publication. Le fichier de paramètres de publication peut ensuite être importé en tant que paramètre de configuration local persistant dont l’interface de ligne de commande Azure se servira pour les opérations ultérieures. Vous n’avez besoin d’importer vos paramètres de publication qu’une seule fois.

> [AZURE.NOTE] Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.


1.	Ouvrez une **Invite de commandes**.
2.	Exécutez la commande suivante pour télécharger le fichier de paramètres de publication :

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	La commande ouvre la page web sur laquelle télécharger le fichier de paramètres de publication.

3.	À l'invite d'enregistrement du fichier, cliquez sur **Enregistrer** et indiquez un emplacement pour l'enregistrement du fichier.
5.	À partir de la fenêtre d’invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Configuration de clusters HDInsight à l’aide de l’interface de ligne de commande Azure

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant l’interface de ligne de commande Azure :

- Création d'un compte Azure Storage
- Mise en service d'un cluster

**Pour créer un compte Azure Storage**

HDInsight utilise le conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight. Il doit se trouver dans le même centre de données.

- À partir de la fenêtre d’invite de commandes, exécutez la commande suivante pour créer un compte Azure Storage :

		azure storage account create [options] <StorageAccountName>

	À l’invite d’emplacement, sélectionnez un lieu sur lequel approvisionner un cluster HDInsight. Le stockage doit se trouver dans le lieu que le cluster HDInsight. Actuellement, seul les régions **Est de l'Asie**, **Sud-Est de l'Asie**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Centre Nord des États-Unis** et **Centre Sud des États-Unis** peuvent héberger des clusters HDInsight.

Pour plus d’informations sur la création d’un compte Azure Storage au moyen du portail Azure Classic, consultez la rubrique [Création, gestion ou suppression d’un compte de stockage](../storage/storage-create-storage-account.md).

Si vous disposez déjà d’un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Pour plus d’informations sur l’obtention d’informations au moyen du portail Azure Classic, consultez la section *Affichage, copie et régénération des clés d’accès de stockage* de la rubrique [Création, gestion ou suppression d’un compte de stockage](../storage/storage-create-storage-account.md).

Un cluster HDInsight requiert également un conteneur dans un compte de stockage. Si le compte de stockage que vous fournissez ne contient pas de conteneur, la commande *azure hdinsight cluster create* vous invite à entrer un nom de conteneur avant de le créer. Cependant, si vous voulez créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour approvisionner un cluster HDInsight**

- À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Approvisionnement d’un cluster HDInsight au moyen d’un fichier de configuration**

Généralement, vous approvisionnez un cluster HDInsight, vous exécutez les tâches, puis vous supprimez le cluster pour réduire les coûts. L’interface de ligne de commande Azure vous donne la possibilité d’enregistrer les configurations dans un fichier, afin de pouvoir les réutiliser chaque fois que vous approvisionnez un cluster.

- À partir de la fenêtre d’invite de commandes, exécutez les commandes suivantes :


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Pour énumérer et afficher les détails de cluster**

- Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Pour supprimer un cluster**

- Utilisez la commande suivante pour supprimer un cluster :

		azure hdinsight cluster delete <ClusterName>



### Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET.

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

- Installation du Kit de développement logiciel (SDK) HDInsight
- Créer un certificat auto-signé
- Création d’une application console
- Exécution de l'application


**Pour installer le Kit de développement logiciel (SDK) .NET HDInsight**

Vous pouvez installer la dernière version publiée du Kit de développement logiciel (SDK) disponible sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

Créez un certificat auto-signé, installez-le sur votre station de travail et chargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


**Pour créer une application console Visual Studio**

1. Ouvrir Visual Studio 2013 ou 2015

2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

	|Propriété|Valeur|
	|--------|-----|
	|Modèle|Templates/Visual C#/Windows/Console Application|
	|Name|CreateHDICluster|

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.

6. Exécutez la commande suivante dans la console pour installer les packages :

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.Resources -Pre

	Ces commandes ajoutent des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.
8. Remplacez le code par ce qui suit :

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
                private const string NewClusterType = "Hadoop";
		        private const OSType NewClusterOSType = OSType.Windows;
		        private const string NewClusterVersion = "3.2";

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";
		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 
		
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		            
		            var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
		            resourceManagementClient.Providers.Register("Microsoft.HDInsight");
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Remplacez les valeurs de membre de classe.

**Exécution de l’application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.


##<a id="nextsteps"></a> Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d’Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) : apprenez à utiliser votre cluster HDInsight
* [Utilisation de Sqoop avec HDInsight](hdinsight-use-sqoop.md) : découvrez comment copier des données entre HDInsight et une base de données SQL Azure ou SQL Server
* [Administration de HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md) : découvrez comment utiliser HDInsight avec PowerShell
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md) : découvrez comment envoyer des tâches par programme à HDInsight
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation] : découvrez le Kit de développement logiciel (SDK) HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0817_2016-->