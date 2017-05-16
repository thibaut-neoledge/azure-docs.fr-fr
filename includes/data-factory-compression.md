## <a name="specifying-compression"></a>Spécification de la compression
Le traitement de jeux de données de grande taille peut provoquer des goulots d’étranglement des E/S et du réseau. Par conséquent, les données compressées dans les magasins peuvent non seulement accélérer le transfert des données sur le réseau et économiser l’espace disque, mais également apporter des améliorations significatives des performances du traitement du Big Data. Actuellement, la compression est prise en charge pour les magasins de données de fichiers, comme les objets blob Azure ou un système de fichiers local.  

> [!NOTE]
> Les paramètres de compression ne sont pas pris en charge pour les données au format **AvroFormat**, **OrcFormat** ou **ParquetFormat**. Lors de la lecture des fichiers de mise en forme, Azure Data Factory détecte et utilise le codec de compression dans les métadonnées ; lors de l’écriture de données dans des fichiers présentant ces formats, le logiciel choisit le codec de compression par défaut pour ce format. Par exemple, ZLIB pour OrcFormat et SNAPPY pour ParquetFormat.
>
>

Pour spécifier la compression pour un jeu de données, utilisez la propriété **compression** du jeu de données JSON, comme dans l'exemple suivant :   

```json
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
```
Partons du principe que l’exemple de jeu de données ci-dessus est utilisé comme sortie d’une activité de copie. Cette dernière va compresser les données de sortie avec le codec GZIP en utilisant le taux optimal, puis écrire les données compressées dans un fichier nommé pagecounts.csv.gz dans le stockage Blob Azure.   

La section **compression** a deux propriétés :  

* **Type** : le codec de compression, qui peut être **GZIP**, **Deflate**, **BZIP2** ou **ZipDeflate**.  
* **Level** : le taux de compression, qui peut être **Optimal** ou **Fastest**.

  * **Fastest** : l'opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n'est pas compressé de façon optimale.
  * **Optimal**: l'opération de compression doit aboutir à une compression optimale, même si l'opération prend plus de temps.

    Pour plus d’informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) .

Lorsque vous spécifiez la propriété `compression` dans un jeu de données JSON d’entrée, le pipeline peut lire les données compressées à partir de la source. Lorsque vous spécifiez la propriété dans un jeu de données JSON de sortie, l’activité de copie peut écrire les données compressées dans la destination. Voici quelques exemples de scénarios :

* Lire les données compressées GZIP à partir d’un objet blob Azure, les décompresser et écrire des données du résultat dans une SQL Database Azure. Dans ce cas, vous définissez le jeu de données d’objets Blob Azure d’entrée avec la propriété JSON `compression` `type` au format GZIP.
* Lire les données d’un fichier de texte brut dans le système de fichiers local, les compresser en utilisant le format GZIP et écrire les données compressées dans un objet blob Azure. Dans ce cas, vous définissez le jeu de données d’objets Blob Azure de sortie avec la propriété JSON `compression` `type` au format GZIP.
* Lisez le fichier .zip à partir du serveur FTP, décompressez-le pour accéder aux fichiers qu’il contient et placez ces derniers dans Azure Data Lake Store. Dans ce cas, vous définissez le jeu de données FTP d’entrée avec la propriété JSON `compression` `type` au format ZipDeflate.
* Lire les données compressées au format GZIP à partir d’un objet blob Azure, les décompresser, les compresser en utilisant le format BZIP2 et écrire les données résultantes dans un objet blob Azure. Dans ce cas, vous définissez le jeu de données d’objet blob Azure d’entrée avec le paramètre `compression` `type` défini sur GZIP et le jeu de données de sortie avec le paramètre `compression` `type` défini sur BZIP2.   
