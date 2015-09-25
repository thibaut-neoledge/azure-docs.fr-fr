<properties
   pageTitle="Approvisionnement personnalisé de clusters Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à approvisionner et à personnaliser les clusters pour Azure HDInsight à l'aide du portail Azure, d'Azure PowerShell, d'une ligne de commande ou du Kit de développement (SDK) .NET HDInsight."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/03/2015"
   ms.author="jgao"/>

# Approvisionnement de clusters dans HDInsight

Apprenez à planifier l’approvisionnement de clusters HDInsight.

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]

* [Approvisionnement de clusters dans HDInsight](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [version préliminaire du portail azure hdinsight](../../includes/hdinsight-azure-preview-portal.md)]

* [Approvisionnement de clusters dans HDInsight](hdinsight-provision-clusters-v1.md)

**Configuration requise :**

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Options de configuration de base


- **Nom du cluster**

	Le nom du cluster permet d’identifier un cluster. Le nom du cluster doit respecter les instructions suivantes :

	- Le champ doit être une chaîne comportant entre 3 et 63 caractères.
	- Le champ ne peut contenir que des lettres, des chiffres et des traits d'union.

- **Nom d'abonnement**

	Un cluster HDInsight est lié à un abonnement Azure.

- **Nom de groupe ressources**

	Les applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et services tiers. Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d’informations, consultez la page[Présentation du gestionnaire des ressources Azure](resource-group-overview.md).	
- **Système d’exploitation**

	Vous pouvez configurer des clusters HDInsight sur un des deux systèmes d’exploitation suivants : - **HDInsight sur Windows (Windows Server 2012 R2 Datacenter)** : - **HDInsight sur Linux (Ubuntu 12.04 LTS pour Linux) (version préliminaire)** : HDInsight vous offre la possibilité de configurer des clusters Linux sur Azure. Configurez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour plus d’informations, consultez [Prise en main de Hadoop sur Linux dans HDInsight](hdinsight-hadoop-linux-get-started.md).


- **Version de HDInsight**

	Elle permet de déterminer la version de HDInsight à utiliser pour ce cluster. Pour plus d’informations, consultez la rubrique [Versions et composants de cluster Hadoop dans HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Type de cluster** et **taille du cluster (également appelé nœuds de données)**

	HDInsight permet aux clients de déployer de nombreux types de cluster pour différentes charges de travail d’analyse des données. Les types de cluster proposés actuellement sont :

	- Les clusters Hadoop : pour les charges de travail de requête et d’analyse
	- Les clusters HBase : pour les charges de travail NoSQL
	- Les clusters Storm : pour les charges de travail de traitement des événements en temps réel
	- Les clusters Spark (version préliminaire) : pour les charges de travail de traitement en mémoire, de requêtes interactives, de diffusion en continu et d’apprentissage automatique.

	![Clusters HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]Le *cluster Azure HDInsight* est également appelé *Cluster Hadoop dans HDInsight* ou *Cluster HDInsight*. Parfois, il est utilisé de manière interchangeable avec le *cluster Hadoop*. Ils font tous référence aux clusters Hadoop hébergés dans l'environnement Microsoft Azure.

	Un type de cluster donné comprend différents rôles correspondant aux différents nœuds, qui permettent à un client de dimensionner ces nœuds dans un rôle donné, approprié aux détails de leur charge de travail. Les nœuds de travail d’un cluster Hadoop peuvent, par exemple, être dotés d'une grande capacité de mémoire si les types d'analyses en cours d'exécution utilisent beaucoup de mémoire.

	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Les clusters Hadoop pour HDInsight sont déployés avec deux rôles :

	- Nœud principal (2 nœuds)
	- Nœud de données (au moins 1 nœud)

	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	Les clusters HBase pour HDInsight sont déployés avec trois rôles : - les serveurs principaux (2 nœuds) - les serveurs Region (au moins 1 nœud) - les nœuds Master/Zookeeper (3 nœuds)

	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Les clusters Storm pour HDInsight sont déployés avec trois rôles : - les nœuds Nimbus (2 nœuds) - les serveurs Supervisor (au moins 1 nœud) - les nœuds Zookeeper (3 nœuds)


	![Rôles de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Les clusters Spark pour HDInsight sont déployés avec trois rôles : - le nœud principal (2 nœuds) - le nœud de travail (au moins 1 nœud) - les nœuds Zookeeper (3 nœuds) (gratuits pour les Zookeepers A1)

	L’utilisation de ces nœuds est facturée aux clients pendant toute la durée de vie du cluster. Cette facturation démarre une fois qu'un cluster est créé et s'arrête lorsque le cluster est supprimé (les clusters ne peuvent pas être désalloués ou mis en suspens). La taille du cluster a une incidence sur le prix du cluster. À des fins d’apprentissage, il est recommandé d'utiliser le nœud de données 1. Pour plus d’informations sur la tarification de HDInsight, consultez la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]La limite de taille de cluster varie selon les abonnements Azure. Contactez le support de facturation pour augmenter la limite.

- **Région/réseau virtuel (également appelé emplacement)**

	![Régions Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Taille du nœud**

	![tailles de nœud de machine virtuelle HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Sélectionnez une taille de machine virtuelle pour les nœuds. Pour en savoir plus, consultez la rubrique [Tailles de services cloud](cloud-services-sizes-specs.md)

	Selon votre choix de machines virtuelles, les coûts peuvent varier. HDInsight utilise toutes les machines virtuelles de niveau standard pour les nœuds de cluster. Pour plus d’informations sur les répercussions de la taille des machines virtuelles sur les prix, consultez <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Tarification HDInsight</a>.


- **Utilisateurs HDInsight**

	Les clusters HDInsight vous permettent de configurer deux comptes d'utilisateur lors de la configuration :

	- Utilisateur HTTP. Si vous utilisez la configuration de base à partir de la version préliminaire du portail Azure, le nom d'utilisateur par défaut est admin.
	- Utilisateur RDP (clusters Windows) : il est utilisé pour se connecter au cluster à l'aide de RDP. Lorsque vous créez le compte, vous devez définir une date d'expiration qui est de 90 jours à compter d'aujourd'hui.
	- Utilisateur SSH (clusters Linux) : il est utilisé pour se connecter au cluster à l'aide de SSH. Vous pouvez créer des comptes d’utilisateur SSH supplémentaires une fois le cluster créé, en suivant la procédure décrite dans la rubrique [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Un compte Azure Storage**

	Le HDFS d'origine utilise de nombreux disques locaux sur le cluster. HDInsight utilise à la place le stockage d'objets blob Azure pour stocker des données. Le stockage d’objets blob Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. Grâce à une interface HDFS (Hadoop Distributed File System), l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées dans le stockage d’objets blob. Le stockage de données dans le stockage d’objets blob vous permet de supprimer les clusters HDInsight servant aux calculs, sans perte de données utilisateur.

	Durant la configuration, vous devez spécifier un compte de stockage Azure, ainsi qu’un conteneur de stockage d’objets blob Azure sur le compte de stockage Azure. Certains processus de configuration requièrent le compte de stockage Azure et le conteneur de stockage d'objets blob qui a été créé au préalable. Le conteneur de stockage d’objets blob est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des comptes Azure Storage supplémentaires (stockage lié) qui seront accessibles via le cluster. Le cluster peut également accéder aux conteneurs d’objets blob qui sont configurés avec un accès public en lecture complet ou un accès public en lecture pour les objets blob uniquement. Pour en savoir plus sur la restriction d’accès, consultez la rubrique [Gestion de l’accès aux ressources d’Azure Storage](storage-manage-access-to-resources.md).

	![Stockage HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Un conteneur de stockage d'objets blob regroupe un ensemble d'objets blob, comme illustré dans l'image :

	![Stockage des objets blob](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]Ne partagez pas un conteneur de stockage d’objets blob sur plusieurs clusters. Ce n’est pas pris en charge.

	Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

- **Metastore Hive/Oozie**

	Le metastore contient les métadonnées Hive et Oozie, telles que les tables, les partitions, les schémas et les colonnes Hive. L’utilisation du metastore vous permet de conserver vos métadonnées Hive et Oozie, afin de ne pas devoir recréer des tables Hive ou des tâches Oozie lorsque vous configurez un nouveau cluster. Hive utilise par défaut une base de données intégrée Azure SQL pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé. Par exemple, vous disposez d’un cluster qui a été configuré avec un metastore Hive. Vous avez créé des tables Hive. Après avoir supprimé et recréé le cluster à l'aide du même metastore Hive, vous ne pourrez pas afficher les tables Hive que vous avez créées dans le cluster d'origine.

## Options de configuration avancées

>[AZURE.NOTE]Cette section ne s'applique actuellement qu’aux clusters HDInsight Windows.

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

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la configuration. Ces scripts sont appelés à l’aide de l’option **action de script**, une option de configuration qui peut être utilisée à partir de la version préliminaire du portail Azure, des applets de commande HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).


### Utilisation des réseaux virtuels Azure

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Vous devez créer le réseau Azure Virtual Network avant d’approvisionner un cluster HDInsight. Pour plus d’informations, consultez [Approvisionnement d’un cluster Hadoop sur un réseau virtuel](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network).
>
> Azure HDInsight prend uniquement en charge les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec les réseaux virtuels basés sur des groupes d'affinités. Utilisez l’applet de commande Get-AzureVNetConfig d’Azure PowerShell pour vérifier si un réseau virtuel Azure est basé sur l'emplacement. Si votre réseau virtuel n'est pas basé sur l'emplacement, vous disposez des options suivantes :
>
> - Exportez la configuration du réseau virtuel existant, puis créez un réseau virtuel. Les nouveaux réseaux virtuels sont basés par défaut sur l’emplacement.
> - Migrez vers un réseau virtuel basé sur l’emplacement. Consultez la rubrique [Migration des services existants vers une portée régionale](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

## Utilisation de la version préliminaire du portail Azure

Vous pouvez vous référer aux [options de configuration de base] et aux [options de configuration avancées] pour obtenir une description des champs.

**Création d'un cluster HDInsight**

1. Connectez-vous à la [version préliminaire du portail Azure][azure-preview-portal].
2. Cliquez sur **NOUVEAU**, cliquez sur **Analyse de données**, puis sur **HDInsight**.

    ![Création d'un nouveau cluster dans la version préliminaire du portail Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Création d'un nouveau cluster dans la version préliminaire du portail Azure")

3. Tapez ou sélectionnez les valeurs suivantes :

  * **Nom de cluster** : saisissez un nom pour le cluster. Une coche verte s‘affiche en face du nom de cluster si le nom est disponible.
  * **Type de cluster** : sélectionnez **Hadoop**.
  * **Système d’exploitation de cluster** : sélectionnez **Windows Server 2012 R2 Datacenter**.
  * **Abonnement** : sélectionnez l’abonnement Azure qui sera utilisé pour l’approvisionnement de ce cluster.
  * **Groupe de ressources** : sélectionnez un groupe de ressources existant ou créez un nouveau groupe de ressource. Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.
  * **Informations d’identification** : configurer le nom d’utilisateur et le mot de passe de l’utilisateur Hadoop (HTTP). Si vous activez le bureau à distance du cluster, vous devrez configurer le nom d'utilisateur et le mot de passe du bureau distant, et une date d'expiration de compte. Cliquez sur **Sélectionner** au bas de l’écran pour enregistrer les modifications.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Source de données** : créez un compte de stockage Azure ou sélectionnez un compte de stockage Azure existant à utiliser comme système de fichiers par défaut pour le cluster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Méthode de sélection**: affectez la valeur **De tous les abonnements** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez la valeur **Clé d’accès** si vous souhaitez entrer le **Nom de stockage** et la **Clé d’accès** d’un compte de stockage existant.
  		* **Sélectionner le compte de stockage/Créer un compte**: cliquez sur **Sélectionner le compte de stockage** pour rechercher et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer** pour créer un compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.
  		* **Choisir le conteneur par défaut**: utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.
  		* **Emplacement**: région géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé. Cet emplacement détermine l'emplacement du cluster.  Le cluster et le compte de stockage par défaut doit se situer dans le même datacenter Azure.
  	
  * **Niveaux de tarification du nœud** : définissez le nombre de nœuds de travail dont vous avez besoin pour le cluster. Le coût estimé du cluster s'affiche dans le panneau.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuration facultative** sert à sélectionner la version de cluster et à configurer d’autres paramètres facultatifs, par exemple ajouter un **Réseau virtuel**, définir un **Metastore externe** pour conserver les données Hive et Oozie, utilisez des Actions de Script pour personnaliser un cluster et installer des composants personnalisés ou utiliser des comptes de stockage supplémentaires avec le cluster.

  		* **Version HDInsight**: sélectionnez la version que vous souhaitez utiliser pour le cluster. Pour plus d’informations, consultez la rubrique [Versions de clusters HDInsight](hdinsight-component-versioning.md).
  		* **Réseau virtuel**: sélectionnez un réseau virtuel Azure et le sous-réseau si vous souhaitez placer le cluster dans un réseau virtuel.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Le cluster HDInsight Windows ne peut être placé que sur un réseau virtuel classique.
  

  		
		* **Metastores externes**: permet de spécifier la base de données SQL 
 

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			Pour **Utiliser une base de données SQL existante pour les métadonnées Hive**, cliquez sur **Oui**, sélectionnez une base de données SQL, puis indiquez le nom d’utilisateur/mot de passe pour la base de données. Répétez ces étapes si vous souhaitez **utiliser une base de données SQL existante pour les métadonnées Oozie**. Cliquez sur **Sélectionner** jusqu’à ce que vous reveniez au panneau **Configuration facultative**.


			>[AZURE.NOTE] La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur.  Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.
		
  		* **Actions de script** vous permet d’utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md). Dans le panneau Actions de script, saisissez les informations requises, comme illustré dans la capture d’écran ci-dessous.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Clés de stockage Azure**: permet de spécifier des comptes de stockage supplémentaire à associer au cluster. Dans le panneau **Clés de stockage Azure**, cliquez sur **Ajouter une clé de stockage**, puis sélectionnez un compte de stockage existant ou créez-en un.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Cliquez sur **Create**. En sélectionnant l’option **Épingler au Tableau d’accueil**, une vignette est ajoutée pour le cluster dans le Tableau d’accueil de votre portail en version préliminaire. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.


	| Pendant l’approvisionnement | Approvisionnement terminé |
	| ------------------ | --------------------- |
	| ![Indicateur d’approvisionnement sur le tableau d’accueil](./media/hdinsight-provision-clusters/provisioning.png) | ![Vignette de cluster approvisionné](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du Tableau d’accueil, ou l’entrée **Notifications** à gauche de la page pour vérifier le processus de d'approvisionnement.
	

5. Une fois la configuration terminée, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.


	![Panneau Cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propriétés du cluster")


	Utilisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans la section dans la section **Bases** :


	* **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.
	* **Tableau de bord**, **Tableau de bord du cluster** et **URL** : il s’agit de toutes les façons d’accéder au tableau de bord de cluster, qui est un portail web permettant d’exécuter des tâches sur le cluster.
	* **Bureau à distance** : permet d’activer/de désactiver le Bureau à distance sur les nœuds de cluster.
	* **Mise à l’échelle du cluster** : permet de modifier le nombre de nœuds de travail pour ce cluster.
	* **Supprimer** : permet de supprimer le cluster HDInsight.
	* **Démarrage rapide** (![icône nuage et foudre = démarrage rapide](./media/hdinsight-provision-clusters/quickstart.png)) : affiche des informations qui vous aideront à prendre en main HDInsight.
	* **Utilisateurs** (![icône d’utilisateurs](./media/hdinsight-provision-clusters/users.png)) : permet de définir des autorisations pour la _gestion via le portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.
	

		> [AZURE.IMPORTANT]Il affecte _uniquement_ les autorisations d’accès à ce cluster dans la version préliminaire du portail et n’a aucun effet sur les personnes autorisées à se connecter au cluster HDInsight, ou à soumettre des travaux.
		
	* **Balises** (![icône de balise](./media/hdinsight-provision-clusters/tags.png)) : permettent de spécifier des paires clé/valeur pour définir une classification personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.


## Approvisionnement à l’aide du modèle Azure Resource Manager

Le modèle Azure Resource Manager (ARM) facilite le déploiement et le redéploiement du cluster. La procédure qui suit crée un cluster Hadoop sur le système d’exploitation Linux dans le centre de données Europe du Nord avec 4 nœuds Workerl.

**Pour déployer un cluster à l’aide du modèle ARM**

1. Enregistrez le fichier json dans l’annexe A de votre station de travail.
2. Vérifiez les paramètres si nécessaire.
3. Exécutez le modèle à l’aide du script PowerShell suivant.

		$resourceGroupName = "<ResourceGroupName>"
		$Location = "<ResourceGroupLocation>"
		
		$armDeploymentName = "<ARMDeploymentName>"
		$clusterName = "<ClusterName>"
		$clusterStorageAccountName = "<DefaultStorageAccountName>"
		
		# Connect to Azure
		Switch-AzureMode -Name AzureResourceManager
		Add-AzureAccount
		
		# Create a resource group
		New-AzureResourceGroup -Name $resourceGroupName -Location $Location
		
		# Provision cluster and the dependent storage accounge
		$parameters = @{clusterName="$clusterName";clusterStorageAccountName="$clusterStorageAccountName"}
		
		New-AzureResourceGroupDeployment `
		    -Name $armDeploymentName `
		    -ResourceGroupName $resourceGroupName `
		    -TemplateFile E:\Tutorials\HDIARMTemplates\ARMTemplate-create-hadoop-cluster-with-storage.json `
		    -TemplateParameterObject $parameters
		
		# List cluster
		Get-AzureHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName

Pour déployer un modèle ARM en utilisant les autres méthodes, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](resource-group-template-deploy.md).


## En cas d’utilisation d’Azure PowerShell
Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur la configuration d’un cluster HDInsight à l’aide d’Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des cmdlets HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande Windows PowerShell pour HDInsight, consultez la rubrique [Référence des applets de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant Azure PowerShell :

- Création d’un groupe de ressources Azure
- Création d’un compte Azure Storage
- Création d'un conteneur d'objets blob Azure
- Création d'un cluster HDInsight


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop


	![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

## Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET. Suivez les instructions ci-dessous pour créer une application de console Visual Studio et collez le code pour la création d’un cluster.

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

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Gérer les packages NuGet pour la solution**. Dans la zone de texte de recherche de la boîte de dialogue, recherchez **HDInsight**. Parmi les résultats qui s’affichent, installez les éléments suivants :

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Recherchez « Azure Authentication » et, parmi les résultats qui s’affichent, installez **Microsoft.Azure.Common.Authentication**.

6. Dans l’Explorateur de solutions, double-cliquez sur **Program.cs** pour l’ouvrir, collez le code suivant et fournissez des valeurs pour les variables :


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
		        }

		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                Location = NewClusterLocation,
		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. Appuyez sur **F5** pour exécuter l'application. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. Vous êtes invité à saisir les informations d’identification de votre compte Azure. La création d'un cluster HDInsight peut prendre plusieurs minutes.


## Création d’un cluster HDInsight à l’aide des services SQL Server Integration Services locaux

Vous pouvez également utiliser les services SQL Server Integration Services (SSIS) pour créer ou supprimer un cluster HDInsight. Le pack de fonctionnalités Azure pour SSIS fournit les composants suivants, compatibles avec les clusters HDInsight.


- [Tâche de création de clusters Azure HDInsight][ssisclustercreate]
- [Tâche de suppression de clusters Azure HDInsight][ssisclusterdelete]
- [Gestionnaire de connexions d’abonnement Azure][connectionmanager]

Pour en savoir plus sur le pack de fonctionnalités Azure pour SSIS, cliquez [ici][ssispack].


##<a id="nextsteps"></a>Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d’Azure HDInsight](hdinsight-get-started.md) : apprenez à utiliser votre cluster HDInsight
* [Utilisation de Sqoop avec HDInsight](hdinsight-use-sqoop.md) : découvrez comment copier des données entre HDInsight et une base de données SQL Azure ou SQL Server
* [Administration de HDInsight à l’aide de PowerShell](hdinsight-administer-use-powershell.md) : découvrez comment utiliser HDInsight avec PowerShell
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md) : découvrez comment envoyer des tâches par programme à HDInsight
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation] : découvrez le Kit de développement logiciel (SDK) HDInsight



##Annexe A - modèle ARM

Le modèle Azure Resource Manager suivant crée un cluster Hadoop avec le compte de stockage Azure dépendant.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "hdiuser",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
	    },
	    "clusterStorageType": {
	      "type": "string",
	      "defaultValue": "Standard_LRS",
	      "allowedValues": [
	        "Standard_LRS",
	        "Standard_GRS",
	        "Standard_ZRS"
	      ]
	    },
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/fr-FR/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/fr-FR/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/fr-FR/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/fr-FR/library/mt146778(v=sql.120).aspx

<!----HONumber=Sept15_HO2-->