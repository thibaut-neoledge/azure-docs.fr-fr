## Spécification des formats

### Définition de TextFormat

Si le format est défini sur **TextFormat**, vous pouvez spécifier les propriétés **facultatives** suivantes dans la section **Format**.

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------- | -------- | 
| columnDelimiter | Caractère utilisé pour séparer les colonnes dans un fichier. | Un seul caractère est autorisé. La valeur par défaut est virgule (,). | Non |
| rowDelimiter | Caractère utilisé pour séparer les lignes dans un fichier. | Un seul caractère est autorisé. La valeur par défaut est : [« \\r\\n », « \\r », « \\n »] en lecture, et « \\r\\n » en écriture. | Non |
| escapeChar | Caractère spécial utilisé pour échapper au délimiteur de colonnes dans le contenu du fichier d’entrée. <br/><br/>Vous ne pouvez pas spécifier à la fois escapeChar et quoteChar pour une table. | Un seul caractère est autorisé. Aucune valeur par défaut. <br/><br/>Par exemple, si vous avez une virgule (,) comme séparateur de colonnes mais que vous voulez avoir le caractère virgule dans le texte (par exemple : « Hello, world »), vous pouvez définir « $ » comme caractère d’échappement et utiliser la chaîne « Hello$, world » dans la source. | Non | 
| quoteChar | Le caractère utilisé pour entourer de guillemets une valeur de chaîne. Les séparateurs de colonnes et de lignes à l'intérieur des caractères de guillemets sont considérés comme faisant partie de la valeur de la chaîne. Cette propriété s’applique aux jeux de données d’entrée et de sortie.<br/><br/>Vous ne pouvez pas spécifier à la fois escapeChar et quoteChar pour une table. | Un seul caractère est autorisé. Aucune valeur par défaut. <br/><br/>Par exemple, si vous avez une virgule (,) comme séparateur de colonnes mais que vous voulez avoir le caractère virgule dans le texte (par exemple : « Hello, world »), vous pouvez définir " (guillemet droit) comme caractère de guillemet et utiliser la chaîne "Hello, world" dans la source. | Non |
| nullValue | Un ou plusieurs caractères utilisés pour représenter une valeur null. | Un ou plusieurs caractères. Les valeurs par défaut sont « \\N » et « NULL » en lecture, et « \\N » en écriture. | Non |
| encodingName | Spécifier le nom d'encodage. | Une liste de noms d’encodage valides. Consultez : [Propriété Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exemple : windows-1250 ou shift\_jis. La valeur par défaut est : UTF-8. | Non | 
| firstRowAsHeader | Spécifie si la première ligne doit être considérée comme un en-tête. Pour un jeu de données d’entrée, Data Factory lit la première ligne comme un en-tête. Pour un jeu de données de sortie, Data Factory écrit la première ligne comme un en-tête. <br/><br/>Consultez la page [Scénarios d’utilisation de **firstRowAsHeader** et **skipLineCount**](#scenarios-for-using-firstrowasheader-and-skiplinecount) pour obtenir des exemples de scénarios. | True<br/>False (valeur par défaut) | Non |
| skipLineCount | Indique le nombre de lignes à ignorer lors de la lecture des données à partir des fichiers d’entrée. Si skipLineCount et firstRowAsHeader sont spécifiés, les lignes sont d’abord ignorées, puis les informations d’en-tête sont lues à partir du fichier d’entrée. <br/><br/>Consultez la page [Scénarios d’utilisation de firstRowAsHeader et skipLineCount](#scenarios-for-using-firstrowasheader-and-skiplinecount) pour obtenir des exemples de scénarios. | Integer | Non | 
| treatEmptyAsNull | Spécifie si une chaîne null ou vide doit être traitée comme une valeur null lors de la lecture des données à partir d’un fichier d’entrée. | True (valeur par défaut)<br/>False | Non |  

#### Exemple pour TextFormat
L'exemple suivant illustre certaines des propriétés de format pour TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
			"firstRowAsHeader": true,
			"skipLineCount": 0,
			"treatEmptyAsNull": true
	    }
	},

Pour utiliser escapeChar à la place de quoteChar, remplacez la ligne contenant quoteChar par ce qui suit :

	"escapeChar": "$",



### Scénarios d’utilisation de firstRowAsHeader et skipLineCount

- Vous copiez à partir d’une source hors fichier vers un fichier texte et vous souhaitez ajouter une ligne d’en-tête qui contient les métadonnées de schéma (par exemple : schéma SQL). Définissez **firstRowAsHeader** sur true dans le jeu de données de sortie pour ce scénario.
- Vous copiez à partir d’un fichier texte contenant une ligne d’en-tête vers un récepteur hors fichier et souhaitez supprimer cette ligne. Définissez **firstRowAsHeader** sur true dans le jeu de données d’entrée.
- Vous copiez à partir d’un fichier texte et souhaitez ignorer quelques lignes au début qui ne sont ni des données ni un en-tête. Spécifiez **skipLineCount** pour indiquer le nombre de lignes à ignorer. Si le reste du fichier contient une ligne d’en-tête, vous pouvez également spécifier **firstRowAsHeader**. Si **skipLineCount** et **firstRowAsHeader** sont spécifiés, les lignes sont d’abord ignorées, puis les informations d’en-tête sont lues à partir du fichier d’entrée

### Définition d'AvroFormat
Si le format est défini sur AvroFormat, il est inutile de spécifier des propriétés dans la section Format de la section typeProperties. Exemple :

	"format":
	{
	    "type": "AvroFormat",
	}

Pour utiliser le format Avro dans une table Hive, vous pouvez faire référence au [didacticiel Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

### Définition de JsonFormat

Si le format est défini sur **JsonFormat**, vous pouvez spécifier les propriétés **facultatives** suivantes dans la section **Format**.

| Propriété | Description | Obligatoire |
| -------- | ----------- | -------- |
| filePattern | Indiquez le modèle des données stockées dans chaque fichier JSON. Les valeurs autorisées sont les suivantes : **setOfObjects** et **arrayOfObjects**. La valeur **par défaut** est **setOfObjects**. Pour plus d’informations sur ces modèles, consultez les sections suivantes.| Non |
| encodingName | Spécifier le nom d'encodage. Pour obtenir une liste de noms d’encodage valides, consultez : [Propriété Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Par exemple : windows-1250 ou shift\_jis. La valeur **par défaut** est : **UTF-8**. | Non | 
| nestingSeparator | Caractère utilisé pour séparer les niveaux d'imbrication. La valeur par défaut est . (point). | Non | 


#### Modèle de fichier setOfObjects

Chaque fichier contient un objet unique, ou plusieurs objets concaténés/délimités par des lignes. Quand cette option est sélectionnée dans un jeu de données de sortie, l’activité de copie produit un seul fichier JSON contenant un objet par ligne (format délimité par des lignes).

**objet unique**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}

**JSON délimité par des lignes**

	{"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
	{"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
	{"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"789037573","callingnum2":"789044691","switch1":"UK","switch2":"Australia"}
	{"time":"2015-04-29T07:13:22.0960000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789044691","switch1":"US","switch2":"Australia"}

**JSON concaténé**

	{
		"time": "2015-04-29T07:12:20.9100000Z",
		"callingimsi": "466920403025604",
		"callingnum1": "678948008",
		"callingnum2": "567834760",
		"switch1": "China",
		"switch2": "Germany"
	}
	{
		"time": "2015-04-29T07:13:21.0220000Z",
		"callingimsi": "466922202613463",
		"callingnum1": "123436380",
		"callingnum2": "789037573",
		"switch1": "US",
		"switch2": "UK"
	}
	{
		"time": "2015-04-29T07:13:21.4370000Z",
		"callingimsi": "466923101048691",
		"callingnum1": "678901578",
		"callingnum2": "345626404",
		"switch1": "Germany",
		"switch2": "UK"
	}


#### Modèle de fichier arrayOfObjects 

Chaque fichier contient un tableau d’objets.

	[
	    {
	        "time": "2015-04-29T07:12:20.9100000Z",
	        "callingimsi": "466920403025604",
	        "callingnum1": "678948008",
	        "callingnum2": "567834760",
	        "switch1": "China",
	        "switch2": "Germany"
	    },
	    {
	        "time": "2015-04-29T07:13:21.0220000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789037573",
	        "switch1": "US",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:21.4370000Z",
	        "callingimsi": "466923101048691",
	        "callingnum1": "678901578",
	        "callingnum2": "345626404",
	        "switch1": "Germany",
	        "switch2": "UK"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "789037573",
	        "callingnum2": "789044691",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:22.0960000Z",
	        "callingimsi": "466922202613463",
	        "callingnum1": "123436380",
	        "callingnum2": "789044691",
	        "switch1": "US",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:24.2120000Z",
	        "callingimsi": "466922201102759",
	        "callingnum1": "345698602",
	        "callingnum2": "789097900",
	        "switch1": "UK",
	        "switch2": "Australia"
	    },
	    {
	        "time": "2015-04-29T07:13:25.6320000Z",
	        "callingimsi": "466923300236137",
	        "callingnum1": "567850552",
	        "callingnum2": "789086133",
	        "switch1": "China",
	        "switch2": "Germany"
	    }
	]

### Exemple pour JsonFormat

Prenons un fichier JSON avec le contenu suivant :

	{
		"Id": 1,
		"Name": {
			"First": "John",
			"Last": "Doe"
		},
		"Tags": ["Data Factory”, "Azure"]
	}

Vous voulez copier ce contenu dans un tableau SQL Azure dans le format suivant :

ID | Name.First | Name.Middle | Name.Last | Tags
--- | ---------- | ----------- | --------- | ----
1 | John | null | Doe | ["Data Factory", "Azure"]

Le jeu de données d’entrée ayant le type JsonFormat est défini comme suit : (définition partielle présentant uniquement les éléments pertinents)

	"properties": {
		"structure": [
			{"name": "Id", "type": "Int"},
			{"name": "Name.First", "type": "String"},
			{"name": "Name.Middle", "type": "String"},
			{"name": "Name.Last", "type": "String"},
			{"name": "Tags", "type": "string"}
		],
		"typeProperties":
		{
			"folderPath": "mycontainer/myfolder",
			"format":
			{
				"type": "JsonFormat",
				"filePattern": "setOfObjects",
				"encodingName": "UTF-8",
				"nestingSeparator": "."
			}
		}
	}

Si la structure n’est pas définie, l’activité de copie aplatit la structure par défaut et copie tout le contenu.

#### Structure JSON prise en charge
Notez les points suivants :

- Chaque objet avec une collection de paires nom/valeur est mappé à une seule ligne de données dans un format tabulaire. Les objets peuvent être imbriqués, et vous pouvez définir comment aplatir la structure dans un jeu de données en utilisant le séparateur d’imbrication (.) par défaut. Pour obtenir un exemple, consultez la section précédente, [Exemple pour JsonFormat](#jsonformat-example).
- Si la structure n’est pas définie dans le jeu de données Data Factory, l’activité de copie détecte le schéma à partir du premier objet et aplatit l’objet entier.
- Si l’entrée JSON contient un tableau, l’activité de copie convertit la valeur du tableau entier en une chaîne. Vous pouvez choisir d’utiliser à la place le [mappage ou filtrage de colonnes](#column-mapping-with-translator-rules).
- S’il y a plusieurs noms identiques au même niveau, l’activité de copie sélectionne le dernier nom.
- Les noms de propriété respectent la casse. Quand deux propriétés de même nom ont une casse différente, elles sont considérées comme deux propriétés distinctes.

### Spécification d’OrcFormat
Si le format est défini sur OrcFormat, il est inutile de spécifier des propriétés dans la section Format de la section typeProperties. Exemple :

	"format":
	{
	    "type": "OrcFormat",
	}

> [AZURE.IMPORTANT] Si vous ne copiez pas les fichiers ORC **tels quels** entre les magasins de données locaux et cloud, vous devez installer JRE 8 (Java Runtime Environment) sur votre machine de passerelle. La passerelle 64 bits requiert un environnement JRE 64 bits et que la passerelle 32 bits nécessite un environnement JRE 32 bits. Ces deux versions sont disponibles [ici](http://go.microsoft.com/fwlink/?LinkId=808605). Sélectionnez la bonne version.

Notez les points suivants :

-	Les types de données complexes ne sont pas pris en charge (STRUCT, MAP, LIST, UNION)
-	Le fichier ORC a trois [options liées à la compression](http://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) : NONE, ZLIB, SNAPPY. Data Factory prend en charge la lecture des données du fichier ORC dans tous ces formats compressés. Il utilise le codec de compression se trouvant dans les métadonnées pour lire les données. Toutefois, lors de l’écriture dans un fichier ORC, Data Factory choisit ZLIB, qui est la valeur par défaut pour ORC. Actuellement, il n’existe aucune option permettant de remplacer ce comportement.

<!---HONumber=AcomDC_0907_2016-->