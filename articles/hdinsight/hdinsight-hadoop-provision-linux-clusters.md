---
title: "Création de clusters Hadoop, HBase, Kafka, Storm ou Spark dans Azure HDInsight | Microsoft Docs"
description: "Découvrez comment créer des clusters Hadoop, HBase, Storm ou Spark sur Linux pour HDInsight en utilisant un navigateur, le CLI Azure, Azure PowerShell, REST ou un Kit SDK."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 9fc96db2b832f1e57813bebd2d46e4b78ed04677
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="create-hadoop-clusters-in-hdinsight"></a>Création de clusters Hadoop dans HDInsight
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Un cluster Hadoop se compose de plusieurs machines virtuelles (nœuds) utilisées dans le processus de distribution des tâches sur le cluster. Azure HDInsight extrait les détails de l’implémentation de l’installation et de la configuration des nœuds individuels, de sorte que vous n’avez plus qu’à fournir les informations de configuration générales. Cet article aborde ces paramètres de configuration.

## <a name="basic-configurations"></a>Configurations de base

À partir du [portail Azure](https://portal.azure.com), vous pouvez créer un cluster HDInsight en utilisant *Création rapide* ou *Personnalisé*. Cette section présente les paramètres de configuration de base utilisés dans l’option Création rapide. L’option Personnalisé inclut les configurations supplémentaires suivantes :

- [Applications](#hdinsight-applications)
- [Taille du cluster](#cluster-size)
- Paramètres avancés

  - [Actions de script](#customize-clusters-using-script-action)
  - [Réseau virtuel](#use-virtual-network)

### <a name="subscription"></a>Abonnement 
Chaque cluster HDInsight est lié à un abonnement Azure.

### <a name="resource-group-name"></a>Nom de groupe ressources
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.

### <a name="cluster-name"></a>Nom du cluster
Le nom du cluster permet d’identifier un cluster. Le nom du cluster doit être globalement unique et doit respecter les instructions d’affectation de noms suivantes :

* Le champ doit être une chaîne comportant entre 3 et 63 caractères.
* Le champ ne peut contenir que des lettres, des chiffres et des traits d'union.

### <a name="cluster-types"></a> Type du cluster
Actuellement, Azure HDInsight propose cinq types de clusters, chacun avec un ensemble de composants fournissant certaines fonctionnalités :

| Type de cluster | Fonctionnalités |
| --- | --- |
| [Hadoop](hdinsight-hadoop-introduction.md) |Requête et analyse (tâches de traitement par lots) |
| [HBase](hdinsight-hbase-overview.md) |Stockage des données NoSQL |
| [Storm](hdinsight-storm-overview.md) |Traitement d’événements en temps réel |
| [Spark](hdinsight-apache-spark-overview.md) |Traitement en mémoire, requêtes interactives, traitement du flux de traitement micro-batch |
| [Kafka (version préliminaire)](hdinsight-apache-kafka-introduction.md) | Plateforme de diffusion en continu distribuée qui permet de générer des pipelines de données et des applications de diffusion en continu en temps réel |
| [R Server](hdinsight-hadoop-r-server-overview.md) |Une large gamme de statistiques Big Data, la modélisation prédictive, ainsi que des fonctionnalités Machine Learning. |
| [Hive interactif (version préliminaire)](hdinsight-hadoop-use-interactive-hive.md) |Mise en cache pour les requêtes Hive interactives et plus rapides |

Chaque type de cluster possède son propre nombre de nœuds dans le cluster, sa terminologie pour les nœuds du cluster et la taille de machine virtuelle par défaut pour chaque type de nœud. Dans le tableau suivant, les chiffres entre parenthèses correspondent au nombre de nœuds de chaque type.

| Type | Nœuds | Diagramme |
| --- | --- | --- |
| Hadoop |Nœud principal (2), nœud de données (1+) |![Nœuds de cluster Hadoop HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Hadoop-roles.png) |
| HBase |Serveur principal (2), serveur de région (1+), nœud principal/ZooKeeper (3) |![Nœuds de cluster HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-HBase-roles.png) |
| Storm |Nœud Nimbus (2), serveur supervisor (1+), nœud ZooKeeper (3) |![Nœuds de cluster HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Storm-roles.png) |
| Spark |Nœud principal (2), nœud worker (1+), nœud ZooKeeper (3) (gratuits pour les machines virtuelles ZooKeeper A1) |![Nœuds de cluster HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/HDInsight-Spark-roles.png) |

Les tableaux suivants répertorient les tailles de machine virtuelle par défaut pour HDInsight :

* Toutes les régions prises en charge à l’exception du sud du Brésil et de l’ouest du Japon :

  | Type de cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Head : taille de machine virtuelle par défaut |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | Head : tailles de machine virtuelle recommandées |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |A3, A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Worker : taille de machine virtuelle par défaut |D3 v2 |D3 v2 |D3 v2 |Windows : D12 v2 ; Linux : D4 v2 |Windows : D12 v2 ; Linux : D4 v2 |
  | Worker : tailles de machine virtuelle recommandées |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2 |Windows : D12 v2, D13 v2, D14 v2; Linux : D4 v2, D12 v2, D13 v2, D14 v2 |Windows : D12 v2, D13 v2, D14 v2; Linux : D4 v2, D12 v2, D13 v2, D14 v2 |
  | Zookeeper : taille de machine virtuelle par défaut | |A3 |A2 | | |
  | Zookeeper : tailles de machine virtuelle recommandées | |A3, A4, A5 |A2, A3, A4 | | |
  | Edge : taille de machine virtuelle par défaut | | | | |Windows : D12 v2 ; Linux : D4 v2 |
  | Edge : taille de machine virtuelle recommandée | | | | |Windows : D12 v2, D13 v2, D14 v2; Linux : D4 v2, D12 v2, D13 v2, D14 v2 |
* Sud du Brésil et ouest du Japon uniquement (aucune v2 tailles ici) :

  | Type de cluster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Head : taille de machine virtuelle par défaut |D3 |D3 |A3 |D12 |D12 |
  | Head : tailles de machine virtuelle recommandées |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Worker : taille de machine virtuelle par défaut |D3 |D3 |D3 |Windows : D12 v2 ; Linux : D4 |Windows : D12 ; Linux : D4 |
  | Worker : tailles de machine virtuelle recommandées |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows : D12, D13, D14 ; Linux : D4, D12, D13, D14 |Windows : D12, D13, D14 ; Linux : D4, D12, D13, D14 |
  | Zookeeper : taille de machine virtuelle par défaut | |A2 |A2 | | |
  | Zookeeper : tailles de machine virtuelle recommandées | |A2, A3, A4 |A2, A3, A4 | | |
  | Edge : tailles de machine virtuelle par défaut | | | | |Windows : D12 ; Linux : D4 |
  | Edge : tailles de machine virtuelle recommandées | | | | |Windows : D12, D13, D14 ; Linux : D4, D12, D13, D14 |

> [!NOTE]
> Head est appelé *Nimbus* pour le type de cluster Storm. Le nœud de travail est appelé *Région* pour le type de cluster HBase et *Superviseur* pour le type de cluster Storm.

> [!IMPORTANT]
> Si vous envisagez d’avoir plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
>

Vous pouvez ajouter d’autres composants, tels que Hue ou R, à ces types de base en utilisant des [actions de script](#customize-clusters-using-script-action).

> [!IMPORTANT]
> Il existe différents types de clusters HDInsight, correspondant aux différentes charges de travail ou technologies pour lesquelles ils sont utilisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster.
>
>

Si votre solution nécessite des technologies qui sont réparties sur plusieurs types de cluster HDInsight, vous devez créer un réseau virtuel Azure et créer les types de cluster requis dans le réseau virtuel. Cette configuration permet aux clusters, et au code déployé sur ces clusters, de communiquer directement entre eux.

Pour plus d’informations sur l’utilisation du réseau virtuel Azure avec HDInsight, consultez [Étendre HDInsight à l’aide de réseaux virtuels Azure](hdinsight-extend-hadoop-virtual-network.md).

Pour voir un exemple d’utilisation de deux types de cluster au sein d’un réseau virtuel Azure, consultez la section [Analyser les données de capteur avec Storm et HBase](hdinsight-storm-sensor-data-analysis.md).

### <a name="operating-system"></a>Système d’exploitation
Vous pouvez créer des clusters HDInsight sous Linux ou Windows.  Pour plus d’informations sur les versions du système d’exploitation, consultez [Versions de HDInsight prises en charge](hdinsight-component-versioning.md#supported-hdinsight-versions). HDInsight 3.4 et les versions ultérieures utilisent la distribution Linux Ubuntu comme système d’exploitation sous-jacent pour le cluster. 

### <a name="version"></a>Version
Cette option permet de déterminer la version de HDInsight nécessaire pour ce cluster. Pour plus d’informations, voir [Versions de HDInsight prises en charge](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-tiers"></a>Niveau de cluster

Azure HDInsight propose deux catégories d’offres de cloud Big Data : Standard et Premium.  Pour plus d’informations, consultez [HDInsight Standard et HDInsight Premium]](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).

La capture d’écran suivante présente les informations du portail Azure pour le choix des types de cluster.

![Configuration de HDInsight Premium](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-type-configuration.png)


### <a name="credentials"></a>Informations d'identification
Les clusters HDInsight vous permettent de configurer deux comptes d’utilisateur lors de la création :

* Utilisateur HTTP. Le nom d’utilisateur par défaut est *admin*. Il utilise la configuration de base sur le portail Azure. Parfois, le nom par défaut est « Utilisateur du cluster ».
* Utilisateur SSH (clusters Linux). Il est utilisé pour se connecter au cluster à l’aide de SSH. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="location"></a>Emplacement
Il n’est pas nécessaire de spécifier explicitement l’emplacement du cluster. Le cluster partage le même emplacement que le stockage par défaut. Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


### <a name="storage"></a>Storage

Le système de fichiers distribués Hadoop (HDFS) d’origine utilise de nombreux disques locaux sur le cluster. HDInsight utilise des objets blob dans le [stockage Azure](hdinsight-hadoop-provision-linux-clusters.md#azure-storage) ou [Azure Data Lake Store](hdinsight-hadoop-provision-linux-clusters.md#azure-data-lake-store). Il existe certaines exigences concernant l’utilisation des magasins Data Lake Store pour HDInsight. Pour plus d’informations, consultez la section introductive [Créer des clusters HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

![Stockage HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)


#### <a name="azure-storage"></a>Stockage Azure
Le stockage Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. Grâce à une interface HDFS, l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées stockées dans les objets blob. Le stockage de données dans Stockage Azure vous permet de supprimer les clusters HDInsight servant aux calculs, sans perte de données utilisateur. HDInsight prend uniquement en charge les comptes de stockage Azure à __usage général__. Il ne prend actuellement pas en charge le type de compte __stockage Blob__.

Durant la configuration, vous devez spécifier un compte de stockage Azure, ainsi qu’un conteneur d’objets blob dans le compte de stockage Azure. Le conteneur d’objets blob est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des comptes de stockage Azure supplémentaires (stockage lié) auxquels le cluster peut accéder. Le cluster peut également accéder aux conteneurs d’objets blob qui sont configurés avec un accès en lecture public complet ou un accès en lecture public pour les objets blob uniquement.

Nous vous déconseillons d’utiliser le conteneur d’objets blob par défaut pour stocker des données d’entreprise. Nous vous recommandons de supprimer le conteneur d’objets blob par défaut après chaque utilisation pour réduire les coûts de stockage. Veuillez noter que le conteneur par défaut contient les journaux des applications et du système. Assurez-vous de récupérer les journaux avant de supprimer le conteneur.

Le partage d’un conteneur d’objets blob sur plusieurs clusters n’est pas pris en charge.

Pour plus d’informations sur l’utilisation d’un compte de stockage Azure, consultez [Utilisation du stockage Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md).

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
En plus du stockage Azure, vous pouvez utiliser [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) comme compte de stockage par défaut pour le cluster HBase dans HDInsight, et comme stockage lié pour les quatre types de cluster HDInsight. Pour plus d’informations, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

#### <a name="use-additional-storage"></a>Stockage supplémentaire

Dans certains cas, vous pouvez ajouter un stockage supplémentaire au cluster. Par exemple, vous pouvez avoir plusieurs comptes de stockage Azure correspondant à différentes régions géographiques ou différents services et souhaitez tous les analyser avec HDInsight.

Vous pouvez ajouter des comptes de stockage lorsque vous créez un cluster HDInsight ou après la création d’un cluster.  Consultez [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md).

Pour plus d’informations sur le compte de stockage Azure secondaire, consultez [Utilisation du stockage Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md). Pour plus d’informations sur l’utilisation d’un Data Lake Store secondaire, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).


#### <a name="use-hiveoozie-metastore"></a>Metastore Hive/Oozie
Nous vous recommandons d’utiliser un metastore personnalisé si vous souhaitez conserver vos tables Hive après la suppression de votre cluster HDInsight. Vous pourrez joindre ce metastore à un autre cluster HDInsight.

> [!IMPORTANT]
> Un metastore HDInsight créé pour une version de cluster HDInsight ne peut pas être partagé entre différentes versions de cluster HDInsight. Pour obtenir la liste des versions de HDInsight, consultez la section [Versions de HDInsight prises en charge](hdinsight-component-versioning.md#supported-hdinsight-versions).
>
>

Le metastore contient les métadonnées Hive et Oozie, telles que les tables, les partitions, les schémas et les colonnes Hive. Le metastore vous permet de conserver vos métadonnées Hive et Oozie, afin de ne pas devoir recréer des tables Hive ou des tâches Oozie lorsque vous créez un nouveau cluster. Hive utilise par défaut une base de données intégrée Azure SQL pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé. Lorsque vous créez une table Hive dans un cluster HDInsight avec un metastore Hive configuré, ces tables sont conservées lorsque vous recréez le cluster à l’aide du même metastore Hive.

La configuration Metastore n’est pas disponible pour les types de cluster HBase.

> [!IMPORTANT]
> Lors de la création d’un metastore personnalisé, n’utilisez pas de nom de base de données contenant des traits d’union ou des tirets. Cela risque d’entraîner l’échec du processus de création du cluster.
>
>

## <a name="install-hdinsight-applications"></a>Install custom HDInsight applications

Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight sous Linux. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même. Pour plus d’informations, consultez [Installer des applications Hadoop tierces sur Azure HDInsight](hdinsight-apps-install-applications.md).

La plupart des applications HDInsight sont installées sur un nœud de périmètre vide.  Un nœud de périmètre vide est une machine virtuelle Linux avec les mêmes outils client installés et configurés comme dans le nœud principal. Vous pouvez utiliser le nœud de périmètre pour accéder au cluster, tester vos applications clientes et héberger vos applications clientes. Pour plus d’informations, consultez [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="configure-cluster-size"></a>Configurer la taille du cluster

L’utilisation de ces nœuds est facturée aux clients pendant toute la durée de vie du cluster. La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Les clusters ne peuvent pas être désalloués ou mis en attente.

Les différents types de cluster ont des types de nœuds, des nombres de nœuds et des tailles de nœud différents. Par exemple, un type de cluster Hadoop possède deux *nœuds principaux* et une valeur par défaut de quatre *nœuds de données*, tandis qu’un type de cluster Storm possède deux *nœuds nimbus*, trois *nœuds ZooKeeper* et une valeur par défaut de quatre *nœuds superviseur*. Le coût des clusters HDInsight est déterminé par le nombre de nœuds et par la taille des machines virtuelles pour les nœuds. Par exemple, si vous savez que vous allez effectuer des opérations nécessitant un grand volume de mémoire, vous souhaitez sélectionner une ressource de calcul avec davantage de mémoire. À des fins d’apprentissage, nous vous recommandons d’utiliser un nœud de données. Pour plus d'informations sur la tarification de HDInsight, consultez la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> La limite de taille du cluster varie selon les abonnements Azure. Contactez le support de facturation pour augmenter la limite.
>
> Les nœuds utilisés par votre cluster ne sont pas comptés en tant que machines virtuelles, car les images de machine virtuelle utilisées pour les nœuds sont un détail d’implémentation du service HDInsight. Les cœurs de calcul utilisés par les nœuds sont comptés dans le nombre total de cœurs de calcul disponibles pour votre abonnement. Lorsque vous créez un cluster HDInsight, vous pouvez voir le nombre de cœurs disponibles et les cœurs qui seront utilisés par le cluster dans la section Résumé du panneau **Taille du cluster**.
>
>

Lorsque vous utilisez le portail Azure pour configurer le cluster, la taille de nœud est disponible via le panneau **Niveaux de tarification du nœud**. Vous pouvez également voir le coût associé aux différentes tailles de nœud. La capture d’écran suivante montre les options disponibles pour un cluster Linux Hadoop.

![Tailles de nœuds de machine virtuelle HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

Les tableaux ci-après indiquent les tailles prises en charge par les clusters HDInsight et les capacités qu’elles offrent.

### <a name="standard-tier-a-series"></a>Niveau standard : série A
Dans le modèle de déploiement classique, certaines tailles de machines virtuelles sont légèrement différentes dans PowerShell et l’interface de ligne de commande (CLI).

* Standard_A3 est Large (Grand)
* Standard_A4 est ExtraLarge (Très grand)

| Taille | Cœurs d’unité centrale | Mémoire | Cartes réseau (max) | Bande passante taille du disque | Bande passante disques de données (1 023 Go chacun) | Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A3\Large |4 |7 Go |2 |Temporaire = 285 Go |8 |8 x 500 |
| Standard_A4\ExtraLarge |8 |14 Go |4 |Temporaire = 605 Go |16 |16 x 500 |
| Standard_A6 |4 |28 Go |2 |Temporaire = 285 Go |8 |8 x 500 |
| Standard_A7 |8 |56 Go |4 |Temporaire = 605 Go |16 |16 x 500 |

### <a name="standard-tier-d-series"></a>Niveau standard : série D
| Taille | Cœurs d’unité centrale | Mémoire | Cartes réseau (max) | Bande passante taille du disque | Bande passante disques de données (1 023 Go chacun) | Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque) |
| --- | --- | --- | --- | --- | --- | --- |
| D3 standard |4 |14 Go |4 |Temporaire (SSD) = 200 Go |8 |8 x 500 |
| D4 standard |8 |28 Go |8 |Temporaire (SSD) = 400 Go |16 |16 x 500 |
| D12 standard |4 |28 Go |4 |Temporaire (SSD) = 200 Go |8 |8 x 500 |
| D13 standard |8 |56 Go |8 |Temporaire (SSD) = 400 Go |16 |16 x 500 |
| D14 standard |16 |112 Go |8 |Temporaire (SSD) = 800 Go |32 |32 x 500 |

### <a name="standard-tier-dv2-series"></a>Niveau standard : série Dv2
| Taille | Cœurs d’unité centrale | Mémoire | Cartes réseau (max) | Bande passante taille du disque | Bande passante disques de données (1 023 Go chacun) | Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 Go |4 |Temporaire (SSD) = 200 Go |8 |8 x 500 |
| Standard_D4_v2 |8 |28 Go |8 |Temporaire (SSD) = 400 Go |16 |16 x 500 |
| Standard_D12_v2 |4 |28 Go |4 |Temporaire (SSD) = 200 Go |8 |8 x 500 |
| Standard_D13_v2 |8 |56 Go |8 |Temporaire (SSD) = 400 Go |16 |16 x 500 |
| Standard_D14_v2 |16 |112 Go |8 |Temporaire (SSD) = 800 Go |32 |32 x 500 |

Pour connaître les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources, consultez [Tailles des machines virtuelles dans Azure](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour plus d’informations sur la tarification des différentes tailles, consultez [Tarification de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

> [!IMPORTANT]
> Si vous envisagez d’avoir plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
>

La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Pour plus d’informations sur la tarification, consultez les [détails de tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


> [!WARNING]
> L’utilisation d’un compte de stockage supplémentaire dans un autre emplacement que le cluster HDInsight n’est pas prise en charge.



## <a name="use-virtual-network"></a>Utiliser un réseau virtuel
[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Pour plus d’informations sur l’utilisation de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Extension des capacités de HDInsight à l’aide d’un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).


## <a name="customize-clusters-using-script-action"></a>Personnalisation de clusters à l’aide d’une action de script

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la création. Ces scripts sont appelés à l’aide de l’option **action de script**, une option de configuration qui peut être utilisée à partir du portail Azure, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster-linux.md).

Certains composants Java natifs, comme Mahout et Cascading, peuvent être exécutés sur le cluster en tant que fichiers Java Archive (JAR). Ces derniers peuvent être distribués au stockage Azure et envoyés aux clusters HDInsight à l’aide des mécanismes d’envoi de tâches Hadoop. Pour plus d’informations, consultez la rubrique [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md).

> [!NOTE]
> En cas de problèmes lors du déploiement ou de l’appel des fichiers JAR sur les clusters HDInsight, contactez le [support Microsoft](https://azure.microsoft.com/support/options/).
>
> Cascading n’est pas pris en charge par HDInsight et ne peut pas bénéficier du support Microsoft. Pour obtenir la liste des composants pris en charge, consultez la rubrique [Nouveautés des versions de cluster fournies par HDInsight](hdinsight-component-versioning.md).
>
>

Vous souhaitez parfois configurer des fichiers de configuration suivants pendant le processus de création :

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Pour plus d’informations, consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="cluster-creation-methods"></a>Méthodes de création de cluster
Grâce à cet article, vous avez acquis les informations de base sur la création d’un cluster HDInsight sur Linux. Utilisez le tableau suivant pour trouver des informations spécifiques sur la création d’un cluster à l’aide de la méthode qui correspond le mieux à vos besoins.

| Clusters créés avec | un navigateur Web | Ligne de commande | API REST | Foundation | Linux, Mac OS X ou Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Le portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |✔ |✔ |
| [Interface de ligne de commande Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Modèles Microsoft Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Étapes suivantes

- [Présentation de HDInsight](hdinsight-hadoop-introduction.md).
- [Tutoriel Hadoop : prise en main de Hadoop dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
