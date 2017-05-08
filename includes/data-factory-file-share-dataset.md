## <a name="fileshare-dataset-type-properties"></a>Propriétés de type du jeu de données FileShare
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](../articles/data-factory/data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données.

La section **typeProperties** est différente pour chaque type de jeu de données. Elle fournit des informations spécifiques au type de jeu de données. La section typeProperties pour un jeu de données de type **FileShare** a les propriétés suivantes :

| Propriété | Description | Requis |
| --- | --- | --- |
| folderPath |Sous-chemin du dossier. Utilisez le caractère d’échappement « \ » pour les caractères spéciaux contenus dans la chaîne. Consultez la section [Exemples de définitions de jeux de données et de service liés](#sample-linked-service-and-dataset-definitions) pour obtenir des exemples.<br/><br/>Vous pouvez également effectuer une combinaison avec la propriété **partitionBy** pour que les chemins d’accès de dossier soient basés sur les dates et heures de démarrage et d’arrêt de la tranche. |Oui |
| fileName |Spécifiez le nom du fichier dans l’élément **folderPath** si vous souhaitez que la table se réfère à un fichier spécifique du dossier. Si vous ne spécifiez aucune valeur pour cette propriété, le tableau pointe vers tous les fichiers du dossier.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie, le nom du fichier généré aura ce format dans l’exemple suivant : <br/><br/>Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Non |
| fileFilter |Spécifiez un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers.<br/><br/>Les valeurs autorisées sont : `*` (plusieurs caractères) et `?` (caractère unique).<br/><br/>Exemple 1 : `"fileFilter": "*.log"`<br/>Exemple 2 : `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter s’applique à un jeu de données FileShare d’entrée. Cette propriété n’est pas prise en charge avec HDFS. |Non |
| partitionedBy |partitionedBy peut être utilisé pour spécifier un folderPath dynamique, fileName pour les données de série chronologique. Par exemple, folderPath peut être paramétré pour toutes les heures de données. |Non |
| format | Les types de formats suivants sont pris en charge : **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour en savoir plus, consultez les sections relatives à [format Text](#specifying-textformat), [format Json](#specifying-jsonformat), [format Avro](#specifying-avroformat), [format Orc](#specifying-orcformat) et [format Parquet](#specifying-parquetformat). <br><br> Si vous souhaitez **copier des fichiers en l’état** entre des magasins de fichiers (copie binaire), ignorez la section Format dans les deux définitions de jeu de données d’entrée et de sortie. |Non |
| compression | Spécifiez le type et le niveau de compression pour les données. Types pris en charge : **GZip**, **Deflate**, **BZip2** et **ZipDeflate**. Niveaux pris en charge : **Optimal** et **Fastest**. Pour en savoir plus, voir [Prise en charge de la compression](#specifying-compression). |Non |
| useBinaryTransfer |Spécifiez si vous voulez utiliser le mode de transfert binaire. True pour le mode binaire et false pour ASCII. Valeur par défaut : true. Cette propriété peut uniquement être utilisée lorsque le type de service lié associé est : FtpServer. |Non |

> [!NOTE]
> fileName et fileFilter ne peuvent pas être utilisés simultanément.
>
>

### <a name="using-partionedby-property"></a>Utilisation de la propriété partitionedBy
Comme mentionné dans la section précédente, vous pouvez spécifier un folderPath dynamique, fileName pour les données de série chronologique avec partitionedBy. Vous pouvez le faire avec les macros Data Factory et les variables système SliceStart et SliceEnd, qui indiquent la période logique d’une tranche de données spécifique.

Consultez les articles [Jeux de données dans Azure Data Factory](../articles/data-factory/data-factory-create-datasets.md), [Planification et exécution avec Data Factory](../articles/data-factory/data-factory-scheduling-and-execution.md) et [Pipelines et activités dans Azure Data Factory](../articles/data-factory/data-factory-create-pipelines.md) pour en savoir plus sur les jeux de données de série chronologique, la planification et les segments.

#### <a name="sample-1"></a>Exemple 1 :

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Dans cet exemple, {Slice} est remplacé par la valeur de la variable système Data Factory SliceStart au format (AAAAMMJJHH) spécifié. SliceStart fait référence à l'heure de début du segment. folderPath est différent pour chaque segment. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemple 2 :

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Dans cet exemple, l'année, le mois, le jour et l'heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés folderPath et fileName.
