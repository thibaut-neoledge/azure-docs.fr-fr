<properties 
	pageTitle="Échantillonner des données dans le stockage d'objets blob Azure | Azure" 
	description="Échantillonner des données dans le stockage d'objets blob Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="sunliangms,fashah,msolhab" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="sunliangms,fashah,msolhab,garye"/> 

#<a name="heading"></a>Échantillonner des données dans le stockage d'objets blob Azure

Ce document traite de l'échantillonnage des données conservées dans le stockage d'objets blob Azure via le téléchargement de ces données par programme, puis leur échantillonnage à l'aide d'un exemple de code Python. La procédure est la suivante :

1. Téléchargez les données du stockage d'objets blob Azure à l'aide du service BLOB en utilisant l'exemple de code suivant : 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Lisez les données dans une trame de données pandas à partir du fichier téléchargé à l'étape précédente.

		import pandas as pd

	    #directly ready from file on disk
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Échantillonnez les données en utilisant l'élément `random.choice` de `numpy`, comme suit :

	    # A 1 percent sample
    	sample_ratio = 0.01 
    	sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
    	sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
    	dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

	Vous pouvez à présent travailler sur l'échantillon de 1 % de la trame de données ci-dessus à d'autres fins d'exploration et de génération de fonctionnalités.

##<a name="heading"></a>Connexion à Azure Machine Learning

Vous pouvez sous-échantillonner les données et les utiliser directement dans Azure Machine Learning en utilisant l'exemple de code ci-dessous :

1. Écrivez la trame de données dans un fichier local :

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Chargez le fichier local dans un objet blob Azure au moyen de l'exemple de code suivant :

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
		    print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Lisez les données de l'objet blob Azure à l'aide du *Reader Module* d'Azure Machine Learning, comme illustré dans la capture d'écran ci-dessous :
 
![reader blob][1]

[1]: ./media/machine-learning-data-science-sample-data-blob/reader_blob.png

<!--HONumber=49-->