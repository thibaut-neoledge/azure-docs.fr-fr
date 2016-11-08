### Définition de JsonFormat
Si le format est défini sur **JsonFormat**, vous pouvez spécifier les propriétés **facultatives** suivantes dans la section **Format**.

| Propriété | Description | Obligatoire |
| --- | --- | --- |
| filePattern |Indiquez le modèle des données stockées dans chaque fichier JSON. Les valeurs autorisées sont les suivantes : **setOfObjects** et **arrayOfObjects**. La valeur **par défaut** est **setOfObjects**. Pour plus d’informations sur ces modèles, consultez les sections ci-dessous. |Non |
| encodingName |Spécifiez le nom du codage. Pour obtenir une liste des noms d’encodage valides, consultez la propriété [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Par exemple : windows-1250 ou shift\_jis. La valeur **par défaut** est **UTF-8**. |Non |
| nestingSeparator |Caractère utilisé pour séparer les niveaux d'imbrication. La valeur **par défaut** est **. (point)**. |Non |

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

| Id | Name.First | Name.Middle | Name.Last | Tags |
| --- | --- | --- | --- | --- |
| 1 |John |null |Doe |["Data Factory", "Azure"] |

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
Notez les points suivants :

* Chaque objet avec une collection de paires nom/valeur est mappé à une seule ligne de données dans un format tabulaire. Les objets peuvent être imbriqués, et vous pouvez définir comment aplatir la structure dans un jeu de données en utilisant le séparateur d’imbrication (.) par défaut. Pour obtenir un exemple, consultez la section [Exemple pour JsonFormat](#jsonformat-example) ci-dessus.  
* Si la structure n’est pas définie dans le jeu de données Data Factory, l’activité de copie détecte le schéma à partir du premier objet et aplatit l’objet entier. 
* Si l’entrée JSON contient un tableau, l’activité de copie convertit la valeur du tableau entier en une chaîne. Vous pouvez choisir d’utiliser à la place le [mappage ou filtrage de colonnes](#column-mapping-with-translator-rules).
* S’il y a plusieurs noms identiques au même niveau, l’activité de copie sélectionne le dernier nom.
* Les noms de propriété respectent la casse. Quand deux propriétés de même nom ont une casse différente, elles sont considérées comme deux propriétés distinctes. 

<!---HONumber=AcomDC_0316_2016-->