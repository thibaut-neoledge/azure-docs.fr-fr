---
title: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store | Microsoft Docs"
description: "Recommandations en matière d’optimisation des performances d’Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: af11866fc812cd8a375557b7bf9df5cdc9bba610
ms.openlocfilehash: f0d0c05c08ce198e2702c76ad35b348107c664c7


---
# <a name="performance-tuning-guidance-for-azure-data-lake-store"></a>Recommandations en matière d’optimisation des performances pour Azure Data Lake Store

Cet article fournit des recommandations sur le moyen d’obtenir des performances optimales lors de l’écriture ou de la lecture de données dans Azure Data Lake Store. Cet article est destiné à familiariser les utilisateurs avec les paramètres configurables pour les outils de chargement/téléchargement de données couramment utilisés et pour les charges de travail d’analyse de données. La procédure d’optimisation décrite dans ce guide cible spécifiquement les charges de travail nécessitant de nombreuses ressources qui impliquent un volume massif de données lues ou écrites dans Data Lake Store.

## <a name="prerequisites"></a>Composants requis

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Azure Data Lake Store**. Pour savoir comment en créer un, consultez [Prise en main d'Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** ayant accès à un compte Data Lake Store. Voir [Créer un cluster HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à activer le Bureau à distance pour le cluster.


## <a name="guidelines-for-data-ingestion-tools"></a>Recommandations relatives aux outils d’ingestion de données

Cette section fournit des recommandations générales destinées à améliorer les performances lors de la copie ou du déplacement de données dans Data Lake Store. Dans cette section, nous présentons les facteurs qui limitent les performances, ainsi que les moyens de surmonter ces restrictions. Voici quelques considérations à prendre en compte.

* **Sources de données** : de nombreuses contraintes peuvent découler de la provenance des données sources. Le débit peut constituer un goulot d’étranglement si les données sources figurent sur des broches lentes ou dans un stockage étendu n’offrant qu’un faible débit. L’utilisation de disques SSD, de préférence sur un disque local, fournit les meilleures performances en raison d’un débit de disque plus élevé.

* **Réseau** : si vos données sources résident sur des machines virtuelles, la connexion réseau entre la machine virtuelle et Data Lake Store a son importance. Utilisez des machines virtuelles équipées de la plus grande carte d’interface réseau disponible pour bénéficier d’une bande passante réseau plus importante.

* **Copie entre régions** : les E/S de données entre régions induisent un coût réseau élevé, par exemple en cas d’exécution d’un outil d’ingestion de données sur une machine virtuelle basée à l’est du pays pour écrire des données sur un compte Data Lake Store situé au centre du pays. Si vous copiez des données entre régions, vous pouvez constater une diminution des performances. Nous vous recommandons d’utiliser des travaux d’ingestion de données sur des machines virtuelles situées dans la même région que le compte Data Lake Store de destination afin d’optimiser le débit réseau.                                                                                                                                        

* **Cluster** : si vous exécutez des travaux d’ingestion de données par le biais d’un cluster HDInsight (par exemple pour l’outil DistCp), nous vous recommandons d’utiliser des machines virtuelles de série D pour le cluster, car elles disposent d’une mémoire plus importante. L’utilisation d’un plus grand nombre de mémoires à tores magnétiques contribue également à augmenter le débit.                                                                                                                                                                                                                                                                                                            

* **Concurrence de threads** : si vous utilisez un cluster HDInsight pour copier des données à partir d’un conteneur de stockage, le nombre de threads parallèles utilisables est limité par la taille de votre cluster, la taille du conteneur et les paramètres de thread. L’un des meilleurs moyens d’optimiser les performances sur Data Lake Store consiste à accroître la concurrence. Vous devez optimiser vos paramètres de façon à obtenir la capacité de concurrence maximale en vue d’augmenter le débit. Le tableau ci-après présente les paramètres relatifs à chaque méthode d’ingestion que vous pouvez configurer pour bénéficier d’un surcroît de concurrence. Cliquez sur les liens figurant dans ce tableau pour accéder à des articles décrivant comment utiliser l’outil pour ingérer des données dans Data Lake Store et comment optimiser les performances de l’outil afin d’obtenir un débit maximal.

    | Outil               | Paramètre de concurrence                                                                |
    |--------------------|------------------------------------------------------------------------------------|
    | [PowerShell](data-lake-store-get-started-powershell.md)       | PerFileThreadCount, ConcurrentFileCount |
    | [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)    | Unités Azure Data Lake Analytics         |
    | [DistCp](data-lake-store-copy-data-wasb-distcp.md)            | -m (mappeur)                             |
    | [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md)| parallelCopies                          |
    | [Sqoop](data-lake-store-data-transfer-sql-sqoop.md)           | fs.azure.block.size, -m (mappeur)        |


## <a name="guidelines-while-working-with-hdinsight-workloads"></a>Recommandations en cas d’utilisation de charges de travail HDInsight

Lorsque vous exécutez des charges de travail analytiques pour l’utilisation de données dans Data Lake Store, nous vous recommandons d’utiliser les versions de cluster HDInsight 3.5 pour bénéficier des meilleures performances avec Data Lake Store. Lorsque votre travail nécessite une grande quantité d’E/S, vous pouvez configurer certains paramètres pour améliorer les performances. Par exemple, si le travail consiste principalement en une série de lectures ou d’écritures, l’augmentation de la concurrence pour les E/S à destination et en provenance d’Azure Data Lake Store peut accroître les performances.

L’optimisation des performances d’Azure Data Lake Store se révèle plus efficace lorsque la concurrence est accrue. Vous disposez de plusieurs méthodes générales pour augmenter la concurrence des travaux qui nécessitent une grande quantité d’E/S.

1. **Exécutez un plus grand nombre de conteneurs YARN de calcul plutôt qu’un petit nombre de conteneurs YARN volumineux** : l’utilisation d’un nombre de conteneurs plus élevé augmente la concurrence pour les opérations d’entrée et de sortie et tire ainsi profit des capacités de Data Lake Store.

    Par exemple, supposons que votre cluster HDInsight comporte 2 nœuds D3v2. Chaque nœud D3v2 dispose de 12 Go de mémoire YARN, de sorte que 2 machines D3v2 totaliseront 24 Go de mémoire YARN. Supposons également que vous ayez défini la taille des conteneurs YARN sur 6 Go. Autrement dit, vous pouvez disposer de 4 conteneurs de 6 Go chacun. Par conséquent, vous pouvez exécuter 4 tâches concurrentes en parallèle. Pour accroître la concurrence, vous pouvez abaisser la taille des conteneurs à 3 Go, ce qui vous offrira 8 conteneurs de 3 Go chacun. Ceci vous permettra alors d’exécuter 8 tâches concurrentes en parallèle. Ce processus est illustré ci-dessous.

    ![Performances de Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-1.png)

    L’une des questions fréquentes des utilisateurs est la suivante : pourquoi ne pas abaisser la taille des conteneurs à 1 Go de mémoire de façon à obtenir 24 conteneurs et étendre encore davantage la concurrence ? Cela dépend de si la tâche a besoin de 3 Go de mémoire ou si une quantité de 1 Go lui suffit.  L’opération que vous effectuez dans le conteneur peut être une opération simple ne nécessitant que 1 Go de mémoire ou une opération complexe pouvant solliciter 3 Go de mémoire.  Si vous réduisez trop la taille du conteneur, vous risquez d’obtenir une exception de mémoire insuffisante.  Vous devrez alors augmenter la taille de vos conteneurs.  Outre la mémoire, le nombre de mémoires à tores magnétiques virtuelles peut également avoir une incidence sur le parallélisme.

    ![Performances de Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-2.png)

2. **Augmentez la mémoire de votre cluster pour bénéficier d’un surcroît de concurrence** : vous pouvez accroître la mémoire de votre cluster en augmentant la taille du cluster ou en choisissant un type de machine virtuelle disposant d’une mémoire plus importante. Cette opération augmentera la quantité de mémoire YARN disponible, ce qui vous permettra de créer un plus grand nombre de conteneurs et d’accroître ainsi la concurrence.  

    Par exemple, supposons que votre cluster HDInsight comporte un seul nœud D3v2 disposant de 12 Go de mémoire YARN et des conteneurs de 3 GB.  En mettant votre cluster à l’échelle de façon à obtenir 2 nœuds D3v2, vous élevez la mémoire YARN à 24 Go.  Le nombre de tâches concurrentes exécutables en parallèle passe alors de 4 à 8.

    ![Performances de Data Lake Store](./media/data-lake-store-performance-tuning-guidance/image-3.png)

3. **Commencez par définir le nombre de tâches sur la capacité de concurrence dont vous disposez** : à ce stade, vous avez déjà défini la taille de conteneur de manière appropriée pour bénéficier de la valeur de concurrence maximale. Vous devez à présent définir le nombre de tâches pour l’utilisation de la totalité de ces conteneurs. Les tâches de chaque charge de travail portent des noms distincts.

    Vous pouvez également prendre en compte la taille de votre travail. Si la taille de ce travail est volumineuse, chaque tâche peut avoir à traiter une grande quantité de données. Il est également possible que vous souhaitiez utiliser davantage de tâches afin de réduire la quantité de données traitée par chaque tâche.

    Par exemple, supposons que vous disposiez de 6 conteneurs. Nous devrions définir notre nombre de tâches sur 6 pour commencer. Vous pouvez alors chercher à déterminer si l’utilisation d’un nombre supérieur de tâches améliore les performances. La définition d’un nombre de tâches plus élevé n’accroît pas la concurrence. Si nous définissons un nombre de tâches supérieur à 6, la tâche ne s’exécutera pas jusqu’à la prochaine vague. Si nous définissons un nombre de tâches inférieur à 6, la concurrence ne sera pas pleinement exploitée.

    Chaque charge de travail comporte différents paramètres permettant de définir le nombre de tâches. Le tableau ci-après répertorie certains de ces paramètres.

    | Charge de travail               | Paramètre de définition des tâches                                                         |
    |--------------------|------------------------------------------------------------------------------------|
    | [Spark sur HDInsight](data-lake-store-performance-tuning-spark.md)       | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
    | [Hive sur HDInsight](data-lake-store-performance-tuning-hive.md)    | hive.tez.container.size         |
    | [MapReduce sur HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
    | [Storm sur HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Nombre de processus de travail</li><li>Nombre d’instances d’exécuteur de spout</li><li>Nombre d’instances d’exécuteur de bolt </li><li>Nombre de tâches de spout</li><li>Nombre de tâches de bolt</li></ul>|

## <a name="see-also"></a>Voir aussi
* [Présentation d’Azure Data Lake Store](data-lake-store-overview.md)
* [Prise en main d'Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



<!--HONumber=Jan17_HO3-->


