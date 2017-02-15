---
title: "Explorer les données d’une machine virtuelle SQL Server sur Azure | Microsoft Docs"
description: "Comment explorer les données stockées dans une machine virtuelle SQL Server sur Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1654c100c1c57b04a61cf2b9d2cf37d58cec1870


---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Explorer les données d’une machine virtuelle SQL Server sur Azure
Ce document explique comment explorer les données stockées dans une machine virtuelle SQL Server sur Azure. Cela est possible avec le retraitement des données à l'aide de SQL ou en utilisant un langage de programmation comme Python.

Le **menu** suivant pointe vers des rubriques qui expliquent comment utiliser des outils pour explorer des données dans différents environnements de stockage. Cette tâche est une étape du processus Cortana Analytics (CAP).

[!INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> Les exemples d’instructions SQL qui figurent dans ce document reposent sur l’hypothèse que les données sont stockées dans SQL Server. Dans le cas contraire, reportez-vous à la cartographie du processus de science des données du cloud pour découvrir comment déplacer vos données vers SQL Server.
> 
> 

## <a name="a-namesql-dataexplorationaexplore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>Explorer les données SQL avec des scripts SQL
Voici quelques exemples de scripts SQL utilisables pour l’exploration de magasins de données dans SQL Server.

1. Obtenir le nombre d’observations par jour
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Obtenir les niveaux dans une colonne catégorielle
   
    `select  distinct <column_name> from <databasename>`
3. Obtenir le nombre de niveaux en combinant deux colonnes catégorielles 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Obtenir la distribution relative aux colonnes numériques 
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Pour découvrir un exemple pratique, vous pouvez utiliser le [jeu de données des taxis new-yorkais NYC Taxi](http://www.andresmh.com/nyctaxitrips/) et vous reporter au notebook IPython intitulé [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) (Retraitement des données de New-York City à l’aide de Notebook IPython et de SQL Server) pour connaître la procédure pas à pas.
> 
> 

## <a name="a-namepythonaexplore-sql-data-with-python"></a><a name="python"></a>Explorer les données SQL avec Python
L’utilisation de Python pour explorer les données et générer des fonctionnalités quand les données sont stockées dans SQL Server est comparable au traitement des données dans l’objet blob Azure à l’aide de Python comme expliqué dans [Traiter les données Azure Blob dans votre environnement de science des données](machine-learning-data-science-process-data-blob.md). Les données doivent être chargées à partir de la base de données dans une trame de données pandas, puis faire l’objet d’un traitement complémentaire. Nous décrivons dans cette section le processus de connexion à la base de données et de chargement des données dans la trame de données.

Le format de chaîne de connexion ci-après vous permet de vous connecter à une base de données SQL Server à partir de Python à l’aide de pyodbc (en remplaçant les variables servername, dbname, username et password par les valeurs qui vous correspondent) :

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [bibliothèque Pandas](http://pandas.pydata.org/) de Python offre un ensemble élaboré de structures de données et d’outils d’analyse des données pour la manipulation des données dans le cadre d’une programmation en Python. Le code suivant lit les résultats renvoyés par une base de données SQL Server dans une trame de données Pandas :

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Vous pouvez à présent utiliser la trame de données Pandas comme décrit dans la rubrique [Traiter les données Azure Blob dans votre environnement de science des données](machine-learning-data-science-process-data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>Exemple de processus Cortana Analytics en action
Pour obtenir un exemple de procédure pas à pas complet du processus Cortana Analytics à l’aide d’un jeu de données public, consultez [Processus TDSP (Team Data Science Process) en action : utilisation de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).




<!--HONumber=Nov16_HO3-->


