---
title: "Présentation de Data Factory, un service d’intégration de données | Microsoft Docs"
description: "Découvrez Azure Data Factory : un service d’intégration de données cloud qui gère et automatise le déplacement et la transformation des données."
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
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8d4d574dee4b993d8a464482b244e1f63ade8a57
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="introduction-to-azure-data-factory"></a>Présentation d'Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 (disponibilité générale)](data-factory-introduction.md)
> * [Version 2 (aperçu)](../introduction.md)

> [!NOTE]
> Cet article s’applique à la version 1 de la fabrique de données, mise à la disposition générale (GA). Si vous utilisez la version 2 du service de fabrique de données, qui est une version d’évaluation, consultez l’article relatif à la [présentation de Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>qu'est-ce qu'Azure Data Factory ?
Dans le monde du Big Data, comment les entreprises exploitent-elles les données existantes ? Est-il possible d’enrichir les données générées dans le cloud en utilisant des données de référence provenant de sources locales ou d’autres sources disparates ? Par exemple, une entreprise qui produit des jeux collecte de nombreux journaux générés par les jeux dans le cloud. Elle souhaite analyser ces journaux afin d’obtenir un aperçu des préférences des clients, des données démographiques, etc. pour identifier les opportunités de vente incitative et de vente croisée, développer des nouvelles fonctionnalités afin d’optimiser la croissance et fournir une meilleure expérience aux clients. 

Pour analyser ces journaux, l’entreprise doit utiliser des données de référence comme des informations sur le client, des informations sur les jeux, des informations sur la campagne marketing qui sont contenues dans un magasin de données local. L’entreprise souhaite donc ingérer des données de journal du magasin de données cloud et des données de référence du magasin de données local. Elle souhaite ensuite traite les données à l’aide de Hadoop dans le cloud (Azure HDInsight) et publier les données de résultat dans un entrepôt de données cloud comme Azure SQL Data Warehouse ou un magasin de données local tel que SQL Server. Elle souhaite que ce flux de travail s’exécute une fois toutes les semaines. 

Elle a besoin d’une plateforme qui permet à l’entreprise de créer un flux de travail capable d’ingérer des données des magasins de données local et cloud, de transformer ou de traiter les données à l’aide de services de calcul existants comme Hadoop, puis de publier les résultats dans un magasin de données local ou cloud pour que des applications BI puissent les utiliser. 

![Présentation de Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory est la plateforme pour ce genre de scénarios. Il s’agit d’un **service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement des données et la transformation des données**. Grâce à Azure Data Factory, vous pouvez créer et planifier des flux de travail orientés données (appelés pipelines) capables d’ingérer des données provenant de différents magasins de données, de traiter/transformer les données à l’aide de services de calcul comme Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning, et de publier des données de sortie dans des magasins de données tels qu’Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser.  

Il s’agit plus d’une plateforme d’extraction et de chargement (EL) puis de transformation et chargement (TL) qu’une plateforme d’extraction, de transformation-et de chargement (ETL) traditionnelle. Les transformations effectuées visent à transformer/traiter les données à l’aide de services de calcul plutôt que d’effectuer des transformations comme celles permettant d’ajouter des colonnes dérivées, de compter le nombre de lignes, de trier les données, etc. 

Dans Azure Data Factory, les données utilisées et générées par les flux de travail sont actuellement des **données à tranches temporelles** (toutes les heures, tous les jours, toutes les semaines, etc.). Par exemple, un pipeline peut lire des données d’entrée, traiter des données et générer des données de sortie une fois par jour. Vous ne pouvez également exécuter un flux de travail qu’une seule fois.  
  

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ? 
Les pipelines (flux de travail orientés données) dans Azure Data Factory effectuent généralement les trois étapes suivantes :

![Trois étapes d’Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Se connecter et collecter
Les entreprises disposent de données de divers types situées dans des sources diverses. Quand vous mettez en place un système de production d’informations, la première étape consiste à vous connecter à toutes les sources nécessaires de données et à leur traitement (par exemple, les services SaaS, web, FTP et de partage de fichiers), puis à déplacer les données vers un emplacement centralisé en vue de leur traitement, en fonction des besoins.

Sans Data Factory, les entreprises doivent concevoir des composants personnalisés chargés du déplacement des données ou écrire des services personnalisés pour intégrer ces sources de données et leur traitement. De tels systèmes sont onéreux et leur intégration et maintenance tout particulièrement difficiles. Souvent, ils ne sont pas équipés des dispositifs de surveillance et d’alerte habituellement indispensables aux entreprises, ni même des commandes qu’un service entièrement géré peut offrir.

Avec Data Factory, vous pouvez utiliser la fonction de copie dans un pipeline de données. L’intérêt ? Déplacer les données des magasins de données locaux et dans le cloud dans un magasin de données centralisé dans le cloud, pour les besoins d’analyse ultérieurs. Par exemple, vous pouvez collecter des données d’un Azure Data Lake Store, puis les transformer à l’aide d’un service de calcul Azure Data Lake Analytics. Vous pouvez aussi collecter des données dans un stockage Blob Azure, puis les transformer à l’aide d’un cluster Azure HDInsight Hadoop.

### <a name="transform-and-enrich"></a>Transformer et enrichir
Une fois que les données sont présentes dans un magasin de données centralisé dans le cloud, vous souhaitez que les données collectées soient traitées ou transformées à l’aide de services de calcul tels que HDInsight Hadoop, Spark, Data Lake Analytics et Machine Learning. Vous souhaitez générer de manière fiable des données transformées selon une planification facile à gérer et contrôlée afin de fournir aux environnements de production des données approuvées. 

### <a name="publish"></a>Publier 
Fournissez les données transformées issues du cloud aux sources locales, telles que SQL Server, ou conservez-les dans vos sources de stockage sur le cloud à des fins de consommation par les outils décisionnels et d’analyse, et d’autres applications.

## <a name="key-components"></a>Composants clés
Un abonnement Azure peut contenir une ou plusieurs instances Azure Data Factory (ou fabriques de données). Azure Data Factory s’articule autour de quatre composants clés. Ils fonctionnent ensemble et vous dotent de la plateforme sur laquelle composer des flux de travail orientés données constitués d’étapes de déplacement et de transformation des données. 

### <a name="pipeline"></a>Pipeline
Une fabrique de données peut avoir un ou plusieurs pipelines. Un pipeline est un groupe d’activités. Ensemble, les activités d’un pipeline effectuent une tâche. Par exemple, un pipeline peut contenir un groupe d’activités qui ingère des données à partir d’un objet Blob Azure, puis exécute une requête Hive sur un cluster HDInsight pour partitionner les données. L’avantage de cette opération, c’est que le pipeline vous permet de gérer les activités en tant que groupe et non pas individuellement. Par exemple, vous pouvez déployer et planifier le pipeline, plutôt que chaque activité séparément. 

### <a name="activity"></a>Activité
Un pipeline peut contenir une ou plusieurs activités. Les activités définissent les actions à effectuer sur les données. Par exemple, vous pouvez utiliser une activité de copie pour copier des données d’une banque de données vers une autre. De même, vous pouvez utiliser une activité Hive qui exécute une requête Hive sur un cluster Azure HDInsight afin de convertir ou d’analyser vos données. Data Factory prend en charge deux types d’activités : les activités de déplacement des données et les activités de transformation des données.

### <a name="data-movement-activities"></a>Activités de déplacement des données
L’activité de copie dans Data Factory permet de copier les données d’un magasin de données source vers un magasin de données récepteur. Data Factory prend en charge les magasins de données suivants. Les données de n’importe quelle source peuvent être écrites dans n’importe quel récepteur. Cliquez sur une banque de données pour découvrir comment copier des données depuis/vers cette banque.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Pour plus d’informations, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Activités de transformation des données
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Pour plus d’informations, consultez l’article [Activités de déplacement des données](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Activités .NET personnalisées
Si vous devez déplacer des données vers ou à partir d’une banque de données qui n’est pas prise en charge par l’activité de copie, ou transformer des données à l’aide de votre propre logique, créez une **activité .NET personnalisée**. Pour plus d’informations sur la création et l’utilisation d’une activité personnalisée, consultez [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Groupes de données
Une activité accepte ou non des jeux de données en tant qu’entrées et produit un ou plusieurs jeux de données en tant que sorties. Les jeux de données représentent les structures des données dans les magasins. Ils pointent ou référencent simplement en tant qu’entrées ou sorties les données que vous voulez utiliser dans vos activités. Par exemple, un jeu de données d’objets Blob Azure spécifie le conteneur et le dossier du stockage Blob Azure à partir duquel le pipeline doit lire les données. Ou un jeu de données Azure SQL Table spécifie la table dans laquelle les données de sortie sont écrites par l’activité. 

### <a name="linked-services"></a>Services liés
Les services liés ressemblent à des chaînes de connexion. Ils définissent les informations de connexion nécessaires à Data Factory pour se connecter à des ressources externes. Voyez les choses de la façon suivante : un service lié définit la connexion à la source de données et un jeu de données représente la structure des données. Par exemple, un service lié Stockage Azure spécifie la chaîne de connexion pour se connecter au compte de stockage Azure. Et un jeu de données blob Azure spécifie le conteneur d’objets blob et le dossier qui contient les données.   

Data Factory fait appel aux services liés pour deux raisons :

* Pour représenter une **banque de données** et notamment une instance SQL Server, une base de données Oracle, un partage de fichiers locaux ou un compte de stockage d’objets blob Azure. Pour obtenir la liste des banques de données prises en charge, consultez la section [Activités de déplacement des données](#data-movement-activities) .
* Pour représenter une **ressource de calcul** qui peut héberger l’exécution d’une activité. Par exemple, l’activité HDInsightHive s’exécute sur un cluster HDInsight Hadoop. Pour obtenir la liste des environnements de calcul pris en charge, consultez la section [Activités de transformation des données](#data-transformation-activities).

### <a name="relationship-between-data-factory-entities"></a>Relation entre des entités Data Factory
![Diagramme : Data Factory, un service d’intégration de données cloud - Concepts clés](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figure 2.** Relations entre jeu de données, activité, pipeline et service lié

## <a name="supported-regions"></a>Régions prises en charge
Actuellement, vous pouvez créer des fabriques de données aux **États-Unis de l’Ouest**, aux **États-Unis de l’Est** et en **Europe du Nord**. Une fabrique de données peut toutefois accéder à des magasins de données et à des services de calcul situés dans d’autres régions Azure pour déplacer des données entre des magasins de données ou pour traiter des données à l’aide des services de calcul.

Azure Data Factory ne permet pas en soi de stocker des données. Il vous permet de créer des workflows pilotés par les données afin d’orchestrer le déplacement de données entre les [banques de données prises en charge](#data-movement-activities), ainsi que le traitement des données à l’aide des [services de calcul](#data-transformation-activities) situés dans d’autres régions ou dans un environnement local. Il vous permet également de [surveiller et gérer des workflows](data-factory-monitor-manage-pipelines.md) au moyen de programmes et à l’aide des mécanismes de l’interface utilisateur.

Même si Data Factory est disponible uniquement aux **États-Unis de l’Ouest**, aux **États-Unis de l’Est** ainsi qu’en **Europe du Nord**, le service de déplacement des données intégré à Data Factory est [mondialement](data-factory-data-movement-activities.md#global) disponible dans plusieurs régions. Si un magasin de données se trouve derrière un pare-feu, le déplacement des données est assuré au moyen d’une [passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) installée dans votre environnement local.

Supposons que vos environnements de calcul (cluster Azure HDInsight et Azure Machine Learning, par exemple) s’exécutent hors de la région Europe de l’ouest. Vous pouvez dans ce cas créer et utiliser une instance Azure Data Factory en Europe du Nord et l’utiliser pour planifier des tâches sur vos environnements de calcul en Europe de l’ouest. Quelques millisecondes suffisent à Data Factory pour déclencher la tâche dans votre environnement de calcul, mais l’heure d’exécution du travail dans votre environnement informatique ne change pas.

## <a name="get-started-with-creating-a-pipeline"></a>Prise en main de la création d’un pipeline
Vous pouvez utiliser un des outils ou API suivants pour créer des pipelines de données dans Azure Data Factory : 

- Portail Azure
- Visual Studio
- PowerShell
- API .NET
- API REST
- Modèle Azure Resource Manager. 

Pour découvrir comment créer des fabriques de données avec des pipelines de données, suivez les instructions pas à pas des didacticiels suivants :

| Didacticiel | Description |
| --- | --- |
| [Déplacer des données entre deux magasins de données cloud](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Dans ce didacticiel, vous créez une fabrique de données avec un pipeline qui **déplace des données** de Blob Storage vers la base de données SQL. |
| [Transformer des données à l’aide du cluster Hadoop](data-factory-build-your-first-pipeline.md) |Dans ce didacticiel, vous devez générer votre première fabrique de données Azure avec un pipeline de données qui **traite les données** en exécutant le script Hive sur un cluster Azure HDInsight (Hadoop). |
| [Déplacer des données entre une banque de données locale et une banque de données cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) |Dans ce didacticiel, vous créez une fabrique de données avec un pipeline qui **déplace des données** d’une base de données SQL Server **locale** vers un objet blob Azure. Dans le cadre de la procédure pas à pas, vous installez et configurez la passerelle de gestion des données sur votre ordinateur. |

