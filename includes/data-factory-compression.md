### Prise en charge de la compression  
Le traitement de jeux de données de grande taille peut provoquer des goulots d’étranglement des E/S et du réseau. Par conséquent, les données compressées dans les magasins peuvent non seulement accélérer le transfert des données sur le réseau et économiser l’espace disque, mais également apporter des améliorations significatives des performances du traitement du Big Data. À ce stade, la compression est prise en charge pour les magasins de données de fichiers, comme les objets blob Azure ou un système de fichiers local.

Pour spécifier la compression pour un jeu de données, utilisez la propriété **compression** du jeu de données JSON, comme dans l’exemple suivant :

	{  
		"name": "AzureBlobDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureBlob",  
	    	"linkedServiceName": "StorageLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
Notez que la section **compression** a deux propriétés :
  
- **Type** : le codec de compression, qui peut être **GZIP**, **Deflate** ou **BZIP2**.  
- **Level** : le taux de compression, qui peut être **Optimal** ou **Fastest**. 
	- **Fastest** : l’opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n’est pas compressé de façon optimale. 
	- **Optimal** : l’opération de compression doit aboutir à une compression optimale, même si l’opération prend plus de temps. 
	
	Pour plus d’informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Supposons que l’exemple de jeu de données ci-dessus est utilisé comme sortie d’une activité de copie. L’activité de copie va compresser les données de sortie avec le codec GZIP en utilisant le taux optimal, puis va écrire les données compressées dans un fichier nommé pagecounts.csv.gz dans le stockage d’objets Blob Azure.

Quand vous spécifiez la propriété compression dans un jeu de données JSON d’entrée, le pipeline peut lire les données compressées à partir de la source et, quand vous spécifiez la propriété dans un jeu de données JSON de sortie, l’activité de copie peut écrire les données compressées dans la destination. Voici quelques exemples de scénarios :

- Lire les données compressées GZIP à partir d’un objet blob Azure, les décompresser et écrire des données du résultat dans une base de données SQL Azure. Dans ce cas, vous définissez le jeu de données d’objets blob Azure d’entrée avec propriété JSON compression. 
- Lire les données d’un fichier de texte brut dans le système de fichiers local, les compresser en utilisant le format GZIP et écrire les données compressées dans un objet blob Azure. Dans ce cas, vous définissez le jeu de données d’objets blob Azure de sortie avec propriété JSON compression.  
- Lire les données compressées au format GZIP à partir d’un objet blob Azure, les décompresser, les compresser en utilisant le format BZIP2 et écrire les données résultantes dans un objet blob Azure. Dans ce cas, vous définissez le jeu de données d’objet blob Azure d’entrée avec le type de compression défini sur GZIP et le jeu de données de sortie avec le type de compression défini sur BZIP2.   

<!---HONumber=September15_HO1-->