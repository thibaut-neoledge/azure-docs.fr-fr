<properties
	pageTitle="Développement d’une API pour PowerApps Enterprise | Microsoft Azure"
	description="Génération ou création d’API personnalisées pour PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="rajram"/>

# Développer une API pour PowerApps

Vous pouvez créer ou développer vos propres API afin de les utiliser dans PowerApps. Procédez comme suit :

- Créer et déployer votre API
- Créer une définition d’API [Swagger 2.0](http://swagger.io/) pour votre API

Cette rubrique répertorie ces étapes.

## Étape 1 : Créer et déployer votre API

La création et le déploiement d'une API pour PowerApps sont identiques à la création d'une API standard. Vous pouvez choisir votre langage de programmation et votre infrastructure préférés. Vous pouvez également choisir d'héberger votre API n'importe où. Nous vous recommandons de l'héberger dans le même environnement de services d’applications PowerApps que l’[Application API](https://azure.microsoft.com/services/app-service/api/).

Les articles suivants vous montrent comment créer et déployer une API .NET, Java ou Node.js dans l'environnement App Service :

- [Créer et déployer une infrastructure .NET dans Azure App Service](../app-service-api-dotnet-get-started.md)
- [Créer et déployer une application API Java dans Azure App Service](../app-service-api-java-api-app.md)
- [Créer et déployer une application API Node.js dans Azure App Service](../app-service-api-nodejs-api-app.md)


## Étape 2 : Créer une définition d’API Swagger 2.0 pour votre API

Si vous suivez l'un des articles référencés à l’*Étape 1*, une définition d'API Swagger 2.0 standard est générée automatiquement pour votre API. En vue d’une optimisation pour PowerApps, vous pouvez également personnaliser la définition de l'API Swagger 2.0 à l'aide des extensions de schéma suivantes.

Pour savoir comment personnaliser la définition de l'API Swagger 2.0 en général, consultez [Personnaliser des définitions d’API générées par Swashbuckle](../app-service-api-dotnet-swashbuckle-customize.md).

### Extensions de schéma
Outre le swagger généré automatiquement par Swashbuckle, il existe d’autres extensions swagger disponibles lors de la création d'une API pour PowerApps. Cette section répertorie et décrit ces extensions.

##### x-ms-summary
Chaîne qui décrit les noms d’affichage des entités qui n'ont pas de champ Résumé défini dans la spécification Swagger. Les**Noms de paramètres** en sont un exemple.

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
	
- operationId : une chaîne qui correspond à l'operationId pour l'opération appelée
- parameters : un objet dont les propriétés définissent les paramètres requis pour l'opération
- value-collection : une chaîne de chemin d'accès qui correspond à un tableau d'objets dans la charge utile de réponse
- value-path : une chaîne de chemin d'accès de l'objet à l'intérieur de « value-collection » qui fait référence à la valeur du paramètre.
- value-title : une chaîne de chemin d'accès de l'objet à l'intérieur de « value-collection » qui fait référence à une description de la valeur.


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

dans l'exemple ci-dessus, le swagger définit une opération appelée _TableData\_CreateItem_ qui crée un objet dans Salesforce.

Salesforce a de nombreux objets incorporés. _x-ms-dynamic-values_ est utilisé ici pour aider le concepteur à faire la liste des objets Salesforce incorporés. Il l'obtient en appelant _TableMetadata\_ListTables_.

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

c’est utile dans les scénarios où les entrées d'une opération sont dynamiques. Considérez par exemple le cas SQL. Le schéma de chaque table est différent. Par conséquent, lorsqu'un utilisateur sélectionne une table particulière, le concepteur de flux logique doit comprendre la structure de la table pour pouvoir afficher les noms de colonnes. Dans ce contexte, si la définition swagger a _x-ms-dynamique-schema_, elle appelle l'opération correspondante pour extraire le schéma.

<!---HONumber=AcomDC_1203_2015-->