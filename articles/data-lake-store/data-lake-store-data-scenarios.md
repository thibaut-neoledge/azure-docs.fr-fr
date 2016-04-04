<properties 
   pageTitle="Scénarios de données impliquant Data Lake Store | Microsoft Azure" 
   description="Comprendre les différents scénarios et outils à l’aide desquels les données peuvent reçues, traitées, téléchargées et affichées dans Data Lake Store" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="03/09/2016"
   ms.author="nitinme"/>

# Scénarios de données impliquant Azure Data Lake Store

Il existe quatre étapes principales dans traitement des données Big Data :

* Réception de grandes quantités de données dans un magasin de données, en temps réel ou par lots
* Traitement des données
* Téléchargement des données
* Visualisation des données


Dans cet article, nous allons examiner ces étapes en ce qui concerne Azure Data Lake Store pour comprendre les options et les outils disponibles pour répondre à vos besoins en termes de Big Data.

## Réception de données dans Data Lake Store

Cette section présente les différentes sources de données et les différentes façons selon lesquelles ces données peuvent être reçues dans un compte Data Lake Store.

![Réception de données dans Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "Réception de données dans Data Lake Store")

### Données ad hoc

Ceci représente les petits jeux de données qui sont utilisés pour créer un prototype d’une application de Big Data. Il existe différentes façons de recevoir des données ad hoc en fonction de la source de données.

| Source de données | Réception avec |
|--------------------|----------------------------------------------------------------------------------------|
| Ordinateur local | <ul> <li>[Portail Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Interface de ligne de commande inter-plateforme Azure](data-lake-store-get-started-cli.md)</li> <li>[Utilisation de Data Lake Tools pour Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md#upload-source-data-files) </li></ul> |
| Azure Storage Blob | <ul> <li>[Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)</li> <li>[Outil AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li> </ul> |

 
### Flux de données

Ceci représente les données qui peuvent être générées par diverses sources, telles que des applications, des appareils, des capteurs, etc. Ces données peuvent être reçues dans un Data Lake Store par des outils divers. En général, ces outils capturent et traitent les données sur la base de l’événement en temps réel, puis ils écrivent les événements par lots dans Data Lake Store afin qu’ils puissent être traités.

Voici les outils que vous pouvez utiliser :
 
* [Azure Stream Analytics](../stream-analytics-data-lake-output) : les événements reçus dans Event Hubs peuvent être écrits dans Azure Data Lake à l’aide d’une sortie Azure Data Lake Store.
* [Azure HDInsight Storm](../hdinsight/hdinsight-storm-write-data-lake-store.md) : vous pouvez écrire des données directement dans Data Lake Store à partir du cluster Storm.
* [EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost) : vous pouvez recevoir des événements à partir d’Event Hubs, puis les écrire dans Data Lake Store à l’aide du [Kit de développement logiciel (SDK) .NET Data Lake Store](data-lake-store-get-started-net-sdk.md).

### Données relationnelles

Les bases de données relationnelles peuvent également être utilisées comme sources des données. Sur une période donnée, les bases de données relationnelles collectent de grandes quantités de données qui peuvent fournir des informations clés si elles sont traitées via un pipeline de Big Data. Vous pouvez utiliser les outils suivants pour déplacer ces données vers Data Lake Store.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

### Données de journal de serveur web (téléchargement à l’aide d’applications personnalisées)

Ce type de jeu de données est spécifiquement indiqué, car l’analyse des données de journal de serveur web constitue un cas d’usage courant pour les applications de Big Data et nécessite le téléchargement d’importants volumes de fichiers journaux dans Data Lake Store. Vous pouvez utiliser les outils suivants pour écrire vos propres scripts ou applications pour télécharger ces données.

* [Interface de ligne de commande inter-plateforme Azure](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Kit de développement logiciel (SDK) .NET Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)

Pour télécharger des données de journal de serveur web, et également pour télécharger d’autres types de données (par exemple, les données relatives aux sentiments sociaux), il est préférable d’écrire vos propres scripts/applications personnalisés, car cela vous donne la possibilité d’inclure votre composant de téléchargement de données dans le cadre de votre application de Big Data plus étendue. Dans certains cas, ce code peut prendre la forme d’un script ou d’un utilitaire de ligne de commande simple. Dans d’autres cas, le code peut être utilisé pour intégrer le traitement de Big Data dans une solution ou une application métier.


### Données associées aux clusters Azure HDInsight

La plupart des types de clusters HDInsight (Hadoop, HBase, Storm) prend en charge Data Lake Store comme référentiel de stockage des données. Les clusters HDInsight accèdent aux données à partir des objets blob d’Azure Storage (WASB). Pour optimiser les performances, vous pouvez copier les données à partir de WASB sur un compte Data Lake Store associé au cluster. Vous pouvez utiliser les outils suivants pour copier les données.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)

### Jeux de données très volumineux

Pour télécharger des jeux de données qui comptent plusieurs téraoctets, l’utilisation des méthodes décrites ci-dessus peut parfois être lente et coûteuse. Dans ce cas, vous pouvez utiliser les options suivantes.

* **Téléchargement « hors connexion » des données**. Vous pouvez utiliser le [service Azure Import/Export](../storage/storage-import-export-service.md) pour expédier des disques durs contenant vos données à un centre de données Azure. Vos données sont alors téléchargées vers un objet blob Azure Storage. Vous pouvez ensuite utiliser [Azure Data Factory](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store) ou l’[outil AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) pour déplacer des données des objets blob Azure Storage vers Data Lake Store.

	>[AZURE.NOTE] Si vous utilisez le service Import/Export, la taille des fichiers sur les disques durs que vous envoyez au centre de données Azure ne doit pas être supérieure à 200 Go.

* **Utilisation d’Azure ExpressRoute**. Azure ExpressRoute vous permet de créer des connexions privées entre les infrastructures et les centres de données Azure dans votre environnement local. Ceci constitue une option fiable pour le transfert de grandes quantités de données. Pour plus d’informations, consultez [Documentation Azure ExpressRoute](../expressroute/expressroute-introduction.md).

## Traitement des données stockées dans Data Lake Store

Une fois que les données sont disponibles dans Data Lake Store, vous pouvez exécuter une analyse sur ces données à l’aide des applications de Big Data prises en charge. Actuellement, vous pouvez utiliser Azure HDInsight et Azure Data Lake Analytics pour exécuter des tâches d’analyse des données sur les données stockées dans Data Lake Store.

![Analyse des données dans Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "Analyse des données dans Data Lake Store")

Vous pouvez consulter les exemples suivants.

* [Créer un cluster HDInsight avec Data Lake Store comme stockage.](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)



## Téléchargement de données à partir de Data Lake Store

Vous pouvez également être amené à télécharger ou à déplacer les données à partir d’Azure Data Lake Store dans certains cas, tels que :

* Déplacer des données vers d’autres référentiels pour créer une interface avec vos pipelines de traitement des données existantes. Par exemple, vous pouvez vouloir déplacer des données à partir de Data Lake Store vers Azure SQL Database ou vers SQL Server sur site.
* Télécharger des données sur votre ordinateur local pour le traitement dans des environnements IDE lors de la création de prototypes d’applications.

![Sortie des données à partir de Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "Sortie des données à partir de Data Lake Store")

Dans ce cas, vous pouvez utiliser l’une des options suivantes :

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/data-factory-data-movement-activities.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Vous pouvez également utiliser les méthodes suivantes pour écrire votre propre application/script pour télécharger les données à partir de Data Lake Store.

* [Interface de ligne de commande inter-plateforme Azure](data-lake-store-get-started-cli.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Kit de développement logiciel (SDK) .NET Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)

## Visualisation des données dans Data Lake Store

Vous pouvez utiliser une combinaison de services pour créer des représentations visuelles des données stockées dans Data Lake Store.

![Visualisation des données dans Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "Visualisation des données dans Data Lake Store")

* Vous pouvez commencer par utiliser [Azure Data Factory pour déplacer les données de Data Lake Store vers Azure SQL Data Warehouse](../data-factory/data-factory-data-movement-activities.md#supported-data-stores)
* Ensuite, vous pouvez [intégrer Power BI à Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi) pour créer une représentation visuelle des données.

<!---HONumber=AcomDC_0323_2016-->