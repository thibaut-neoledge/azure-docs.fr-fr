---
title: "Présentation de Data Factory, un service d’intégration de données | Microsoft Docs"
description: "Découvrez Azure Data Factory : un service d’intégration de données cloud qui gère et automatise le déplacement et la transformation des données."
keywords: "intégration de données, intégration de données cloud, description d’azure data factory"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: 40552b5d3cea5b04826c08e7b4b1d046a9fcefba
ms.lasthandoff: 04/23/2017


---
# <a name="introduction-to-azure-data-factory-service-a-data-integration-service-in-the-cloud"></a>Présentation du service Azure Data Factory, un service d’intégration de données dans le cloud
## <a name="what-is-azure-data-factory"></a>Qu’est-ce qu’Azure Data Factory ?
Dans le monde du Big Data, comment les entreprises exploitent-elles les données existantes ? Est-il possible d’enrichir les données générées dans le cloud en utilisant des données de référence provenant de sources locales ou d’autres sources disparates ? Pour y parvenir, il est nécessaire de posséder une plateforme qui se charge de l’agrégation et du traitement des données à partir d’un large éventail de sources. Azure Data Factory est un service d’intégration de données dans le cloud qui gère et automatise le **déplacement** et la **transformation** des données. Vous pouvez créer des solutions d’intégration de données qui ingèrent des données d’entrée provenant de différents magasins de données, les transforment et les traitent, puis publient les données de sortie dans d’autres magasins de données. 

![Diagramme : présentation de Data Factory, un service d’intégration de données](./media/data-factory-introduction/what-is-azure-data-factory.png)

**Figure1.** Ingérez les données de diverses sources de données, préparez-les, transformez-les et analysez-les, puis publiez des données prêtes à l’emploi en vue de leur consommation.


## <a name="what-does-it-offer"></a>Quels sont les avantages d’un tel système ? 
En règle générale, les projets d’intégration de données s’appuient sur des processus d’extraction, de transformation et de chargement (ETL). Ils extraient les données des différentes sources d’une organisation, les transforment pour les rendre conformes au schéma cible d’un entrepôt de données d’entreprise (EDW), puis les chargent dans un EDW, comme indiqué dans l’image suivante. L’EDW est ensuite accessible et s’impose comme la seule source véritable pour les solutions BI analytiques.

![Processus ETL classique](media/data-factory-introduction/traditional-etl.png)
**Processus ETL classique**

De nos jours, les entreprises font face à une croissance exponentielle de leurs données, tant en volume qu’en diversité et en complexité. C’est d’ailleurs ce qu’illustre l’image suivante. La diversité est plus que jamais de mise avec des données enregistrées en local et d’autres apparues dans le cloud. Leurs formes varient, tout comme la vitesse à laquelle elles sont créées. Le traitement de ces données doit intervenir sur différents sites géographiques. Il englobe toute une combinaison de logiciels open source, de solutions commerciales et de services de traitement personnalisé, tous aussi coûteux et difficiles à intégrer et à gérer. L’agilité nécessaire pour s’adapter au paysage actuel en constante évolution du Big Data constitue une réelle opportunité, la finalité étant de fusionner l’EDW classique avec les fonctionnalités indispensables à un système de production de l’information moderne. Azure Data Factory est justement la plateforme de composition qui fait le lien entre les EDW classiques et ces données en constante évolution. Elle permet aux entreprises d’exploiter toutes les données disponibles pour prendre des décisions pilotées par les données.

![Nouveau paysage du Big Data](media/data-factory-introduction/new-big-data-landscape.png)
**Nouveau paysage du Big Data**

Le service Azure Data Factory permet aux entreprises d’exploiter cette diversité. Il les dote d’une plateforme à même de **composer des services de traitement, de stockage et de déplacement des données dans les pipelines de production d’informations**, puis de gérer les ressources de données approuvées obtenues.

Avec le service Azure Data Factory :
- **Vous utilisez sans peine plusieurs services de traitement et de stockage des données**. 

    Les entreprises disposent de données de différents types situées dans des sources diverses. Quand vous mettez en place un système de production d’informations, la première étape consiste à vous connecter à toutes les sources nécessaires de données et à leur traitement (par exemple, les services SaaS, web, FTP et de partage de fichiers), puis à déplacer les données vers un emplacement centralisé en vue de leur traitement, en fonction des besoins.

    Sans Data Factory, les entreprises doivent concevoir des composants personnalisés chargés du déplacement des données ou écrire des services personnalisés pour intégrer ces sources de données et leur traitement. De tels systèmes sont onéreux et leur intégration et maintenance tout particulièrement difficiles. Souvent, ils ne sont pas équipés des dispositifs de surveillance et d’alerte habituellement indispensables aux entreprises, ni même des commandes qu’un service entièrement géré peut offrir.

    Avec Data Factory, vous pouvez utiliser la fonction de copie dans un pipeline de données. L’intérêt ? Déplacer les données des magasins de données locaux et dans le cloud dans un magasin de données centralisé dans le cloud, pour les besoins d’analyse ultérieurs. Par exemple, vous pouvez collecter des données d’un Azure Data Lake Store, puis les transformer à l’aide d’un service de calcul Azure Data Lake Analytics. Vous pouvez aussi collecter des données dans un stockage Blob Azure, puis les transformer à l’aide d’un cluster Azure HDInsight Hadoop.
- **Vous transformez les données en informations fiables**. 

    Une fois que les données sont enregistrées dans un magasin de données centralisé dans le cloud, vous pouvez créer et déployer des pipelines de données pour produire des données transformées fiables, selon un planning contrôlé et facile à gérer. Ces données de confiance serviront à alimenter des environnements de production. La transformation des données dans Azure Data Factory est liée à des activités de transformation du type Hive, Pig, MapReduce, Azure Machine Learning Batch Execution et des activités C# personnalisées sur le cluster Azure HDInsight Hadoop, les machines virtuelles Azure Machine Learning ou le pool de machines virtuelles Azure Batch.
- **Vous surveillez les pipelines de données dans un même emplacement**.

    Dès lors que vous possédez un portefeuille de données diversifié, il est important d’avoir une vue complète et fiable de vos services de stockage, de traitement et de déplacement des données. Data Factory vous permet d’évaluer rapidement l’intégrité des pipelines de données de bout en bout, de détecter les problèmes et de prendre les mesures nécessaires, le cas échéant. Suivez visuellement le lignage des données et les relations entre vos données, dans vos diverses sources. Consultez l’historique complet des tâches exécutées, de l’intégrité du système et des dépendances dans un tableau de surveillance unique.

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ? 
Il existe trois phases de production des informations dans un service Azure Data Factory :

![Trois phases de production d’informations](media/data-factory-introduction/three-information-production-stages.png)

- **Connexion et collecte** : dans cette étape, les données provenant des différentes sources sont collectées et enregistrées dans un seul endroit.
- **Transformation et enrichissement** : dans cette étape, les données collectées sont traitées ou transformées.
- **Publication** : dans cette étape, les données sont publiées pour pouvoir être consommées par les outils BI, les outils analytiques et d’autres applications.

## <a name="key-components"></a>Composants clés
Un abonnement Azure peut contenir une ou plusieurs instances Azure Data Factory (ou fabriques de données). Azure Data Factory s’articule autour de quatre composants clés. Ils fonctionnent ensemble et vous dotent de la plateforme sur laquelle composer des orchestrations de déplacement et de transformation de données, de simples à complexes, pour votre flux de données.

### <a name="activity"></a>Activité
Les activités définissent les actions à effectuer sur les données. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’une banque de données vers une autre. De même, vous pouvez utiliser une activité Hive qui exécute une requête Hive sur un cluster Azure HDInsight afin de convertir ou d’analyser vos données. Data Factory prend en charge deux types d’activités : les activités de déplacement des données et les activités de transformation des données.

Chaque activité peut avoir zéro ou plusieurs jeux de données d’entrée et produire un ou plusieurs jeux de données de sortie. 


### <a name="data-movement-activities"></a>Activités de déplacement des données
L’activité de copie dans Data Factory permet de copier les données d’un magasin de données source vers un magasin de données récepteur. Data Factory prend en charge les magasins de données suivants. Les données de n’importe quelle source peuvent être écrites dans n’importe quel récepteur. Cliquez sur une banque de données pour découvrir comment copier des données depuis/vers cette banque.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Pour plus d’informations, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Activités de transformation des données
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

Pour plus d’informations, consultez l’article [Activités de déplacement des données](data-factory-data-transformation-activities.md).

Si vous devez déplacer des données vers ou à partir d’une banque de données qui n’est pas prise en charge par l’activité de copie, ou transformer des données à l’aide de votre propre logique, créez une **activité .NET personnalisée**. Pour plus d’informations sur la création et l’utilisation d’une activité personnalisée, consultez [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="pipeline"></a>Pipeline
Un pipeline est un groupe d’activités. Ensemble, les activités d’un pipeline effectuent une tâche. Par exemple, un pipeline peut contenir un groupe d’activités qui ingère des données à partir d’un objet Blob Azure, puis exécute une requête Hive sur un cluster HDInsight pour partitionner les données du journal. L’avantage de cette opération, c’est que le pipeline vous permet de gérer les activités en tant que groupe et non pas individuellement. Par exemple, vous pouvez déployer et planifier le pipeline, plutôt que chaque activité séparément.

### <a name="datasets"></a>Groupes de données
Les jeux de données représentent les structures des données dans les magasins. Ils pointent ou référencent simplement en tant qu’entrées ou sorties les données que vous voulez utiliser dans vos activités. Par exemple, un jeu de données d’objets Blob Azure spécifie le conteneur et le dossier du stockage Blob Azure à partir duquel le pipeline doit lire les données. 

### <a name="linked-services"></a>Services liés
Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Voyez les choses de la façon suivante : le jeu de données représente la structure des données, et le service lié définit la connexion à la source de données.  Data Factory fait appel aux services liés pour deux raisons :

* Pour représenter une **banque de données** et notamment une instance SQL Server, une base de données Oracle, un partage de fichiers locaux ou un compte de stockage d’objets blob Azure. Pour obtenir la liste des banques de données prises en charge, consultez la section [Activités de déplacement des données](#data-movement-activities) .
* Pour représenter une **ressource de calcul** qui peut héberger l’exécution d’une activité. Par exemple, l’activité HDInsightHive s’exécute sur un cluster HDInsight Hadoop. Pour obtenir la liste des environnements de calcul pris en charge, consultez la section [Activités de transformation des données](#data-transformation-activities).

### <a name="relationship-between-data-factory-entities"></a>Relation entre des entités Data Factory
![Diagramme : Data Factory, un service d’intégration de données cloud - Concepts clés](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figure 2.** Relations entre jeu de données, activité, pipeline et service lié

## <a name="supported-regions"></a>Régions prises en charge
Actuellement, vous pouvez créer des fabriques de données aux **États-Unis de l’Ouest**, aux **États-Unis de l’Est** et en **Europe du Nord**. Une fabrique de données peut toutefois accéder à des magasins de données et à des services de calcul situés dans d’autres régions Azure pour déplacer des données entre des magasins de données ou pour traiter des données à l’aide des services de calcul.

Azure Data Factory ne permet pas en soi de stocker des données. Il vous permet de créer des flux pilotés par les données afin d’orchestrer le déplacement de données entre les [magasins de données pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats) ainsi que le traitement des données à l’aide des [services de calcul](data-factory-compute-linked-services.md) situés dans d’autres régions ou dans un environnement local. Il vous permet également de [surveiller et gérer des workflows](data-factory-monitor-manage-pipelines.md) au moyen de programmes et à l’aide des mécanismes de l’interface utilisateur.

Même si Data Factory est disponible uniquement aux **États-Unis de l’Ouest**, aux **États-Unis de l’Est** ainsi qu’en **Europe du Nord**, le service de déplacement des données intégré à Data Factory est [mondialement](data-factory-data-movement-activities.md#global) disponible dans plusieurs régions. Lorsqu’un magasin de données se trouve derrière un pare-feu, le déplacement des données est assuré au moyen d’une [passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) installée dans votre environnement local.

Supposons que vos environnements de calcul (cluster Azure HDInsight et Azure Machine Learning, par exemple) s’exécutent hors de la région Europe de l’ouest. Vous pouvez dans ce cas créer et utiliser une instance Azure Data Factory en Europe du Nord et l’utiliser pour planifier des tâches sur vos environnements de calcul en Europe de l’ouest. Quelques millisecondes suffisent à Data Factory pour déclencher la tâche dans votre environnement de calcul, mais l’heure d’exécution du travail dans votre environnement informatique ne change pas.

Nous avons l’intention d’étendre la prise en charge d’Azure Data Factory à toutes les zones géographiques.

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir comment créer des fabriques de données avec des pipelines de données, suivez les instructions pas à pas des didacticiels suivants :

| Didacticiel | Description |
| --- | --- |
| [Déplacer des données entre deux magasins de données cloud](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Dans ce didacticiel, vous créez une fabrique de données avec un pipeline qui **déplace des données** de Blob Storage vers la base de données SQL. |
| [Transformer des données à l’aide du cluster Hadoop](data-factory-build-your-first-pipeline.md) |Dans ce didacticiel, vous devez générer votre première fabrique de données Azure avec un pipeline de données qui **traite les données** en exécutant le script Hive sur un cluster Azure HDInsight (Hadoop). |
| [Déplacer des données entre une banque de données locale et une banque de données cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) |Dans ce didacticiel, vous créez une fabrique de données avec un pipeline qui **déplace des données** d’une base de données SQL Server **locale** vers un objet blob Azure. Dans le cadre de la procédure pas à pas, vous installez et configurez la passerelle de gestion des données sur votre ordinateur. |

