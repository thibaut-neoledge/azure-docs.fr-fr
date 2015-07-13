<properties
   pageTitle="Approvisionnement personnalisé de clusters Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à approvisionner et à personnaliser les clusters pour Azure HDInsight à l’aide du portail Azure, d’Azure PowerShell, d’une ligne de commande ou du Kit de développement (SDK) .NET HDInsight."
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
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#Approvisionnement de clusters Hadoop dans HDInsight à l’aide d’options personnalisées

Découvrez les différentes méthodes pour approvisionner de façon personnalisée des clusters Hadoop sur Azure HDInsight, en utilisant le portail Azure, Azure PowerShell, l’interface de ligne de commande ou le Kit de développement logiciel (SDK) .NET HDInsight. Pour obtenir des instructions sur la configuration des clusters HBase et Storm, consultez [Approvisionnement de clusters HBase dans HDInsight](../hdinsight-hbase-get-started.md) et [Prise en main de Storm sur HDInsight](../hdinsight-storm-getting-started.md). Consultez l’article <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Différences entre Hadoop et HBase</a> pour pouvoir choisir plus facilement entre l’un et l’autre.

## Qu'est-ce qu'un cluster HDInsight ?

Vous êtes-vous déjà demandé pourquoi nous évoquons les clusters à chaque fois que nous parlons d’Hadoop ou des données volumineuses ? C'est parce qu'Hadoop permet le traitement distribué de grandes quantités de données, diffusées entres les différents nœuds d'un cluster. Le cluster dispose d'une architecture maître/esclave composée d'un maître (également nommé nœud ou nœud de nom) et de plusieurs esclaves (également nommés nœuds de données). Pour plus d'informations, consultez [Apache Hadoop][apache-hadoop].

![Cluster HDInsight][img-hdi-cluster]

Un cluster HDInsight résume les détails de l'implémentation Hadoop pour que vous n'ayez pas à vous demander comment communiquer avec différents nœuds d'un cluster. Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Pour plus d'informations, consultez la rubrique [Introduction à Hadoop dans HDInsight](hdinsight-hadoop-introduction.md). Les données destinées à l’attrition sont stockées dans un stockage d’objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

Cet article fournit des instructions sur les différentes méthode d'approvisionnement de cluster. Si vous recherchez une approche de démarrage rapide pour approvisionner un cluster, consultez [Prise en main d’Azure HDInsight](../hdinsight-get-started.md).

**Configuration requise :**

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les cmdlets Windows PowerShell pour HDInsight permettent d’effectuer les tâches en utilisant votre abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][azure-purchase-options], les [offres spéciales membres][azure-member-offers] ou la [version d'évaluation gratuite][azure-free-trial].

##<a id="configuration"></a>Options de configuration

###Clusters sur Linux

HDInsight offre la possibilité de configurer des clusters Linux sur Azure. Configurez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour plus d’informations, consultez [Prise en main de Hadoop sur Linux dans HDInsight](../hdinsight-hadoop-linux-get-started.md).

###Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d’objet blob Azure, ainsi qu’un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier un compte de stockage Azure supplémentaire, également associé à votre cluster.

>[AZURE.NOTE]Ne partagez pas un conteneur de stockage d’objets blob sur plusieurs clusters. Ce n’est pas pris en charge.

Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

###Metastore

Le metastore contient les métadonnées Hive et Oozie, telles que les tables, les partitions, les schémas et les colonnes Hive. L’utilisation du metastore vous permet de conserver vos métadonnées Hive et Oozie, afin de ne pas devoir recréer des tables Hive ou des tâches Oozie lorsque vous configurez un nouveau cluster. Hive utilise par défaut une base de données intégrée pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé.

### Personnalisation des clusters

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la configuration. Ces scripts sont appelés à l’aide de l’option **action de script**, une option de configuration qui peut être utilisée à partir du portail Azure, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).


###Réseau virtuel

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Vous devez créer le réseau Azure Virtual Network avant d’approvisionner un cluster HDInsight. Pour plus d'informations, consultez [Tâches de configuration de réseau virtuel](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

##<a id="portal"></a>Dans le portail Azure

Les clusters HDInsight utilisent un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire avant de pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la page [Création d’un compte de stockage](../storage-create-storage-account.md).


> [AZURE.NOTE]Actuellement, seul les régions **Est de l'Asie**, **Sud-Est de l'Asie**, **Europe du Nord**, **Europe de l'Ouest**, **Est des États-Unis**, **Ouest des États-Unis**, **Centre Nord des États-Unis** et **Centre Sud des États-Unis** peuvent héberger des clusters HDInsight.

**Pour créer un cluster HDInsight en utilisant l’option Création personnalisée**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Dans la partie inférieure de la page, cliquez sur **+ NEW**, sur **DATA SERVICES**, sur **HDINSIGHT**, puis sur **CUSTOM CREATE**.
3. Sur la page **Détails du cluster**, entrez ou sélectionnez les valeurs suivantes :

	![Fournir des détails du cluster Hadoop HDInsight][image-customprovision-page1]

    <table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Cluster Name</td>
		<td><p>Nom du cluster. </p>
			<ul>
			<li>Le nom DNS (Domain Name System ) doit commencer et finir par un caractère alphanumérique et peut contenir des traits d’union.</li>
			<li>Le champ doit être une chaîne comportant entre 3&#160;et 63&#160;caractères.</li>
			</ul></td></tr>
	<tr><td>Type du cluster</td>
		<td>Pour le type du cluster, sélectionnez <strong>Hadoop</strong>.</td></tr>
	<tr><td>Système d'exploitation</td>
		<td>Sélectionnez <b>Windows Server&#160;2012 R2 Datacenter</b> pour configurer un cluster Windows. Pour configurer un cluster Linux, consultez <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Approvisionnement de clusters Hadoop&#160;Linux dans HDInsight</a>.</td></tr>
	<tr><td>Version de HDInsight</td>
		<td>Choisissez la version. Pour Hadoop, la valeur par défaut est HDInsight version&#160;3.1, qui utilise Hadoop&#160;2.4.</td></tr>
	</table>Entrez ou sélectionnez les valeurs, comme indiqué dans le tableau, puis cliquez sur la flèche de droite.

4. Sur la page **Configurer le cluster**, entrez ou sélectionnez les valeurs suivantes :

	![Fournir des détails du cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>Nœuds de données</td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique. <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
<tr><td>Région/Réseau virtuel</td><td><p>Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu’un compte de stockage situé dans la région spécifiée ici.</p><p>Les régions disponibles sont&#160;: <strong>Asie de l’Est</strong>, <strong>Asie du Sud-Est</strong>, <strong>Europe du Nord</strong>, <strong>Europe de l’Ouest</strong>, <strong>Est des États-Unis</strong>, <strong>Ouest des États-Unis</strong>, <strong>Nord du centre des États-Unis</strong>, <strong>Sud du centre des États-Unis</strong>.<br/>Si vous avez créé un réseau virtuel Azure, vous pouvez sélectionner le réseau pour lequel le cluster HDInsight va être configuré.</p><p>Pour plus d’informations sur la création d’un réseau virtuel Azure, consultez la rubrique <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Tâches de configuration d’un réseau virtuel</a>.</p></td></tr>
<tr><td>Taille du nœud principal</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud principal.</p></td></tr>
<tr><td>Taille du nœud de données</td><td><p>Sélectionnez une taille de machine virtuelle pour le nœud de données.</p></td></tr>
</table>>[AZURE.NOTE]Selon votre choix de machines virtuelles, les coûts peuvent varier. HDInsight utilise toutes les machines virtuelles de niveau standard pour les nœuds de cluster. Pour plus d’informations concernant les répercussions de la taille des machines virtuelles sur les prix, consultez la rubrique <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Tarification HDInsight</a>.


5. Sur la page **Configuration de l'utilisateur du cluster**, entrez les valeurs suivantes :

    ![Fournir des détails utilisateur et metastore du cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom d’utilisateur HTTP</td>
		<td>Spécifiez le nom d'utilisateur de cluster HDInsight.</td></tr>
	<tr><td>Mot de passe HTTP/Confirmer le mot de passe</td>
		<td>Spécifiez le mot de passe de cluster HDInsight.</td></tr>
	<tr><td>Activation du Bureau à distance pour le cluster</td>
		<td>Cochez cette case pour spécifier une date d’expiration, un nom d’utilisateur et un mot de passe pour un utilisateur de bureau à distance pouvant se connecter aux nœuds de cluster à distance, après configuration. Vous pouvez également activer le Bureau à distance ultérieurement, une fois le cluster configuré. Pour la marche à suivre, consultez <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l’aide de RDP</a>.</td></tr>
	<tr><td>Sélection du metastore Hive/Oozie</td>
		<td>Cochez cette case pour spécifier une base de données SQL sur le même centre de données que le cluster, afin de l’utiliser en tant que metastore Hive/Oozie. Si vous cochez cette case, vous devez renseigner plusieurs informations concernant la base de données SQL Azure dans les pages suivantes de l’Assistant. Ceci est utile si vous voulez conserver les métadonnées sur les tâches Hive/Oozie après la suppression d'un cluster.</td></tr>
	</td></tr>
</table>Cliquez sur la flèche droite.

6. Sur la page **Configuration du metastore Hive/Oozie**, entrez les valeurs suivantes :

    ![Fournir l’utilisateur du cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	Spécifiez la base de données SQL Azure qui sera utilisée comme metastore Hive/Oozie. Vous pouvez spécifier la même base de données pour les metastores Hive et Oozie. Cette base de données SQL doit se trouver dans le même centre de données que le cluster HDInsight. La liste répertorie uniquement les bases de données SQL situées dans le même centre de données que celui spécifié sur la page <strong>Détails du cluster</strong>. Spécifiez également le nom d’utilisateur et un mot de passe pour se connecter à la base de données SQL Azure que vous avez sélectionnée.

    >[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

    Cliquez sur la flèche droite.


7. Sur la page **Compte de stockage**, entrez les valeurs suivantes :

    ![Fournir un compte de stockage pour le cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

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

7. Si vous avez choisi de configurer un stockage supplémentaire pour le cluster, dans la page **Compte de stockage**, entrez les informations du compte pour le compte de stockage supplémentaire :

	![Fournir des détails de stockage supplémentaires pour un cluster HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    De nouveau, vous avez le choix entre le stockage existant, la création d'un stockage ou l'utilisation du stockage d'un autre abonnement Azure. La procédure pour fournir les valeurs est semblable à la procédure précédente.

    > [AZURE.NOTE]Une fois qu’un compte de stockage Azure a été choisi pour votre cluster HDInsight, vous ne pouvez ni le supprimer, ni le remplacer par un autre.

8. Dans la page **Actions de Script**, cliquez sur **Ajouter une action de script** pour fournir des détails sur le script personnalisé que vous souhaitez exécuter pour personnaliser un cluster, pendant la création du cluster. Pour plus d’informations, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).

	![Configuration de l’action de script pour personnaliser un cluster HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

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
Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur la configuration d’un cluster HDInsight à l’aide d’Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des cmdlets HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des cmdlets Windows PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight][hdinsight-powershell-reference].

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

**Pour mettre en service un cluster HDInsight**

> [AZURE.NOTE]Les cmdlets Azure PowerShell constituent la seule méthode recommandée pour modifier les variables de configuration d’un cluster HDInsight. Les modifications apportées aux fichiers de configuration Hadoop pendant une connexion au cluster via le Bureau distant peuvent être remplacées en cas de mise à jour corrective du cluster. Dans ce cas, les valeurs de configuration définies via Azure PowerShell seront conservées.

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

	>[AZURE.NOTE]Les commandes $hadoopUserName et $hadoopUserPassword sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Vous utiliserez ce compte pour vous connecter au cluster et exécuter des tâches. Si vous utilisez l’option Création rapide à partir du portail Azure pour configurer un cluster, le nom d’utilisateur Hadoop par défaut est « admin ». Ne confondez pas ce compte avec le compte d’utilisateur de protocole RDP (Remote Desktop Protocol) Le compte d’utilisateur RDP doit être différent du compte d’utilisateur Hadoop. Pour plus d’informations, consultez la rubrique [Gestion des clusters Hadoop dans HDInsight au moyen du portail de gestion Azure][hdinsight-admin-portal].

	La mise en service du cluster peut prendre plusieurs minutes.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Approvisionnement d’un cluster HDInsight au moyen d’options de configuration personnalisées**

Tout en approvisionnant un cluster, vous pouvez utiliser d’autres options de configuration, comme une connexion à plusieurs conteneurs de stockage d’objets blob Azure en utilisant un réseau virtuel ou en utilisant une base de données SQL Azure pour les metastores Hive et Oozie. Ceci vous permet de séparer la durée de vie de vos données et métadonnées de celle du cluster.

> [AZURE.NOTE]Les cmdlets Windows PowerShell constituent la seule méthode recommandée pour modifier les variables de configuration d’un cluster HDInsight. Les modifications apportées aux fichiers de configuration Hadoop pendant une connexion au cluster via le Bureau distant peuvent être remplacées en cas de mise à jour corrective du cluster. Dans ce cas, les valeurs de configuration définies via Azure PowerShell seront conservées.

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

	>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

**Pour afficher la liste des clusters HDInsight**

- Exécutez la commande suivante à partir de la fenêtre de console Azure PowerShell pour répertorier le cluster HDInsight et vérifier qu’il est correctement approvisionné :

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Utilisation de l’interface de ligne de commande Azure

> [AZURE.NOTE]Depuis le 29 août 2014, l’interface de ligne de commande Azure ne permet plus d’associer un cluster à un réseau virtuel Azure.

Une autre possibilité pour l’approvisionnement d’un cluster HDInsight est l’interface de ligne de commande Azure. L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. Vous pouvez installer l'interface CLI à partir des emplacements suivants :

- **Kit de développement logiciel (SDK) Node.js** : <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Interface de ligne de commande Azure** - <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Pour une aide générale sur l’utilisation de l’interface de ligne de commande Azure, consultez la page [Interface de ligne de commande Azure pour Mac, Linux et Windows](../xplat-cli.md).

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

Avant d’utiliser l’interface de ligne de commande Azure, vous devez configurer la connectivité entre votre poste de travail et Azure. L’interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d’Azure dans un fichier de paramètres de publication. Le fichier de paramètres de publication peut ensuite être importé en tant que paramètre de configuration local persistant dont l’interface de ligne de commande Azure se servira pour les opérations ultérieures. Vous n’avez besoin d’importer vos paramètres de publication qu’une seule fois.

> [AZURE.NOTE]Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés du dossier ou utilisez le chiffrement du lecteur BitLocker.


1.	Ouvrez une fenêtre de terminal.
2.	Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

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

	> [AZURE.NOTE]Si vous obtenez une erreur indiquant que la commande du NPM est introuvable, vérifiez que les chemins d’accès suivants figurent dans la variable d’environnement PATH : <i>C:\Program Files (x86)\nodejs;C:\Users[nomutilisateur]\AppData\Roaming\npm</i> ou <i>C:\Program Files\nodejs;C:\Users[nomutilisateur]\AppData\Roaming\npm</i>

5.	Exécutez la commande suivante pour vérifier l'installation :

		azure hdinsight -h

	Vous pouvez utiliser le commutateur *-h* à différents niveaux pour afficher les informations d'aide. Par exemple :

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Installation de l’interface de ligne de commande à l’aide de Windows Installer**

1.	Accédez à **http://azure.microsoft.com/downloads/**. 2.	Faites défiler l’écran pour accéder à la section **Outils en ligne de commande**, puis cliquez sur **Interface de ligne de commande Azure** et suivez les étapes de l’Assistant Web Platform Installer.

**Pour télécharger et importer des paramètres de publication**

Avant d’utiliser l’interface de ligne de commande Azure, vous devez configurer la connectivité entre votre poste de travail et Azure. L’interface de ligne de commande se sert des informations sur votre abonnement Azure pour se connecter à votre compte. Ces informations peuvent être obtenues d’Azure dans un fichier de paramètres de publication. Le fichier de paramètres de publication peut ensuite être importé en tant que paramètre de configuration local persistant dont l’interface de ligne de commande Azure se servira pour les opérations ultérieures. Vous n’avez besoin d’importer vos paramètres de publication qu’une seule fois.

> [AZURE.NOTE]Le fichier de paramètres de publication contient des informations critiques. Microsoft vous recommande de supprimer le fichier ou de prendre des mesures supplémentaires pour chiffrer le dossier utilisateur contenant le fichier. Sous Windows, modifiez les propriétés de dossier ou utilisez BitLocker.


1.	Ouvrez une **Invite de commandes**.
2.	Exécutez la commande suivante pour télécharger le fichier de paramètres de publication :

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	La commande ouvre la page web sur laquelle télécharger le fichier de paramètres de publication.

3.	À l'invite d'enregistrement du fichier, cliquez sur **Enregistrer** et indiquez un emplacement pour l'enregistrement du fichier.
5.	À partir de la fenêtre d'invite de commandes, exécutez la commande suivante pour importer le fichier de paramètres de publication :

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

Pour plus d’informations sur la création d’un compte Azure Storage au moyen du portail Azure, consultez la rubrique [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account.md).

Si vous disposez déjà d’un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Pour plus d’informations sur l’obtention d’informations au moyen du portail Azure, consultez la section *Affichage, copie et régénération des clés d’accès de stockage* de la rubrique [Création, gestion ou suppression d’un compte de stockage](../storage-create-storage-account.md).

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

	>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Pour énumérer et afficher les détails de cluster**

- Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Pour supprimer un cluster**

- Utilisez les commandes suivantes pour supprimer un cluster :

		azure hdinsight cluster delete <ClusterName>



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


9. Dans la fonction Main(), copiez et collez le code suivant :

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
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
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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

10. Remplacez les variables au début de la fonction Main().

**Exécution de l’application**

Lorsque l'application est ouverte dans Visual Studio, appuyez sur **F5** pour l'exécuter. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. La création d'un cluster HDInsight peut prendre plusieurs minutes.



##<a id="nextsteps"></a> Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d’Azure HDInsight](../hdinsight-get-started.md) : apprenez à utiliser votre cluster HDInsight
* [Utilisation de Sqoop avec HDInsight](hdinsight-use-sqoop.md) : découvrez comment copier des données entre HDInsight et une base de données SQL Azure ou SQL Server
* [Administration de HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md) : découvrez comment utiliser HDInsight avec PowerShell
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md) : découvrez comment envoyer des tâches par programme à HDInsight
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation] : découvrez le Kit de développement logiciel (SDK) HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli.md

[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Fournir des détails du cluster Hadoop HDInsight"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Fournir les utilisateurs du cluster Hadoop HDInsight"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Fournir les détails du compte de stockage pour le cluster Hadoop HDInsight"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Fournir des détails supplémentaires du compte de stockage pour le cluster Hadoop HDInsight"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Configuration de l’action de script pour personnaliser un cluster HDInsight"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Énumération et affichage des clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

  [89e2276a]: hdinsight-use-sqoop.md "Utilisation de Sqoop avec HDInsight"
 

<!---HONumber=62-->