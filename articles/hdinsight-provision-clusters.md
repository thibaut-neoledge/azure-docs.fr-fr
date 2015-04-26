<properties 
	pageTitle="Approvisionnement de clusters Hadoop dans HDInsight | Azure" 
	description="Découvrez comment approvisionner des clusters pour Azure HDInsight en utilisant le portail de gestion, PowerShell ou la ligne de commande." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

#Approvisionnement de clusters Hadoop dans HDInsight à l'aide d'options personnalisées

Dans cet article, vous allez découvrir les différentes méthodes pour approvisionner de façon personnalisée un cluster Hadoop sur Azure HDInsight, en utilisant le portail de gestion Azure, PowerShell, des outils de ligne de commande ou le Kit de développement logiciel (SDK) .NET HDInsight. Cet article présente l'approvisionnement des clusters Hadoop. Pour obtenir des instructions sur l'approvisionnement d'un cluster HBase, consultez la page [Approvisionnement d'un cluster HBase dans HDInsight][hdinsight-hbase-custom-provisisur]. Consultez la rubrique <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Différence entre Hadoop et HBase</a> pour découvrir les raisons qui pourraient vous pousser à choisir un produit plutôt que l'autre.

## Qu'est-ce qu'un cluster HDInsight ?

Vous-êtes-vous déjà demandé pourquoi nous évoquons les clusters à chaque fois que nous parlons d'Hadoop ou des BigData ? C'est parce qu'Hadoop permet le traitement distribué de grandes quantités de données, diffusées entre les différents nœuds d'un cluster. Le cluster dispose d'une architecture principal/secondaire composée d'un maître (également nommé nœud ou nœud de nom) et de plusieurs esclaves (également nommés nœuds de données). Pour plus d'informations, consultez [Apache Hadoop][apache-hadoop].

![HDInsight Cluster][img-hdi-cluster]

Un cluster HDInsight résume les détails de l'implémentation Hadoop pour que vous n'ayez pas à vous demander comment communiquer avec différents nœuds d'un cluster. Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Pour plus d'informations, consultez la rubrique [Présentation de Hadoop dans HDInsight][hadoop-hdinsight-intro]. Les données destinées à l'attrition sont stockées dans un stockage d'objets blob Azure, également nommé *Azure Storage - Blob* (ou WASB) dans le contexte de HDInsight. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

Cet article fournit des instructions sur les différentes méthodes d'approvisionnement de cluster. Si vous recherchez une approche de démarrage rapide pour approvisionner un cluster, consultez [Prise en main d'Azure HDInsight][hdinsight-get-started].

**Conditions préalables :**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les cmdlets HDInsight PowerShell permettent d'effectuer les tâches associées à votre abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][azure-purchase-options], les [offres spéciales membres][azure-member-offers] ou la [version d'évaluation gratuite][azure-free-trial].

##Dans cet article

* [Options de configuration](#configuration)
* [Utilisation du portail de gestion Azure](#portal)
* [Utilisation d'Azure PowerShell](#powershell)
* [Utilisation de la ligne de commande interplateforme](#cli)
* [Utilisation du Kit de développement logiciel (SDK) HDInsight .NET](#sdk)
* [Étapes suivantes](#nextsteps)

##<a id="configuration"></a>Options de configuration

###Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d'objet blob Azure, ainsi qu'un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des objets blob supplémentaires qui seront également associés à votre cluster.

Pour plus d'informations sur l'utilisation des magasins d'objets blob secondaires, consultez [Utilisation du stockage d'objets blob Azure avec HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-use-blob-storage/).

###Metastore

Le Metastore contient des informations sur les tables Hive, les partitions, les schémas, les colonnes, etc. Ces informations sont utilisées par Hive pour localiser l'emplacement de stockage des données sur HDFS (ou WASB pour HDInsight.) Hive utilise par défaut une base de données intégrée pour stocker ces informations.

Lors de l'approvisionnement d'un cluster HDInsight, vous pouvez indiquer une base de données SQL qui contiendra le Metastore pour Hive. Ceci permet de conserver les informations des métadonnées lorsque vous supprimez un cluster, car elles sont alors stockées de manière externe dans la base de données SQL.

### Personnalisation des clusters

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration des clusters pendant la création d'un cluster HDInsight. Vous pouvez personnaliser les clusters en écrivant des scripts qui sont exécutés pendant leur création. Ces scripts sont appelés à l'aide de l'option **Action de script**, une option de configuration qui peut être utilisée à partir du portail de gestion Azure, de cmdlets HDInsight PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d'informations, consultez la page [Personnalisation d'un cluster HDInsight à l'aide d'une d'action de script][hdinsight-customize-cluster].


###Réseau virtuel

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud) ;

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d'un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE] Vous devez créer un Azure Virtual Network avant d'approvisionner un cluster HDInsight. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight prend uniquement en charge les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec les réseaux virtuels basés sur des groupes d'affinités.
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

##<a id="portal"></a> Utilisation du portail de gestion Azure

Les clusters HDInsight utilisent un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire avant de pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la page [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage]. Pour plus d'informations sur la création d'un compte Azure Storage, consultez la page [Création d'un compte de stockage][azure-create-storageaccount].


> [AZURE.NOTE] Actuellement, seules les régions **Est de l'Asie**, **Sud-Est de l'Asie**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Centre Nord des États-Unis** et **Centre-Sud des États-Unis** peuvent héberger des clusters HDInsight.

**Création d'un cluster HDInsight au moyen de l'option de création personnalisée**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal].
2. Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.
3. Dans la page **Cluster Details**, tapez ou choisissez les valeurs suivantes :

	![Provide Hadoop HDInsight cluster details][image-customprovision-page1]

    <table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom du cluster</td>
			<td><p>Nom du cluster. </p>
				<ul>
				<li>Le nom DNS doit commencer et finir par un caractère alphanumérique, et peut contenir des traits d'union.</li>
				<li>Le champ doit être une chaîne comportant entre 3 et 63 caractères.</li>
				</ul></td></tr>
		<tr><td>Type du cluster</td>
			<td>Pour le type du cluster, sélectionnez <strong>Hadoop</strong>.</td></tr>
		<tr><td>Version de HDInsight</td>
			<td>Choisissez la version. Pour Hadoop, la valeur par défaut est HDInsight version 3.1, qui utilise Hadoop 2.4.</td></tr>
		</table>

	Entrez ou sélectionnez les valeurs, comme indiqué dans le tableau, puis cliquez sur la flèche de droite.

4. Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
	<tr><td>Région/Réseau virtuel</td><td><p>Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici.</p><p>Les régions disponibles sont : <strong>Est de l'Asie</strong>, <strong>Asie du Sud-Est</strong>, <strong>Europe du Nord</strong>, <strong>Europe de l'Ouest</strong>, <strong>Est des États-Unis</strong>, <strong>Ouest des États-Unis</strong>, <strong>Nord du centre des États-Unis</strong>, <strong>Sud du centre des États-Unis</strong><br/>Si vous avez créé un réseau virtuel Azure, vous pouvez sélectionner le réseau pour lequel le cluster HDInsight va être configuré.</p><p>Pour plus d'informations sur la création d'un réseau virtuel Azure, consultez la rubrique <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Tâches de configuration du réseau virtuel</a>.</p></td></tr>
	</table>



5. Sur la page **Configuration de l'utilisateur du cluster**, entrez les valeurs suivantes :

    ![Provide Hadoop HDInsight cluster user][image-customprovision-page3]

    <table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom d'utilisateur</td>
			<td>Spécifiez le nom d'utilisateur de cluster HDInsight.</td></tr>
		<tr><td>Mot de passe/Confirmer le mot de passe</td>
			<td>Spécifiez le mot de passe de cluster HDInsight.</td></tr>
		<tr><td>Enter Hive/Oozie Metastore</td>
			<td>Activez cette case à cocher pour spécifier une base de données SQL sur le même centre de données que le cluster, pour l'utiliser en tant que metastore Hive/Oozie. Ceci est utile si vous voulez conserver les métadonnées sur les tâches Hive/Oozie après la suppression d'un cluster.</td></tr>
		<tr><td>Base de données de metastore</td>
			<td>Spécifiez la base de données SQL Azure qui sera utilisée comme metastore pour Hive/Oozie. Cette base de données SQL doit se trouver dans le même centre de données que le cluster HDInsight. La zone de liste répertorie uniquement les bases de données SQL situées dans le même centre de données que celui spécifié dans la page <strong>Détails du cluster</strong> .</td></tr>
		<tr><td>Utilisateur de la base de données</td>
			<td>Spécifiez l'utilisateur de base de données SQL qui sera utilisé pour se connecter à la base de données.</td></tr>
		<tr><td>Mot de passe utilisateur de la base de données</td>
			<td>Spécifiez le mot de passe de la base de données SQL.</td></tr>
	</table>

	>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l'affichage du serveur, cliquez sur **Configurer**, puis pour **Services Microsoft Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

    Cliquez sur la flèche droite.


6. Sur la page **Compte de stockage**, entrez la valeur suivante :

    ![Provide storage account for Hadoop HDInsight cluster][image-customprovision-page4]

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Compte de stockage</td>
			<td>Spécifiez le compte de stockage Azure qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes :
			<ul>
				<li>Utiliser le stockage existant</li>
				<li>Créer un nouveau stockage</li>
				<li>Utiliser le stockage associé à un autre abonnement</li>
			</ul>
			</td></tr>
		<tr><td>Nom du compte</td>
			<td><ul>
				<li>Si vous avez opté pour le stockage existant, sélectionnez un compte de stockage existant pour <strong>Nom du compte</strong>. La liste déroulante répertorie uniquement les comptes de stockage situés dans le centre de données sur lequel vous voulez approvisionner le cluster.</li>
				<li>Si vous avez sélectionné l'option <strong>Créer un nouveau stockage</strong> ou <strong>Utiliser le stockage associé à un autre abonnement</strong> , vous devez indiquer le nom du compte de stockage.</li>
			</ul></td></tr>
		<tr><td>Clé du compte</td>
			<td>Si vous avez sélectionné l'option <strong>Utiliser le stockage associé à un autre abonnement</strong> , indiquez la clé de ce compte de stockage.</td></tr>
		<tr><td>Conteneur par défaut</td>
			<td><p>Permet de spécifier le conteneur par défaut du compte de stockage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Lorsque l'option <strong>Utiliser le stockage existant</strong> est utilisée pour le champ <strong>Compte de stockage</strong> et qu'il n'y a aucun conteneur existant dans ce compte, le conteneur est créé par défaut avec le même nom que celui du cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite. Cependant, si le compte de stockage existant a un conteneur dont le nom est différent de celui du cluster spécifié, vous pouvez également utiliser ce conteneur.</p>
            <p>Si vous choisissez de créer un stockage ou d'utiliser le stockage d'un autre abonnement Azure, vous devez spécifier le nom du conteneur par défaut.</p>
        </td></tr>
		<tr><td>Comptes de stockage supplémentaires</td>
			<td>HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n'est pas limité. Toutefois, si vous créez un cluster au moyen du portail de gestion, la limite est établie à sept en raison de contraintes liées à l'interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez dans ce champ ajoute une page Compte de stockage supplémentaire vers l'Assistant vous permettant de spécifier les informations de compte. Par exemple, dans la capture d'écran suivante, 1 compte de stockage supplémentaire est sélectionné, la page 5 est donc ajoutée à la boîte de dialogue.</td></tr>
	</table>

	Cliquez sur la flèche droite.

7. Si vous avez choisi de configurer un stockage supplémentaire pour le cluster, dans la page **Compte de stockage**, entrez les informations du compte pour le compte de stockage supplémentaire :

	![Provide additional storage details for HDInsight cluster][image-customprovision-page5]

    De nouveau, vous avez le choix entre le stockage existant, la création d'un stockage ou l'utilisation du stockage d'un autre abonnement Azure. La procédure pour fournir les valeurs est semblable à la procédure précédente.

    > [AZURE.NOTE] Une fois qu'un compte de stockage Azure a été choisi pour votre cluster HDInsight, vous ne pouvez ni le supprimer, ni le remplacer par un autre.

8. Dans la page **Actions de Script**, cliquez sur **Ajouter une action de script** pour fournir des détails sur le script personnalisé que vous souhaitez exécuter pour personnaliser un cluster, pendant la création du cluster. Par exemple, vous pouvez utiliser une action de Script pour personnaliser un cluster et installer <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Pour plus d'informations, consultez la page [Personnalisation de clusters HDInsight à l'aide d'une d'action de script][hdinsight-customize-cluster]. 
	
	![Configure Script Action to customize an HDInsight cluster][image-customprovision-page6]

	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom</td>
			<td>Entrez un nom pour l'action du script.</td></tr>
		<tr><td>URI du script</td>
			<td>Spécifiez l'URI du script appelé pour personnaliser le cluster.</td></tr>
		<tr><td>Type de nœud</td>
			<td>Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Vous pouvez choisir <b>Tous les nœuds</b>, <b>Uniquement les nœuds principaux</b> ou <b>Nœuds de travail</b> uniquement.
		<tr><td>Paramètres</td>
			<td>Spécifiez les paramètres, si le script le demande.</td></tr>
	</table>

	Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster. 

##<a id="powershell"></a> Utilisation d'Azure PowerShell
Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur l'approvisionnement d'un cluster HDInsight en utilisant Pour plus d'informations sur la configuration d'un poste de travail pour exécuter les cmdlets HDInsight Powershell, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure]. Pour plus d'informations sur l'utilisation de PowerShell avec HDInsight, consultez la rubrique [Administration de HDInsight avec PowerShell][hdinsight-admin-powershell]. Pour la liste des cmdlets PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE] Bien que vous puissiez utiliser les scripts de cette section pour configurer un cluster HDInsight sur un réseau virtuel Azure, ceux-ci ne permettent pas de créer un réseau de ce type. Pour plus d'informations sur la création d'un réseau Azure Virtual Network, consultez la rubrique [Tâches de configuration d'un réseau virtuel](http://msdn.microsoft.com/library/azure/jj156206.aspx).

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant PowerShell :

- Création d'un compte Azure Storage
- Création d'un conteneur d'objets blob Azure
- Création d'un cluster HDInsight

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. Ce compte de stockage doit se situer dans le même centre de données que le cluster HDInsight.


**Pour créer un compte Azure Storage**

- Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

		$storageAccountName = "<StorageAcccountName>"	# Provide a storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

	Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes PowerShell suivantes pour récupérer les informations :

		# List storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Création d'un conteneur de stockage d'objets blob Azure**

- Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

		$storageAccountName = "<StorageAccountName>"	# Provide the storage account name
		$storageAccountKey = "<StorageAccountKey>"		# Provide either primary or secondary key
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour approvisionner un cluster HDInsight**

> [AZURE.NOTE] Les cmdlets PowerShell constituent la seule méthode recommandée pour modifier les variables de configuration d'un cluster HDInsight.  Les modifications apportées aux fichiers de configuration Hadoop pendant une connexion au cluster via le Bureau distant peuvent être remplacées en cas de mise à jour corrective du cluster.  Dans ce cas, les valeurs de configuration définies via PowerShell seront conservées.

- Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription.
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container.
		$containerName = "<ContainerName>"				# Azure Blob container that is used as the default file system for the HDInsight cluster.

		$clusterName = "<HDInsightClusterName>"			# The name you will name your HDInsight cluster.
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNodes>			# The number of nodes in the HDInsight cluster.

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

	Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La mise en service du cluster peut prendre plusieurs minutes.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Approvisionnement d'un cluster HDInsight au moyen d'options de configuration personnalisées**

Tout en approvisionnant un cluster, vous pouvez utiliser d'autres options de configuration, telle qu'une connexion à plusieurs stockages d'objets blob Azure en utilisant un réseau virtuel ou en utilisant une base de données SQL Azure pour les metastores Hive et Oozie. Ceci vous permet de séparer la durée de vie de vos données et métadonnées de celle du cluster.

> [AZURE.NOTE] Les cmdlets PowerShell constituent la seule méthode recommandée pour modifier les variables de configuration d'un cluster HDInsight.  Les modifications apportées aux fichiers de configuration Hadoop pendant une connexion au cluster via le Bureau distant peuvent être remplacées en cas de mise à jour corrective du cluster.  Dans ce cas, les valeurs de configuration définies via PowerShell seront conservées.

- Exécutez les commandes suivantes à partir d'une fenêtre PowerShell Windows :

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

		# Get the storage account keys
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

	>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l'affichage du serveur, cliquez sur **Configurer**, puis pour **Services Microsoft Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

**Affichage de la liste des clusters HDInsight**

- Exécutez les commandes suivantes à partir de la fenêtre de console Azure PowerShell pour répertorier le cluster HDInsight et vérifier qu'il est correctement approvisionné :

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Utilisation de la ligne de commande interplateforme

> [AZURE.NOTE] Depuis le 29/08/2014, l'interface de ligne de commande interplateforme ne permet plus d'associer un cluster à un réseau virtuel Azure.

Une autre possibilité pour la mise en service d'un cluster HDInsight est l'interface de ligne de commande interplateforme. L'outil en ligne de commande est mis en œuvre dans Node.js. Il peut être utilisé sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. Il s'agit d'une technologie open source.  Le code source est géré dans GitHub à l'adresse <a href= "https://github.com/Azure/azure-sdk-tools-xplat">https://github.com/Azure/azure-sdk-tools-xplat</a>. Pour une aide générale sur l'utilisation de l'interface de ligne de commande, consultez la page [Utilisation des outils en ligne de commande Azure pour Mac et Linux][azure-command-line-tools]. Pour accéder à une documentation de référence exhaustive, consultez la rubrique [Outil en ligne de commande Azure pour Mac et Linux][azure-command-line-tool]. Cet article traite uniquement de l'utilisation de l'interface de ligne de commande dans Windows.

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant la ligne de commande interplateforme :

- Installation de la ligne de commande interplateforme
- Téléchargement et importation de paramètres de publication de compte Azure
- Création d'un compte Azure Storage
- Approvisionnement d'un cluster

L'interface de ligne de commande peut être installée au moyen du *Node.js Package Manager (NPM)* ou de Windows Installer. Microsoft vous recommande de procéder à l'installation en utilisant uniquement l'une des deux options suivantes.

**Pour installer l'interface de ligne de commande au moyen de NPM**

1.	Accédez à **www.nodejs.org**.
2.	Cliquez sur **INSTALL** et suivez les instructions en conservant les paramètres par défaut.
3.	Ouvrez l'**invite de commandes** (ou *Azure Command Prompt*, ou *Developer Command Prompt for VS2012*) à partir de votre station de travail.
4.	Exécutez la commande suivante dans la fenêtre d'invite de commandes.

		npm install -g azure-cli

	> [AZURE.NOTE] Si un message d'erreur indique que la commande NPM est introuvable, vérifiez que les chemins d'accès suivants se trouvent dans la variable d'environnement : <i>C:\Program Files (x86)\nodejs;C:\Users\[nom_utilisateur]\AppData\Roaming\npm</i> ou <i>C:\Program Files\nodejs;C:\Users\[nom_utilisateur]\AppData\Roaming\npm</i>

5.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur *-h* à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Pour installer l'interface de ligne de commande au moyen de Windows Installer**

1.	Accédez à **http://azure.microsoft.com/downloads/**.
2.	Faites défiler l'écran pour accéder à la section **Outils de ligne de commande**, puis cliquez sur **Interface de ligne de commande interplateforme** et suivez les étapes de l'Assistant Web Platform Installer.

**Pour télécharger et importer des paramètres de publication**

Avant d'utiliser l'interface de ligne de commande, vous devez configurer la connectivité entre votre poste de travail et Azure. L'interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d'Azure dans un fichier de paramètres de publication. Ce dernier peut ensuite être importé en tant que paramètre de configuration local persistant dont l'interface de ligne de commande se servira pour les opérations ultérieures. Vous importez vos paramètres de publication une fois pour toutes.

> [AZURE.NOTE] Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.


1.	Ouvrez une **Invite de commandes**.
2.	Exécutez la commande suivante pour télécharger le fichier de paramètres de publication.

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	La commande ouvre la page web sur laquelle télécharger le fichier de paramètres de publication.

3.	﻿À l'invite d'enregistrement du fichier, cliquez sur **Enregistrer** et indiquez un emplacement pour l'enregistrement du fichier.
5.	À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

		azure account import <file>

	Dans la capture d'écran précédente, le fichier de paramètres de publication a été enregistré dans le dossier C:\HDInsight sur le poste de travail.

**Pour créer un compte Azure Storage**

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage est nécessaire avant de pouvoir créer un cluster HDInsight. Il doit se trouver dans le même centre de données.

- À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour créer un compte Azure Storage :

		azure storage account create [options] <StorageAccountName>

	À l'invite demandant d'indiquer un emplacement, sélectionnez l'endroit où approvisionner un cluster HDINsight. Le stockage doit se trouver dans le lieu que le cluster HDInsight. Actuellement, seules les régions **Est de l'Asie**, **Sud-Est de l'Asie**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Centre Nord des États-Unis** et **Centre-Sud des États-Unis** peuvent héberger des clusters HDInsight.  

Pour plus d'informations sur la création d'un compte de stockage Azure au moyen du portail de gestion Azure, consultez la rubrique [Création, gestion ou suppression d'un compte de stockage][azure-create-storageaccount].

Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	-- lists storage accounts
	azure storage account list

	-- Shows information for a storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a storage account
	azure storage account keys list <StorageAccountName>

Pour plus d'informations sur l'obtention des informations à l'aide du portail de gestion, consultez la section *How to: View, copy and regenerate storage access keys* de la rubrique [Création, gestion ou suppression d'un compte de stockage][azure-create-storageaccount].

Un cluster HDInsight requiert également un conteneur dans un compte de stockage. Si le compte de stockage que vous fournissez ne contient pas de conteneur, la commande *azure hdinsight cluster create* vous invite à entrer un nom de conteneur avant de le créer. Cependant, si vous voulez créer le conteneur au préalable, vous pouvez utiliser la commande suivante :

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

**Pour approvisionner un cluster HDInsight**

- À partir de la fenêtre d'invite de commandes, exécutez la commande suivante :

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Approvisionnement d'un cluster HDInsight à l'aide d'un fichier de configuration**

Généralement, vous approvisionnez un cluster HDInsight, vous exécutez les tâches, puis vous supprimez le cluster pour réduire les coûts. L'interface de ligne de commande vous donne la possibilité d'enregistrer les configurations dans un fichier, de sorte que vous pouvez les réutiliser chaque fois que vous mettez en service un cluster.

- À partir de la fenêtre d'invite de commandes, exécutez les commandes suivantes :


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

		#If requred, include commands to use additional blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l'affichage du serveur, cliquez sur **Configurer**, puis pour **Services Microsoft Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Pour énumérer et afficher les détails de cluster**

- Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Suppression d'un cluster**

- Utilisez les commandes suivantes pour supprimer un cluster :

		azure hdinsight cluster delete <ClusterName>



##<a id="sdk"></a> Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET.

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant le SDK :

- Installation du Kit de développement logiciel (SDK) HDInsight .NET
- Créer un certificat auto-signé
- Création d'une application console
- Exécution de l'application


**Pour installer le SDK .NET HDInsight**

Vous pouvez installer la dernière version publiée du Kit de développement logiciel (SDK) disponible sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

Créez un certificat auto-signé, installez-le sur votre poste de travail et téléchargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


**Création d'une application console Visual Studio**

1. Ouvrez Visual Studio 2013.

2. Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
	</table>

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.

6. Exécutez les commandes suivantes dans la console pour installer les packages.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions using suivantes au début du fichier :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;


9. Dans la fonction Main(), copiez et collez le code suivant :

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Remplacez les variables au début de la fonction main().

**Pour exécuter l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.



##<a id="nextsteps"></a> Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d'Azure HDInsight][hdinsight-get-started] - Apprenez à utiliser votre cluster HDInsight
* [Utilisation de Sqoop avec HDInsight][89e2276a] - Découvrez comment copier des données entre HDInsight et une base de données SQL Azure ou SQL Server
* [Administration de HDInsight à l'aide de PowerShell][hdinsight-admin-powershell] - Découvrez comment utiliser HDInsight avec PowerShell
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs] - Découvrez comment envoyer des tâches par programme à HDInsight
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation] - Découvrez le Kit de développement logiciel (SDK) HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Provide Hadoop HDInsight cluster details"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Provide Hadoop HDInsight cluster users"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Provide storage account details for Hadoop HDInsight cluster"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Provide additional storage account details for Hadoop HDInsight cluster"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Configure Script Action to customize an HDInsight cluster"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "List and show clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: /fr-fr/documentation/articles/hdinsight-use-sqoop/ "Use Sqoop with HDInsight"

<!--HONumber=42-->
