<properties 
	pageTitle="Échantillonner des données dans SQL Server sur Azure | Microsoft Azure" 
	description="Échantillonner des données dans SQL Server sur Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/29/2015" 
	ms.author="fashah;garye;bradsev" />

#<a name="heading"></a>Échantillonner des données dans SQL Server sur Azure

Ce document traite des données d’échantillonnage stockées dans SQL Server sur Azure à l’aide de SQL et à l'aide du langage de programmation Python.

>[AZURE.NOTE]L’exemple de code SQL figurant dans ce document repose sur l’hypothèse que les données sont stockées dans SQL Server sur Azure. Si ce n'est pas le cas, reportez-vous à la section [Déplacer des données vers SQL Server sur Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) du [Guide de processus de données avancée](machine-learning-data-science-advanced-data-processing.md) pour obtenir des instructions pour déplacer vos données vers un serveur SQL Server dans Azure.

##<a name="SQL"></a>Utilisation de SQL

Cette section décrit différentes méthodes permettant d’effectuer un échantillonnage aléatoire simple des données de la base de données via SQL. Choisissez une méthode en fonction de la taille de vos données et de leur distribution.

Les deux options ci-après indiquent comment utiliser l’élément newid dans SQL Server pour procéder à l’échantillonnage. La méthode que vous choisissez dépend du degré aléatoire qui doit caractériser l’échantillon (dans l’exemple de code ci-après, l’élément pk_id est supposé correspondre à une clé primaire générée automatiquement).

1. Échantillon aléatoire moins strict

	    select  * from <table_name> where <primary_key> in 
    	(select top 10 percent <primary_key> from <table_name> order by newid())

2. Échantillon plus aléatoire

	    SELECT * FROM <table_name>
    	WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Vous pouvez également utiliser l’élément TABLESAMPLE pour l’échantillonnage, comme illustré ci-dessous. L’utilisation de cette méthode peut constituer une meilleure approche si vos données sont volumineuses (en supposant que les données figurant sur des pages différentes ne sont pas corrélées) et que vous souhaitez que la requête s’exécute dans un délai acceptable.

	SELECT *
	FROM <table_name> 
	TABLESAMPLE (10 PERCENT)

**Remarque :**
> Vous pouvez explorer et générer des fonctionnalités à partir de ces données échantillonnées en les stockant dans une nouvelle table.


###<a name="sql-aml"></a>Connexion à Azure Machine Learning

Vous pouvez utiliser directement les exemples de requêtes ci-dessus dans le module Lecteur d’Azure Machine Learning afin de sous-échantillonner les données à la volée et de les importer dans une expérience Azure Machine Learning. La capture d’écran ci-après illustre l’utilisation du module Lecteur pour lire les données échantillonnées :
   
![lecteur sql][1]

##<a name="python"></a>Utilisation du langage de programmation Python 

Cette section décrit l’utilisation de la bibliothèque pyodbc pour vous connecter à une base de données SQL Server dans Python. La chaîne de connexion de base de données se présente comme suit :(remplacez les variables servername, dbname, username et password par les valeurs de votre configuration) :

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La bibliothèque [Pandas](http://pandas.pydata.org/) de Python offre un ensemble élaboré de structures de données et d’outils d’analyse des données pour la manipulation des données dans le cadre d’une programmation en Python. Le code ci-après lit un échantillon de 0,1 % des données d’une table de la base de données Azure SQL dans une trame de données pandas :

	import pandas as pd

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Vous pouvez à présent travailler sur les données échantillonnées dans la trame de données pandas.

###<a name="python-aml"></a>Connexion à Azure Machine Learning

Vous pouvez utiliser l’exemple de code ci-après pour enregistrer les données sous-échantillonnées dans un fichier et les charger dans un objet blob Azure. Les données figurant dans l’objet blob peuvent être lues directement dans une expérimentation Azure Machine Learning à l’aide du module *Lecteur*. La procédure comporte trois étapes :

1. Écrire la trame de données pandas dans un fichier local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Charger le fichier local dans un objet blob Azure

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Lisez les données de l’objet blob Azure à l’aide du module *Lecteur* d’Azure Machine Learning comme illustré dans la capture d’écran ci-dessous :
 
![objet blob de lecteur][2]

## Exemple de processus d’analyse avancée et technologie en action ADAPT (Advanced Analytics Process and Technology)

Pour obtenir un exemple de procédure pas à pas de bout en bout à l’aide du processus d’analyse avancée et technologie (ADAPT) avec un jeu de données public, consultez [Processus d’analyse avancée et technologie en action : utilisation de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/machine-learning-data-science-sample-sql-server-virtual-machine/reader_blob.png

 

<!---HONumber=July15_HO1-->