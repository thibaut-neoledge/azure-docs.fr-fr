<properties
    pageTitle="Interroger votre index Azure Search à l’aide de l’API REST | Microsoft Azure | Service de recherche cloud hébergé"
    description="Créez une requête de recherche dans Azure Search et utilisez des paramètres de recherche pour filtrer et trier les résultats de recherche."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# Interroger votre index Azure Search à l’aide de l’API REST
> [AZURE.SELECTOR]
- [Vue d'ensemble](search-query-overview.md)
- [Portail](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Cet article explique comment interroger un index à l’aide de l’[API REST d’Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Avant de commencer cette procédure, vous devez déjà avoir [créé un index Azure Search](search-what-is-an-index.md) et y avoir [ajouté des données](search-what-is-data-import.md).

## I. Identifier la clé API de requête de votre service Azure Search
La *clé API* générée pour le service que vous avez configuré est un composant essentiel de chaque opération de recherche exécutée sur l’API REST Azure Search. L’utilisation d’une clé valide permet d’établir, en fonction de chaque demande, une relation de confiance entre l’application qui envoie la demande et le service qui en assure le traitement.

1. Pour accéder aux clés API de votre service, vous devez vous connecter au [portail Azure](https://portal.azure.com/)
2. Accédez au panneau de votre service Azure Search
3. Cliquez sur l’icône « Clés »

Votre service comporte à la fois des *clés d’administration* et des *clés de requête*.

 - Les *clés d’administration* principales et secondaires vous accordent des droits d’accès complets à toutes les opérations, avec notamment la possibilité de gérer le service ou de créer et supprimer des index, des indexeurs et des sources de données. Deux clés sont à votre disposition afin que vous puissiez continuer à utiliser la clé secondaire si vous décidez de régénérer la clé primaire et inversement.
 - Vos *clés de requête* vous accordent un accès en lecture seule aux index et documents ; elles sont généralement distribuées aux applications clientes qui émettent des demandes de recherche.

Dans le cadre de l’interrogation d’un index, vous pouvez utiliser l’une de vos clés de requête. Vos clés d’administration peuvent également vous servir pour exécuter des requêtes, mais il est recommandé d’utiliser une clé de requête dans votre code d’application, car cette approche respecte davantage le [principe du moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

## II. Formuler votre requête
Deux méthodes permettent d’effectuer une [recherche dans un index à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/dn798927.aspx). L’une consiste à émettre une requête HTTP POST dans laquelle vos paramètres de requête seront définis dans un objet JSON contenu dans le corps de la requête. L’autre méthode consiste à émettre une requête HTTP GET dans laquelle vos paramètres de requête seront définis à l’intérieur de l’URL de requête. Notez qu’une méthode POST comporte des [limites plus souples](https://msdn.microsoft.com/library/azure/dn798927.aspx) que la méthode GET quant à la taille des paramètres de requête. Pour cette raison, nous vous recommandons d’utiliser POST, à moins que la situation justifie l’utilisation de GET.

Pour les méthodes POST et GET, vous devez indiquer dans l’URL de la demande le *nom de votre service*, le *nom de l’index* ainsi que la *version d’API* appropriée (la version actuelle de l’API est celle du `2015-02-28` au moment de la publication de ce document). Pour GET, vous allez renseigner les paramètres de requête au niveau de la *chaîne de requête* à la fin de l’URL. Voici le format URL à utiliser :

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2015-02-28

La méthode POST suit un format identique, mais seule la version d’API figure dans les paramètres de chaîne de requête.



#### Exemples de requêtes

Voici quelques exemples de requêtes effectuées sur un index nommé « hotels ». Ces requêtes sont présentées aux formats GET et POST.

Rechercher le terme « budget » dans la totalité de l’index et retourner uniquement le champ `hotelName` :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=budget&$select=hotelName&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "budget",
    "select": "hotelName"
}
```

Appliquer un filtre à l’index pour trouver des hôtels à moins de 150 $ la nuit et retourner les champs `hotelId` et `description` :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

Effectuer une recherche dans l’ensemble de l’index, appliquer un tri sur un champ spécifique (`lastRenovationDate`) dans l’ordre décroissant, retenir les deux premiers résultats et afficher uniquement les champs `hotelName` et `lastRenovationDate` :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate&api-version=2015-02-28

POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
{
    "search": "*",
    "orderby": "lastRenovationDate desc",
    "select": "hotelName,lastRenovationDate",
    "top": 2
}
```

## III. Envoyer votre requête HTTP
Maintenant que vous avez formulé votre requête dans l’URL (pour GET) ou dans le corps (pour POST) de votre requête HTTP, vous pouvez définir vos en-têtes de requête et envoyer votre requête.

#### Requête et en-têtes de requête
Vous devez définir deux en-têtes de requête pour GET, ou trois en-têtes pour POST :
1. L’en-tête `api-key` doit être défini sur la clé de requête obtenue à l’étape I ci-dessus. Vous pouvez également utiliser une clé d’administration en tant qu’en-tête `api-key`, mais il est recommandé d’utiliser une clé de requête pour pouvoir obtenir exclusivement un accès en lecture seule aux index et aux documents.
2. L’en-tête `Accept` doit être défini sur `application/json`.
3. Pour la méthode POST uniquement, l’en-tête `Content-Type` doit également être défini sur `application/json`.

L’exemple ci-dessous illustre une requête HTTP GET permettant d’effectuer une recherche sur l’index « hotels » à l’aide de l’API REST Azure, en utilisant une simple requête qui recherche le terme « motel » :

```
GET https://[service name].search.windows.net/indexes/hotels/docs?search=motel&api-version=2015-02-28
Accept: application/json
api-key: [query key]
```

Voici le même exemple de requête, cette fois à l’aide de HTTP POST :

```
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2015-02-28
Content-Type: application/json
Accept: application/json
api-key: [query key]

{
    "search": "motel"
}
```

Une demande de requête réussie génère un code d’état `200 OK` et les résultats de recherche sont retournés au format JSON dans le corps de la réponse. Voici les résultats obtenus pour la requête ci-dessus, en supposant que l’index « hotels » contient les exemples de données fournis dans l’article [Importer des données dans Azure Search à l’aide de l’API REST](search-import-data-rest-api.md) (notez que JSON a été formaté pour plus de clarté).

```JSON
{
    "value": [
        {
            "@search.score": 0.59600675,
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "description_fr": "Hôtel le moins cher en ville",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags":["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": {
                "type": "Point",
                "coordinates": [-122.131577, 49.678581],
                "crs": {
                    "type":"name",
                    "properties": {
                        "name": "EPSG:4326"
                    }
                }
            }
        }
    ]
}
```

Pour plus d’informations, consultez la section « Réponse » de la page [Rechercher dans des documents](https://msdn.microsoft.com/library/azure/dn798927.aspx). Pour plus d’informations sur les autres codes d’état HTTP pouvant être renvoyés en cas d’échec, consultez la page [Codes d’état HTTP (Azure Search)](https://msdn.microsoft.com/library/azure/dn798925.aspx).

<!---HONumber=AcomDC_0831_2016-->