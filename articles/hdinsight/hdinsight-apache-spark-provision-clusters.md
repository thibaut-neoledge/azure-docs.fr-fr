<properties 
   pageTitle="Approvisionner des clusters Apache Spark dans HDInsight | Azure" 
   description="Découvrez comment configurer des clusters Spark pour Azure HDInsight à l’aide du portail Azure, d’Azure PowerShell, d’une ligne de commande ou du Kit de développement logiciel (SDK) .NET HDInsight." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# Approvisionner des clusters Apache Spark dans HDInsight à l’aide d’options personnalisées

Dans la plupart des scénarios, vous pouvez approvisionner un cluster Spark à l’aide de la méthode de création rapide décrite dans la rubrique [Prendre en main Apache Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Dans certains scénarios, vous souhaiterez peut-être approvisionner un cluster personnalisé. Par exemple, vous pouvez souhaiter attacher un magasin de métadonnées externe pour que vos métadonnées Hive demeurent persistantes au-delà de la durée de vie d’un cluster, ou utiliser un espace de stockage supplémentaire avec le cluster.

Pour ces scénarios, entre autres, cet article fournit des instructions sur l’utilisation du portail Azure, d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight pour approvisionner un cluster Spark personnalisé sur HDInsight.


**Configuration requise :**

Avant de suivre les instructions de cet article, vous devez disposer d’un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quelles sont les différentes options de configuration ?

###Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d’objet blob Azure, ainsi qu’un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier un compte de stockage Azure supplémentaire, également associé à votre cluster.

>[AZURE.NOTE]Ne partagez pas un conteneur de stockage d’objets blob sur plusieurs clusters. Ce n’est pas pris en charge.

Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

###Metastore

Spark permet de définir un schéma et des tables Hive sur des données brutes. Vous pouvez enregistrer ces schémas et ces métadonnées de table dans des metastores externes. L’utilisation de metastores vous permet de conserver vos métadonnées Hive, afin de ne pas devoir recréer de tables Hive lorsque vous approvisionnez un nouveau cluster. Hive utilise par défaut une base de données intégrée pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé.

### Personnalisation des clusters

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la configuration. Ces scripts sont appelés à l’aide de l’option **action de script**, une option de configuration qui peut être utilisée à partir du portail Azure, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script][hdinsight-customize-cluster].


###Réseau virtuel

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Vous devez créer le réseau Azure Virtual Network avant d’approvisionner un cluster. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

##<a id="portal"></a>Utilisation du portail Azure

Les clusters Spark sur HDInsight utilisent un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire avant de pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la rubrique [Création d’un compte de stockage][azure-create-storageaccount].

**Pour créer un cluster HDInsight en utilisant l’option Création personnalisée**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.
3. Sur la page **Détails du cluster**, entrez ou sélectionnez les valeurs suivantes :

	![Fournir des détails du cluster Spark sur HDInsight](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "Fournir des détails du cluster Spark sur HDInsight")

    <table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Cluster Name</td>
		<td><p>Nom du cluster. </p>
			<ul>
			<li>Le nom DNS (Domain Name System ) doit commencer et finir par un caractère alphanumérique et peut contenir des traits d’union.</li>
			<li>Le champ doit être une chaîne comportant entre 3&#160;et 63&#160;caractères.</li>
			</ul></td></tr>
	<tr><td>Type du cluster</td>
		<td>Pour le type du cluster, sélectionnez <strong>Spark</strong>.</td></tr>
	<tr><td>Système d'exploitation</td>
		<td>Sélectionnez <b>Windows Server&#160;2012 R2 Datacenter</b> pour approvisionner un cluster s’exécutant sur Windows. </td></tr>
	<tr><td>Version de HDInsight</td>
		<td>Choisissez la version. Pour Spark, la seule option de version disponible est&#160;<b>HDInsight&#160;3.2</b>, qui utilise Spark&#160;1.3.1.</td></tr>
	</table>Entrez ou sélectionnez les valeurs, comme indiqué dans le tableau, puis cliquez sur la flèche de droite.

4. Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	![Indiquer le nombre de nœuds et les types de nœud du cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "Indiquer le nombre de nœuds et les types de nœud du cluster")

	<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
<tr><td>Région/Réseau virtuel</td><td><p>Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu’un compte de stockage situé dans la région spécifiée ici.</p>.<br/>Si vous avez créé un réseau virtuel Azure, vous pouvez sélectionner le réseau pour lequel le cluster HDInsight va être configuré.</p><p>Pour plus d’informations sur la création d’un réseau virtuel Azure, consultez la rubrique <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Tâches de configuration d’un réseau virtuel</a>.</p></td></tr>
<tr><td>Taille du nœud principal</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud principal.</p></td></tr>
<tr><td>Taille du nœud de données</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud de données.</p></td></tr>
</table>>[AZURE.NOTE]Selon votre choix de machines virtuelles, les coûts peuvent varier. HDInsight utilise toutes les machines virtuelles de niveau standard pour les nœuds de cluster. Pour plus d’informations concernant les répercussions de la taille des machines virtuelles sur les prix, consultez la rubrique <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Tarification HDInsight</a>.


5. Sur la page **Configuration de l'utilisateur du cluster**, entrez les valeurs suivantes :

    ![Fournir des détails sur l’utilisateur Admin et l’utilisateur RDP](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "Fournir des détails sur l’utilisateur Admin et l’utilisateur RDP")

    <table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom d’utilisateur HTTP</td>
		<td>Spécifiez le nom d'utilisateur de cluster HDInsight.</td></tr>
	<tr><td>Mot de passe HTTP/Confirmer le mot de passe</td>
		<td>Spécifiez le mot de passe de cluster HDInsight.</td></tr>
	<tr><td>Activation du Bureau à distance pour le cluster</td>
		<td>Cochez cette case pour spécifier une date d’expiration, un nom d’utilisateur et un mot de passe pour un utilisateur de bureau à distance pouvant se connecter aux nœuds de cluster à distance, après configuration. Vous pouvez également activer le Bureau à distance ultérieurement, une fois le cluster configuré. Pour la marche à suivre, consultez <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l’aide de RDP</a>.</td></tr>
	<tr><td>Sélection du metastore Hive/Oozie</td>
		<td>Cochez cette case pour spécifier une base de données SQL sur le même centre de données que le cluster, afin de l’utiliser en tant que metastore Hive/Oozie. Si vous cochez cette case, vous devez renseigner plusieurs informations concernant la base de données SQL Azure dans les pages suivantes de l’Assistant. Ceci est utile si vous voulez conserver les métadonnées sur les tâches Hive/Oozie après la suppression d'un cluster. Pour obtenir des instructions sur la création d’un metastore, consultez&#160;<a href="https://azure.microsoft.com/fr-fr/documentation/articles/sql-database-get-started/" target="_blank">Créer votre première base de données SQL&#160;Azure</a>.</td></tr>
	</td></tr>
</table>>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois dans l’affichage du serveur, cliquez sur **Configurer**, puis pour **Services** **Microsoft Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

    Cliquez sur la flèche droite.

6. Sur la page **Configuration du metastore Hive/Oozie**, entrez les valeurs suivantes :

    ![Fournir des détails sur la base de données de metastore Hive/Oozie](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "Fournir des détails sur la base de données de metastore Hive/Oozie")

	Spécifiez la base de données SQL Azure qui sera utilisée comme metastore Hive/Oozie. Vous pouvez spécifier la même base de données pour les metastores Hive et Oozie. Cette base de données SQL doit se trouver dans le même centre de données que le cluster HDInsight. La liste répertorie uniquement les bases de données SQL situées dans le même centre de données que celui spécifié sur la page <strong>Détails du cluster</strong>. Spécifiez également le nom d’utilisateur et un mot de passe pour se connecter à la base de données SQL Azure que vous avez sélectionnée.

    >[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

    Cliquez sur la flèche droite.

7. Sur la page **Compte de stockage**, entrez les valeurs suivantes :

    ![Entrer les détails du compte de stockage](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "Entrer les détails du compte de stockage")

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Compte de stockage</td>
		<td>Spécifiez le compte Azure Storage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Vous pouvez choisir l'une des trois options suivantes&#160;:
		<ul>
			<li><strong>Utiliser le stockage existant</strong></li>
			<li><strong>Créer un nouveau stockage</strong></li>
			<li><strong>Utiliser le stockage associé à un autre abonnement</strong></li>
		</ul>
		</td></tr>
	<tr><td>Nom du compte</td>
		<td><ul>
			<li>Si vous choisissez d’utiliser un stockage existant, pour <strong>Nom du compte</strong>, sélectionnez un compte de stockage existant. La liste déroulante répertorie uniquement les comptes de stockage situés dans le centre de données sur lequel vous voulez approvisionner le cluster.</li>
			<li>Si vous choisissez l’option <strong>Créer un nouveau stockage</strong> ou <strong>Utiliser le stockage associé à un autre abonnement</strong>, vous devez fournir le nom du compte de stockage.</li>
		</ul></td></tr>
	<tr><td>Clé du compte</td>
		<td>Si vous choisissez l’option <strong>Utiliser le stockage associé à un autre abonnement</strong>, veuillez indiquer la clé de ce compte de stockage.</td></tr>
	<tr><td>Conteneur par défaut</td>
		<td><p>Spécifiez le conteneur par défaut du compte de stockage qui sera utilisé comme système de fichiers par défaut pour le cluster HDInsight. Si vous sélectionnez l’option <strong>Utiliser le stockage existant</strong> pour le champ <strong>Compte de stockage</strong> et qu’il n’y a aucun conteneur existant dans ce compte, le conteneur est créé par défaut avec le même nom que celui du cluster. Si un conteneur portant le nom du cluster existe déjà, un numéro de séquence est ajouté au nom de conteneur. Par exemple, mon_conteneur1, mon_conteneur2, et ainsi de suite. Cependant, si le compte de stockage existant a un conteneur dont le nom est différent de celui du cluster spécifié, vous pouvez également utiliser ce conteneur.</p>
        <p>Si vous choisissez de créer un stockage ou d’utiliser le stockage d’un autre abonnement Azure, vous devez spécifier le nom du conteneur par défaut.</p>
    </td></tr>
	<tr><td>Comptes de stockage supplémentaires</td>
		<td>HDInsight prend en charge plusieurs comptes de stockage. Le nombre de comptes de stockage supplémentaires pouvant être utilisés par un cluster n’est pas limité. Toutefois, si vous créez un cluster au moyen du portail Azure, la limite est établie à sept en raison de contraintes liées à l’interface utilisateur. Chaque compte de stockage supplémentaire que vous spécifiez dans ce champ ajoute une page **Compte de stockage** supplémentaire vers l’Assistant vous permettant de spécifier les informations de compte. Par exemple, dans la capture d’écran suivante, 1&#160;compte de stockage supplémentaire est sélectionné, la page&#160;5 est donc ajoutée à la boîte de dialogue.</td></tr>
</table>Cliquez sur la flèche droite.

8. Si vous avez choisi de configurer un stockage supplémentaire pour le cluster, dans la page **Compte de stockage**, entrez les informations du compte pour le compte de stockage supplémentaire :

	![Fournir des détails supplémentaires sur le compte de stockage](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "Fournir des détails supplémentaires sur le compte de stockage")

    De nouveau, vous avez le choix entre le stockage existant, la création d'un stockage ou l'utilisation du stockage d'un autre abonnement Azure. La procédure pour fournir les valeurs est semblable à la procédure précédente.

    > [AZURE.NOTE]Une fois qu’un compte de stockage Azure a été choisi pour votre cluster HDInsight, vous ne pouvez ni le supprimer, ni le remplacer par un autre.

8. Dans la page **Actions de script**, cliquez sur **ajouter une action de script** et fournissez des détails sur le script personnalisé que vous souhaitez exécuter pour personnaliser un cluster pendant la création de ce dernier. Pour plus d’informations, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script][hdinsight-customize-cluster].

	Si vous devez utiliser une action de script, indiquez les entrées suivantes.
	
	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l'action de script.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster.</td></tr>
	<tr><td>Type de nœud</td>
		<td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande.</td></tr>
</table>Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.



##<a id="powershell"></a> Utilisation d’Azure PowerShell

Cette section fournit des instructions sur l’approvisionnement d’un cluster Apache Spark dans Azure HDInsight à l’aide d’Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des cmdlets HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell][powershell-install-configure]. Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell][hdinsight-admin-powershell]. Pour la liste des cmdlets Windows PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE]Bien que vous puissiez utiliser les scripts de cette section pour configurer un cluster HDInsight sur un réseau virtuel Azure, ceux-ci ne permettent pas de créer un réseau de ce type. Pour plus d’informations sur la création d’un réseau Azure Virtual Network, consultez la rubrique [Tâches de configuration d’un réseau virtuel](http://msdn.microsoft.com/library/azure/jj156206.aspx).

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

**Pour approvisionner un cluster HDInsight**

- Exécutez les commandes suivantes à partir d'une fenêtre de console Azure PowerShell :

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE]Les informations d’identification que vous spécifiez sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Vous utiliserez ce compte pour vous connecter au cluster et exécuter des tâches. Si vous utilisez l’option Création rapide à partir du portail Azure pour configurer un cluster, le nom d’utilisateur Hadoop par défaut est « admin ». Ne confondez pas ce compte avec le compte d’utilisateur de protocole RDP (Remote Desktop Protocol) Le compte d’utilisateur RDP doit être différent du compte d’utilisateur Hadoop. Pour plus d’informations, consultez la rubrique [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][hdinsight-admin-portal].

	La mise en service du cluster peut prendre plusieurs minutes.

	![HDI.PS.Provision](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "Approvisionner un cluster Spark à l’aide de la configuration de base dans PowerShell")


**Approvisionnement d’un cluster HDInsight au moyen d’options de configuration personnalisées**

Lorsque vous approvisionnez un cluster, vous pouvez utiliser d’autres options de configuration, telles qu’une connexion à plusieurs conteneurs de stockage d’objets blob Azure ou l’utilisation d’une base de données SQL Azure pour les metastores Hive et Oozie. Ceci vous permet de séparer la durée de vie de vos données et métadonnées de celle du cluster.

> [AZURE.NOTE]Les cmdlets Windows PowerShell constituent la seule méthode recommandée pour modifier les variables de configuration d’un cluster HDInsight. Les modifications apportées aux fichiers de configuration Hadoop pendant une connexion au cluster via le Bureau distant peuvent être remplacées en cas de mise à jour corrective du cluster. Dans ce cas, les valeurs de configuration définies via Azure PowerShell seront conservées.

- Exécutez les commandes suivantes à partir d'une fenêtre PowerShell Windows :

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois dans l’affichage du serveur, cliquez sur **Configurer**, puis pour **Services** **Microsoft Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

	L’approvisionnement du cluster peut prendre plusieurs minutes.

**Pour afficher la liste des clusters HDInsight**

- Exécutez la commande suivante à partir de la fenêtre de console Azure PowerShell pour répertorier le cluster HDInsight et vérifier qu’il est correctement approvisionné :

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a> Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET.

Les procédures suivantes sont nécessaires pour approvisionner un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

- Installation du Kit de développement logiciel (SDK) HDInsight
- Créer un certificat auto-signé
- Création d'une application console
- Exécution de l'application


**Pour installer le Kit de développement logiciel (SDK) .NET HDInsight**

Vous pouvez installer la dernière version publiée du Kit de développement logiciel (SDK) disponible sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

Créez un certificat auto-signé, installez-le sur votre poste de travail et téléchargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


**Pour créer une application console Visual Studio**

1. Ouvrez Visual Studio 2013.

2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :

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

6. Exécutez la commande suivante dans la console pour installer les packages :

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Cette commande ajoute des bibliothèques .NET et leurs références nécessaires au projet Visual Studio en cours.

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions using suivantes au début du fichier :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. Dans la fonction main(), copiez et collez le code suivant :

        string thumbprint = "<CertificateFriendlyName>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. Remplacez les variables au début de la fonction main().

**Exécution de l'application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.


##<a id="nextsteps"></a>Étapes suivantes

* Consultez [Effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](hdinsight-apache-spark-use-bi-tools.md) pour découvrir comment utiliser Apache Spark dans HDInsight avec des outils décisionnels tels que Power BI et Tableau. 
* Consultez [Utiliser Spark sur HDInsight pour créer des applications d’apprentissage automatique](hdinsight-apache-spark-ipython-notebook-machine-learning.md) pour découvrir comment créer des applications d’apprentissage automatique à l’aide d’Apache Spark sur HDInsight.
* Consultez [Utiliser Spark sur HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) pour découvrir comment créer des applications de diffusion en continu à l’aide d’Apache Spark sur HDInsight.
* Consultez [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md) pour découvrir comment utiliser le gestionnaire de ressources pour gérer les ressources allouées au cluster Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Utilisation de Sqoop avec HDInsight"

<!---HONumber=July15_HO3-->