---
title: Filtres dans Recherche Azure | Microsoft Docs
description: "Filtrez par identité de sécurité d’utilisateur, langue, emplacement géographique ou valeurs numériques pour réduire les résultats de recherche des requêtes effectuées dans Recherche Azure, service de recherche dans le cloud hébergé sur Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/19/2017
ms.author: heidist
ms.openlocfilehash: 2e8721684b1d4ed0e7392d85ea1df0f595860a05
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="filters-in-azure-search"></a>Filtres dans Recherche Azure 

Un *filtre* établit des critères pour la sélection des documents sur lesquels doit porter une requête de Recherche Azure. Une recherche non filtrée inclut tous les documents figurant dans l’index. Un filtre détermine l’étendue d’une requête de recherche en limitant celle-ci à un sous-ensemble de documents. Par exemple, un filtre peut restreindre une recherche en texte intégral à des produits d’une marque ou d’une couleur spécifiques, commercialisés à des niveaux de prix supérieurs à un seuil défini.

Certaines expériences de recherche imposent des exigences de filtre dans le cadre de leur implémentation, mais vous pouvez utiliser des filtres à chaque fois que vous souhaitez limiter la recherche à l’aide de critères *basés sur des valeurs* (par exemple, en définissant l’étendue de la recherche sur le type de produit « livres » de la catégorie « non-fiction » publiés par « Simon & Schuster »).

Si, au lieu de cela, votre objectif est une recherche ciblée sur des *structures* de données spécifiques (par exemple, en définissant l’étendue de la recherche sur un champ contenant des avis de clients), il existe des méthodes alternatives décrites ci-dessous.

## <a name="when-to-use-a-filter"></a>Quand utiliser un filtre

Certains filtres sont fondamentaux pour diverses expériences de recherche. C’est notamment le cas des filtres de « recherche à proximité », de navigation par facettes et de sécurité, qui montrent uniquement les documents qu’un utilisateur est autorisé à afficher. Si vous implémentez l’une de ces expériences, un filtre est requis. C’est le filtre associé à la requête de recherche qui fournit les coordonnées de géolocalisation, la catégorie de facettes sélectionnée par l’utilisateur, ou l’ID de sécurité du demandeur.

Voici des exemples de scénarios :

1. Utilisez un filtre pour découper votre index sur la base de valeurs de données figurant dans l’index. À partir d’un schéma donné, avec une ville, un type de logement et des commodités, vous pouvez créer un filtre permettant de sélectionner explicitement les documents correspondant à vos critères (par exemple, Marseille, appartement, front de mer). 

  Une recherche en texte intégral portant sur les mêmes entrées produit souvent des résultats similaires, mais un filtre est plus précis car il requiert une correspondance exacte entre la condition de filtre et le contenu de votre index. 

2. Utilisez un filtre si l’expérience de recherche intègre une condition de filtre :

 * La [navigation par facettes](search-faceted-navigation.md) utilise un filtre pour re-transmettre la catégorie de facettes sélectionnée par l’utilisateur.
 * La recherche basée sur la géolocalisation utilise un filtre pour transmettre les coordonnées de la localisation actuelle dans des applications de « recherche à proximité ». 
 * Les filtres de sécurité transmettent des identificateurs de sécurité en tant que critères de filtre, où une correspondance dans l’index sert de proxy pour les droits d’accès au document.

3. Utilisez un filtre si vous souhaitez appliquer des critères de recherche sur un champ numérique. 

  Les champs numériques sont récupérables dans le document et peuvent apparaître dans des résultats de recherche, mais ils ne peuvent pas faire l’objet individuellement d’une recherche en texte intégral. Si vous avez besoin de critères de sélection basés sur des données numériques, utilisez un filtre.

### <a name="alternative-methods-for-reducing-scope"></a>Autres méthodes de réduction de l’étendue de recherche

Si vous souhaitez restreindre vos résultats de recherche, les filtres ne sont pas le seul choix possible. Les solutions alternatives suivantes peuvent être plus appropriées selon votre objectif :

 + Le paramètre de requête `searchFields` permet de focaliser une recherche sur des champs spécifiques. Par exemple, si votre index comprend des champs distincts pour les descriptions en anglais et en espagnol, le paramètre searchFields vous permet de cibler les champs à utiliser pour une recherche en texte intégral. 

+ Le paramètre `$select` permet de spécifier les champs à inclure dans un jeu de résultats, ce qui a pour effet de réduire la réponse avant l’envoi de celle-ci à l’application appelante. Ce paramètre n’affine pas la requête et ne réduit pas la collection de documents. En revanche, si votre objectif est d’obtenir une réponse granulaire, ce paramètre constitue une option à envisager. 

Pour plus d’informations sur ces deux paramètres, voir [Rechercher des documents > Demande > Paramètres de requête](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtres dans le pipeline de requête

Au moment de la requête, un analyseur de filtre accepte les critères en entrée, convertit l’expression en expressions booléennes atomiques et génère un arbre de filtre qui est ensuite évalué sur les champs filtrables dans un index.  

Le filtrage se produit avant la recherche. Il permet de qualifier les documents à inclure dans le traitement en aval pour la récupération de documents et la notation de leur pertinence. En association avec une chaîne de recherche, le filtre réduit efficacement la surface d’exposition de l’opération de recherche suivante. Utilisé seul (par exemple, lorsque la chaîne de requête est vide, où `search=*`), le critère de filtre est la seule entrée. 

## <a name="filter-definition"></a>Définition de filtre

Les filtres sont des expressions OData, articulées à l’aide d’un [sous-ensemble de la syntaxe OData V4 prise en charge dans Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Vous pouvez spécifier un filtre pour chaque opération de **recherche**, mais le filtre lui-même peut inclure plusieurs champs, plusieurs critères et, si vous utilisez une fonction **ismatch**, plusieurs expressions. Dans une expression de filtre comportant plusieurs parties, vous pouvez spécifier des prédicats dans n’importe quel ordre. Vous n’obtenez aucun gain sensible des performances si vous tentez de réorganiser les prédicats dans une séquence particulière.

La limite inconditionnelle sur une expression de filtre est la limite maximale définie sur la demande. La demande entière, filtre inclus, peut être un maximum de 16 Mo pour la commande POST ou de 8 Ko pour la commande GET. Les limites conditionnelles correspondent au nombre de clauses dans votre expression de filtre. Une règle empirique est que, si vous avez des centaines de clauses, vous risquez d’atteindre la limite. Nous vous recommandons de concevoir votre application de telle sorte qu’elle ne génère pas de filtres de taille illimitée.

Les exemples suivants illustrent des définitions de filtre prototypiques dans plusieurs API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

```

## <a name="filter-design-patterns"></a>Modèles de conception de filtre

Les exemples suivants illustrent plusieurs modèles de conception pour des scénarios de filtre. Pour d’autres idées, voir [Syntaxe d’expression OData > Exemples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ **$filter** autonome, sans chaîne de requête, utile lorsque l’expression de filtre est en mesure de qualifier complètement les documents d’intérêt. À défaut de chaîne de requête, il n’y a ni analyse lexicale ou linguistique, ni notation, ni classement. Vous pouvez remarquer que la chaîne de recherche est vide.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Combinaison de chaîne de requête et de **$filter**, où le filtre crée le sous-ensemble, et la chaîne de requête fournit les entrées de condition de recherche en texte intégral sur le sous-ensemble filtré. L’utilisation d’un filtre avec une chaîne de requête est le modèle de code le plus courant.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Requêtes composées, séparées par « OR » (ou), chacune avec ses propres critères de filtre (par exemple, « beagle » dans « chien » ou « siamois » dans « chat »). Les expressions séparées par OR sont évaluées individuellement, les réponses à chacune d’elles étant combinées dans une seule réponse renvoyée à l’application appelante. Ce modèle de conception est obtenu via la fonction search.ismatch. Vous pouvez utiliser la version sans notation (search.ismatch) ou la version avec notation (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Pour obtenir des instructions complètes sur des cas d’usage spécifiques, consultez les articles suivants :

+ [Filtres de facette](search-filters-facets.md)
+ [Filtres de langage](search-filters-language.md)
+ [Filtrage de sécurité](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Conditions requises des champs pour le filtrage

Dans l’API REST, la propriété filterable (filtrable) est *activée* par défaut. Les champs filtrables augmentent la taille de l’index. Veillez à définir `filterable=FALSE` pour les champs que vous ne prévoyez pas réellement d’utiliser dans un filtre. Pour plus d’informations sur les paramètres des définitions de champ, voir [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Créer un index).

Dans le Kit de développement logiciel (SDK) .NET, la propriété filterable (filtrable) est *désactivée* par défaut. L’API pour définir la propriété filterable (filtrable) [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). Dans l’exemple ci-dessous, elle est paramétrée sur la définition de champ BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Conditions requises de réindexation

Si un champ n’est pas filtrable et si vous souhaitez le rendre filtrable, vous devez ajouter un champ ou régénérer le champ existant. La modification d’une définition de champ a pour effet de modifier la structure physique de l’index. Dans Recherche Azure, tous les chemins d’accès autorisés sont indexés pour accélérer les requêtes, ce qui nécessite une régénération des structures de données en cas de modification des définitions de champ. 

La régénération de champs individuels peut être une opération de faible incidence, ne nécessitant qu’une opération de fusion qui envoie la clé de document existante et les valeurs associées à l’index, laissant intact le reste de chaque document. Si vous rencontrez une condition de régénération, consultez les liens suivants pour obtenir des instructions :

 + [Actions d’indexation à l’aide du Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Actions d’indexation à l’aide de l’API REST](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Notions de base concernant les filtres de texte

Les filtres de texte sont valides pour des champs de chaîne dont vous voulez extraire une collection arbitraire de documents en fonction de valeurs définies dans un corpus de recherche.

Les filtres de texte composés de chaînes n’appliquant aucune analyse lexicale ou césure de mots, les comparaisons portent uniquement sur des correspondances exactes. Par exemple, supposons un champ *f* contenant les mots « Sunny day » (journée ensoleillée). `$filter=f eq 'Sunny day'` sera une correspondance, mais pas `$filter=f eq 'Sunny'`. 

Les chaînes de texte respectent la casse. Il n’y a pas de conversion en minuscules des mots contenant des majuscules. Ainsi, la chaîne `$filter=f eq 'Sunny day'` ne permet pas de trouver « sunny day ».


| Approche | Description | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Fonction fournissant un liste de chaînes délimitées par des virgules pour un champ donné. Les chaînes comprennent des critères de filtre qui sont appliqués à tous les champs figurant dans l’étendue de la requête. <br/><br/>La syntaxe `search.in(f, ‘a, b, c’)` équivaut sémantiquement à `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, à ceci près qu’elle s’exécute beaucoup plus rapidement lorsque la liste des valeurs est volumineuse.<br/><br/>Nous recommandons l’usage de la fonction **search.in** pour les [filtres de sécurité](search-security-trimming-for-azure-search.md) et, pour tous les filtres composés de texte brut, d’établir une correspondance sur des valeurs d’un champ donné. L’objectif de cette approche est la vitesse. Vous pouvez vous attendre à un temps de réponse inférieur à la seconde pour des centaines de milliers de valeurs. S’il n’existe aucune limite explicite au nombre d’éléments que vous pouvez transmettre à la fonction, la latence augmente proportionnellement au nombre de chaînes que vous fournissez. | 
| [Search.IsMatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Fonction permettant de combiner des opérations de recherche en texte intégral avec des opérations de filtre strictement booléen dans une même expression de filtre. Elle permet d’utiliser plusieurs combinaisons de filtre de requête dans une seule requête. Vous pouvez également l’utiliser pour un filtre *contains* afin de filtrer sur une chaîne partielle figurant à l’intérieur d’une chaîne de plus grande taille. |  
| [$filter=field operator string](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Expression définie par l’utilisateur composée de champs, d’opérateurs et de valeurs. | 

## <a name="numeric-filter-fundamentals"></a>Notions de base concernant les filtres numériques

Les champs numériques ne sont pas de type `searchable` (il n’est pas possible d’y effectuer une recherche) dans le contexte d’une recherche en texte intégral. Seules des chaînes peuvent faire l’objet d’une recherche en texte intégral. Par exemple, le terme de recherche 99,99 ne permet pas d’obtenir la liste des articles dont le prix est 99,99. Au lieu de cela, vous obtenez la liste des documents dont des champs de chaîne contiennent le nombre 99. Par conséquent, si vous avez des données numériques, l’hypothèse est que vous allez les utiliser pour des filtres, dont des plages, des facettes, des groupes, etc. 

Les documents contenant des champs numériques (prix, taille, référence, ID, etc.) fournissent ces valeurs dans les résultats de recherche si ces champs sont marqués en tant que `retrievable`. Ce qu’il importe de souligner ici, c’est que la recherche en texte intégral proprement dite ne s’applique pas aux champs de type numérique.

## <a name="next-steps"></a>Étapes suivantes

Tout d’abord, essayez l’**Explorateur de recherche** dans le portail pour soumettre des requêtes avec des paramètres de **$filter**. L’[index d’exemple immobilier](search-get-started-portal.md) fournit des résultats intéressants pour les requêtes filtrées suivantes lorsque vous les collez dans la barre de recherche :

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Pour utiliser d’autres exemples, voir [Syntaxe d’expression de filtre OData > Exemples](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Voir aussi

+ [Fonctionnement de la recherche en texte intégral dans la Recherche Azure](search-lucene-query-architecture.md)
+ [API REST de recherche de documents](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Syntaxe de requête Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Types de données prises en charge](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
