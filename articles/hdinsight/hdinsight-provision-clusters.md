---
title: "Création de clusters Hadoop basés sur Windows dans HDInsight | Microsoft Docs"
description: "Apprenez à créer des clusters pour Azure HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a311f7e0-9333-4886-a726-def79e5db8cb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: f014b92516879a0255dda3736bf5af6142555d92


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight"></a>Création de clusters Hadoop basés sur Windows dans HDInsight

Un cluster Hadoop se compose de plusieurs machines virtuelles (nœuds) utilisées dans le processus de distribution des tâches sur le cluster. Azure isole les détails de l’implémentation de l’installation et la configuration des nœuds individuels afin de fournir les informations de configuration générales. Cet article aborde ces paramètres de configuration.

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date). Les informations de ce document sont propres aux clusters Azure HDInsight sous Windows. Pour plus d’informations sur les clusters Linux, voir [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).



## <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="cluster-types"></a>Types de cluster
Actuellement, HDInsight propose quatre types de clusters, chacun avec un ensemble de composants qui fournissent des fonctionnalités.

| Type de cluster | Fonctionnalités |
| --- | --- |
| Hadoop |Requête et analyse (tâches de traitement par lots) |
| HBase |Stockage des données NoSQL |
| Storm |Traitement d’événements en temps réel |
| Spark (version préliminaire) |Traitement en mémoire, requêtes interactives, traitement du flux de traitement micro-batch |

Chaque type de cluster possède son propre nombre de nœuds, sa terminologie pour les nœuds du cluster et la taille de machine virtuelle par défaut pour chaque type de nœud. Dans le tableau suivant, les chiffres entre parenthèses correspondent au nombre de nœuds de chaque type.

| Type | Nœuds (nombre de nœuds) | Diagramme |
| --- | --- | --- |
| Hadoop |Nœud principal (2), nœud de données (1+) |![Nœuds de cluster Hadoop HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png) |
| HBase |Serveur principal (2), serveur de région (1+), nœud principal/ZooKeeper (3) |![Nœuds de cluster HDInsight HBase](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png) |
| Storm |Nœud Nimbus (2), serveur supervisor (1+), nœud ZooKeeper (3) |![Nœuds de cluster HDInsight Storm](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png) |
| Spark |Nœud principal (2), nœud worker (1+), nœud ZooKeeper (3) (gratuits pour les machines virtuelles ZooKeeper A1) |![Nœuds de cluster HDInsight Spark](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png) |

> [!IMPORTANT]
> Si vous envisagez d’avoir plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
> 
> 

Vous pouvez ajouter d’autres composants, tels que Hue ou R, à ces types de base en utilisant des [actions de script](#customize-clusters-using-script-action).

> [!IMPORTANT]
> Il existe différents types de clusters HDInsight, correspondant aux différentes charges de travail ou technologies pour lesquelles ils sont utilisés. Il n’existe aucune méthode prise en charge pour créer un cluster combinant plusieurs types, tels que Storm et HBase sur un seul cluster. 
> 
> 

Si votre solution nécessite des technologies qui sont réparties sur plusieurs types de cluster HDInsight, vous devez créer un réseau virtuel Azure et créer les types de cluster requis dans le réseau virtuel. Cela permet aux clusters, et au code déployé sur ces clusters, de communiquer directement entre eux.

Pour plus d’informations sur l’utilisation du réseau virtuel Azure avec HDInsight, consultez [Étendre HDInsight à l’aide de réseaux virtuels Azure](hdinsight-extend-hadoop-virtual-network.md).

Pour voir un exemple d’utilisation de deux types de cluster au sein d’un réseau virtuel Azure, consultez la section [Analyser les données de capteur avec Storm et HBase](hdinsight-storm-sensor-data-analysis.md).

## <a name="basic-configuration-options"></a>Options de configuration de base
Voici les options de configuration de base requises pour la création d'un cluster HDInsight.

### <a name="cluster-name"></a>Nom du cluster
Le nom du cluster permet d’identifier un cluster. Le nom du cluster doit être globalement unique et doit respecter ces instructions d’affectation de noms :

* Le champ doit être une chaîne comportant entre 3 et 63 caractères.
* Le champ ne peut contenir que des lettres, des chiffres et des traits d'union.

### <a name="cluster-type"></a>Type de cluster
Consultez [Types de clusters](#cluster-types).

### <a name="operating-system"></a>Système d’exploitation
Vous pouvez créer des clusters HDInsight sur l’un des deux systèmes d’exploitation suivants :

* HDInsight sur Linux. HDInsight offre la possibilité de configurer des clusters Linux sur Azure. Configurez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour plus d’informations, consultez [Prise en main de Hadoop sur Linux dans HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).
* HDInsight sur Windows (Windows Server 2012 R2 Datacenter).

### <a name="hdinsight-version"></a>Version de HDInsight
La version de HDInsight permet de déterminer la version de HDInsight qui doit être utilisée pour ce cluster. Pour plus d’informations, consultez [Quels sont les différents composants Hadoop disponibles avec HDInsight ?](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

### <a name="subscription-name"></a>Nom d’abonnement
Chaque cluster HDInsight est lié à un abonnement Azure.

### <a name="resource-group-name"></a>Nom de groupe ressources
Avec [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), vous pouvez déployer, mettre à jour, surveiller ou supprimer les ressources de votre application.

### <a name="credentials"></a>Informations d'identification
Les clusters HDInsight vous permettent de configurer trois comptes d’utilisateur lors de la création.

* [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe, nommé groupe de ressources Azure. Vous pouvez déployer, mettre à jour, surveiller ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.
* Utilisateur HTTP. Si vous utilisez la configuration de base à partir du portail Azure, le nom d’utilisateur par défaut est *admin* . Parfois, le nom par défaut est « Utilisateur du cluster ».
* Utilisateur RDP (clusters Windows). Connectez-vous au cluster à l’aide de RDP. Lorsque vous créez le compte, vous devez définir une date d'expiration de 90 jours à compter de la date de création du compte.
* Utilisateur SSH (clusters Linux). Connectez-vous au cluster à l’aide de SSH. Vous pouvez créer des comptes d’utilisateur SSH supplémentaires une fois le cluster créé, en suivant la procédure décrite dans la rubrique [Utiliser SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="data-source"></a>Source de données
Le système de fichiers distribués Hadoop (HDFS) d’origine utilise de nombreux disques locaux sur le cluster. HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Le stockage d’objets blob Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. Grâce à une interface HDFS, l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées dans le stockage d’objets blob. Si vous stockez vos données dans un stockage d’objets blob, vous pouvez supprimer en toute sécurité les clusters HDInsight servant aux calculs, sans perte de données utilisateur.

Durant la configuration, vous devez spécifier un compte de stockage Azure, ainsi qu’un conteneur de stockage d’objets blob Azure sur le compte de stockage Azure. Certains processus de création requièrent le compte de stockage Azure et le conteneur de stockage d’objets blob qui a été créé au préalable. Le conteneur de stockage d’objets blob est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des comptes Azure Storage supplémentaires (stockage lié) qui seront accessibles via le cluster. Le cluster peut également accéder aux conteneurs de stockage d’objets blob qui sont configurés avec un accès en lecture public complet ou un accès en lecture public pour les objets blob uniquement.  Pour en avoir plus, voir [Gestion de l’accès aux ressources d’Azure Storage](../storage/storage-manage-access-to-resources.md).

![Stockage HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

> [!NOTE]
> Un conteneur de stockage d’objets blob regroupe un ensemble d’objets blob, comme illustré dans l’image suivante.
> 
> 

Durant la configuration, vous devez spécifier un compte de stockage Azure, ainsi qu’un conteneur de stockage d’objets blob Azure sur le compte de stockage Azure. Certains processus de création requièrent le compte de stockage Azure et le conteneur de stockage d’objets blob qui a été créé au préalable. Le conteneur de stockage d’objets blob est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des comptes Azure Storage supplémentaires (stockage lié) auxquels le cluster peut accéder. Le cluster peut également accéder aux conteneurs d’objets blob qui sont configurés avec un accès en lecture public complet ou un accès en lecture public pour les objets blob uniquement. Pour en avoir plus, voir [Gestion de l’accès aux ressources d’Azure Storage](../storage/storage-manage-access-to-resources.md).

![un stockage Azure Blob](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)

Nous vous déconseillons d’utiliser le conteneur d’objets blob par défaut pour stocker des données d’entreprise. Nous vous recommandons de supprimer le conteneur de stockage d’objets blob par défaut après chaque utilisation pour réduire les coûts de stockage. Le conteneur par défaut contient les journaux des applications et du système. Assurez-vous de récupérer les journaux avant de supprimer le conteneur.

> [!WARNING]
> HDInsight ne prend pas en charge le partage d’un conteneur de stockage d’objets blob sur plusieurs clusters.
> 
> 

Pour plus d’informations sur l’utilisation du stockage d’objets blob secondaire, consultez [Stockage d’objets blob Azure compatible avec HDFS avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md).

En plus du stockage d’objets blob Azure, vous pouvez utiliser [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) comme compte de stockage par défaut pour le cluster HBase dans HDInsight, et comme stockage lié pour les quatre types de cluster HDInsight. Pour plus d’informations, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="location-region"></a>Emplacement (région)
Le cluster HDInsight et son compte de stockage par défaut doivent être situés au même emplacement Azure.

![Régions Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

Pour obtenir la liste des régions prises en charge, cliquez sur la liste déroulante **Région** de la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

### <a name="node-pricing-tiers"></a>Niveaux tarifaires des nœuds
L’utilisation de ces nœuds est facturée aux clients pendant toute la durée de vie du cluster. La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Les clusters ne peuvent pas être désalloués ou mis en attente.

Les différents types de cluster ont des types de nœuds, des nombres de nœuds et des tailles de nœud différents. Par exemple, un type de cluster Hadoop possède deux *nœuds principaux* et une valeur par défaut de quatre *nœuds de données*, tandis qu’un type de cluster Storm possède deux *nœuds nimbus*, trois *nœuds ZooKeeper* et une valeur par défaut de quatre *nœuds superviseur*. Le coût des clusters HDInsight est déterminé par le nombre de nœuds et par la taille de la machine virtuelle pour les nœuds. Par exemple, si vous savez que vous allez effectuer des opérations nécessitant un grand volume de mémoire, vous souhaitez sélectionner une ressource de calcul avec davantage de mémoire. À des fins d’apprentissage, nous vous recommandons d’utiliser un nœud de données. Pour plus d'informations sur la tarification de HDInsight, consultez la rubrique [Tarification HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> La limite de taille du cluster varie selon les abonnements Azure. Contactez le support de facturation pour augmenter la limite.
> 
> Les nœuds utilisés par votre cluster ne sont pas comptés en tant que machines virtuelles, car les images de machine virtuelle utilisées pour les nœuds sont un détail d’implémentation du service HDInsight. Cependant, les cœurs de calcul utilisés par les nœuds sont comptés dans le nombre total de cœurs de calcul disponibles pour votre abonnement. Vous pouvez voir les cœurs disponibles et le nombre de cœurs que le cluster utilisera dans la section Résumé du panneau Niveaux de tarification du nœud lors de la création d’un cluster HDInsight.
> 
> 

Lorsque vous configurez le cluster avec le portail Azure, la taille de nœud est disponible via le panneau **Niveaux de tarification du nœud** . Vous pouvez également voir le coût associé aux différentes tailles de nœud. La capture d’écran suivante montre les options disponibles pour un cluster Linux Hadoop.

![tailles de nœud de machine virtuelle HDInsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

Les tableaux ci-après indiquent les tailles prises en charge par les clusters HDInsight et les capacités qu’elles offrent.

### <a name="standard-tier-a-series"></a>Niveau standard : série A
Dans le modèle de déploiement classique, certaines tailles de machines virtuelles sont légèrement différentes dans PowerShell et l’interface de ligne de commande.

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
| D3 standard |4 |14 Go |4 |Temporaire (SSD) =&200; Go |8 |8 x 500 |
| D4 standard |8 |28 Go |8 |Temporaire (SSD) =&400; Go |16 |16 x 500 |
| D12 standard |4 |28 Go |4 |Temporaire (SSD) =&200; Go |8 |8 x 500 |
| D13 standard |8 |56 Go |8 |Temporaire (SSD) =&400; Go |16 |16 x 500 |
| D14 standard |16 |112 Go |8 |Temporaire (SSD) =&800; Go |32 |32 x 500 |

### <a name="standard-tier-dv2-series"></a>Niveau standard : série Dv2
| Taille | Cœurs d’unité centrale | Mémoire | Cartes réseau (max) | Bande passante taille du disque | Bande passante disques de données (1 023 Go chacun) | Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 Go |4 |Temporaire (SSD) =&200; Go |8 |8 x 500 |
| Standard_D4_v2 |8 |28 Go |8 |Temporaire (SSD) =&400; Go |16 |16 x 500 |
| Standard_D12_v2 |4 |28 Go |4 |Temporaire (SSD) =&200; Go |8 |8 x 500 |
| Standard_D13_v2 |8 |56 Go |8 |Temporaire (SSD) =&400; Go |16 |16 x 500 |
| Standard_D14_v2 |16 |112 Go |8 |Temporaire (SSD) =&800; Go |32 |32 x 500 |

Pour connaître les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources, consultez [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour plus d’informations sur la tarification des différentes tailles, consultez [Tarification de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

> [!IMPORTANT]
> Si vous envisagez d’avoir plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
> 
> 

La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Pour plus d’informations sur la tarification, consultez les [détails de tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

| Taille | Cœurs d’unité centrale | Mémoire | Cartes réseau (max) | Bande passante taille du disque | Bande passante disques de données (1 023 Go chacun) | Bande passante Nombre maximal d’opérations d’E/S par seconde (500 par disque) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D3_v2 |4 |14 Go |4 |Temporaire (SSD) =&200; Go |8 |8 x 500 |
| Standard_D4_v2 |8 |28 Go |8 |Temporaire (SSD) =&400; Go |16 |16 x 500 |
| Standard_D12_v2 |4 |28 Go |4 |Temporaire (SSD) =&200; Go |8 |8 x 500 |
| Standard_D13_v2 |8 |56 Go |8 |Temporaire (SSD) =&400; Go |16 |16 x 500 |
| Standard_D14_v2 |16 |112 Go |8 |Temporaire (SSD) =&800; Go |32 |32 x 500 |

Pour connaître les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources, consultez [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Pour plus d’informations sur la tarification des différentes tailles, consultez [Tarification de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).  

> [!IMPORTANT]
> Si vous envisagez d’avoir plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez sélectionner une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
> 
> 

 La facturation démarre une fois le cluster créé et ne s’arrête que lorsque le cluster est supprimé. Pour plus d’informations sur la tarification, consultez les [détails de tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="add-more-storage"></a>Ajouter du stockage
Dans certains cas, vous souhaiterez ajouter plus de stockage au cluster. Par exemple, vous pouvez avoir plusieurs comptes de stockage Azure correspondant à différentes régions géographiques ou différents services et souhaitez tous les analyser avec HDInsight.

Pour plus d’informations sur l’utilisation du stockage d’objets blob secondaire, consultez [Utilisation du stockage d’objets blob Azure compatible avec HDFS avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md). Pour plus d’informations sur l’utilisation de magasins Data Lake secondaires, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="use-a-hiveoozie-metastore"></a>Utiliser un metastore Hive/Oozie
Nous vous recommandons fortement d’utiliser un metastore personnalisé si vous voulez conserver vos tables Hive après avoir supprimé votre cluster HDInsight dans le but de pouvoir attacher ce metastore à un autre cluster HDInsight.

> [!IMPORTANT]
> Le metastore HDInsight n’est pas rétrocompatible. Par exemple, vous ne pouvez pas utiliser un metastore d’un cluster HDInsight 3.3 pour créer un cluster HDInsight 3.2.
> 
> 

Le metastore contient les métadonnées Hive et Oozie, telles que les tables, les partitions, les schémas et les colonnes Hive. Le metastore vous permet de conserver vos métadonnées Hive et Oozie. Vous n’avez pas besoin de recréer les tables Hive ou les tâches Oozie lorsque vous créez un nouveau cluster. Hive utilise par défaut une base de données intégrée Azure SQL pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé. Par exemple, si vous créez des tables Hive dans un cluster créé avec un metastore Hive, vous pouvez voir ces tables si vous supprimez et recréez le cluster avec le même metastore Hive.

La configuration Metastore n’est pas disponible pour les types de cluster HBase.

> [!IMPORTANT]
> Lors de la création d’un metastore personnalisé, n’utilisez pas de nom de base de données contenant des traits d’union ou des tirets, car cela risque d’entraîner l’échec du processus de création du cluster.
> 
> 

## <a name="use-azure-virtual-network"></a>Utiliser un réseau virtuel Azure
[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Avec un réseau virtuel, vous pouvez :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).
  
    ![diagram of cloud-only configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)
* Connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou de point à site) en utilisant un réseau privé virtuel (ou VPN).

| Configuration de site à site | Configuration de point à site |
| --- | --- |
| Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données au réseau virtuel Azure en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).<br />![diagram of site-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) |Une configuration de point à site vous permet de connecter une ressource spécifique au réseau virtuel Azure en utilisant un VPN logiciel.<br />![diagram of point-to-site configuration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Les clusters Windows nécessitent un réseau virtuel (classique), tandis que les clusters Linux nécessitent un réseau virtuel Azure Resource Manager. Si le type de réseau est incorrect, il ne sera pas utilisable lorsque vous créerez le cluster.

Pour plus d’informations sur le fonctionnement de HDInsight avec un réseau virtuel, notamment la configuration spécifique requise pour le réseau virtuel, consultez [Extension des capacités de HDInsight à l’aide d’un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="customize-clusters-by-using-hdinsight-cluster-customization-bootstrap"></a>Personnalisation de clusters à l'aide de la personnalisation de cluster de HDInsight (bootstrap)
Vous souhaitez parfois configurer des fichiers de configuration suivants :

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

Pour conserver les modifications apportées pendant la durée de vie des clusters, vous pouvez utiliser la personnalisation de cluster HDInsight au cours du processus de création. Vous pouvez également utiliser Ambari dans les clusters Linux. Pour plus d’informations, consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

> [!NOTE]
> Les clusters Windows ne peuvent pas conserver les modifications lorsqu’ils ont été réinitialisés. Pour plus d'informations, consultez la page [Redémarrages d'instances de rôles pour cause de mise à jour du système d'exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Pour conserver les modifications apportées pendant la durée de vie d’un cluster, vous devez utiliser la personnalisation des clusters HDInsight au cours du processus de création.
> 
> 

## <a name="customize-clusters-by-using-script-action"></a>Personnalisation de clusters en utilisant une action de script
Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la création. Ces scripts sont appelés à l’aide de l’option **action de script**, une option de configuration qui peut être utilisée à partir du portail Azure, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez [Personnalisation d’un cluster HDInsight en utilisant une action de script](hdinsight-hadoop-customize-cluster.md).

## <a name="cluster-creation-methods"></a>Méthodes de création de cluster
Grâce à cet article, vous avez acquis les informations de base sur la création d'un cluster HDInsight basé sur Windows. Utilisez le tableau suivant pour trouver des informations spécifiques sur la création d’un cluster à l’aide de la méthode qui correspond le mieux à vos besoins.

| Clusters créés avec | un navigateur Web | Ligne de commande | API REST | Foundation | Linux, Mac OS X ou Unix | Windows |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| [Le portail Azure](hdinsight-hadoop-create-windows-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |
| [Interface de ligne de commande Azure](hdinsight-hadoop-create-windows-clusters-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |✔ |✔ |
| [KIT DE DÉVELOPPEMENT LOGICIEL (SDK) .NET](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |✔ |✔ |
| [Modèles Microsoft Azure Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |✔ |✔ |




<!--HONumber=Feb17_HO1-->


