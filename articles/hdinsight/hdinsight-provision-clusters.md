<properties
   pageTitle="Création de clusters Hadoop basés sur Windows dans HDInsight | Microsoft Azure"
   	description="Apprenez à créer des clusters pour Azure HDInsight."
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
   ms.date="02/12/2016"
   ms.author="jgao"/>

# Création de clusters Hadoop basés sur Windows dans HDInsight

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-create-windows-cluster-selector.md)]

Apprenez à planifier la création de clusters HDInsight.

###Configuration requise :

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Options de configuration de base

Voici les options de configuration de base pour la création d'un cluster HDInsight.

- **Nom du cluster**

	Le nom du cluster permet d’identifier un cluster. Le nom du cluster doit respecter les instructions suivantes :

	- Le champ doit être une chaîne comportant entre 3 et 63 caractères.
	- Le champ ne peut contenir que des lettres, des chiffres et des traits d'union.

- **Nom d'abonnement**

	Un cluster HDInsight est lié à un abonnement Azure.

- **Nom de groupe ressources**

	Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](resource-group-overview.md).
	
- **Système d’exploitation**

	Vous pouvez créer des clusters HDInsight sur un des deux systèmes d’exploitation suivants : - **HDInsight sur Windows (Windows Server 2012 R2 Datacenter)** : - **HDInsight sur Linux (Ubuntu 12.04 LTS pour Linux)** : HDInsight vous offre la possibilité de configurer des clusters Linux sur Azure. Configurez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour plus d’informations, consultez [Prise en main de Hadoop sur Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

- **Type de cluster** et **taille du cluster (également appelé nœuds de données)**

	HDInsight permet aux clients de déployer de nombreux types de cluster pour différentes charges de travail d’analyse des données. Les types de cluster proposés actuellement sont :

	- Les clusters Hadoop : pour les charges de travail de requête et d’analyse
	- Les clusters HBase : pour les charges de travail NoSQL
	- Les clusters Storm : pour les charges de travail de traitement des événements en temps réel
	- Les clusters Spark : pour les charges de travail de traitement en mémoire, de requêtes interactives, de diffusion en continu et d’apprentissage automatique.

	![Clusters HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] Le *cluster Azure HDInsight* est également appelé *Cluster Hadoop dans HDInsight* ou *Cluster HDInsight*. Parfois, il est utilisé de manière interchangeable avec le *cluster Hadoop*. Ils font tous référence aux clusters Hadoop hébergés dans l'environnement Microsoft Azure.

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

	L’utilisation de ces nœuds est facturée aux clients pendant toute la durée de vie du cluster. Cette facturation démarre une fois qu'un cluster est créé et s'arrête lorsque le cluster est supprimé (les clusters ne peuvent pas être désalloués ou mis en suspens). La taille du cluster a une incidence sur le prix du cluster. À des fins d’apprentissage, il est recommandé d'utiliser le nœud de données 1. Pour plus d'informations sur la tarification de HDInsight, consultez la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] La limite de taille de cluster varie selon les abonnements Azure. Contactez le support de facturation pour augmenter la limite.

- **Version de HDInsight**

	Elle permet de déterminer la version de HDInsight à utiliser pour ce cluster. Pour plus d’informations, consultez [Versions et composants de cluster Hadoop dans HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).


- **Emplacement (région)**

	Le cluster et son compte de stockage par défaut doivent figurer dans le même emplacement Azure.
	
	![Régions Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Taille du nœud**

	![tailles de nœud de machine virtuelle HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Sélectionnez une taille de machine virtuelle pour les nœuds. Pour en savoir plus, consultez la rubrique [Tailles de services cloud](cloud-services-sizes-specs.md). Vous pouvez sélectionner le volume de ressources de calcul utilisé par le cluster. Par exemple, si vous savez que vous allez effectuer des opérations nécessitant un grand volume de mémoire, vous souhaitez sélectionner une ressource de calcul avec davantage de mémoire.

	>[AZURE.NOTE] Les nœuds utilisés par votre cluster ne sont pas considérés comme des machines virtuelles car les images de machines virtuelles utilisées pour les nœuds sont un détail d'implémentation du service HDInsight. Toutefois, les cœurs de calcul utilisés par les nœuds ne sont pas pris en compte dans le total de cœurs de calcul disponibles pour votre abonnement. Vous pouvez voir le nombre de cœurs qui seront utilisés par le cluster, ainsi que le nombre de cœurs disponibles, dans la section Résumé du panneau Niveaux de tarification du nœud lors de la création d'un cluster HDInsight.

	Différents types de cluster ont des types de nœuds, des nombres de nœuds et des tailles de nœud différents. Par exemple, un type de cluster Hadoop possède deux _nœuds principaux_ et une valeur par défaut de quatre _nœuds de données_, tandis qu’un type de cluster Storm possède deux _nœuds nimbus_, trois _nœuds zookeeper_ et une valeur par défaut de quatre _nœuds superviseur_.

	> [AZURE.IMPORTANT] Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.

	Quand vous utilisez le portail Azure pour configurer le cluster, la taille du nœud est disponible via le panneau __Niveau de tarification de nœud__ qui affiche également le coût associé aux différentes tailles de nœud.

	> [AZURE.IMPORTANT] La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Pour plus d’informations sur la tarification, consultez les [détails de tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


- **Utilisateurs HDInsight**

	Les clusters HDInsight vous permettent de configurer deux comptes d'utilisateur lors de la configuration :

	- Utilisateur HTTP. Si vous utilisez la configuration de base à partir du portail Azure, le nom d'utilisateur par défaut est admin.
	- Utilisateur RDP (clusters Windows) : il est utilisé pour se connecter au cluster à l'aide de RDP. Lorsque vous créez le compte, vous devez définir une date d'expiration qui est de 90 jours à compter d'aujourd'hui.
	- Utilisateur SSH (clusters Linux) : il est utilisé pour se connecter au cluster à l'aide de SSH. Vous pouvez créer des comptes d’utilisateur SSH supplémentaires une fois le cluster créé, en suivant la procédure décrite dans la rubrique [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Un compte Azure Storage**

	Le HDFS d'origine utilise de nombreux disques locaux sur le cluster. HDInsight utilise à la place le stockage d'objets blob Azure pour stocker des données. Le stockage d’objets blob Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. Grâce à une interface HDFS (Hadoop Distributed File System), l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées dans le stockage d’objets blob. Le stockage de données dans le stockage d’objets blob vous permet de supprimer les clusters HDInsight servant aux calculs, sans perte de données utilisateur.

	Durant la configuration, vous devez spécifier un compte de stockage Azure, ainsi qu’un conteneur de stockage d’objets blob Azure sur le compte de stockage Azure. Certains processus de création requièrent le compte de stockage Azure et le conteneur de stockage d'objets blob qui a été créé au préalable. Le conteneur de stockage d’objets blob est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des comptes Azure Storage supplémentaires (stockage lié) qui seront accessibles via le cluster. Le cluster peut également accéder aux conteneurs d’objets blob qui sont configurés avec un accès public en lecture complet ou un accès public en lecture pour les objets blob uniquement. Pour en savoir plus sur la restriction d’accès, consultez la rubrique [Gérer l’accès aux ressources d’Azure Storage](storage-manage-access-to-resources.md).

	![Stockage HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Un conteneur de stockage d'objets blob regroupe un ensemble d'objets blob, comme illustré dans l'image :

	![Stockage des objets blob](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] Ne partagez pas un conteneur de stockage d’objets blob sur plusieurs clusters. Ce n’est pas pris en charge.

	Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

- **Metastore Hive/Oozie**

	Le metastore contient les métadonnées Hive et Oozie, telles que les tables, les partitions, les schémas et les colonnes Hive. L’utilisation du metastore vous permet de conserver vos métadonnées Hive et Oozie, afin de ne pas devoir recréer des tables Hive ou des tâches Oozie lorsque vous créez un nouveau cluster. Hive utilise par défaut une base de données intégrée Azure SQL pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé. Par exemple, vous disposez d’un cluster qui a été créé avec un metastore Hive. Vous avez créé des tables Hive. Après avoir supprimé et recréé le cluster à l'aide du même metastore Hive, vous ne pourrez pas afficher les tables Hive que vous avez créées dans le cluster d'origine.
    
    > [AZURE.NOTE] La configuration Metastore n’est pas disponible pour les types de cluster HBase.

## Personnalisation de clusters à l'aide de la personnalisation de cluster de HDInsight (bootstrap)

Vous souhaitez parfois configurer des fichiers de configuration :

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Les clusters ne peuvent pas conserver les modifications lorsqu’ils ont été réimagés. Pour plus d'informations, consultez la page [Redémarrages d'instances de rôles pour cause de mise à jour du système d'exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Pour conserver les modifications apportées pendant la durée de vie des clusters, vous pouvez utiliser la personnalisation de cluster HDInsight au cours du processus de création. Il s’agit de la méthode recommandée pour modifier les configurations d’un cluster et les rendre persistantes dans l’ensemble de ces événements de redémarrage et de réinitialisation Azure. Ces modifications de configuration sont appliquées avant le démarrage du service. Il n’est donc pas nécessaire de redémarrer ce dernier.

Pour obtenir un exemple supplémentaire, consultez [Personnalisation de clusters HDInsight à l'aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## Personnalisation de clusters HDInsight à l'aide d'une action de script

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la création. Ces scripts sont appelés à l’aide de l’option de configuration **Action de script** qui peut être utilisée à partir du portail, d’applets de commande HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).


## Utilisation des réseaux virtuels Azure

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Étendre les capacités de HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

## Méthodes de création de cluster

Grâce à cet article, vous avez acquis les informations de base sur la création d'un cluster HDInsight basé sur Windows. Utilisez le tableau ci-dessous pour trouver des informations spécifiques sur la création d’un cluster à l’aide de la méthode qui correspond le mieux à vos besoins :

| En utilisant cette méthode pour créer un cluster... | En utilisant un navigateur web... | En utilisant une ligne de commande | En utilisant REST API | En utilisant un kit de développement logiciel | À partir de Linux, Mac OS X ou Unix | À partir de Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portail Azure](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Interface de ligne de commande Azure](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Modèles ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0218_2016-->