<properties 
	pageTitle="Fabrique de données Azure - terminologie" 
	description="Cet article vous présente la terminologie utilisée dans la création de fabriques de données à l'aide du service de fabrique de données Azure" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

#Fabrique de données Azure - terminologie

## Fabrique de données
Un **factory de données Azure** a un ou plusieurs pipelines qui traitent les données dans les magasins de données liées (base de données SQL Azure, le stockage Azure local SQL Server etc.) à l'aide des services de calcul liés, tels que Azure HDInsight. Une fabrique de données Azure elle-même ne contient pas de données. Les données sont plutôt stockées dans les magasins de données mentionnés ci-dessus.

## Service lié
Un **lié service** est un service qui est lié à une fabrique de données Azure. Un service lié peut être l'un des dispositifs suivants :

- Un **stockage de données** service telles que le stockage Azure, base de données SQL Azure ou base de données SQL Server sur site. Un magasin de données est un conteneur de jeux de données d'entrée/sortie.    
- Un **compute** service tels que Azure HDInsight et Azure Machine Learning lot Azure. Un service de calcul traite les données d'entrée et génère les données de sortie.  

## Jeu de données
Un **jeu de données** est une vue nommée des données. Les données décrites peuvent aller de données à octets simples, semi-structurées, comme des fichiers CSV, à des tables relationnelles ou même des modèles. Une fabrique de données **table** est un ensemble de données qui possède un schéma et est rectangulaire. Après avoir créé un service lié dans un magasin de données qui fait référence à un magasin de données, vous définissez des jeux de données qui représentent les données d'entrée/sortie qui sont stockées dans le magasin de données.


##Pipeline
Un **pipeline** dans Azure fabrique de données traite les données dans les services de stockage lié à l'aide des services de calcul lié. Le pipeline contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Par exemple, un **activité copie** copie les données à partir d'un stockage source vers un stockage de destination et **HDInsight activité** utiliser un cluster Azure HDInsight pour traiter des données à l'aide de requêtes Hive ou scripts Pig. Une fabrique de données peut avoir un ou plusieurs pipelines.

Les étapes classiques pour créer une instance Azure Data Factory sont les suivantes :

1. Créer un **fabrique données**.
2. Créer un **lié service** pour chaque magasin ou service de calcul.
3. Créer des entrées et sorties **datasets**.
4. Créer un **pipeline**. 

##Activité
Étape de traitement de données dans un pipeline qui prend un ou plusieurs jeux de données d'entrée et produit un ou plusieurs jeux de données de sortie. Activités exécutées dans un environnement d'exécution (par exemple : cluster Azure HDInsight) et en lecture/écriture des données dans un magasin de données associé/lié à la fabrique de données Azure.

Le service Azure Data Factory prend en charge les activités suivantes dans un pipeline :

- **Activité copie** copie les données d'un magasin de données vers une autre banque de données. Voir [copier des données avec Azure Data Factory][copy-data-with-adf] pour plus d'informations sur les données qui stocke l'activité de copie prend en charge. 
- **HDInsight activité** traite les données en exécutant les scripts Hive/Pig ou programmes MapReduce sur un cluster HDInsight. Voir [Utilisez Pig et Hive avec Data Factory][use-pig-hive] et [appeler des programmes MapReduce à partir de données fabrique][run-map-reduce] pour plus d'informations. 
- **Azure Machine Learning lot notation activité** appelle le lot d'apprentissage automatique de Azure score API. Voir [Pipelines prédictive créer à l'aide de la fabrique de données Azure et Azure Machine Learning][azure-ml-adf] pour plus d'informations. 
- **Activité de procédure stockée** appelle une procédure stockée dans une base de données SQL Azure. Consultez le [activité de procédure stockée][msdn-stored-procedure-activity] sur MSDN Library pour plus d'informations.   

##Tranche
Une table dans une fabrique de données Azure se compose de tranches. La largeur d'une tranche est déterminée par la planification en heure/jour. Quand la planification s'effectue « en heure », une tranche est exécutée toutes les heures avec l'heure de début et de fin d'un pipeline. Par exemple, si le pipeline de date-heure de début est 03/03/2015 06:00:00 (6 heures) et date-heure de fin donnée 03-03/2015 09:00:00 (9 h le même jour), trois tranches de production, une section pour chaque intervalle de 1 heure: 6-7 AM, 7 et 8 AM et AM 8-9.

Secteurs offrent la possibilité de travailler avec un sous-ensemble de données globales pour une fenêtre de temps spécifique (par exemple : la tranche qui est générée pour la durée (en heures): À 13:00 à 14 h).

## Exécution d'activité pour un tranche
Le **exécuter** ou une activité à exécuter est une unité de traitement pour une tranche de données. Une tranche peut être exécutée une ou plusieurs fois en cas de nouvelles tentatives ou de défaillance. Elle est identifiée par son heure de début.

##Passerelle de gestion de données
Microsoft **passerelle de gestion des données** est un logiciel qui se connecte à des sources de données sur site pour la consommation de services de cloud computing. Vous devez disposer d'au moins une passerelle installée dans votre environnement d'entreprise et l'inscrire auprès du portail Azure Data Factory avant d'ajouter des sources de données locales en tant que services liés.
 
##Concentrateur de données
Un **concentrateur de données** est un conteneur logique pour les services de stockage et de calcul des données. Par exemple, un cluster Hadoop avec HDFS pour le stockage et Hive/Pig pour le calcul (traitement) est un concentrateur de données. De même, un entrepôt de données d'entreprise (EDW, Enterprise Data Warehouse) peut être modélisé comme un concentrateur de données (base de données pour le stockage, procédures stockées et/ou outil ETL comme services de calcul). Les pipelines utilisent des magasins de données et les exécutent sur des ressources de calcul dans un concentrateur de données. Seul le hub HDInsight est pris en charge pour l'instant.

Le concentrateur de données permet à une fabrique de données d'être divisée en regroupements logiques ou de domaine, tels que le « concentrateur Azure Amérique de l'Ouest » qui gère tous les services liés (magasins de données et calcul) et les pipelines spécifiques du centre de données de l'Ouest des États-Unis ou un « concentrateur EDW Ventes » qui gère tous les pipelines et services liés, associés au remplissage et au traitement des données pour l'EDW Ventes.

Un pipeline s'exécute sur un concentrateur unique ce qui constitue une caractéristique importante des concentrateurs. Ainsi, quand vous définissez un pipeline, tous les services liés référencés par les tables ou les activités dans ce pipeline doivent avoir le même nom de concentrateur que le pipeline lui-même. Si la propriété HubName n'est pas spécifiée pour un service lié, celui-ci est placé dans le hub « par défaut ».

## Voir aussi

1. [Introduction à la fabrique de données Azure][adf-intro]. Cet article fournit une vue d'ensemble du service Azure Data Factory, de sa valeur ajoutée et des scénarios pris en charge.
2. [Prise en main Data Factory][datafactory-getstarted]. Cet article fournit un didacticiel de bout en bout qui montre comment créer un exemple de fabrique de données Azure qui copie des données à partir d'objets blob Azure vers une base de données SQL Azure.


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

<!---HONumber=GIT-SubDir-->