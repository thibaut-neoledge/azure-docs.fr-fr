### <a name="type-conversion-sample"></a>Exemple de conversion de type
L’exemple suivant montre la copie de données à partir d’un objet blob vers SQL Azure avec des conversions de type.

Supposons que le jeu de données d’objets blob soit au format CSV et contienne trois colonnes. L'une d'elles est une colonne datetime avec un format de date et d'heure personnalisé comprenant un nom abrégé en français pour le jour de la semaine.

Vous allez définir le jeu de données source d’objets blob source comme suit, ainsi que des définitions de type pour les colonnes.

```json
{
    "name": "AzureBlobTypeSystemInput",
    "properties":
    {
         "structure": 
          [
                { "name": "userid", "type": "Int64"},
                { "name": "name", "type": "String"},
                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "external": true,
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```
Étant donné la table de mappage de type SQL vers type .NET ci-dessus, vous devez définir la table SQL Azure avec le schéma suivant.

| Nom de la colonne | Type SQL |
| --- | --- |
| userId |bigint |
| name |texte |
| lastlogindate |datetime |

Ensuite, vous allez définir le jeu de données SQL Azure comme suit. 

> [!NOTE]
> Il est inutile de spécifier la section **structure** à l’aide des informations de type, car celles-ci sont déjà spécifiées dans le magasin de données sous-jacent.

```json
{
    "name": "AzureSQLOutput",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Dans ce cas, Data Factory effectuera automatiquement les conversions de type, y compris pour le champ Datetime avec son format date/heure personnalisé, en utilisant la culture fr-fr lors du déplacement des données à partir de l’objet blob vers SQL Azure.



<!--HONumber=Dec16_HO3-->


