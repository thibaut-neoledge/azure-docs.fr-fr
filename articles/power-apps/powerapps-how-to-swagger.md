<properties
	pageTitle="Comment personnaliser votre définition Swagger pour PowerApps et les flux logiques | Microsoft Azure"
	description="Afficher les extensions de schéma requises par Swagger pour utiliser PowerApps et les flux logiques"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="04/12/2016"
   ms.author="mandia"/>


# Personnaliser votre définition de Swagger pour PowerApps et les flux logiques

## Créer une définition d’API Swagger 2.0 pour votre API

Pour savoir comment ajouter Swagger à votre WebAPI, consultez [Swashbuckle][1].

## Extensions de schéma
Outre les spécifications Swagger standard, d’autres extensions swagger sont disponibles lors de la création d'une API personnalisée pour PowerApps et les flux logiques. Cette section répertorie et décrit ces extensions.

##### x-ms-summary
Chaîne qui décrit les noms d’affichage des entités qui n'ont pas de champ `summary` défini dans la spécification Swagger. Les**Noms de paramètres** en sont un exemple.

##### x-ms-visibility
Cette valeur indique si l'entité est affichée dans le concepteur de flux logique. Les valeurs suivantes sont disponibles :

- « none » (par défaut)
- « advanced »
- « internal » - Le concepteur de flux logique n'affiche pas ces opérations

Si une opération est marquée comme « importante », le client de flux logique devrait mettre en évidence ces opérations.

##### x-ms-trigger
Indique si cette opération peut être utilisée comme un déclencheur dans le flux logique. Les options incluent :
	
- none (par défaut) : l'opération ne peut pas être utilisée comme un déclencheur.
- single : cette opération peut également être utilisée comme un déclencheur.
- batched : cette opération peut être utilisée comme un déclencheur. En outre, cette opération répond avec un « tableau » d'objets JSON, et le flux logique active un déclencheur pour chaque élément du tableau.


##### x-ms-dynamic-values
Il s'agit d'un indicateur pour le concepteur de flux de la logique que l'API fournit une liste de valeurs dynamiquement autorisées pour ce paramètre. Le concepteur de flux logique peut appeler une opération telle que définie par la valeur de ce champ, et extraire les valeurs possibles du résultat. Le concepteur de flux logique peut ensuite afficher ces valeurs sous forme d'options pour l'utilisateur final.

La valeur est un objet qui contient les propriétés suivantes :
	
- `operationId` : une chaîne qui correspond à l'operationId pour l'opération appelée
- `parameters` : un objet dont les propriétés définissent les paramètres requis pour l'opération
- `value-collection` : une chaîne de chemin d'accès qui correspond à un tableau d'objets dans la charge utile de réponse
- `value-path` : une chaîne de chemin d'accès de l'objet à l'intérieur de « value-collection » qui fait référence à la valeur du paramètre.
- `value-title` : une chaîne de chemin d'accès de l'objet à l'intérieur de « value-collection » qui fait référence à une description de la valeur.


Exemple :

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

Dans cet exemple, le swagger définit l’opération `TableData_CreateItem` qui crée un objet dans Salesforce.

Salesforce a de nombreux objets incorporés. `x-ms-dynamic-values` est utilisé ici pour aider le concepteur à faire la liste des objets Salesforce incorporés. Il obtient la liste en appelant `TableMetadata_ListTables`.

##### x-ms-dynamic-schema
Il s'agit d'un indicateur pour le concepteur de flux logique que le schéma pour ce paramètre (ou cette réponse) est dynamique par nature. Il peut appeler une opération telle que définie par la valeur de ce champ et découvrir le schéma de manière dynamique. Il peut ensuite afficher une interface utilisateur appropriée pour prendre des entrées de l'utilisateur ou pour afficher les champs disponibles.

Exemple :

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

c’est utile dans les scénarios où les entrées d'une opération sont dynamiques. Considérez par exemple le cas SQL. Le schéma de chaque table est différent. Par conséquent, lorsqu'un utilisateur sélectionne une table particulière, le concepteur de flux logique doit comprendre la structure de la table pour pouvoir afficher les noms de colonnes. Dans ce contexte, si la définition swagger a `x-ms-dynamic-schema`, elle appelle l'opération correspondante pour extraire le schéma.


<!--Reference links in article-->
[1]: https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md

<!---HONumber=AcomDC_0413_2016-->