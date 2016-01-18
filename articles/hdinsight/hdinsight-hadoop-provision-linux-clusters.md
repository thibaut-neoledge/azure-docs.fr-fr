<properties
   	pageTitle="Créer des clusters Hadoop, HBase, Storm ou Spark sur Linux dans HDInsight | Microsoft Azure"
   	description="Découvrez comment créer des clusters Hadoop, HBase, Storm ou Spark sur Linux pour HDInsight en utilisant un navigateur, le CLI Azure, Azure PowerShell, REST ou via un kit de développement logiciel (SDK)."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/08/2015"
   	ms.author="nitinme"/>


#Création de clusters Hadoop basés sur Linux dans HDInsight

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-create-linux-cluster-selector.md)]

Dans ce document, vous découvrirez les différentes façons de créer un cluster HDInsight basé sur Linux dans Azure, ainsi que les configurations facultatives qui peuvent être utilisées avec votre cluster. HDInsight fournit des services Apache Hadoop, Apache Storm et Apache HBase sur la plateforme cloud Azure.

> [AZURE.NOTE]Ce document fournit des instructions sur les différentes méthodes de création d’un cluster. Si vous recherchez une approche de démarrage rapide pour créer un cluster, consultez [Prise en main d’Azure HDInsight sous Linux](../hdinsight-hadoop-linux-get-started.md).

## Qu’est-ce qu’un cluster HDInsight ?

Un cluster Hadoop se compose de plusieurs machines virtuelles (nœuds) utilisées dans le processus de distribution des tâches sur le cluster. Azure isole les détails de l’implémentation de l’installation et la configuration des nœuds individuels afin de fournir uniquement les informations de configuration générales.

###Types de cluster

Plusieurs types d’HDInsight sont disponibles :

| Type de cluster | Utilisez-le si vous devez... |
| ------------ | ----------------------------- |
| Hadoop | requête et analyse (tâches de traitement par lots) |
| HBase | Stockage des données NoSQL |
| Storm | Traitement d’événements en temps réel |
| Spark (version préliminaire) | Traitement en mémoire, requêtes interactives, traitement du flux de traitement micro-batch |

Lors de la configuration, vous allez sélectionner un de ces types pour le cluster. Vous pouvez ajouter d’autres technologies telles que Hue ou R à ces types de base en utilisant des [Actions de script](#scriptaction).

Chaque type de cluster possède sa propre terminologie pour les nœuds appartenant au cluster, ainsi que le nombre de nœuds et la taille de machine virtuelle par défaut pour chaque type de nœud :

> [AZURE.IMPORTANT]Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.

![Nœuds de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

Les clusters Hadoop de HDInsight possèdent deux nœuds type :

- Nœud principal (2 nœuds)
- Nœud de données (au moins 1 nœud)

![Nœuds de cluster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

Les clusters HBase pour HDInsight présentent trois types de nœuds : les serveurs principaux (2 nœuds), les serveurs Region (au moins 1 nœud), les nœuds Master/Zookeeper (3 nœuds)

![Nœuds de cluster HDInsight Storm](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

Les clusters Storm pour HDInsight présentent trois types de nœuds : les nœuds Nimbus (2 nœuds), les serveurs Supervisor (au moins 1 nœud), les nœuds Zookeeper (3 nœuds)


![Nœuds de cluster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

Les clusters Spark pour HDInsight présentent trois types de nœuds : le nœud principal (2 nœuds), le nœud worker (au moins 1 nœud), les nœuds Zookeeper (3 nœuds) (gratuits pour les machines virtuelles Zookeeper A1)

###Stockage Azure pour HDInsight

Chaque type de cluster possède également un ou plusieurs comptes de stockage Azure associés au cluster. HDInsight utilise les objets BLOB Azure issus de ces comptes en tant que stockage de données de votre cluster. Le fait de conserver ces données séparées du cluster vous permet de supprimer les clusters lorsqu’ils ne sont pas en cours d’utilisation, tout en conservant vos données. Vous pouvez ensuite utiliser le même compte de stockage pour un nouveau cluster si vous devez effectuer une analyse supplémentaire. Pour plus d'informations, consultez la page [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

## <a id="configuration"></a>Options de configuration de base

Les sections suivantes décrivent les options de la configuration requise disponibles lors de la création d’un cluster HDInsight.

###Nom du cluster

Le nom de cluster fournit un identificateur unique pour le cluster, et est utilisé en tant que partie du nom de domaine pour accéder au cluster via Internet. Par exemple, un cluster nommé _mycluster_ sera disponible sur Internet en tant que _mycluster_.azurehdinsight.net.

Le nom de cluster doit respecter les instructions suivantes :

- Le champ doit contenir une chaîne comprise entre 3 et 63 caractères.
- Le champ peut contenir seulement des lettres, des chiffres et des traits d’union.

###Type de cluster

Le type de cluster vous permet de sélectionner des configurations spéciales pour le cluster. Voici les types disponibles pour HDInsight sur Linux :

| Type de cluster | Utilisez-le si vous devez... |
| ------------ | ----------------------------- |
| Hadoop | requête et analyse (tâches de traitement par lots) |
| HBase | Stockage des données NoSQL |
| Storm | Traitement d’événements en temps réel |
| Spark (version préliminaire) | Traitement en mémoire, requêtes interactives, traitement du flux de traitement micro-batch |

Vous pouvez ajouter d’autres technologies telles que Hue ou R à ces types de base en utilisant des [Actions de script](#scriptaction).

###Système d’exploitation du cluster

Vous pouvez configurer des clusters HDInsight sur l’un des deux systèmes d’exploitation suivants :

- **HDInsight sous Windows (Windows Server 2012 R2 Datacenter)** : sélectionnez cette option si vous devez intégrer des services basés sur Windows et des technologies destinés à s’exécuter sur le cluster avec Hadoop, ou si vous migrez à partir d’une distribution Hadoop basée sur Windows existante.

- **HDInsight sous Linux (Ubuntu 12.04 LTS pour Linux)** : sélectionnez cette option si vous maîtrisez Linux ou Unix, pour une migration à partir d’une solution Hadoop Linux existante ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour plus d’informations, consultez [Prise en main de Hadoop sur Linux dans HDInsight](hdinsight-hadoop-linux-get-started.md).

> [AZURE.NOTE]Les informations présentes dans ce document supposent que vous utilisez un cluster HDInsight Linux. Pour obtenir des informations spécifiques aux clusters basés sur Windows, consultez [Création de clusters Hadoop Windows dans HDInsight](hdinsight-provision-clusters.md).

###Nom d'abonnement

Si vous disposez de plusieurs abonnements Azure, utilisez cette option pour sélectionner le nom d’abonnement à utiliser lors de la création du cluster HDInsight.

###Groupe de ressources

Les applications sont généralement constituées de nombreux composants, par exemple une application web, base de données, serveur de base de données, stockage et services tiers. Azure Resource Manager (ARM) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée. Vous utilisez un modèle de déploiement pouvant fonctionner avec différents environnements (environnements de test, intermédiaire et de production). Vous pouvez clarifier la facturation pour votre organisation en visualisant les coûts cumulés pour l’ensemble du groupe. Pour plus d’informations, consultez la page [Présentation d’Azure Resource Manager](resource-group-overview.md).

###Informations d'identification

Il existe deux types d’authentification utilisés avec les serveurs HDInsight :

* Utilisateur __Admin__ ou __HTTP__ : le compte d’administrateur d’un cluster est avant tout utilisé lors de l’accès aux services web ou REST exposés par le cluster. Il ne peut pas être utilisé pour vous connecter directement au cluster.

* __Nom d’utilisateur SSH__ : un compte d’utilisateur SSH est utilisé pour accéder à distance au cluster à l’aide d’un client [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell). Cette méthode est très souvent utilisée pour fournir un accès par ligne de commande à distance aux nœuds principaux du cluster.

Le compte administrateur est protégé par mot de passe et toutes les communications web vers le cluster à l’aide du compte administrateur doivent l’être via une connexion HTTPS sécurisée.

L’utilisateur SSH peut être authentifié à l’aide d’un mot de passe ou d’un certificat. L’authentification par certificat est l’option la plus sûre, mais elle exige la création et la conservation d’une paire de certificats (public et privé).

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, notamment la création et l’utilisation des clés SSH, consultez l’un des articles suivants :

* [Pour les clients Linux, Unix ou Mac OS X : utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Pour les clients Windows : utilisation de SSH avec HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

###Source de données

HDInsight utilise le stockage d’objets Blob Azure comme stockage sous-jacent pour le cluster. En interne, Hadoop et d’autres logiciels sur le cluster considèrent ce stockage en tant que système compatible Hadoop (Hadoop Distributed File System - HDFS).

Lorsque vous créez un nouveau cluster, vous devez créer également un nouveau compte de stockage Azure, ou utiliser un compte existant.

> [AZURE.IMPORTANT]L’emplacement géographique sélectionné pour le compte de stockage détermine l’emplacement du cluster HDInsight, et le cluster doit se trouver dans le même centre de données que le compte de stockage par défaut.
>
> Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

####Conteneur de stockage par défaut

HDInsight crée également un _conteneur de stockage par défaut_ sur le compte de stockage. Il s’agit de l’espace de stockage par défaut du cluster HDInsight.

Par défaut, ce conteneur porte le même nom que le cluster HDInsight. Pour plus d’informations sur le fonctionnement d’HDInsight avec le stockage d’objets blob Azure, consultez [Utilisation du stockage d’objets blob Azure compatibles avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md).

>[AZURE.WARNING]Ne répartissez pas un conteneur sur plusieurs clusters. Ce n’est pas pris en charge.

###Taille du nœud

Vous pouvez sélectionner le volume de ressources de calcul utilisé par le cluster. Par exemple, si vous savez que vous allez effectuer des opérations nécessitant un grand volume de mémoire, vous souhaitez sélectionner une ressource de calcul avec davantage de mémoire.

Différents types de cluster ont des types de nœuds, des nombres de nœuds et des tailles de nœud différents. Par exemple, un type de cluster Hadoop possède deux _nœuds principaux_ et une valeur par défaut de quatre _nœuds de données_, tandis qu’un type de cluster Storm possède deux _nœuds nimbus_, trois _nœuds zookeeper_ et une valeur par défaut de quatre _nœuds superviseur_.

> [AZURE.IMPORTANT]Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.

Quand vous utilisez la version préliminaire du portail Azure pour configurer le cluster, la taille du nœud est disponible via le panneau __Niveau de tarification de nœud__ qui affiche également le coût associé aux différentes tailles de nœud.

> [AZURE.IMPORTANT]La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Pour plus d’informations sur la tarification, consultez les [détails de tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a id="optionalconfiguration"></a>Configuration facultative

Les sections suivantes décrivent les options de configuration facultatives, ainsi que des scénarios nécessitant ces configurations.

### Version de HDInsight

Utilisez cette option pour déterminer la version de HDInsight à utiliser pour ce cluster. Pour plus d’informations, consultez [Versions et composants de cluster Hadoop dans HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

### Utilisation des réseaux virtuels Azure

Un réseau [Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et permanent contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

    | Configuration de site à site | Configuration de point à site |
    | -------------------------- | --------------------------- |
    | Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données à Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service, service Routage et accès distant).<br />![diagram of site-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.<br />![diagram of point-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Vous devez créer le réseau Azure Virtual Network avant de créer un cluster. Pour plus d’informations, consultez [Création d’un réseau virtuel](virtual-networks-create-vnet.md).
>
> Azure HDInsight prend uniquement en charge les réseaux virtuels basés sur l'emplacement et ne fonctionne pas pour le moment avec les réseaux virtuels basés sur des groupes d'affinités. Utilisez l’applet de commande Get-AzureVNetConfig d’Azure PowerShell pour vérifier si un réseau virtuel Azure est basé sur l'emplacement. Si votre réseau virtuel n'est pas basé sur l'emplacement, vous disposez des options suivantes :
>
> - Exportez la configuration du réseau virtuel existant, puis créez un réseau virtuel. Par défaut, les nouveaux réseaux virtuels sont basés sur l’emplacement.
> - Migrez vers un réseau virtuel basé sur l’emplacement. Consultez la rubrique [Migrer des services existants vers une portée régionale](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.
>
> À la date de la rédaction de cet article (8/25/2015), vous ne pouvez qu’un cluster basé sur Linux dans Azure Virtual Network.
>
> Vous ne pouvez pas utiliser de réseau virtuel Azure v1 (classique) avec un cluster HDInsight Linux. Le réseau virtuel doit être v2 (Azure Resource Manager), pour être affiché en tant qu’option pendant le processus de création du cluster HDInsight dans le portail Azure en version préliminaire, ou pour être utilisable pendant la création d’un cluster à partir de l’interface de ligne de commande Azure ou d’Azure PowerShell.
>
> Si vous disposez de ressources sur un réseau v1 et que vous souhaitez rendre HDInsight directement accessible à ces ressources via un réseau virtuel, consultez [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../virtual-network/virtual-networks-arm-asm-s2s.md) pour plus d’informations sur la connexion d’un réseau virtuel v2 à un réseau virtuel v1. Une fois cette connexion établie, vous pouvez créer le cluster HDInsight dans le réseau virtuel v2.

### Metastore

Le metastore contient les métadonnées Hive et Oozie, telles que les informations sur les tables, les partitions, les schémas et les colonnes Hive. L’utilisation du metastore vous permet de conserver vos métadonnées Hive et Oozie, afin de ne pas avoir à recréer des tables Hive ou des tâches Oozie lorsque vous créez un nouveau cluster.

L’utilisation de l’option de configuration Metastore vous permet de spécifier un stockage metastore externe utilisant Base de données SQL. Cela permet aux informations de type métadonnées d’être préservées en cas de suppression d’un cluster, car il est stocké à l’extérieur par rapport à la base de données. Pour obtenir des instructions sur la création d’une base de données SQL dans Azure, consultez [Créer votre première base de données SQL Azure](sql-database-get-started.md).

> [AZURE.NOTE]La configuration Metastore n’est pas disponible pour les types de cluster HBase.

###<a id="scriptaction"></a>Action de script

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster en utilisant les scripts pendant l’approvisionnement du cluster. Ces scripts sont appelés via une **action de script**. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT]L’ajout de composants supplémentaires après la création d’un cluster n’est pas pris en charge, car ces composants ne sont pas disponibles après la réinitialisation d’un nœud de cluster. Les composants installés à l'aide d’actions de script sont réinstallés dans le cadre du processus de réinitialisation.

### Stockage supplémentaire

Dans certains cas, vous pouvez souhaiter ajouter un stockage supplémentaire au cluster. Par exemple, si vous avez plusieurs comptes de stockage Azure correspondant à différentes régions géographiques ou différents services, mais si vous souhaitez les analyser tous avec HDInsight.

Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).

##<a id="nextsteps"></a><a id="options"></a> Méthodes de création

Grâce à cet article, vous avez acquis les informations de base sur la création d’un cluster HDInsight sur Linux. Utilisez le tableau ci-dessous pour trouver des informations spécifiques sur la création d’un cluster à l’aide de la méthode qui correspond le mieux à vos besoins :

| En utilisant cette méthode pour créer un cluster... | En utilisant un navigateur web... | En utilisant une ligne de commande | En utilisant REST API | En utilisant un kit de développement logiciel | À partir de Linux, Mac OS X ou Unix | À partir de Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portail Azure en version préliminaire](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Interface de ligne de commande Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |



[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Énumération et affichage des clusters"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Utilisation de Sqoop avec HDInsight"

<!---HONumber=AcomDC_0107_2016-->