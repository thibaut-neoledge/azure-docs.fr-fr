<properties
    pageTitle="Importation de données dans Azure Search à l’aide de l’API REST | Microsoft Azure | Service de recherche cloud hébergé"
    description="Comment charger des données sur un index dans Azure Search à l’aide de l’API REST."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="02/29/2016"
    ms.author="ashmaka"/>

# Importer des données dans Azure Search à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d'ensemble](search-what-is-data-import.md)
- [Portail](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexeurs](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)


Cet article vous explique comment utiliser l’[API REST Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour importer des données dans un index Azure Search. Avant de commencer cette procédure, vous devez déjà avoir [créé un index Azure Search](search-create-index-rest-api.md).

Pour distribuer des documents dans l’index à l’aide de l’API REST, vous allez envoyer une requête HTTP POST au point de terminaison URL de votre index. Le corps de la requête HTTP est un objet JSON contenant les documents à ajouter, à modifier ou à supprimer.

## I. Identifier la clé API d’administration de votre service Azure Search
Lors de l’émission de requêtes HTTP sur votre service à l’aide de l’API REST, *chaque* demande d’API doit inclure la clé API générée pour le service Search que vous avez configuré. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, vous devez vous connecter au [portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte à la fois des *clés d’administration* et des *clés de requête*.
  * Les *clés d’administration* principales et secondaires vous accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
  * Vos *clés de requête* vous accordent un accès en lecture seule aux index et documents ; elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Pour importer des données dans un index, vous pouvez utiliser votre clé d’administration principale ou secondaire.

## II. Déterminer l’action d’indexation à utiliser
Lorsque vous utilisez l’API REST, vous allez émettre des requêtes HTTP POST avec un corps de requête JSON à l’URL de point de terminaison de votre index Azure Search. L’objet JSON contenu dans le corps de la requête HTTP comporte un seul tableau JSON nommé « value », qui renferme les objets JSON représentant les documents que vous allez ajouter à votre index, mettre à jour ou supprimer.

Chaque objet JSON du tableau « value » représente un document à indexer. Chacun de ces objets contient les clés du document et spécifie l’action d’indexation souhaitée (téléchargement, fusion, suppression, etc.). Selon le type d’action que vous allez choisir, seuls certains champs doivent être inclus dans chaque document :

@search.action | Description | Champs requis pour chaque document | Remarques
--- | --- | --- | ---
`upload` | Une action `upload` est similaire à celle d’un « upsert », où le document est inséré s’il est nouveau et mis à jour/remplacé s’il existe déjà. | une clé, ainsi que tout autre champ que vous souhaitez définir | Lors de la mise à jour ou du remplacement d’un document existant, un champ qui n’est pas spécifié dans la requête sera défini sur la valeur `null`, y compris lorsque le champ a été précédemment défini sur une valeur non null.
`merge` | Met à jour un document existant avec les champs spécifiés. Si le document n’existe pas dans l’index, la fusion échoue. | une clé, ainsi que tout autre champ que vous souhaitez définir | N'importe quel champ que vous spécifiez dans une fusion remplace le champ existant dans le document. Cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ `tags` avec la valeur `["budget"]` et que vous exécutez une fusion avec la valeur `["economy", "pool"]` pour le champ `tags`, la valeur finale du champ `tags` sera `["economy", "pool"]`, et non `["budget", "economy", "pool"]`.
`mergeOrUpload` | Cette action est similaire à celle d’une action `merge` s’il existe déjà dans l’index un document comportant la clé spécifiée. Dans le cas contraire, elle exécutera une action `upload` avec un nouveau document. | une clé, ainsi que tout autre champ que vous souhaitez définir |- `delete` | Supprime le document spécifié de l’index. | une clé uniquement | Tous les champs que vous spécifiez seront ignorés, à l’exception du champ clé. Si vous souhaitez supprimer un champ d’un document, utilisez plutôt l’opération *merge* et définissez simplement le champ de manière explicite avec la valeur null.

## III. Construire votre requête HTTP et le corps de la requête
Maintenant que vous avez recueilli les valeurs de champ requises pour les actions de votre index, vous pouvez construire votre requête HTTP et le corps de requête JSON pour importer vos données.

#### Requête et en-têtes de requête
Dans l’URL, vous devez fournir le nom de votre service, le nom de l’index (« hotels » dans notre exemple) ainsi que la version d’API appropriée (la version actuelle de l’API est celle du `2015-02-28` au moment de la publication de ce document). Vous devez définir les en-têtes de requête `Content-Type` et `api-key`. Pour cette dernière, utilisez l’une des clés d’administration de votre service.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### Corps de la requête

```JSON
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "description_fr": "Meilleur hôtel en ville",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

Dans ce cas, nous utilisons `upload`, `mergeOrUpload` et `delete` comme actions de recherche.

Supposons que cet exemple d’index « hotels » contient déjà un certain nombre de documents. Notez que nous n’avons pas eu à spécifier tous les champs de document possibles en utilisant `merge` et que nous n’avons fait que spécifier la clé de document (`hotelId`) avec la commande `delete`.

Notez également que chaque requête d’indexation ne peut contenir que 1 000  documents (ou 16 Mo) maximum.

## IV. Comprendre votre code de réponse HTTP
#### 200
Lorsque votre demande d’indexation aboutit, vous recevez une réponse HTTP avec le code d’état `200 OK`. Le corps JSON de la réponse HTTP se présentera comme suit :

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### 207
Un code d’état `207` est renvoyé lorsqu’au moins un élément n’a pas été correctement indexé. Le corps JSON de la réponse HTTP contient des informations sur les documents ayant échoué.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Cela signifie généralement que la charge de votre service de recherche a atteint un point tel que les demandes d’indexation commencent à renvoyer des réponses `503`. Dans ce cas, nous vous recommandons vivement de désactiver votre code client et d’attendre avant d’effectuer une nouvelle tentative. En laissant au système le temps de récupérer, vous aurez davantage de chance de voir vos futures requêtes aboutir. Si vous renouvelez rapidement vos tentatives de requête, vous ne ferez que prolonger la situation.

#### 429
Le code d’état `429` est renvoyé lorsque vous avez dépassé votre quota de nombre de documents par index.

#### 503
Le code d’état `503` est renvoyé si aucun des éléments de la requête n’a été correctement indexé. Cette erreur signifie que le système est surchargé et que votre requête ne peut pas être traitée pour le moment.

> [AZURE.NOTE] Dans ce cas, nous vous recommandons vivement de désactiver votre code client et d’attendre avant d’effectuer une nouvelle tentative. En laissant au système le temps de récupérer, vous aurez davantage de chance de voir vos futures requêtes aboutir. Si vous renouvelez rapidement vos tentatives de requête, vous ne ferez que prolonger la situation.

Pour plus d’informations sur les actions de document et les réponses de réussite/d’erreur, consultez [cette page](https://msdn.microsoft.com/library/azure/dn798925.aspx). Pour plus d’informations sur les autres codes d’état HTTP pouvant être renvoyés en cas d’échec, consultez [cet article](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## Suivant
Une fois votre index Azure Search renseigné, vous pouvez commencer à exécuter des requêtes de recherche de documents.

<!---HONumber=AcomDC_0302_2016-->