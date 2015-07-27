<properties 
	pageTitle="Azure Data Factory - Terminologie" 
	description="Cet article vous présente la terminologie utilisée dans la création de fabriques de données à l’aide du service Azure Data Factory" 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

#Azure Data Factory - Terminologie

## Fabrique de données
Une **fabrique de données Azure** possède un ou plusieurs pipelines qui traitent les données dans les magasins de données liés (Azure Storage, Azure SQL Database, base de données SQL Server locale, etc.) à l’aide de services de calcul liés, tels qu’Azure HDInsight. Une fabrique de données Azure elle-même ne contient pas de données. Les données sont plutôt stockées dans les magasins de données mentionnés ci-dessus.

## Service lié
Un **service lié** est un service qui est lié à une fabrique de données Azure. Un service lié peut être l'un des dispositifs suivants :

- Un **service de stockage** de données tel qu’Azure Storage, Base de données SQL Azure ou une base de données SQL Server locale. Un magasin de données est un conteneur de jeux de données d'entrée/sortie.    
- Un service de **calcul** tel qu’Azure HDInsight, Azure Machine Learning et Azure Batch. Un service de calcul traite les données d'entrée et génère les données de sortie.  

## Jeu de données
Un **jeu de données** est une vue nommée des données. Les données décrites peuvent aller de données à octets simples, semi-structurées, comme des fichiers CSV, à des tables relationnelles ou même des modèles. Une **table** Data Factory est un jeu de données qui comporte un schéma et est rectangulaire. Après avoir créé un service lié dans un magasin de données qui fait référence à un magasin de données, vous définissez des jeux de données qui représentent les données d'entrée/sortie qui sont stockées dans le magasin de données.


##Pipeline
Un **pipeline** d’une fabrique de données Azure traite les données des services de stockage liés à l’aide des services de calcul liés. Le pipeline contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Par exemple, une **activité de copie** copie les données d’un emplacement de stockage source vers un emplacement de stockage cible, tandis qu’une **activité HDInsight** utilise un cluster Azure HDInsight pour traiter des données à l’aide de requêtes Hive ou de scripts Pig. Une fabrique de données peut avoir un ou plusieurs pipelines.

Les étapes classiques pour créer une instance Azure Data Factory sont les suivantes :

1. Création d'une **fabrique de données**.
2. Création d'un **service lié** pour chaque magasin de données ou service de calcul.
3. Créez des **jeux de données** d’entrée et de sortie.
4. Création d'un **pipeline**. 

##Activité
Étape de traitement de données dans un pipeline qui prend un ou plusieurs jeux de données d'entrée et produit un ou plusieurs jeux de données de sortie. Les activités s’exécutent dans un environnement d’exécution (par exemple : un cluster Azure HDInsight) et lisent ou écrivent des données dans un magasin de données associé/lié à la fabrique de données Azure.

Le service Azure Data Factory prend en charge les activités suivantes dans un pipeline :

- L’**activité de copie** copie les données d’un magasin de données vers un autre magasin de données. Pour plus d’informations sur les magasins de données pris en charge par l’activité de copie, consultez [Copie de données avec Azure Data Factory][copy-data-with-adf]. 
- L’**activité HDInsight** traite les données en exécutant des scripts Hive/Pig ou des programmes MapReduce sur un cluster HDInsight. Pour plus d’informations, consultez les pages [Utilisation de Pig et Hive avec Data Factory][use-pig-hive] et [Appel de programmes MapReduce à partir de Data Factory][run-map-reduce]. 
- L’**activité d’évaluation par lots Azure Machine Learning** appelle l’API d’évaluation par lots Azure Machine Learning. Pour plus d’informations, consultez la page [Créer des pipelines prédictifs à l’aide d’Azure Data Factory et Azure Machine Learning][azure-ml-adf]. 
- L’**activité de procédure stockée** appelle une procédure stockée dans une base de données SQL Azure. Pour plus d’informations, consultez l’[activité de procédure stockée][msdn-stored-procedure-activity] dans la bibliothèque MSDN.   

##Tranche
Une table dans une fabrique de données Azure se compose de tranches. La largeur d'une tranche est déterminée par la planification en heure/jour. Quand la planification s'effectue « en heure », une tranche est exécutée toutes les heures avec l'heure de début et de fin d'un pipeline. Par exemple, si le pipeline commence le 03/03/2015 à 06:00:00 (6h) et se termine le 03/03/2015 à 09:00:00 (9h le même jour), trois tranches de données sont créées, à raison d’une tranche par heure : 6h-7h, 7h-8h et 8h-9h.

Les tranches permettent d’utiliser un sous-jeu de données globales pendant une période spécifique (par exemple : la tranche exécutée pour la durée (heure) : de 13 à 14H00.

## Exécution d'activité pour un tranche
L’**exécution**, ou exécution d’activité, est une unité de traitement d’une tranche. Une tranche peut être exécutée une ou plusieurs fois en cas de nouvelles tentatives ou de défaillance. Elle est identifiée par son heure de début.

##Passerelle de gestion de données
La **passerelle de gestion des données** Microsoft est un logiciel qui connecte les sources de données locales aux services cloud pour la consommation. Vous devez disposer d'au moins une passerelle installée dans votre environnement d'entreprise et l'inscrire auprès du portail Azure Data Factory avant d'ajouter des sources de données locales en tant que services liés.
 
##Concentrateur de données
Un **concentrateur de données** est un conteneur logique pour les services de calcul et de stockage des données. Par exemple, un cluster Hadoop avec HDFS pour le stockage et Hive/Pig pour le calcul (traitement) est un concentrateur de données. De même, un entrepôt de données d'entreprise (EDW, Enterprise Data Warehouse) peut être modélisé comme un concentrateur de données (base de données pour le stockage, procédures stockées et/ou outil ETL comme services de calcul). Les pipelines utilisent des magasins de données et les exécutent sur des ressources de calcul dans un concentrateur de données. Seul le hub HDInsight est pris en charge pour l'instant.

Le concentrateur de données permet à une fabrique de données d'être divisée en regroupements logiques ou de domaine, tels que le « concentrateur Azure Amérique de l'Ouest » qui gère tous les services liés (magasins de données et calcul) et les pipelines spécifiques du centre de données de l'Ouest des États-Unis ou un « concentrateur EDW Ventes » qui gère tous les pipelines et services liés, associés au remplissage et au traitement des données pour l'EDW Ventes.

Un pipeline s'exécute sur un concentrateur unique ce qui constitue une caractéristique importante des concentrateurs. Ainsi, quand vous définissez un pipeline, tous les services liés référencés par les tables ou les activités dans ce pipeline doivent avoir le même nom de concentrateur que le pipeline lui-même. Si la propriété HubName n'est pas spécifiée pour un service lié, celui-ci est placé dans le hub « par défaut ».

## Voir aussi

1. [Présentation d’Azure Data Factory][adf-intro] Cet article fournit une vue d'ensemble du service Azure Data Factory, de sa valeur ajoutée et des scénarios pris en charge.
2. [Prise en main d'Azure Data Factory][datafactory-getstarted]. Cet article fournit un didacticiel de bout en bout qui montre comment créer un exemple de fabrique de données Azure qui copie des données à partir d'objets blob Azure vers une base de données SQL Azure.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
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



 

<!---HONumber=July15_HO3-->