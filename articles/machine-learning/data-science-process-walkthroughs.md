<properties 
	pageTitle="Procédures pas à pas du processus TDSP (Team Data Science Process) | Microsoft Azure" 
	description="Les procédures pas à pas montrent comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/14/2016" 
	ms.author="bradsev" />


# Procédures pas à pas du processus TDSP (Team Data Science Process)

Les procédures pas à pas détaillées ici illustrent les étapes du processus TDSP (Team Data Science Process) pour **des scénarios spécifiques** et expliquent comment combiner les outils et services dans le cloud et sur dans un flux de travail ou un pipeline pour créer une application intelligente.

## Utilisation de SQL Server
La procédure pas à pas [Processus TDSP (Team Data Science Process) en action : utilisation de SQL Server](machine-learning-data-science-process-sql-walkthrough.md) vous montre comment créer et déployer un modèle Machine Learning à l’aide de SQL Server et d’un jeu de données disponible publiquement, le jeu de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/)


## Utilisation de clusters HDInsight Hadoop
Dans la procédure pas à pas [Processus TDSP (Team Data Science Process) en action : utilisation de clusters Hadoop HDInsight](machine-learning-data-science-process-hive-walkthrough.md), nous utilisons [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pour effectuer des opérations sur un jeu de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/), disponible publiquement, telles que le stockage, l’exploration et la conception de fonctionnalités.


## Processus TDSP (Team Data Science Process) en action : utilisation de clusters Azure HDInsight Hadoop sur un jeu de données de 1 To
Dans la procédure pas à pas [Processus TDSP (Team Data Science Process) en action : utilisation de clusters Azure HDInsight Hadoop sur un jeu de données de 1 To](machine-learning-data-science-process-hive-criteo-walkthrough.md), nous présentons un scénario de bout en bout qui utilise un [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) pour effectuer des opérations sur un des jeux de données [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/), disponibles publiquement, telles que le stockage, l’exploration, la conception de fonctionnalités et la réduction d’échantillon.

## Science des données avec Spark sur Azure HDInsight
Dans la procédure pas à pas [Science des données avec Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md), nous utilisons le processus TDSP (Team Data Science Process) dans un scénario de bout en bout au moyen d’un [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) pour effectuer des opérations sur le jeu de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) disponible publiquement, telles que le stockage, l’exploration, la conception de fonctionnalités.


## Science des données évolutive dans Azure Data Lake
La section [Science des données évolutive dans Azure Data Lake : une procédure de bout en bout](machine-learning-data-science-process-data-lake-walkthrough.md) montre comment utiliser Azure Data Lake pour effectuer des tâches d’exploration de données et de classification binaire sur un échantillon de jeu de données NYC taxi trip and fare afin de prédire si le pourboire est compris dans le prix du billet.

## Procédure de bout en bout de science des données à l’aide de services SQL Server R
La [procédure de bout en bout de science des données à l’aide de services SQL Server R](https://msdn.microsoft.com/library/mt612857.aspx) utilise un jeu de données connu publiquement, le jeu de données New York City taxi. Vous utilisez une combinaison de code R, de données SQL Server et de fonctions SQL personnalisées pour générer un modèle de classification qui indique la probabilité que le chauffeur recevra un pourboire lors d’un trajet en taxi particulier. Vous déployez également votre modèle R vers SQL Server et utiliser les données du serveur pour générer des scores basés sur le modèle.

## Analyses avancées des bases de données pour les développeurs SQL

La procédure pas à pas [Analyses avancées des bases de données pour les développeurs SQL](https://msdn.microsoft.com/library/mt683480.aspx) vise à fournir aux programmeurs SQL une expérience pratique pour créer une solution d’analyse avancée à l’aide de services SQL Server R. Vous allez apprendre à intégrer R dans une application ou une solution BI en encapsulant le code R dans des procédures stockées.

<!---HONumber=AcomDC_0622_2016-->