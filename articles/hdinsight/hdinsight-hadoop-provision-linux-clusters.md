<properties
   	pageTitle="Créer des clusters Hadoop, HBase, Storm ou Spark sur Linux dans HDInsight | Microsoft Azure"
   	description="Découvrez comment créer des clusters Hadoop, HBase, Storm ou Spark sur Linux pour HDInsight en utilisant un navigateur, le CLI Azure, Azure PowerShell, REST ou via un Kit SDK."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/21/2016"
   	ms.author="jgao"/>


# Création de clusters Hadoop basés sur Linux dans HDInsight

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-selector-create-clusters.md)]

Un cluster Hadoop se compose de plusieurs machines virtuelles (nœuds) utilisées dans le processus de distribution des tâches sur le cluster. Azure isole les détails de l’implémentation de l’installation et la configuration des nœuds individuels afin de fournir uniquement les informations de configuration générales. Cet article aborde ces paramètres de configuration.

##Types de cluster

Actuellement, HDInsight propose quatre types de clusters, chacun avec un ensemble de composants qui fournissent des fonctionnalités :

| Type de cluster | Utilisez-le si vous devez... |
| ------------ | ----------------------------- |
| Hadoop | requête et analyse (tâches de traitement par lots) |
| HBase | Stockage des données NoSQL |
| Storm | Traitement d’événements en temps réel |
| Spark (version préliminaire) | Traitement en mémoire, requêtes interactives, traitement du flux de traitement micro-batch |

Chaque type de cluster possède sa propre terminologie pour les nœuds appartenant au cluster, ainsi que le nombre de nœuds et la taille de machine virtuelle par défaut pour chaque type de nœud :

| Type| Nœuds (nombre de nœuds)| Diagramme|
|-----|------|--------|
|Hadoop| Nœud principal (2), Nœud de données (1+)|![Nœuds de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)|
|HBase|Serveur principal (2), Serveur de région (1+), Nœud principal/Zookeeper (3)|![Nœuds de cluster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)|
|Storm|Nœud Nimbus (2), Serveur Supervisor (1+), Nœud Zookeeper (3)|![Nœuds de cluster HDInsight Storm](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)|
|Spark|Nœud principal (2), Nœud Worker (1+), Nœud Zookeeper (3) (gratuits pour les machines virtuelles Zookeeper A1)|![Nœuds de cluster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)|

* Les chiffres entre parenthèses correspondent au nombre de nœuds de chaque type.

> [AZURE.IMPORTANT] Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.

Vous pouvez ajouter d’autres composants, tels que Hue ou R, à ces types de base en utilisant des [actions de script](#customize-clusters-using-script-action).

## Options de configuration de base

Voici les options de configuration de base pour la création d'un cluster HDInsight.

- **Nom du cluster**

	Le nom du cluster permet d’identifier un cluster. Le nom du cluster doit être globalement unique et doit respecter les instructions d’affectation de noms suivantes :

	- Le champ doit être une chaîne comportant entre 3 et 63 caractères.
	- Le champ ne peut contenir que des lettres, des chiffres et des traits d'union.

- **Type de cluster**

    Consultez [Types de clusters](#cluster-types).

- **Système d’exploitation**

	Vous pouvez créer des clusters HDInsight sur l’un des deux systèmes d’exploitation suivants :
	- **HDInsight sur Linux (Ubuntu 12.04 LTS pour Linux)** : HDInsight offre la possibilité de configurer des clusters Linux sur Azure. Configurez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour plus d’informations, consultez [Prise en main de Hadoop sur Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
	- **HDInsight sur Windows (Windows Server 2012 R2 Datacenter)** :
    
- **Version de HDInsight**

	Elle permet de déterminer la version de HDInsight à utiliser pour ce cluster. Pour plus d’informations, consultez [Versions et composants de cluster Hadoop dans HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

- **Nom d'abonnement**

	Chaque cluster HDInsight est lié à un abonnement Azure.
    
- **Nom de groupe ressources**

	[Azure Resource Manager (ARM)](resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.

- **Informations d'identification**

	Les clusters HDInsight vous permettent de configurer deux comptes d’utilisateur lors de la création :

	- Utilisateur HTTP. Si vous utilisez la configuration de base à partir du portail Azure, le nom d'utilisateur par défaut est admin. Parfois, le nom par défaut est « Utilisateur du cluster ».
	- Utilisateur SSH (clusters Linux) : il est utilisé pour se connecter au cluster à l'aide de SSH. Vous pouvez créer des comptes d’utilisateur SSH supplémentaires une fois le cluster créé, en suivant la procédure décrite dans la rubrique [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

    >[AZURE.NOTE] Pour les clusters Windows, vous pouvez créer un utilisateur RDP pour vous connecter au cluster à l’aide d’une connexion Bureau à distance.

- **Source de données**

	Le système HDFS d’origine utilise de nombreux disques locaux du cluster. HDInsight utilise à la place le stockage d'objets blob Azure pour stocker des données. Le stockage d’objets blob Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. Grâce à une interface HDFS (Hadoop Distributed File System), l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées dans le stockage d’objets blob. Le stockage de données dans le stockage d’objets blob vous permet de supprimer les clusters HDInsight servant aux calculs, sans perte de données utilisateur.

	Durant la configuration, vous devez spécifier un compte de stockage Azure, ainsi qu’un conteneur de stockage d’objets blob Azure sur le compte de stockage Azure. Certains processus de création requièrent le compte de stockage Azure et le conteneur de stockage d'objets blob qui a été créé au préalable. Le conteneur de stockage d’objets blob est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des comptes Azure Storage supplémentaires (stockage lié) qui seront accessibles via le cluster. Le cluster peut également accéder aux conteneurs d’objets blob qui sont configurés avec un accès en lecture public complet ou un accès en lecture public pour les objets blob uniquement. Pour en savoir plus sur la restriction d’accès, consultez la rubrique [Gérer l’accès aux ressources d’Azure Storage](storage-manage-access-to-resources.md).

	![Stockage HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Un conteneur de stockage d'objets blob regroupe un ensemble d'objets blob, comme illustré dans l'image :

	![Stockage des objets blob](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

    Il n’est pas recommandé d’utiliser le conteneur d’objets blob par défaut pour stocker des données d’entreprise. Il est conseillé de supprimer le conteneur d’objets blob par défaut après chaque utilisation pour réduire les coûts de stockage. Notez que le conteneur par défaut contient les journaux des applications et du système. Assurez-vous de récupérer les journaux avant de supprimer le conteneur.
    
	>[AZURE.WARNING] Le partage d’un conteneur Blob Storage sur plusieurs clusters n’est pas pris en charge.

	Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

    En plus d’Azure Blob Storage, vous pouvez également utiliser [Azure Data Lake Store](data-lake-store-overview.md) comme compte de stockage par défaut pour le cluster HBase dans HDInsight, et comme stockage lié pour les quatre types de cluster HDInsight. Pour obtenir des instructions, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
    
- **Emplacement (région)**

	Le cluster HDInsight et son compte de stockage par défaut doivent être situés au même emplacement Azure.
	
	![Régions Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Niveaux tarifaires**

    L’utilisation de ces nœuds est facturée aux clients pendant toute la durée de vie du cluster. Cette facturation démarre une fois qu'un cluster est créé et s'arrête lorsque le cluster est supprimé (les clusters ne peuvent pas être désalloués ou mis en suspens).

	Différents types de cluster ont des types de nœuds, des nombres de nœuds et des tailles de nœud différents. Par exemple, un type de cluster Hadoop possède deux _nœuds principaux_ et une valeur par défaut de quatre _nœuds de données_, tandis qu’un type de cluster Storm possède deux _nœuds nimbus_, trois _nœuds zookeeper_ et une valeur par défaut de quatre _nœuds superviseur_. Le coût des clusters HDInsight est déterminé par le nombre de nœuds et par la taille des machines virtuelles. Par exemple, si vous savez que vous allez effectuer des opérations nécessitant un grand volume de mémoire, vous souhaitez sélectionner une ressource de calcul avec davantage de mémoire. À des fins d’apprentissage, il est recommandé d'utiliser le nœud de données 1. Pour plus d'informations sur la tarification de HDInsight, consultez la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

	>[AZURE.NOTE] La limite de taille de cluster varie selon les abonnements Azure. Contactez le support de facturation pour augmenter la limite.
	
    >Les nœuds utilisés par votre cluster ne sont pas considérés comme des machines virtuelles car les images de machines virtuelles utilisées pour les nœuds sont un détail d'implémentation du service HDInsight. Toutefois, les cœurs de calcul utilisés par les nœuds ne sont pas pris en compte dans le total de cœurs de calcul disponibles pour votre abonnement. Vous pouvez voir le nombre de cœurs qui seront utilisés par le cluster, ainsi que le nombre de cœurs disponibles, dans la section Résumé du panneau Niveaux de tarification du nœud lors de la création d'un cluster HDInsight.

	Quand vous utilisez le portail Azure pour configurer le cluster, la taille du nœud est disponible via le panneau __Niveau de tarification de nœud__ qui affiche également le coût associé aux différentes tailles de nœud. La capture d’écran suivante montre les options disponibles pour un cluster Linux Hadoop :

	![tailles de nœud de machine virtuelle HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

    Les tableaux ci-après indiquent les tailles prises en charge par les clusters HDInsight et les capacités qu’elles offrent.

    - Niveau standard : série A

        Dans le modèle de déploiement classique, certaines tailles de machines virtuelles sont légèrement différentes dans PowerShell et l’interface de ligne de commande.

        * Standard\_A3 est Large (Grand)
        * Standard\_A4 est ExtraLarge (Très grand)

        <br>

        |Taille |Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Taille maximale du disque|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
        |---|---|---|---|---|---|---|
        |Standard\_A3\\Large|4|7 Go|2|Temporaire = 285 Go |8|8 x 500|
        |Standard\_A4\\ExtraLarge|8|14 Go|4|Temporaire = 605 Go |16|16 x 500|
        |Standard\_A6|4|28 Go|2|Temporaire = 285 Go |8|8 x 500|
        |Standard\_A7|8|56 Go|4|Temporaire = 605 Go |16|16 x 500|


    - Niveau standard : série D

        |Taille |Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Taille maximale du disque|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
        |---|---|---|---|---|---|---|
        |D3 standard |4|14 Go|4|Temporaire (SSD) = 200 Go |8|8 x 500|
        |D4 standard |8|28 Go|8|Temporaire (SSD) = 400 Go |16|16 x 500|
        |D12 standard |4|28 Go|4|Temporaire (SSD) = 200 Go |8|8 x 500|
        |D13 standard |8|56 Go|8|Temporaire (SSD) = 400 Go |16|16 x 500|
        |D14 standard |16|112 Go|8|Temporaire (SSD) = 800 Go |32|32 x 500|

    - Niveau standard : série Dv2

        |Taille |Cœurs d’unité centrale|Mémoire|Cartes réseau (max)|Taille maximale du disque|Nombre maximal de disques de données (1 023 Go chacun)|Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque)|
        |---|---|---|---|---|---|---|
        |Standard\_D3\_v2 |4|14 Go|4|Temporaire (SSD) = 200 Go |8|8 x 500|
        |Standard\_D4\_v2 |8|28 Go|8|Temporaire (SSD) = 400 Go |16|16 x 500|
        |Standard\_D12\_v2 |4|28 Go|4|Temporaire (SSD) = 200 Go |8|8 x 500|
        |Standard\_D13\_v2 |8|56 Go|8|Temporaire (SSD) = 400 Go |16|16 x 500|
        |Standard\_D14\_v2 |16|112 Go|8|Temporaire (SSD) = 800 Go |32|32 x 500|    
 
    Pour connaître les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources, consultez [Tailles des machines virtuelles](../virtual-machines/virtual-machines-size-specs.md). Pour plus d’informations sur la tarification des différentes tailles, consultez [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).
    
	> [AZURE.IMPORTANT] Si vous envisagez d’utiliser plus de 32 nœuds worker lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM. La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Pour plus d’informations sur la tarification, consultez les [détails de tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## Utiliser du stockage supplémentaire

Dans certains cas, vous pouvez souhaiter ajouter un stockage supplémentaire au cluster. Par exemple, si vous avez plusieurs comptes de stockage Azure correspondant à différentes régions géographiques ou différents services, mais si vous souhaitez les analyser tous avec HDInsight.

Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md). Pour plus d’informations sur l’utilisation d’un Data Lake Store secondaire, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


## Utiliser un metastore Hive/Oozie

Nous vous recommandons fortement d’utiliser un metastore personnalisé si vous voulez conserver vos tables Hive après avoir supprimé votre cluster HDInsight dans le but de pouvoir attacher ce metastore à un autre cluster HDInsight.

Le metastore contient les métadonnées Hive et Oozie, telles que les tables, les partitions, les schémas et les colonnes Hive. L’utilisation du metastore vous permet de conserver vos métadonnées Hive et Oozie, afin de ne pas devoir recréer des tables Hive ou des tâches Oozie lorsque vous créez un nouveau cluster. Hive utilise par défaut une base de données intégrée Azure SQL pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé. Par exemple, vous disposez d’un cluster qui a été créé avec un metastore Hive. Vous avez créé des tables Hive. Après avoir supprimé et recréé le cluster à l’aide du même metastore Hive, vous ne pourrez pas afficher les tables Hive que vous avez créées dans le cluster d’origine.

> [AZURE.NOTE] La configuration Metastore n’est pas disponible pour les types de cluster HBase.

## Utilisation des réseaux virtuels Azure

Un réseau [Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et permanent contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

    | Configuration de site à site | Configuration de point à site |
    | -------------------------- | --------------------------- |
    | Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données à Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service, service Routage et accès distant).<br />![diagram of site-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.<br />![diagram of point-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Étendre les capacités de HDInsight en utilisant un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

## Personnalisation de clusters à l'aide de la personnalisation de cluster de HDInsight (bootstrap)

Vous souhaitez parfois configurer des fichiers de configuration :

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Pour conserver les modifications apportées pendant la durée de vie des clusters, vous pouvez utiliser la personnalisation des clusters HDInsight au cours du processus de création ou utiliser Ambari sur les clusters Linux. Pour plus d’informations, consultez la page [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

>[AZURE.NOTE] Les clusters Windows ne peuvent pas conserver les modifications lorsqu’ils ont été réinitialisés. Pour plus d'informations, consultez la page [Redémarrages d'instances de rôles pour cause de mise à jour du système d'exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Pour conserver les modifications apportées pendant la durée de vie des clusters, vous devez utiliser la personnalisation des clusters HDInsight au cours du processus de création.

## Personnalisation de clusters HDInsight à l'aide d'une action de script

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la création. Ces scripts sont appelés à l’aide de l’option de configuration **Action de script** qui peut être utilisée à partir du portail, d’applets de commande HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md).



## Méthodes de création de cluster

Grâce à cet article, vous avez acquis les informations de base sur la création d’un cluster HDInsight sur Linux. Utilisez le tableau ci-dessous pour trouver des informations spécifiques sur la création d’un cluster à l’aide de la méthode qui correspond le mieux à vos besoins :

| À utiliser pour créer des clusters... | Avec un navigateur web | Avec la ligne de commande | Avec l’API REST | Avec le SDK | À partir de Linux, Mac OS X ou Unix | À partir de Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) | ✔ | ✔ | ✔ |✔ | ✔ | ✔ |
| [Interface de ligne de commande Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Modèles ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0323_2016-->