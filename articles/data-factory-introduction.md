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
	ms.date="03/09/2015" 
	ms.author="spelluru"/>

# Présentation du service Azure Data Factory
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->

Le service **Azure Data Factory** est un service entièrement géré pour composer des services de stockage de données, de traitement de données et de déplacement de données dans des pipelines de production rationalisés, évolutifs et fiables. Le service Data Factory vous permet d'effectuer les opérations suivantes : 

- Générer des flux de travail (pipelines) pilotés par les données qui joignent, agrègent et transforment des données provenant de magasins de données Internet, locaux et basés sur le cloud 
- Transformer en informations fiables des données structurées, semi-structurées et non structurées issues de diverses sources de données
- Générer des données qui peuvent être facilement utilisées au moyen de l'aide à la décision, d'outils d'analyse et d'autres applications 
- Configurer un traitement de données complexe dans un simple script JSON
- Surveiller et gérer des pipelines grâce à une riche expérience visuelle proposée via le portail Azure en version préliminaire  


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## Vue d'ensemble
Les projets d'intégration de données se sont toujours articulés autour de la création de processus ETL (Extraction, Transformation et Chargement) qui extraient des données provenant de différentes sources d'une organisation, transforment ces données pour qu'elles correspondent au schéma cible d'un entrepôt de données d'entreprise (EDW, Enterprise Data Warehouse) et chargent ces données dans un EDW. L'EDW est ensuite accessible comme seule source approuvée pour les solutions d'analyse décisionnelle. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

Le paysage actuel de données pour les entreprises croît de manière exponentielle en termes de volume, de diversité et de complexité. Il est plus varié que jamais avec des données locales et du cloud, présentant des formes et des vitesses différentes.  Le traitement des données doit se produire à divers emplacements géographiques et inclut une combinaison de logiciels open source, de solutions commerciales et de services de traitement personnalisé, coûteux et difficiles à intégrer et à gérer.  L'agilité nécessaire pour s'adapter au contexte actuel des données volumineuses offre l'opportunité de fusionner l'EDW classique avec les fonctionnalités nécessaires d'un système de production modernes de l'information.  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

Le service **Azure Data Factory** est la plateforme de composition entre les EDW classiques et le contexte des données évolutives qui permet aux entreprises d'exploiter toutes les données disponibles pour la prise de décision pilotée par celles-ci. Il permet aux entreprises d'exploiter cette diversité en fournissant une plateforme pour composer des services de traitement, de stockage et de déplacement des données dans des pipelines de production d'informations, et gérer les ressources de données fiables.

Le service Azure Data Factory vous permet d'effectuer les opérations suivantes :

- **Utiliser facilement des systèmes de stockage et de traitement de données divers.** 
Le service Data Factory vous permet de créer des pipelines de production d'informations qui déplacent et traitent les données locales (telles que SQL Server) et les sources de données du cloud comme Azure SQL Database, Azure Table et les objets blob. 
- **Transformer des données en informations fiables.** 
Le service Data Factory prend en charge le traitement Hive, Pig et C#, ainsi que des fonctionnalités de traitement clés telles que la gestion automatique de cluster Hadoop (HDInsight), les nouvelles tentatives après des échecs temporaires, les stratégies de délai d'expiration configurables et les alertes.  
- **Surveiller les pipelines de données dans un seul emplacement.** 
Le service Data Factory offre une vision fiable et complète de vos services de stockage, de traitement et de déplacement des données.  Il vous permet d'évaluer rapidement l'intégrité du pipeline de données de bout en bout, d'identifier les problèmes et de prendre des mesures correctives, si nécessaire. En outre, vous pouvez suivre visuellement le lignage des données et les relations entre vos données sur n'importe quelle source et consulter un historique complet de l'exécution des travaux, de l'intégrité du système et des dépendances à partir d'un tableau de bord de surveillance unique.
- **Obtenir des aperçus étoffés de données transformées**
Le service Data Factory vous permet de créer des pipelines de données qui produisent des données fiables, exploitables par des outils d'aide à la décision et d'analyse et d'autres applications.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Modèle d'application
Le diagramme suivant illustre le modèle d'application pris en charge par le service Azure Data Factory.

![Application Model][image-data-factory-application-model]

Il existe trois phases de production des informations dans le service Azure Data Factory :

- **Se connecter et collecter**. Dans cette étape, les données de différentes sources sont importées dans des hubs de données. Un pipeline dans une fabrique de données peut avoir une ou plusieurs activités. Vous utilisez une ou plusieurs activités de **copie** dans un pipeline de données pour collecter des données depuis des magasins de données sources vers un magasin de données de destination avec un hub de données en vue d'un traitement ultérieur. Un cluster HDInsight (calcul) et son dispositif de stockage d'objets blob Azure associé (stockage) forment un hub de données (hub de données HDInsight). Pour utiliser un hub de données HDInsight, vous copiez toutes les données sources dans un magasin d'objets blob Azure associé avec HDInsight afin que le cluster HDInsight puisse traiter les données. Un pipeline s'exécute sur une ressource de calcul dans un hub de données tel qu'un cluster HDInsight.      
- **Transformer et enrichir**. Dans cette étape, les données collectées sont traitées. Par exemple, une **activité HDInsight** dans un pipeline peut traiter les données stockées dans le magasin d'objets blob Azure associé en effectuant des transformations à l'aide de scripts Hive/Pig pour produire des informations fiables. Les pipelines peuvent être chaînés (comme illustré dans le diagramme) de manière à ce que les jeux de données de sortie d'un pipeline puissent être des jeux de données d'entrée d'un autre pipeline dans le même hub de données ou dans un autre hub de données.  
- **Publier**. Dans cette étape, les données sont publiées pour être consommées par les outils décisionnels, outils d'analyse et autres applications. Par exemple, une activité de copie dans le pipeline peut copier des données issues de la phase de transformation et d'enrichissement dans un magasin de données (par exemple, une base de données SQL Server locale) à partir duquel pourront être créées des solutions d'aide à la décision.   

<!--

Data Factory permet aux développeurs de créer des pipelines qui sont des groupes de déplacement des données et/ou des activités de traitement qui acceptent un ou plusieurs jeux de données d'entrée et produisent un ou plusieurs jeux de données de sortie. Les pipelines peuvent être exécutés une fois ou sur un ensemble de planifications (horaire, quotidienne, hebdomadaire, etc...). Un jeu de données est une vue nommée de données. Les données décrites peuvent aller des données simples octets, semi-structurées (par exemple les fichiers CSV) aux données des tables ou modèles.

Les pipelines sont constitués d'activités de déplacement de données(par exemple : activité de copie) qui servent souvent à importer/exporter des données à partir de toutes les sources de données (bases de données, fichiers, services SaaS, etc.) utilisées par l'organisation vers un hub de données.
 
Une fois les données dans un **hub**, les **pipelines** hébergés par les services de calcul du hub sont utilisés pour transformer les données dans un format adapté à la consommation (par les outils décisionnels, les applications, les clients, etc.).  
  
Enfin, les **pipelines** peuvent être chaînés (comme illustré dans le diagramme) pour que les **jeux de données** de sortie des uns soient les entrées des autres.  Ainsi, les flux de données complexes sont intégrés dans des **pipelines** qui s'exécutent dans un hub de données ou s'étendent sur plusieurs hubs.  En utilisant les **pipelines** de cette façon, les organisations disposent de fondations pour composer les meilleurs services locaux, de cloud et SaaS (Software-as-a-Service), le tout rassemblé dans une seule fabrique de données facile à gérer.
--> 

## Expérience de création

Vous pouvez créer des fabriques de données à l'aide d'un des dispositifs suivants :

- **Portail Azure en version préliminaire**. Les panneaux du service Data Factory dans le portail Azure en version préliminaire fournissent une interface utilisateur complète qui vous permet de créer des fabriques de données et des services liés. L'**éditeur du service Data Factory**, qui fait également partie du portail, vous permet de créer facilement des services liés, des tables, des jeux de données et des pipelines en spécifiant des définitions JSON pour ces artefacts. Consultez [Éditeur du service Data Factory][data-factory-editor] pour obtenir un exemple d'utilisation du portail et de l'éditeur pour créer et déployer une fabrique de données, et [Prise en main d'Azure Data Factory][datafactory-getstarted] pour obtenir une vue d'ensemble de l'éditeur.   
- **Azure PowerShell**. Si vous êtes familiarisé avec PowerShell et que vous le préférez à l'interface utilisateur du portail, vous pouvez utiliser les applets de commande Azure Data Factory fournies dans le cadre d'Azure PowerShell pour créer et déployer des fabriques de données. Consultez [Création et surveillance d'une fabrique de données Azure à l'aide d'Azure PowerShell][create-data-factory-using-powershell] pour obtenir un exemple simple, et [Didacticiel : Déplacement et traitement des fichiers journaux à l'aide d'Azure Data Factory][adf-tutorial] pour obtenir un exemple avancé d'utilisation d'applets de commande PowerShell pour créer et déployer une fabrique de données. pour obtenir une documentation complète sur les applets de commande Data Factory, consultez le contenu de [référence des applets de commande Data Factory][adf-powershell-reference] sur MSDN Library.  
- **Bibliothèque de classes .NET**. Vous pouvez créer par programmation des fabriques de données à l'aide du Kit de développement logiciel (SDK) Data Factory .NET. Pour découvrir la procédure permettant de créer une fabrique de données à l'aide du Kit de développement logiciel (SDK) .NET, consultez [Créer, surveiller et gérer des fabriques de données à l'aide du Kit de développement logiciel (SDK) .NET][create-factory-using-dotnet-sdk]. Pour obtenir une documentation complète sur le Kit de développement logiciel (SDK) Data Factory .NET, consultez les [informations de référence sur la bibliothèque de classes Data Factory][msdn-class-library-reference].  
- **API REST**. Vous pouvez également utiliser l'API REST exposée par le service Azure Data Factory pour créer et déployer des fabriques de données. Pour obtenir une documentation complète sur l'API REST Data Factory, consultez les [informations de référence sur l'API REST Data Factory][msdn-rest-api-reference]. 


##Terminologie
Cette section présente la terminologie liée à Azure Data Factory.

### Fabrique de données
Une **fabrique de données Azure** possède un ou plusieurs pipelines qui traitent les données dans les magasins de données liés (Azure Storage, Azure SQL Database, base de données SQL Server locale, etc.) à l'aide de services de calcul liés, tels qu'Azure HDInsight. Une fabrique de données Azure elle-même ne contient pas de données. Les données sont plutôt stockées dans les magasins de données mentionnés ci-dessus.  

### Service lié
Un **service lié** est un service qui est lié à une fabrique de données Azure. Un service lié peut être l'un des dispositifs suivants :

- Un service de **stockage de données** tel qu'Azure Storage, Azure SQL Database ou une base de données SQL Server locale. Un magasin de données est un conteneur de jeux de données d'entrée/sortie.    
- Un service de **calcul** tel qu'Azure HDInsight et Azure Machine Learning. Un service de calcul traite les données d'entrée et génère les données de sortie.  

### Jeu de données
Un **jeu de données** est une vue nommée des données. Les données décrites peuvent aller de données à octets simples, semi-structurées, comme des fichiers CSV, à des tables relationnelles ou même des modèles. Une **table** Data Factory est un jeu de données qui comporte un schéma et est rectangulaire. Après avoir créé un service lié dans un magasin de données qui fait référence à un magasin de données, vous définissez des jeux de données qui représentent les données d'entrée/sortie qui sont stockées dans le magasin de données. 


###Pipeline
Un **pipeline** dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Le pipeline contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Par exemple, une **activité de copie** se charge de copier les données d'un emplacement de stockage source vers un emplacement de stockage cible, tandis qu'une **activité HDInsight** utilise un cluster Azure HDInsight pour traiter des données à l'aide de requêtes Hive ou de scripts Pig. Une fabrique de données peut avoir un ou plusieurs pipelines. 

Les étapes classiques pour créer une instance Azure Data Factory sont les suivantes :

1. Créer une **fabrique de données**.
2. Créer un **service lié** pour chaque magasin de données ou service de calcul.
3. Créer des **jeux de données** d'entrée et de sortie.
4. Créer un **pipeline**. 

###Activité
Étape de traitement de données dans un pipeline qui prend un ou plusieurs jeux de données d'entrée et produit un ou plusieurs jeux de données de sortie.  Les activités ont lieu dans un environnement d'exécution (par exemple : cluster Azure HDInsight) et lisent ou écrivent des données dans un magasin de données associé/lié à la fabrique de données Azure. 

Le service Azure Data Factory prend en charge les activités suivantes dans un pipeline : 

- L'**activité de copie** copie les données d'un magasin de données vers un autre magasin de données. Pour plus d'informations sur les magasins de données pris en charge par l'activité de copie, consultez [Copie de données avec Azure Data Factory][copy-data-with-adf] . 
- L'**activité HDInsight** traite les données en exécutant des scripts Hive/Pig ou des programmes MapReduce sur un cluster HDInsight. Pour plus d'informations, consultez [Utilisation de Pig et Hive avec Data Factory][use-pig-hive] et [Appeler des programmes MapReduce à partir de Data Factory][run-map-reduce]. 
- L'**activité d'évaluation par lots Azure Machine Learning** appelle l'API d'évaluation par lots Azure Machine Learning. Pour plus d'informations, consultez [Créer des pipelines prédictifs à l'aide d'Azure Data Factory et Azure Machine Learning][azure-ml-adf]. 
- L'**activité de procédure stockée** appelle une procédure stockée dans une base de données SQL Azure. Pour plus d'informations, consultez l'[activité de procédure stockée][msdn-stored-procedure-activity] sur MSDN Library.   

###Tranche
Une table dans une fabrique de données Azure se compose de tranches. La largeur d'une tranche est déterminée par la planification en heure/jour. Quand la planification s'effectue " en heure ", une tranche est exécutée toutes les heures avec l'heure de début et de fin d'un pipeline. Par exemple, si le pipeline commence le 03/03/2015 à 06:00:00 (6h) et se termine le 03/03/2015 à 09:00:00 (9h le même jour), trois tranches de données sont créées, à raison d'une tranche par heure : 6h-7h, 7h-8h et 8h-9h.    

Les tranches permettent d'utiliser un sous-jeu de données globales pendant une période spécifique (par exemple : la tranche exécutée pour la durée (heure) : De 13 à 14H00). 

### Exécution d'activité pour un tranche
L'**exécution**, ou exécution d'activité, est une unité de traitement d'une tranche. Une tranche peut être exécutée une ou plusieurs fois en cas de nouvelles tentatives ou de défaillance. Elle est identifiée par son heure de début.

###Passerelle de gestion de données
La **passerelle de gestion de données** Microsoft est un logiciel qui connecte les sources de données locales aux services cloud pour la consommation. Vous devez disposer d'au moins une passerelle installée dans votre environnement d'entreprise et l'inscrire auprès du portail Azure Data Factory avant d'ajouter des sources de données locales en tant que services liés.
 
###Concentrateur de données
Un **hub de données** est un conteneur logique pour les services de stockage et de calcul des données. Par exemple, un cluster Hadoop avec HDFS pour le stockage et Hive/Pig pour le calcul (traitement) est un concentrateur de données. De même, un entrepôt de données d'entreprise (EDW, Enterprise Data Warehouse) peut être modélisé comme un concentrateur de données (base de données pour le stockage, procédures stockées et/ou outil ETL comme services de calcul).  Les pipelines utilisent des magasins de données et les exécutent sur des ressources de calcul dans un concentrateur de données. Seul le hub HDInsight est pris en charge pour l'instant.

Le concentrateur de données permet à une fabrique de données d'être divisée en regroupements logiques ou de domaine, tels que le " concentrateur Azure Amérique de l'Ouest " qui gère tous les services liés (magasins de données et calcul) et les pipelines spécifiques du centre de données de l'Ouest des États-Unis ou un " concentrateur EDW Ventes " qui gère tous les pipelines et services liés, associés au remplissage et au traitement des données pour l'EDW Ventes.

Un pipeline s'exécute sur un concentrateur unique ce qui constitue une caractéristique importante des concentrateurs. Ainsi, quand vous définissez un pipeline, tous les services liés référencés par les tables ou les activités dans ce pipeline doivent avoir le même nom de concentrateur que le pipeline lui-même. Si la propriété HubName n'est pas spécifiée pour un service lié, celui-ci est placé dans le hub " par défaut ".

##Étapes suivantes

1. [Prise en main de Data Factory][datafactory-getstarted]. Cet article fournit un didacticiel de bout en bout qui montre comment créer un exemple de fabrique de données Azure qui copie des données à partir d'objets blob Azure vers une base de données SQL Azure.
2. [Didacticiel : déplacement et traitement des fichiers journaux à l'aide d'Azure Data Factory][adf-tutorial]. Cet article comporte une **procédure pas à pas de bout en bout** qui montre comment implémenter un **scénario réel** à l'aide d'Azure Data Factory pour transformer des données de fichiers journaux en informations pertinentes.
3. [Data Factory - Forum aux Questions][adf-faq]. Cet article fournit une liste de questions fréquemment posées et les réponses correspondantes. 
3. [Scénarios courants d'utilisation d'Azure Data Factory][adf-common-scenarios]. Cet article décrit quelques scénarios courants d'utilisation du service Azure Data Factory.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

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

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->