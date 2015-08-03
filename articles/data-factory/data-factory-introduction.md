<properties 
	pageTitle="Présentation d'Azure Data Factory" 
	description="Découvrez comment utiliser le service Azure Data Factory pour composer des services de traitement de données, de stockage de données et de déplacement de données pour créer des pipelines qui génèrent des informations fiables." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Présentation du service Azure Data Factory
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


Le service **Azure Data Factory** est un service entièrement géré pour composer des services de stockage de données, de traitement de données et de déplacement de données dans des pipelines de production rationalisés, évolutifs et fiables. Le service Data Factory vous permet d'effectuer les opérations suivantes :

- Générer des flux de travail (pipelines) pilotés par les données qui joignent, agrègent et transforment des données provenant de magasins de données Internet, locaux et basés sur le cloud 
- Transformer en informations fiables des données structurées, semi-structurées et non structurées issues de diverses sources de données
- Générer des données qui peuvent être facilement utilisées au moyen de l'aide à la décision, d'outils d'analyse et d'autres applications 
- Configurer un traitement de données complexe dans un simple script JSON
- Surveiller et gérer des pipelines grâce à une riche expérience visuelle proposée via le portail Azure en version préliminaire  

La vidéo suivante présente brièvement le service Azure Data Factory.


- [Vidéo : Présentation d'Azure Data Factory](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## Vue d'ensemble
Les projets d'intégration de données se sont toujours articulés autour de la création de processus ETL (Extraction, Transformation et Chargement) qui extraient des données provenant de différentes sources d'une organisation, transforment ces données pour qu'elles correspondent au schéma cible d'un entrepôt de données d'entreprise (EDW, Enterprise Data Warehouse) et chargent ces données dans un EDW. L'EDW est ensuite accessible comme seule source approuvée pour les solutions d'analyse décisionnelle.

![Processus ETL classique][image-data-factory-introduction-traditional-ETL]

Le paysage actuel de données pour les entreprises croît de manière exponentielle en termes de volume, de diversité et de complexité. Il est plus varié que jamais avec des données locales et du cloud, présentant des formes et des vitesses différentes. Le traitement des données doit se produire à divers emplacements géographiques et inclut une combinaison de logiciels open source, de solutions commerciales et de services de traitement personnalisé, coûteux et difficiles à intégrer et à gérer. L'agilité nécessaire pour s'adapter au contexte actuel des données volumineuses offre l'opportunité de fusionner l'EDW classique avec les fonctionnalités nécessaires d'un système de production modernes de l'information.

![Méthodes actuelles de traitement : un paysage diversifié][image-data-factory-introduction-todays-diverse-processing-landspace]

Le service **Azure Data Factory** est la plateforme de composition entre les EDW classiques et le contexte des données évolutives qui permet aux entreprises d'exploiter toutes les données disponibles pour la prise de décision pilotée par celles-ci. Il permet aux entreprises d'exploiter cette diversité en fournissant une plateforme pour composer des services de traitement, de stockage et de déplacement des données dans des pipelines de production d'informations, et gérer les ressources de données fiables.

Le service Azure Data Factory vous permet d'effectuer les opérations suivantes :

- **Utiliser facilement des systèmes de stockage et de traitement de données divers.** Le service Data Factory vous permet de créer des pipelines de production d'informations qui déplacent et traitent les données locales (telles que SQL Server) et les sources de données du cloud comme Azure SQL Database, Azure Table et les objets blob. 
- **Transformer les données en informations fiables.** Le service Data Factory prend en charge le traitement Hive, Pig et C#, ainsi que des fonctionnalités de traitement clés telles que la gestion automatique de cluster Hadoop (HDInsight), les nouvelles tentatives après des échecs temporaires, les stratégies de délai d'expiration configurables et les alertes.  
- **Surveiller les pipelines de données dans un seul emplacement.** Le service Data Factory offre une vision fiable et complète de vos services de stockage, de traitement et de déplacement des données. Il vous permet d'évaluer rapidement l'intégrité du pipeline de données de bout en bout, d'identifier les problèmes et de prendre des mesures correctives, si nécessaire. En outre, vous pouvez suivre visuellement le lignage des données et les relations entre vos données sur n'importe quelle source et consulter un historique complet de l'exécution des travaux, de l'intégrité du système et des dépendances à partir d'un tableau de bord de surveillance unique.
- **Obtenir des aperçus étoffés de données transformées.** Le service Data Factory vous permet de créer des pipelines de données qui produisent des données fiables, exploitables par des outils d'aide à la décision et d'analyse et d'autres applications.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modèle d'application
Le diagramme suivant illustre le modèle d'application pris en charge par le service Azure Data Factory.

![Modèle d'application][image-data-factory-application-model]

Il existe trois phases de production des informations dans le service Azure Data Factory :

- **Connecter et collecter**. Dans cette étape, les données de différentes sources sont importées dans des hubs de données. Un pipeline dans une fabrique de données peut avoir une ou plusieurs activités. Vous utilisez une ou plusieurs activités de **copie** dans un pipeline de données pour collecter des données depuis des magasins de données sources vers un magasin de données de destination avec un hub de données en vue d'un traitement ultérieur. Un cluster HDInsight (calcul) et son dispositif de stockage d'objets blob Azure associé (stockage) forment un hub de données (hub de données HDInsight). Pour utiliser un hub de données HDInsight, vous copiez toutes les données sources dans un magasin d'objets blob Azure associé avec HDInsight afin que le cluster HDInsight puisse traiter les données. Un pipeline s'exécute sur une ressource de calcul dans un hub de données tel qu'un cluster HDInsight.      
- **Transformer et enrichir**. Dans cette étape, les données collectées sont traitées. Par exemple, une **activité HDInsight** dans un pipeline peut traiter les données stockées dans le magasin d'objets blob Azure associé en effectuant des transformations à l'aide de scripts Hive/Pig pour produire des informations fiables. Les pipelines peuvent être chaînés (comme illustré dans le diagramme) de manière à ce que les jeux de données de sortie d'un pipeline puissent être des jeux de données d'entrée d'un autre pipeline dans le même hub de données ou dans un autre hub de données.  
- **Publier**. Dans cette étape, les données sont publiées pour être consommées par les outils décisionnels, outils d'analyse et autres applications. Par exemple, une activité de copie dans le pipeline peut copier des données issues de la phase de transformation et d'enrichissement dans un magasin de données (par exemple, une base de données SQL Server locale) à partir duquel pourront être créées des solutions d'aide à la décision.   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##Étapes suivantes
1. [Prise en main de Data Factory][datafactory-getstarted]. Cet article fournit un didacticiel de bout en bout qui montre comment créer un exemple de fabrique de données Azure qui copie des données à partir d'objets blob Azure vers une base de données SQL Azure.
2. [Didacticiel : déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial]. Cet article comporte une **procédure pas à pas de bout en bout** qui montre comment implémenter un **scénario réel** à l'aide d'Azure Data Factory pour transformer des données de fichiers journaux en informations pertinentes.

## Voir aussi
- [Données Factory - Terminologie][adf-terminology]. Cet article vous présente la terminologie utilisée dans la création de fabriques de données à l'aide du service Azure Data Factory. 
- [Data Factory - Forum aux Questions][adf-faq]. Cet article fournit une liste de questions fréquemment posées et les réponses correspondantes.
- [Scénarios courants d'utilisation d'Azure Data Factory][adf-common-scenarios]. Cet article décrit quelques scénarios courants d'utilisation du service Azure Data Factory. 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png



 

<!---HONumber=July15_HO4-->