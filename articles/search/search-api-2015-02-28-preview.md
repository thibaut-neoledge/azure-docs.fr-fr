<properties
   pageTitle="Azure Search Service REST API Version 2015-02-28-Preview | Microsoft Azure | API Azure Search (version préliminaire)"
   description="L'API REST du service Azure Search version 2015-02-28-Preview comprend des fonctionnalités expérimentales telles que des analyseurs de langage naturel et des recherches moreLikeThis."
   services="search"
   documentationCenter="na"
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search"
   ms.date="03/08/2016"
   ms.author="brjohnst"/>

# API REST du service Azure Search : version 2015-02-28-Preview

Cet article constitue la documentation de référence de `api-version=2015-02-28-Preview`. Cette version préliminaire étend la version actuelle disponible, [api-version=2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), en fournissant les fonctionnalités expérimentales suivantes :

- Paramètre de requête `moreLikeThis` dans l’API [Search Documents](#SearchDocs). Il recherche d’autres documents correspondant à un autre document spécifique.

Certains éléments supplémentaires de l’API REST `2015-02-28-Preview` sont documentés séparément. Vous avez notamment vu les points suivants :

- [Profils de calcul de score](search-api-scoring-profiles-2015-02-28-preview.md)
- [Indexeurs](search-api-indexers-2015-02-28-preview.md)

Le service Azure Search est disponible dans plusieurs versions. Pour plus d'informations, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

## API dans ce document

L’API du service Azure Search prend en charge deux syntaxes d’URL pour les opérations d’API : simple et OData. Pour plus d’informations, consultez [Prise en charge d’OData (API Azure Search)](http://msdn.microsoft.com/library/azure/dn798932.aspx). La liste suivante présente la syntaxe simple.

[Création d'index](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Mise à jour d'index](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obtention d'index](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Liste des index](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obtention de statistiques d'index](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Suppression d'index](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Ajout, suppression et mise à jour de données dans un index](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Recherche dans des documents](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Recherche de document](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Nombre de documents](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Suggestions](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

________________________________________
<a name="IndexOps"></a>
## Opérations d'index

Vous pouvez créer et gérer des index dans le service Azure Search via de simples requêtes HTTP (POST, GET, PUT, DELETE) sur une ressource d'index donnée. Pour créer un index, vous commencez par PUBLIER un document JSON décrivant le schéma d'index. Le schéma définit les champs de l'index, leurs types de données et comment les utiliser (par exemple, dans des recherches en texte intégral, des filtres, des tris ou des facettes). Il définit également des profils de calcul de score, des générateurs de suggestions et d'autres attributs permettant de configurer le comportement de l'index.

L'exemple suivant illustre un schéma utilisé pour rechercher des informations sur des hôtels avec le champ Description défini en deux langues. Notez la façon dont les attributs contrôlent le mode d'utilisation du champ. Par exemple, `hotelId` est utilisé comme clé de document (`"key": true`) et est exclu des recherches en texte intégral (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	  {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Une fois que l'index est créé, vous allez télécharger les documents qui le remplissent. Pour cette étape, consultez [Ajout ou mise à jour de documents](#AddOrUpdateDocuments).

Pour obtenir une présentation vidéo de l'indexation dans Azure Search, consultez l'[épisode Cloud Cover : Azure Search sur Channel 9](http://go.microsoft.com/fwlink/p/?LinkId=511509).


<a name="CreateIndex"></a>
## Création d'index

Dans Azure Search, un index est le principal moyen d'organiser des documents et d'y faire des recherches, un peu comme une table permet d'organiser des enregistrements dans une base de données. Chaque index englobe un ensemble de documents tous conformes à un même schéma d'index (noms de champ, types de données et propriétés), mais il spécifie également des constructions supplémentaires (générateurs de suggestions, profils de calcul de score et options CORS) qui définissent d'autres comportements de recherche.

Vous pouvez créer un index dans un service Azure Search à l'aide d'une requête HTTP POST ou PUT. Le corps de la requête est un schéma JSON qui spécifie les informations de configuration et d'index.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Vous pouvez également utiliser une requête PUT en spécifiant le nom d'index sur l'URI. Si l'index n'existe pas, il est créé.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

La création d'un index détermine la structure des documents stockés et utilisés dans les opérations de recherche. Le remplissage de l'index est une opération distincte. Pour cette étape, vous pouvez utiliser un [indexeur](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponible pour les sources de données prises en charge) ou une opération [Ajout, mise à jour ou suppression de documents](https://msdn.microsoft.com/library/azure/dn798930.aspx). L'index inversé est généré lors de la publication des documents.

**Remarque** : le nombre maximal d'index que vous pouvez créer varie en fonction du niveau de tarification. Le service gratuit autorise jusqu'à 3 index. Le service standard autorise 50 index par service de recherche. Pour plus d'informations, consultez [Limites et contraintes](http://msdn.microsoft.com/library/azure/dn798934.aspx).

**Requête**

Le protocole HTTPS est requis pour toutes les requêtes de service. La requête **Create Index** peut être construite à l'aide d'une méthode POST ou PUT. Si vous utilisez une méthode POST, fournissez un nom d'index dans le corps de la requête, ainsi que la définition du schéma d'index. Avec la méthode PUT, le nom d'index fait partie de l'URL. Si l'index n'existe pas, il est créé. S'il existe déjà, il est mis à jour en fonction de la nouvelle définition.

Le nom d'index doit être en minuscules, commencer par une lettre ou un chiffre, ne contenir ni barres obliques ni points, et comprendre moins de 128 caractères. Après la lettre ou le chiffre du début, le nom d'index peut comprendre des lettres, des chiffres et des tirets (non consécutifs).

Le paramètre `api-version` est obligatoire. Pour obtenir la liste des versions disponibles, consultez [Contrôle de version du service Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `Content-Type` : obligatoire. À définir avec la valeur `application/json`
- `api-key` : obligatoire. L'en-tête `api-key` est utilisé pour
- authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour votre service. La requête **Create Index** doit inclure un en-tête `api-key` défini sur votre clé d'administration (par opposition à une clé de requête).

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

<a name="RequestData"></a> **Syntaxe du corps de la requête**

Le corps de la requête contient une définition de schéma qui inclut la liste des champs de données des documents qui alimenteront cet index, des types de données, des attributs, ainsi qu'une liste facultative de profils de calcul de score utilisés pour les documents correspondants au moment de la requête.

Notez que, pour une requête POST, vous devez spécifier le nom d'index dans le corps de la requête.

Il ne peut exister qu'un seul champ de clé dans l'index. Il doit s'agir d'un champ de chaîne. Ce champ représente l'identificateur unique de chaque document stocké dans l'index.

Les parties principales d'un index sont les suivantes :

- `name`
- `fields` qui alimenteront cet index, y compris le nom, le type de données et les propriétés qui définissent les actions autorisées sur ce champ.
- `suggesters` utilisés pour les requêtes prédictives ou avec saisie semi-automatique.
- `scoringProfiles` utilisés pour le classement personnalisé des scores de recherche. Pour plus d'informations, consultez [Ajout de profils de calcul de score ](https://msdn.microsoft.com/library/azure/dn798928.aspx).
- `defaultScoringProfile` utilisé pour remplacer les comportements de calcul de score par défaut.
- `corsOptions` pour autoriser les requêtes cross-origin sur votre index.

La syntaxe de structuration de la charge utile de la requête est la suivante. Un exemple de requête est fourni plus loin dans cette rubrique.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,		      
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Attributs d'index**

Lors de la création d'un index, les attributs suivants peuvent être définis. Pour plus d'informations sur le calcul de score et les profils de calcul de score, consultez [Ajout de profils de calcul de score](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name` -Définit le nom du champ.

`type` : définit le type de données pour le champ. Pour obtenir la liste des types pris en charge, consultez [Types de données pris en charge](#DataTypes).

`searchable` : indique que le champ peut faire l'objet d'une recherche en texte intégral. Cela signifie qu'il fera l'objet d'une analyse, par exemple lexicale, lors de l'indexation. Si vous définissez un champ `searchable` avec une valeur telle que « journée ensoleillée », cette valeur est fractionnée au niveau interne en jetons individuels « journée » et « ensoleillée ». Cela permet d'effectuer des recherches en texte intégral de ces termes. Les champs de type `Edm.String` ou `Collection(Edm.String)` sont `searchable` par défaut. Les autres types de champs ne peuvent pas être `searchable`.

  - **Remarque** : les champs `searchable` nécessitent davantage d'espace dans votre index, car Azure Search stocke une version supplémentaire tokenisée de la valeur du champ pour les recherches en texte intégral. Si vous voulez économiser de l'espace dans votre index et que vous n'avez pas besoin d'inclure un champ dans les recherches, définissez `searchable` avec la valeur `false`.

`filterable` : permet au champ d'être référencé dans les requêtes `$filter`. `filterable` diffère de `searchable` dans la manière dont sont gérées les chaînes. Les champs de type `Edm.String` ou `Collection(Edm.String)` qui sont `filterable` ne font pas l'objet d'une analyse lexicale, les comparaisons ne concernent donc que les correspondances exactes. Par exemple, si vous définissez un champ de type `f` avec la valeur « journée ensoleillée », `$filter=f eq 'sunny'` ne trouvera aucune correspondance, contrairement à `$filter=f eq 'sunny day'`. Tous les champs sont `filterable` par défaut.

`sortable` : par défaut, le système trie les résultats par score, mais souvent les utilisateurs voudront effectuer un tri par champs dans les documents. Les champs de type `Collection(Edm.String)` ne peuvent pas être `sortable`. Tous les autres champs sont `sortable` par défaut.

`facetable` : généralement utilisé dans une présentation des résultats de recherche qui inclut le nombre d'accès par catégorie (par exemple, vous recherchez des appareils photo numériques et regardez le nombre d'accès par marque, mégapixels, prix, etc.). Cette option ne peut pas être utilisée avec des champs de type `Edm.GeographyPoint`. Tous les autres champs sont `facetable` par défaut.

  - **Remarque** : les champs de type `Edm.String` qui sont `filterable`, `sortable` ou `facetable` ne doivent pas dépasser 32 Ko de longueur. En effet, ces champs sont traités en tant que terme de recherche unique, et la longueur maximale d'un terme dans Azure Search est 32 Ko. Si vous devez stocker plus de texte dans un champ de chaîne unique, définissez explicitement `filterable`, `sortable` et `facetable` avec la valeur `false` dans votre définition d'index.

  - **Remarque** : si aucun des attributs ci-dessus dans un champ n'est défini avec la valeur `true` (`searchable`, `filterable`, `sortable` ou `facetable`), le champ est exclu de l'index inversé. Cette option est utile pour les champs qui ne sont pas utilisés dans les requêtes, mais qui sont nécessaires dans les résultats de recherche. L'exclusion de ces champs de l'index améliore les performances.

`suggestions` : les versions précédentes de l'API incluaient une propriété `suggestions`. Cette propriété booléenne est maintenant déconseillée et n'est plus disponible dans `2015-02-28` ni `2015-02-28-Preview`. Utilisez l'[API Suggesters](#Suggesters) à la place. Dans la version `2014-07-31`, la propriété `suggestions` était utilisée pour spécifier si la saisie semi-automatique en cours de frappe pouvait être utilisée pour les champs de type `Edm.String` ou `Collection(Edm.String)`. La propriété `suggestions` avait la valeur `false` par défaut, car elle nécessitait un espace supplémentaire dans votre index mais, si vous l’avez activée, consultez [Transition de la version préliminaire vers la version générale dans Azure Search](search-transition-from-preview.md) pour obtenir des instructions sur la transition vers la nouvelle API.

`key` : indique que le champ contient des identificateurs uniques pour les documents de l'index. Un seul champ doit être choisi comme champ `key` et il doit être de type `Edm.String`. Les champs de clés peuvent servir à rechercher des documents directement via l'[API de recherche](#LookupAPI).

`retrievable` : définit si le champ peut être retourné dans un résultat de recherche. Cet attribut est utile quand vous voulez utiliser un champ (par exemple, la marge) comme mécanisme de filtre, de tri ou de score, mais que vous ne voulez pas qu'il soit visible par l'utilisateur final. Il doit être `true` pour les champs `key`.

`analyzer` : définit le nom de l’analyseur à utiliser pour le champ au moment de la recherche et de l’indexation. Pour connaître l’ensemble des valeurs autorisées, consultez la rubrique [Analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option ne peut être utilisée qu’avec les champs `searchable` et ne peut être associée à `searchAnalyzer` ou `indexAnalyzer`. Une fois l'analyseur choisi, il ne peut pas être modifié pour le champ.

`searchAnalyzer` : définit le nom de l’analyseur utilisé pour le champ au moment de la recherche. Pour connaître l’ensemble des valeurs autorisées, consultez la rubrique [Analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec les champs `searchable`. Elle doit être associée à `indexAnalyzer` et ne peut pas être associée à l’option `analyzer`. Cet analyseur peut être mis à jour sur un champ existant.

`indexAnalyzer` : définit le nom de l’analyseur utilisé pour le champ au moment de l’indexation. Pour connaître l’ensemble des valeurs autorisées, consultez la rubrique [Analyseurs](https://msdn.microsoft.com/library/mt605304.aspx). Cette option peut être utilisée uniquement avec les champs `searchable`. Elle doit être associée à `searchAnalyzer` et ne peut pas être associée à l’option `analyzer`. Une fois l'analyseur choisi, il ne peut pas être modifié pour le champ.

`suggesters` : définit le mode de recherche et les champs constituant la source du contenu pour obtenir des suggestions. Pour plus d'informations, consultez [Générateurs de suggestions](#Suggesters).

`scoringProfiles` : définit les comportements de calcul de score personnalisés qui vous permettent de choisir les éléments qui s'affichent en haut des résultats de recherche. Les profils de calcul de score sont constitués de pondérations et de fonctions de champ. Pour plus d'informations sur les attributs utilisés dans un profil de calcul de score, consultez [Ajout de profils de calcul de score ](https://msdn.microsoft.com/library/azure/dn798928.aspx).

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a> **Support multilingue**

Les champs pouvant faire l'objet d'une recherche subissent une analyse qui implique la plupart du temps une analyse lexicale, la normalisation du texte et le filtrage des termes. Par défaut, les champs pouvant faire l'objet d'une recherche dans Azure Search sont analysés par l'[Analyseur Apache Lucene Standard](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html), qui découpe le texte en éléments selon les règles de [« Segmentation du texte Unicode »](http://unicode.org/reports/tr29/). Par ailleurs, l'analyseur standard convertit tous les caractères en minuscules. Les documents indexés et les termes de recherche sont analysés pendant l'indexation et le traitement des requêtes.

Azure Search prend en charge l’indexation des champs dans plusieurs langues. Chaque langue requiert un analyseur de texte non standard qui tient compte des caractéristiques d'une langue donnée. Azure Search propose deux types d'analyseurs :

- 35 analyseurs pris en charge par Lucene.
- 50 analyseurs pris en charge par la technologie de traitement du langage naturel Microsoft propriétaire utilisée dans Office et Bing.

Certains développeurs peuvent préférer la solution open source, plus familière et simple de Lucene. Les analyseurs Lucene sont plus rapides, mais les analyseurs Microsoft bénéficient de fonctionnalités avancées, telles que la lemmatisation, la décomposition des mots (dans les langues comme l’allemand, le danois, le néerlandais, le suédois, le norvégien, l’estonien, le finnois, le hongrois, et le slovaque) et la reconnaissance d’entité (URL, courriers électroniques, dates, numéros). Si possible, vous devez comparer les analyseurs Microsoft et Lucene pour savoir lequel vous convient le mieux.

***Comparatif***

L'analyseur Lucene pour l'anglais est une extension de l'analyseur standard. Il supprime la marque du possessif (le « 's ») à la fin des mots, applique la recherche de radical conformément à l'[algorithme de recherche de radical de Porter](http://tartarus.org/~martin/PorterStemmer/) et supprime les [mots vides](http://en.wikipedia.org/wiki/Stop_words) de l'anglais.

En comparaison, l’analyseur Microsoft procède par lemmatisation plutôt que par recherche de radical. Cela signifie qu’il peut bien mieux gérer des mots infléchis et de formes irrégulières, ce qui donne des résultats de recherche plus pertinents (module espion 7 de [Présentation MVA d’Azure Search](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) pour plus de détails).

L’indexation avec les analyseurs Microsoft est en moyenne trois fois plus lente qu’avec leurs équivalents Lucene, en fonction de la langue. Les performances de recherche ne doivent pas être trop affectées pour les requêtes de taille moyenne.

***Configuration***

Pour chaque champ dans la définition d'index, vous pouvez affecter à la propriété `analyzer` un nom d'analyseur qui spécifie la langue et le fournisseur. Le même analyseur sera appliqué lors de l’indexation et de la recherche pour ce champ. Par exemple, vous pouvez avoir des champs distincts pour des descriptions d'hôtel en anglais, français et espagnol qui existent côte à côte dans le même index. Utilisez le [paramètre de requête « searchFields »](#SearchQueryParameters) pour spécifier le champ de langue spécifique à rechercher dans vos requêtes. Vous pouvez consulter des exemples de requêtes qui incluent la propriété `analyzer` dans [Recherche dans des documents](#SearchDocs).

***Liste d'analyseurs***

Voici la liste des langues prises en charge avec les noms d’analyseur Lucene et Microsoft.

<table style="font-size:12">
    <tr>
		<th>Langage</th>
		<th>Nom de l’analyseur Microsoft</th>
		<th>Nom de l’analyseur Lucene</th>
	</tr>
    <tr>
		<td>Arabe</td>
		<td>ar.microsoft</td>
		<td>ar.lucene</td>		
	</tr>
    <tr>
    	<td>Arménien</td>
		<td></td>
    	<td>hy.lucene</td>
  	</tr>
    <tr>
		<td>Bangla</td>
		<td>bn.microsoft</td>
		<td></td>
	</tr>
  	<tr>
    	<td>Basque</td>
		<td></td>
    	<td>eu.lucene</td>
    </tr>
  	<tr>
 		<td>Bulgare</td>
		<td>bg.microsoft</td>
    	<td>bg.lucene</td>
  	</tr>
  	<tr>
    	<td>Catalan</td>
    	<td>ca.microsoft</td>
		<td>ca.lucene</td>  		
  	</tr>
    <tr>
		<td>Chinois simplifié</td>
		<td>zh-Hans.microsoft</td>
		<td>zh-Hans.lucene</td>		
	</tr>
    <tr>
		<td>Chinois traditionnel</td>
		<td>zh-Hant.microsoft</td>
		<td>zh-Hant.lucene</td>		
	<tr>
    <tr>
		<td>Croate</td>
		<td>hr.microsoft</td>
		<td/></td>
	</tr>
    <tr>
		<td>Tchèque</td>
		<td>cs.microsoft</td>
		<td>cs.lucene</td>		
	</tr>    
    <tr>
		<td>Danois</td>
		<td>da.microsoft</td>
		<td>da.lucene</td>		
	</tr>    
    <tr>
		<td>Néerlandais</td>
		<td>nl.microsoft</td>
		<td>nl.lucene</td>	
	</tr>    
    <tr>
		<td>Français</td>		
		<td>en.microsoft</td>
		<td>en.lucene</td>		
	</tr>
    <tr>
		<td>Estonien</td>
		<td>et.Microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Finnois</td>
		<td>fi.microsoft</td>
		<td>fi.lucene</td>		
	</tr>    
    <tr>
		<td>Français</td>
		<td>fr.Microsoft</td>
		<td>fr.lucene</td>		
	</tr>
    <tr>
    	<td>Galicien</td>
	    <td></td>
		<td>gl.lucene</td>    	
  	</tr>
    <tr>
		<td>Allemand</td>
		<td>de.Microsoft</td>
		<td>de.lucene</td>		
	</tr>
    <tr>
		<td>Grec</td>
		<td>el.microsoft</td>
		<td>el.lucene</td>		
	</tr>
    <tr>
		<td>Goudjrati</td>
		<td>gu.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Hébreu</td>
		<td>he.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Hindi</td>
		<td>hi.microsoft</td>
		<td>hi.lucene</td>		
	</tr>
    <tr>
		<td>Hongrois</td>		
		<td>hu.microsoft</td>
		<td>hu.lucene</td>
	</tr>
    <tr>
		<td>Islandais</td>
		<td>is.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Indonésien (Bahasa)</td>
		<td>id.microsoft</td>
		<td>id.lucene</td>		
	</tr>
    <tr>
    	<td>Irlandais</td>
		<td></td>
      	<td>ga.lucene</td>
    </tr>
    <tr>
		<td>Italien</td>
		<td>it.microsoft</td>
		<td>it.lucene</td>		
	</tr>
    <tr>
		<td>Japonais</td>
		<td>ja.Microsoft</td>
		<td>ja.lucene</td>
		
	</tr>
    <tr>
		<td>Kannada</td>
		<td>ka.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Coréen</td>
		<td>ko.microsoft</td>
		<td>ko.lucene</td>
	</tr>
    <tr>
		<td>Letton</td>		
		<td>lv.microsoft</td>
		<td>lv.lucene</td>	
	</tr>
    <tr>
		<td>Lituanien</td>
		<td>lt.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Malayalam</td>
		<td>ml.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Malais (latin)</td>
		<td>ms.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Marathi</td>
		<td>mr.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Norvégien</td>
		<td>nb.Microsoft</td>
		<td>no.lucene</td>		
	</tr>
  	<tr>
    	<td>Persan</td>
		<td></td>
		<td>fa.lucene</td>    	
  	</tr>
    <tr>
		<td>Polonais</td>
		<td>pl.microsoft</td>
		<td>pl.lucene</td>		
	</tr>
    <tr>
		<td>Portugais (Brésil)</td>
		<td>pt-Br.microsoft</td>
		<td>pt-Br.lucene</td>		
	</tr>
    <tr>
		<td>Portugais (Portugal)</td>
		<td>pt-Pt.microsoft</td>		
		<td>pt-Pt.lucene</td>
	</tr>
    <tr>
		<td>Pendjabi</td>
		<td>pa.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Roumain</td>
		<td>ro.microsoft</td>
		<td>ro.lucene</td>
	</tr>
    <tr>
		<td>Russe</td>
		<td>ru.microsoft</td>
		<td>ru.lucene</td>	
	</tr>
    <tr>
		<td>Serbe (cyrillique)</td>
		<td>sr-cyrillic.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Serbe (latin)</td>
		<td>sr-latin.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Slovaque</td>
		<td>sk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Slovène</td>
		<td>sl.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Espagnol</td>
		<td>es.Microsoft</td>
		<td>es.lucene</td>
	</tr>
    <tr>
		<td>Suédois</td>
		<td>sv.microsoft</td>
		<td>sv.lucene</td>
	</tr>

    <tr>
		<td>Tamoul</td>
		<td>ta.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Télougou</td>
		<td>te.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Thaï</td>
		<td>th.microsoft</td>
		<td>th.lucene</td>
	</tr>
    <tr>
		<td>Turc</td>
		<td>tr.microsoft</td>
		<td>tr.lucene</td>		
	</tr>
    <tr>
		<td>Ukrainien</td>
		<td>uk.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Ourdou</td>
		<td>ur.microsoft</td>
		<td></td>
	</tr>
    <tr>
		<td>Vietnamien</td>
		<td>vi.microsoft</td>
		<td></td>
	</tr>
	<td colspan="3">En outre, Azure Search fournit des configurations d'analyseur sans langage spécifié</td>
    <tr>
		<td>Pliage ASCII standard</td>
		<td>standardasciifolding.lucene</td>
		<td>
		<ul>
			<li>Segmentation de texte Unicode (générateur de jetons standard)</li>
			<li>Filtre de pliage ASCII&#160;: convertit les caractères Unicode qui n'appartiennent pas au jeu des 127&#160;premiers caractères ASCII dans leurs équivalents ASCII. Cela est utile pour supprimer les signes diacritiques.</li>
		</ul>
		</td>
	</tr>
</table>

Tous les analyseurs dont les noms sont annotés avec <i>lucene</i> s'appuient sur les [analyseurs de langue d'Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). D’autres informations sur le filtre de pliage ASCII sont disponibles [ici](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Générateurs de suggestions**

Un `suggester` définit les champs d’un index qui sont utilisés pour prendre en charge la saisie semi-automatique dans les recherches. En général, les chaînes recherchées partielles sont envoyées à l’[API Suggestions](#Suggestions) pendant que l’utilisateur tape une requête de recherche, et l’API retourne un ensemble d’expressions suggérées. Un générateur de suggestions que vous définissez dans l’index détermine quels champs sont utilisés pour générer les termes de recherche type-ahead. Consultez [Générateurs de suggestion](#Suggesters) pour plus de détails sur la configuration.

**Profils de score**

Un `scoringProfile` définit les comportements de score personnalisés qui vous permettent de apparaître certains éléments plus haut dans les résultats de recherche. Les profils de calcul de score sont constitués de pondérations et de fonctions de champ. Pour les utiliser, vous spécifiez un profil par nom dans la chaîne de requête.

Un profil de score par défaut fonctionne en arrière-plan pour calculer un score de recherche pour chaque élément d’un jeu de résultats. Vous pouvez utiliser le profil de score interne et sans nom. Vous pouvez aussi définir `defaultScoringProfile` pour utiliser un profil personnalisé par défaut, appelé chaque fois qu’un profil personnalisé n’est pas spécifié dans la chaîne de requête.

Pour plus d’informations, consultez [Ajouter des profils de score à un index de recherche (API REST du Service Azure Search)](search-api-scoring-profiles-2015-02-28-preview.md).

**Options CORS**

Le code Javascript côté client ne peut pas appeler les API par défaut, car le navigateur empêche toutes les requêtes cross-origin. Activez CORS (partage des ressources cross-origin) en définissant l'attribut `corsOptions` pour autoriser les requêtes cross-origin dans l'index. Notez que, pour des raisons de sécurité, seules les API de requête prennent en charge CORS. Les options suivantes peuvent être définies pour CORS :

- `allowedOrigins` (obligatoire) : il s'agit d'une liste d'origines pouvant accéder à votre index. Cela signifie que le code Javascript distribué à partir de ces origines peut interroger votre index (en supposant qu'il fournisse la clé API appropriée). Chaque origine se présente généralement sous la forme `protocol://fully-qualified-domain-name:port`, bien que le port soit souvent omis. Consultez [cet article](http://go.microsoft.com/fwlink/?LinkId=330822) pour plus de détails.
 - Si vous voulez autoriser l'accès à toutes les origines, incluez `*` en tant qu'élément unique dans le tableau `allowedOrigins`. Notez que **cette pratique est déconseillée pour les services de recherche de production.** Toutefois, elle peut être utile à des fins de développement ou de débogage.
- `maxAgeInSeconds` (facultatif) : les navigateurs utilisent cette valeur pour déterminer la durée (en secondes) de mise en cache des réponses CORS préliminaires. Il doit s'agir d'un entier non négatif. Plus cette valeur est importante, meilleures sont les performances, mais plus il faut de temps pour que les modifications apportées à la stratégie CORS prennent effet. Si la valeur n'est pas définie, une durée par défaut de 5 minutes est utilisée.

<a name="CreateUpdateIndexExample"></a> **Exemple de corps de requête**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
	    {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer="fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Réponse**

Pour une requête correcte : « 201 Créé ».

Par défaut, le corps de la réponse contient le code JSON de la définition d'index qui a été créée. Si l'en-tête de la requête `Prefer` est défini avec la valeur `return=minimal`, le corps de la réponse est vide et le code d'état de réussite est « 204 Pas de contenu » au lieu de « 201 Créé ». Cela est vrai, quelle que soit la méthode utilisée (PUT ou POST) pour créer l'index.

**Notes**

Actuellement, la prise en charge des mises à jour de schéma d'index est limitée. Les mises à jour de schéma qui nécessitent une réindexation, par exemple la modification des types de champs, ne sont pas prises en charge pour le moment. De nouveaux champs peuvent être ajoutés à un index existant à tout moment, mais les champs existants ne peuvent pas être modifiés ni supprimés. Quand vous ajoutez un nouveau champ, tous les documents existants de l'index auront automatiquement une valeur null pour ce champ. Aucun espace de stockage supplémentaire n'est consommé jusqu'à ce que de nouveaux documents soient ajoutés à l'index.

<a name="Suggesters"></a>
## Générateurs de suggestions

La fonctionnalité de suggestions dans Azure Search est une fonctionnalité de requête type-ahead ou de saisie semi-automatique, qui fournit une liste de termes de recherche potentiels en réponse à des chaînes partielles entrées dans une zone de recherche. Vous avez probablement remarqué des suggestions de requête lors de l’utilisation de moteurs de recherche web commerciaux : la saisie de « NET » dans Bing génère une liste de termes pour « .NET 4.5 », « .NET Framework 3.5 », etc. Lorsque vous utilisez l’API REST du service de recherche, l’implémentation des suggestions dans une application Azure Search personnalisée requiert les éléments suivants :

- Activer les suggestions en ajoutant une construction de **générateur de suggestion** dans votre index, en donnant le nom, le mode de recherche et la liste des champs pour lesquels la recherche type-ahead est appelée. Par exemple, si vous spécifiez « cityName » comme champ de la source, la saisie de la chaîne de recherche partielle « Sea » affiche « Seattle », « Seaside » et « Seatac » (trois noms réels de ville) comme suggestions de requête pour l’utilisateur.

- Appeler les suggestions en invoquant l’[API Suggestions](#Suggestions) dans votre code d’application. En général, les chaînes de recherche partielles sont envoyées au service pendant que l’utilisateur entre une requête de recherche, et l’API retourne un ensemble d’expressions suggérées.

Cet article explique comment configurer un **générateur de suggestions**. Vous devez également examiner l’[API Suggestions](#Suggestions) pour plus d’informations sur l’utilisation d’un générateur de suggestions.

**Utilisation**

Les `Suggesters` sont créés dans l’index et fonctionnent de façon optimale quand ils sont utilisés pour suggérer des documents spécifiques plutôt que des expressions ou des termes isolés. Les champs les plus appropriés sont les titres, les noms et d’autres expressions relativement courtes qui peuvent identifier un élément. Les champs les moins efficaces sont les champs répétitifs, tels que les catégories et les balises, ou les champs très longs, tels que les champs des descriptions ou des commentaires.

Dans le cadre de la définition d’index, vous pouvez ajouter un générateur de suggestions unique à la collection `suggesters`. Les propriétés suivantes définissent un générateur de suggestions :

- `name` : nom du générateur de suggestions. Vous utilisez le nom du générateur de suggestions quand vous appelez l'API `suggest`.
- `searchMode` : stratégie utilisée pour rechercher des expressions candidates. Le seul mode actuellement pris en charge est `analyzingInfixMatching`, qui effectue une correspondance flexible des expressions en début ou au milieu des phrases.
- `sourceFields` : liste d’un ou de plusieurs champs constituant la source du contenu pour des suggestions. Seuls les champs de type `Edm.String` et `Collection(Edm.String)` peuvent être des sources pour des suggestions. Seuls les champs qui n’ont pas un analyseur de langue personnalisé défini peuvent être utilisés.

**Exemple de générateur de suggestions**

Un générateur de suggestions fait partie de l’index. Un seul générateur de suggestions peut exister dans la collection `suggesters` de la version actuelle, à côté de la collection des champs et de `scoringProfiles`.

		{
		  "name": "hotels",
		  "fields": [
		     . . .
		   ],
		  "suggesters": [
		    {
		    "name": "sg",
		    "searchMode": "analyzingInfixMatching",
		    "sourceFields: ["hotelName", "category"]
		    }
		  ],
		  "scoringProfiles": [
		     . . .
		  ]
		}

> [AZURE.NOTE]  Si vous avez utilisé la version préliminaire publique d’Azure Search, `suggesters` remplace une propriété booléenne (`"suggestions": false`) plus ancienne qui prenait en charge uniquement des suggestions de préfixe pour les chaînes courtes (de 3 à 25 caractères). Son remplacement, `suggesters`, prend en charge la correspondance infixe qui recherche des termes correspondants au début ou au milieu du contenu du champ, avec une meilleure tolérance pour les erreurs dans les chaînes recherchées. À partir de la version disponible sur le marché, il s'agit de la seule implémentation de l'API des suggestions. La propriété `suggestions` plus ancienne introduite dans `api-version=2014-07-31-Preview` continue de fonctionner dans cette version, mais n’est pas opérationnelle dans la version `2015-02-28` ou les version ultérieures d’Azure Search.

<a name="UpdateIndex"></a>
## Mise à jour d'index

Vous pouvez mettre à jour un index existant dans Azure Search à l'aide d'une requête HTTP PUT. Les mises à jour peuvent inclure l'ajout de nouveaux champs au schéma existant, la modification des options CORS et la modification des profils de calcul de score. Pour plus d'informations, consultez [Ajout de profils de calcul de score](https://msdn.microsoft.com/library/azure/dn798928.aspx). Vous spécifiez le nom de l'index à mettre à jour sur l'URI de la requête :

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Important :** la prise en charge des mises à jour de schéma d'index est limitée aux opérations qui ne nécessitent pas la reconstruction de l'index de recherche. Les mises à jour de schéma qui nécessitent une réindexation, par exemple la modification des types de champs, ne sont pas prises en charge pour le moment. De nouveaux champs peuvent être ajoutés à tout moment, mais les champs existants ne peuvent pas être modifiés ni supprimés. Il en va de même pour les `suggesters`. De nouveaux champs peuvent être ajoutés à un générateur de suggestions au moment où les champs sont ajoutés, mais les champs ne peuvent pas être supprimés des `suggesters` et des champs existants ne peuvent pas être ajoutés à des `suggesters`.

Quand vous ajoutez un nouveau champ à un index, tous les documents existants de l'index auront automatiquement une valeur null pour ce champ. Aucun espace de stockage supplémentaire n'est consommé jusqu'à ce que de nouveaux documents soient ajoutés à l'index.

**Requête**

Le protocole HTTPS est requis pour toutes les requêtes de service. La requête **Update Index** est construite à l'aide de HTTP PUT. Avec la méthode PUT, le nom d'index fait partie de l'URL. Si l'index n'existe pas, il est créé. S'il existe déjà, il est mis à jour en fonction de la nouvelle définition.

Le nom d'index doit être en minuscules, commencer par une lettre ou un chiffre, ne contenir ni barres obliques ni points, et comprendre moins de 128 caractères. Après la lettre ou le chiffre du début, le nom d'index peut comprendre des lettres, des chiffres et des tirets (non consécutifs).

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `Content-Type` : obligatoire. À définir avec la valeur `application/json`
- `api-key` : obligatoire. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour votre service. La requête **Update Index** doit inclure un en-tête `api-key` défini avec la valeur de votre clé d'administration (par opposition à une clé de requête).

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Syntaxe du corps de la requête**

Lors de la mise à jour d'un index existant, le corps doit inclure la définition de schéma d'origine et les nouveaux champs que vous ajoutez, ainsi que les profils de score modifiés et les options CORS, le cas échéant. Si vous ne modifiez pas les profils de score et les options CORS, vous devez inclure leur version d'origine, créée en même temps que l'index. En général, le meilleur modèle à utiliser pour les mises à jour est la récupération de la définition d'index avec une méthode GET, sa modification, puis sa mise à jour avec la méthode PUT.

La syntaxe de schéma utilisée pour créer un index est reproduite ici par commodité. Consultez la section [Création d'index](#CreateIndex) pour plus de détails.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
		  "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
			  "tag": {
				"tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Réponse**

Pour une requête correcte : « 204 Pas de contenu ».

Par défaut, le corps de la réponse est vide. Toutefois, si l'en-tête de la requête `Prefer` est défini avec la valeur `return=representation`, le corps de la réponse contient le code JSON pour la définition d'index mise à jour. Dans ce cas, le code d'état de réussite est « 200 OK ».

<a name="ListIndexes"></a>
## Liste des index

L'opération **List Indexes** retourne une liste des index actuellement utilisés dans votre service Azure Search.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Requête**

Le protocole HTTPS est requis pour toutes les requêtes de service. La requête **List Indexes** peut être construite à l'aide de la méthode GET.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `api-key` : obligatoire. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour votre service. La requête **List Indexes** doit inclure un en-tête `api-key` défini avec la valeur d'une clé d'administration (par opposition à une clé de requête).

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Aucun.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

Voici un exemple de corps de réponse :

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Notez que vous pouvez filtrer la réponse pour afficher uniquement les propriétés qui vous intéressent. Par exemple, si vous voulez uniquement une liste des noms d'index, utilisez l'option de requête OData `$select` :

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Dans ce cas, la réponse de l'exemple ci-dessus apparaît comme suit :

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Cette technique est utile pour économiser de la bande passante si votre service de recherche contient un grand nombre d'index.

<a name="GetIndex"></a>
## Obtention d'index

L'opération **Get Index** obtient la définition d'index auprès d'Azure Search.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Requête**

Le protocole HTTPS est requis pour les requêtes de service. La requête **Get Index** peut être construite à l'aide de la méthode GET.

Le [nom d’index] de l’URI de la requête spécifie l’index à retourner à partir de la collection d’index.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `api-key` : l'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour votre service. La requête **Get Index** doit inclure un en-tête `api-key` défini avec la valeur d'une clé d'administration (par opposition à une clé de requête).

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Aucun.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

Consultez l'exemple de document JSON dans [Création et mise à jour d'un index](#CreateUpdateIndexExample) pour obtenir un exemple de charge utile de la réponse.

<a name="DeleteIndex"></a>
## Suppression d'index

L'opération **Delete Index** supprime de votre service Azure Search un index et les documents associés. Vous pouvez obtenir le nom de l'index à partir du tableau de bord de service dans le portail Azure ou à partir de l'API. Consultez la section [List Indexes](#ListIndexes) pour plus d'informations.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Requête**

Le protocole HTTPS est requis pour les requêtes de service. La requête **Delete Index** peut être construite à l'aide de la méthode DELETE.

Le [nom d’index] de l’URI de la requête spécifie l’index à supprimer dans la collection d’index.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `api-key` : obligatoire. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour l'URL de votre service. La requête **Delete Index** doit inclure un en-tête `api-key` défini avec la valeur de votre clé d'administration (par opposition à une clé de requête).

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Aucun.

**Réponse**

Code d'état : 204 Pas de contenu est renvoyé en cas de réponse correcte.

<a name="GetIndexStats"></a>
## Obtenir des statistiques d'index

L'opération **Get Index Statistics** retourne d'Azure Search un nombre de documents pour l'index actuel, ainsi que l'utilisation du stockage.

	GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

**Requête**

Le protocole HTTPS est requis pour toutes les requêtes de services. La requête **Get Index Statistics** peut être construite à l'aide de la méthode GET.

Le [nom d’index] de l’URI de la requête indique au service de retourner les statistiques d’index pour l’index spécifié.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).


**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `api-key` : l'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour votre service. La requête **Get Index Statistics** doit inclure un en-tête `api-key` défini avec la valeur d'une clé d'administration (par opposition à une clé de requête).

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Aucun.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

Le corps de la réponse a le format suivant :

    {
      "documentCount": number,
	  "storageSize": number (size of the index in bytes)
    }

________________________________________
<a name="DocOps"></a>
## Opérations de document

Dans Azure Search, un index est stocké dans le cloud et rempli à l'aide de documents JSON que vous téléchargez sur le service. Tous les documents que vous téléchargez comprennent le corpus de vos données de recherche. Les documents contiennent des champs, dont certains sont tokenisés dans les termes de recherche à mesure qu'ils sont téléchargés. Le segment d'URL `/docs` dans l'API d'Azure Search représente la collection de documents d'un index. Toutes les opérations sur la collection, telles que le chargement, la fusion, la suppression ou l'interrogation de documents, sont effectuées dans un contexte d'index unique, les URL pour ces opérations commencent donc toujours par `/indexes/[index name]/docs` pour un nom d'index donné.

Votre code d’application doit générer des documents JSON à télécharger vers Azure Search ou vous pouvez utiliser un [indexeur](https://msdn.microsoft.com/library/dn946891.aspx) pour charger des documents si la source de données est la base de données SQL Azure ou DocumentDB. En règle générale, les index sont remplis à partir d'un jeu de données unique que vous fournissez.

Vous devez prévoir un document pour chaque élément dans lequel vous voulez effectuer la recherche. Une application de location de films peut avoir un document par film, une application vitrine peut avoir un document par référence, une application de formation en ligne peut avoir un document par cours, un cabinet de recherche peut avoir un document pour chaque document académique de son référentiel, etc.

Les documents sont constitués d'un ou de plusieurs champs. Les champs peuvent contenir du texte tokenisé par Azure Search dans des termes de recherche, ainsi que des valeurs non tokenisées ou non textuelles pouvant être utilisées dans des filtres ou des profils de calcul de score. Les noms, les types de données et les fonctionnalités de recherche prises en charge pour chaque champ sont déterminés par le schéma d'index. Un des champs de chaque schéma d'index doit être désigné en tant qu'ID, et chaque document doit avoir une valeur pour le champ d'ID qui identifie de façon unique ce document dans l'index. Tous les autres champs de document sont facultatifs et ont la valeur null par défaut en l'absence de spécification. Notez que les valeurs null n'occupent pas d'espace dans l'index de recherche.

Avant de pouvoir télécharger des documents, vous devez avoir déjà créé l'index sur le service. Consultez la section [Création d'index](#CreateIndex) pour plus d'informations sur cette première étape.

<a name="AddOrUpdateDocuments"></a>
## Ajout, mise à jour ou suppression de documents

Vous pouvez télécharger, fusionner, fusionner-ou-télécharger ou supprimer des documents à partir d'un index spécifié à l'aide de la requête HTTP POST. Pour un grand nombre de mises à jour, le traitement par lot des documents (jusqu'à 1 000 documents par lot ou 16 Mo par lot) est recommandé.

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Requête**

Le protocole HTTPS est requis pour toutes les requêtes de service. Vous pouvez télécharger, fusionner, fusionner-ou-télécharger ou supprimer des documents à partir d'un index spécifié à l'aide de la requête HTTP POST.

L’URI de la requête inclut le [nom d’index], qui spécifie l’index pour la publication des documents. Vous pouvez publier des documents uniquement dans un index à la fois.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `Content-Type` : obligatoire. À définir avec la valeur `application/json`
- `api-key` : obligatoire. L'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour votre service. La requête **Add Documents** doit inclure un en-tête `api-key` défini avec la valeur de votre clé d'administration (par opposition à une clé de requête).

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Le corps de la requête contient un ou plusieurs documents à indexer. Les documents sont identifiés par une clé unique. Chaque document est associé à une action : upload, merge, mergeOrUpload ou delete. Les demandes de téléchargement doivent inclure les données du document sous forme de paires clé/valeur.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [AZURE.NOTE] Les clés de document ne peuvent contenir que des lettres, des chiffres, des tirets (« - »), des traits de soulignement (« \_ ») et les signes égal (« = »). Pour plus d'informations, consultez les [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx).

**Actions de document**

- `upload` : une action de téléchargement est similaire à un « upsert », où le document est inséré s'il est nouveau et mis à jour/remplacé s'il existe déjà. Notez que tous les champs sont remplacés dans le cas d'une mise à jour.
- `merge` : la fusion met à jour un document existant avec les champs spécifiés. Si le document n'existe pas, la fusion échoue. N'importe quel champ que vous spécifiez dans une fusion remplace le champ existant dans le document. Cela inclut les champs de type `Collection(Edm.String)`. Par exemple, si le document contient un champ « balises » avec la valeur `["budget"]` et que vous exécutez une fusion avec la valeur `["economy", "pool"]` pour « balises », la valeur finale du champ « balises » est `["economy", "pool"]`. Elle n'est **pas** `["budget", "economy", "pool"]`.
- `mergeOrUpload` : se comporte comme `merge` si un document avec la clé spécifiée existe déjà dans l'index. Si le document n'existe pas, l'opération se comporte comme `upload` avec un nouveau document.
- `delete` : cette action supprime de l'index le document spécifié. Notez que tous les champs que vous spécifiez dans une opération `delete`, autre que le champ de clé, sont ignorés. Si vous souhaitez supprimer un champ individuel dans un document, utilisez plutôt `merge` et définissez simplement le champ de manière explicite avec la valeur `null`.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte, ce qui signifie que tous les éléments ont été indexés (comme indiqué par le champ « status » défini avec la valeur true pour tous les éléments) :

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": true,
          "errorMessage": null
        }
      ]
    }  

Code d'état : 207 est retourné quand au moins un élément n'a pas été indexé (comme indiqué par le champ « status » avec la valeur false pour les éléments qui n'ont pas été indexés) :

    {
      "value": [
        {
          "key": "unique_key_of_document",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later."
        }
      ]
    }  

La propriété `errorMessage` indique la raison de l'erreur d'indexation, si possible.

**Remarque** : si votre code client rencontre fréquemment une réponse 207, le système est peut-être en cours de chargement. Vous pouvez vous en assurer en vérifiant la propriété `errorMessage`. Si tel est le cas, nous vous recommandons de ***limiter les requêtes d'indexation***. Sinon, si le trafic d'indexation ne diminue pas, le système peut commencer à rejeter toutes les requêtes avec des erreurs 503.

Code d'état : 429 indique que vous avez dépassé votre quota du nombre de documents par index. Vous devez créer un autre index ou effectuer une mise à niveau pour bénéficier de limites de capacité supérieures.

**Exemple :**

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
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
________________________________________
<a name="SearchDocs"></a>
## Recherche dans des documents

Une opération **Search** est émise en tant que requête GET ou POST et spécifie les paramètres qui donnent les critères de sélection des documents correspondants.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Utilisation de POST au lieu de GET**

Lorsque vous utilisez HTTP GET pour appeler l’API de **recherche**, vous devez savoir que la longueur de l’URL de requête ne peut pas dépasser 8 Ko. Cela est généralement suffisamment pour la plupart des applications. Toutefois, certaines applications produisent des requêtes très volumineuses ou des expressions de filtre OData. Pour ces applications, il est recommandé d'utiliser HTTP POST, car cela permet d'obtenir des filtres et des requêtes plus volumineux que GET. Avec POST, le nombre de termes ou de clauses dans une requête est le facteur limitant, pas la taille de la requête brute, car la limite de la taille de la requête POST est proche de 16 Mo.

> [AZURE.NOTE] Bien que la limite de la taille de la requête POST est très importante, les requêtes de recherche et les expressions de filtre ne peuvent pas être arbitrairement complexes. Consultez [Syntaxe de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx) et [Syntaxe d'expression OData](https://msdn.microsoft.com/library/dn798921.aspx) pour plus d'informations sur les limites de la complexité des filtres et des requêtes de recherche. **Requête**

Le protocole HTTPS est requis pour les requêtes de service. La requête **Search** peut être construite à l’aide des méthodes GET ou POST.

L'URI de la requête spécifie l'index à interroger, pour tous les documents qui correspondent aux paramètres. Les paramètres sont spécifiés dans la chaîne de requête pour les requêtes GET et dans le corps de la requête pour les requêtes POST.

Pendant la création de requêtes GET, il est recommandé d’[encoder par URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) les paramètres de requête spécifiques pour appeler l’API REST directement. Pour les opérations de **recherche**, cela inclut :

- `$filter`
- `facet`
- `highlightPreTag`
- `highlightPostTag`
- `search`
- `moreLikeThis`

L'encodage des URL est recommandé uniquement pour les paramètres de requête ci-dessus. Si vous encodez par URL par inadvertance la chaîne de requête entière (tout ce qui figure après ?), les requêtes sont interrompues.

En outre, l'encodage des URL est nécessaire uniquement lors de l'appel direct de l'API REST à l'aide de GET. Aucun encodage des URL n’est nécessaire pendant l’appel de **Search** à l’aide de POST ou quand vous utilisez la [bibliothèque cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx) qui gère l’encodage des URL pour vous.

<a name="SearchQueryParameters"></a>**Paramètres de requête**

La requête **Search** accepte plusieurs paramètres qui fournissent les critères de la requête et qui spécifient également le comportement de la recherche. Vous fournissez ces paramètres dans la chaîne de requête de l’URL pendant l’appel de **Search** via GET et en tant que propriétés JSON dans le corps de la requête pendant l’appel de **Search** via POST. La syntaxe de certains paramètres est légèrement différente entre GET et POST. Ces différences sont indiquées ci-dessous :

`search=[string]` (facultatif) - le texte à rechercher. Tous les champs `searchable` sont interrogés par défaut, sauf si `searchFields` est spécifié. Lors de l'interrogation des champs `searchable`, le texte de recherche est tokenisé, plusieurs termes peuvent donc être séparés par un espace blanc (par exemple : `search=hello world`). Pour faire correspondre n'importe quel terme, utilisez `*` (ce qui peut être utile pour les requêtes de filtre booléen). L'omission de ce paramètre a le même effet que s'il est défini avec la valeur `*`. Pour obtenir des détails sur la syntaxe de recherche, consultez la section [Syntaxe de requête simple](https://msdn.microsoft.com/library/dn798920.aspx).

  - **Remarque** : les résultats peuvent parfois être surprenants lors de l'interrogation de champs `searchable`. Le générateur de jetons inclut une logique pour gérer les cas courants dans le texte anglais tels que les apostrophes, les virgules des nombres, etc. Par exemple, `search=123,456` correspond à un seul terme 123,456 plutôt qu'aux termes individuels 123 et 456, étant donné que les virgules sont utilisées comme séparateurs de milliers dans les grands nombres en anglais. Pour cette raison, nous vous recommandons d'utiliser un espace blanc au lieu des signes de ponctuation pour séparer les termes du paramètre `search`.

`searchMode=any|all` (facultatif, la valeur par défaut est `any`) : indique si certains ou l'ensemble des termes de recherche doivent correspondre pour que le document soit considéré comme une correspondance.

`searchFields=[string]` (facultatif) : liste des noms de champs séparés par des virgules dans lesquels rechercher le texte spécifié. Les champs cibles doivent être marqués comme `searchable`.

`queryType=simple|full` (facultatif, la valeur par défaut est `simple`) : lorsqu’il est défini sur « simple », le texte recherché est interprété à l’aide d’un langage de requête simple qui autorise des symboles tels que +, * et "". Les requêtes sont évaluées pour tous les champs pouvant faire l’objet d’une recherche (ou les champs indiqués dans `searchFields`) dans chaque document par défaut. Lorsque le type de requête a la valeur `full`, le texte recherché est interprété à l’aide du langage de requête Lucene qui autorise des recherches spécifiques aux champs et pondérées. Pour obtenir des détails sur les syntaxes de recherche, consultez les sections [Syntaxe de requête simple](https://msdn.microsoft.com/library/dn798920.aspx) et [Syntaxe de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx).
 
> [AZURE.NOTE] La recherche de plages dans le langage de requête Lucene n’est pas prise en charge au profit de $filter qui offre des fonctionnalités similaires.

`moreLikeThis=[key]` (facultatif) **Important :** cette fonctionnalité est uniquement disponible dans `2015-02-28-Preview`. Cette option ne peut pas être utilisée dans une requête qui contient le paramètre de recherche de texte, `search=[string]`. Le paramètre `moreLikeThis` trouve les documents qui sont similaires au document spécifié par la clé de document. Quand une requête de recherche est effectuée avec `moreLikeThis`, une liste de termes de recherche est générée en fonction de la fréquence et de la rareté des termes dans le document source. Ces termes sont ensuite utilisés pour effectuer la requête. Par défaut, le contenu de tous les champs `searchable` est pris en compte sauf si `searchFields` est utilisé pour restreindre les champs faisant l'objet de recherches.

`$skip=#` (facultatif) : nombre de résultats de recherche à ignorer. Ne peut pas être supérieur à 100 000. Si vous avez besoin d'analyser des documents dans l'ordre, mais que vous ne pouvez pas utiliser `$skip` en raison de cette limitation, utilisez plutôt `$orderby` sur une clé totalement ordonnée et `$filter` avec une requête de plage de données.

> [AZURE.NOTE] Pendant l’appel de **Search** à l’aide de POST, ce paramètre est nommé `skip` au lieu de `$skip`.

`$top=#` (facultatif) : nombre de résultats de recherche à récupérer. Ceci peut être utilisé conjointement avec `$skip` pour implémenter la pagination côté client des résultats de la recherche.

> [AZURE.NOTE] Lors de l’appel de **Search** à l’aide de POST, ce paramètre est nommé `top` au lieu de `$top`.

`$count=true|false` (facultatif, la valeur par défaut est `false`) : indique s'il faut extraire le nombre total de résultats. Ceci est le nombre de tous les documents qui correspondent aux paramètres `search` et `$filter` en faisant abstraction de `$top` et `$skip`. La valeur `true` peut avoir un impact sur les performances. Notez que le nombre retourné est une approximation.

> [AZURE.NOTE] Pendant l’appel de **Search** à l’aide de POST, ce paramètre est nommé `count` au lieu de `$count`.

`$orderby=[string]` (facultatif) : liste d'expressions séparées par des virgules selon lesquelles les résultats doivent être triés. Chaque expression peut être un nom de champ ou un appel à la fonction `geo.distance()`. Chaque expression peut être suivie par `asc` pour indiquer l'ordre croissant, et par `desc` pour indiquer l'ordre décroissant. La valeur par défaut est l'ordre croissant. Les liens seront rompus par les scores de correspondance des documents. Si aucun `$orderby` n'est spécifié, l'ordre de tri par défaut est décroissant par score de correspondance de documents. Il existe une limite de 32 clauses pour `$orderby`.

> [AZURE.NOTE] Pendant l’appel de **Search** à l’aide de POST, ce paramètre est nommé `orderby` au lieu de `$orderby`.

`$select=[string]` (facultatif) : liste de champs séparés par des virgules à récupérer. Si aucune valeur n'est spécifiée, tous les champs marqués comme récupérables dans le schéma sont inclus. Vous pouvez demander explicitement tous les champs en définissant ce paramètre avec la valeur `*`.

> [AZURE.NOTE] Pendant l’appel de **Search** à l’aide de POST, ce paramètre est nommé `select` au lieu de `$select`.

`facet=[string]` (zéro ou plus) : champ à utiliser pour les facettes. La chaîne peut éventuellement contenir des paramètres pour personnaliser les facettes exprimées sous forme de paires `name:value` séparées par des virgules. Les paramètres valides sont les suivants :

- `count` (nombre maximal de termes de facette ; la valeur par défaut est 10). Il n'y a pas de valeur maximale, mais les valeurs supérieures ont un impact négatif sur les performances, en particulier si le champ à facettes contient un grand nombre de termes uniques.
  - Par exemple : `facet=category,count:5` obtient les cinq premières catégories des résultats de facette.  
  - **Remarque** : si le paramètre `count` est inférieur au nombre de termes uniques, les résultats seront peut-être inexacts. Cela s'explique par la manière dont les requêtes de facettes sont distribuées entre les partitions. L'attribution d'une valeur supérieure pour `count` augmente généralement la précision du nombre de termes, mais au détriment des performances.
- `sort` (`count` pour effectuer un tri par nombre par ordre *décroissant*, `-count` pour effectuer un tri par nombre par ordre *croissant*, `value` pour effectuer un tri par valeur par ordre *croissant* ou `-value` pour effectuer un tri par valeur par ordre *décroissant*)
  - Par exemple : `facet=category,count:3,sort:count` obtient les trois premières catégories des résultats de facette triées par ordre décroissant du nombre de documents de chaque ville. Par exemple, si les trois premières catégories sont Budget, Motel et Luxe, que Budget a 5 accès, Motel en a 6 et Luxe en a 4, les compartiments apparaîtront dans l'ordre Motel, Budget et Luxe.
  - Par exemple : `facet=rating,sort:-value` génère des compartiments pour tous les classements possibles, triés par ordre décroissant des valeurs. Par exemple, si les classements vont de 1 à 5, les compartiments apparaissent dans l'ordre 5, 4, 3, 2, 1, quel que soit le nombre de documents qui correspond à chaque classement.
- `values` (valeur numérique délimitée une barre verticale ou valeurs `Edm.DateTimeOffset` qui spécifient un ensemble dynamique de valeurs d'entrée de facette)
  - Par exemple : `facet=baseRate,values:10|20` génère trois compartiments : un pour le taux de base compris entre 0 et 10 exclus, un de 10 à 20 exclus et un pour 20 et plus.
  - Par exemple : `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` génère deux compartiments : un pour les hôtels rénovés avant février 2010 et un pour les hôtels rénovés à partir du 1er février 2010.
- `interval` (intervalle d'entiers supérieur à 0 pour les nombres ou `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` pour les valeurs de date et heure)
  - Par exemple : `facet=baseRate,interval:100` génère des compartiments basés sur des plages de taux de base comprenant 100 valeurs. Par exemple, si les taux de base sont tous compris entre 60 dollars et 600 dollars, il y aura les compartiments suivants : 0-100, 100-200, 200-300, 300-400, 400-500 et 500-600.
  - Par exemple : `facet=lastRenovationDate,interval:year` génère un compartiment pour chaque année de rénovation des hôtels.
- `timeoffset` ([+-]hh:mm, [+-]hhmm, ou [+-]hh) `timeoffset` est facultatif. Il peut uniquement être associé à l'option `interval` et uniquement lorsqu'il est appliqué à un champ de type `Edm.DateTimeOffset`. La valeur spécifie le décalage horaire UTC à prendre en compte lors de la définition des limites de temps.
  - Par exemple : `facet=lastRenovationDate,interval:day,timeoffset:-01:00` utilise la limite de la journée qui commence à 01:00:00 UTC (minuit dans le fuseau horaire cible)
- **Remarque** : `count` et `sort` peuvent être combinés dans la même spécification de facette, mais ils ne peuvent pas être combinés avec `interval` ou `values`, et `interval` et `values` ne peuvent pas être combinés ensemble.
- **Remarque** : les facettes d'intervalle de date et d'heure sont calculées en fonction de l'heure UTC si `timeoffset` n'est pas spécifié. Par exemple : pour `facet=lastRenovationDate,interval:day`, la limite de la journée commence à 00:00:00 UTC. 

> [AZURE.NOTE] Lors de l’appel de **Search** à l’aide de POST, ce paramètre est nommé `facets` au lieu de `facet`. En outre, vous le spécifiez sous forme de tableau JSON de chaînes où chaque chaîne est une expression de facette distincte.

`$filter=[string]` (facultatif) : expression de recherche structurée avec une syntaxe OData standard. Consultez la section [Syntaxe d'expression OData](#ODataExpressionSyntax) pour plus d'informations sur le sous-ensemble de la grammaire d'expression OData pris en charge par Azure Search.

> [AZURE.NOTE] Pendant l’appel de **Search** à l’aide de POST, ce paramètre est nommé `filter` au lieu de `$filter`.

`highlight=[string]` (facultatif) : ensemble de noms de champ séparés par des virgules pour la mise en surbrillance des correspondances. Seuls les champs `searchable` peuvent être utilisés pour la mise en surbrillance des correspondances.

`highlightPreTag=[string]` (facultatif, la valeur par défaut est `<em>`) : balise de chaîne qui ajoute un préfixe aux mises en surbrillance des correspondances. Doit être défini avec `highlightPostTag`.

> [AZURE.NOTE] Lors de l’appel de **Search** à l’aide de GET, les caractères réservés dans l’URL doivent être codés en pourcentage (par exemple, %23 au lieu de #).

`highlightPostTag=[string]` (facultatif, la valeur par défaut est `</em>`) : balise de chaîne qui ajoute un élément aux mises en surbrillance des correspondances. Doit être défini avec `highlightPreTag`.

> [AZURE.NOTE] Lors de l’appel de **Search** à l’aide de GET, les caractères réservés dans l’URL doivent être codés en pourcentage (par exemple, %23 au lieu de #).

`scoringProfile=[string]` (facultatif) : nom d'un profil de calcul de score pour évaluer les scores de correspondance des documents afin de trier les résultats.

`scoringParameter=[string]` (zéro ou plus) : indique la valeur de chaque paramètre défini dans une fonction de calcul de score (par exemple, `referencePointParameter`) au format nom:valeur. Par exemple, si le profil de calcul de score définit une fonction avec un paramètre appelé « mylocation » l’option de chaîne de requête est &scoringParameter=mylocation:-122.2,44.8

> [AZURE.NOTE] Pendant l’appel de **Search** à l’aide de POST, ce paramètre est nommé `scoringParameters` au lieu de `scoringParameter`. En outre, vous le spécifiez sous forme de tableau JSON de chaînes où chaque chaîne est une paire nom:valeur distincte.

`minimumCoverage` (facultatif, valeur par défaut 100) : nombre compris entre 0 et 100 indiquant le pourcentage de l’index qui doit être couvert par une requête de recherche afin que la requête soit déclarée comme un succès. Par défaut, la totalité de l’index doit être disponible ou `Search` retourne le code d’état HTTP 503. Si vous définissez `minimumCoverage` et que `Search` réussit, le code HTTP 200 est retourné et inclut une valeur `@search.coverage` dans la réponse indiquant le pourcentage de l’index inclus dans la requête.

> [AZURE.NOTE] La définition de ce paramètre à une valeur inférieure à 100 peut être utile pour garantir la disponibilité de la recherche même pour les services ayant un seul réplica. Cependant, il n’y a pas de garantie que tous les documents correspondants seront présents dans les résultats de recherche. Si le rappel de recherche est plus important pour votre application que la disponibilité, il est préférable de laisser `minimumCoverage` avec 100 comme valeur par défaut.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Remarque : pour cette opération, la `api-version` est spécifiée comme paramètre de requête dans l’URL, que vous appeliez **Search** à l’aide de POST ou de GET.

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `api-key` : l'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour l'URL de votre service. La requête **Search** peut spécifier une clé d'administration ou une clé de requête pour `api-key`.

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Pour GET : aucun.

Pour POST :

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Continuation des réponses de recherche partielle**

Parfois, Azure Search ne peut pas rendre tous les résultats requis dans une seule réponse de recherche. Cela peut se produire pour différentes raisons, par exemple lorsque la requête nécessite un trop grand nombre de documents en ne spécifiant pas `$top` ou en spécifiant une valeur pour `$top` qui est trop grande. Dans ce cas, Azure Search inclura l’annotation `@odata.nextLink` dans le corps de réponse, ainsi que `@search.nextPageParameters` s’il s’agissait d’une demande POST. Vous pouvez utiliser les valeurs de ces annotations pour formuler une autre demande de recherche afin d’obtenir la partie suivante de la réponse de recherche. Il s’agit une ***continuation*** de la demande de recherche d’origine et les annotations sont généralement appelées ***jetons de continuation***. Consultez [l’exemple ci-dessous](#SearchResponse) pour plus d’informations sur la syntaxe de ces annotations et où elles apparaissent dans le corps de réponse.

Les raisons pour lesquelles Azure Search peut rendre des jetons de continuation sont liées à l’implémentation et susceptibles d’être modifiées. Les clients puissants doivent toujours être prêts à traiter des cas où des documents plus moins nombreux que prévu sont renvoyés et un jeton de continuation est inclus pour poursuivre la récupération des documents. Notez également que vous devez utiliser la même méthode HTTP que pour la demande d’origine pour pouvoir poursuivre. Par exemple, si vous avez envoyé une demande GET, toutes les demandes de continuation que vous envoyez doivent également utiliser GET (y compris pour POST).

<a name="SearchResponse"></a> **Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Exemples :**

Vous trouverez d'autres exemples dans la page [Syntaxe d'expression OData pour Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx).

1) Effectuer une recherche dans l'index trié par date par ordre décroissant.


    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "orderby": [ "lastRenovationDate desc" ]
    }

2) Dans une recherche à facettes, interroger l'index et récupérer des facettes pour des catégories, des classements, des balises, ainsi que des éléments avec une valeur baseRate comprise dans des plages spécifiques :


    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "category", "rating", "tags", "baseRate,values:80|150|220" ]
    }

3) À l'aide d'un filtre, limiter les résultats de la précédente requête à facettes une fois que l'utilisateur a cliqué sur le classement 3 et la catégorie « Motel » :


    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }

4) Dans une recherche à facettes, définir une limite supérieure sur des termes uniques retournés dans une requête. La valeur par défaut est 10, mais vous pouvez augmenter ou diminuer cette valeur à l'aide du paramètre `count` sur l'attribut `facet` :


    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "test",
      "facets": [ "city,count:5" ]
    }

5) Effectuer une recherche dans des champs spécifiques de l'index. Par exemple, un champ propre à la langue :


    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hôtel",
      "searchFields": [ "description_fr" ]
    }

6) Effectuer une recherche dans plusieurs champs de l'index. Par exemple, vous pouvez stocker et interroger des champs pouvant faire l'objet d'une recherche en plusieurs langues, le tout dans le même index. Si des descriptions en anglais et en français coexistent dans le même document, vous pouvez en retourner certaines ou la totalité dans les résultats de la requête :


	GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2015-02-28-Preview

	POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "hotel",
      "searchFields": [ "description", "description_fr" ]
    }

Notez que vous pouvez interroger uniquement un index à la fois. Ne créez pas plusieurs index pour chaque langue, sauf si vous prévoyez d'interroger un seul index à la fois.

7) Pagination : obtenir la 1re page d'éléments (la taille de la page est 10) :


    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 0,
      "top": 10
    }

8) Pagination : obtenir la 2e page d'éléments (la taille de la page est 10) :


    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "skip": 10,
      "top": 10
    }

9) Récupérer un ensemble spécifique de champs :


    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "*",
      "select": [ "hotelName", "description" ]
    }

10) Récupérer les documents correspondant à une expression de filtre spécifique


    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }

11) Effectuer une recherche dans l'index et retourner des fragments avec des mises en surbrillance des correspondances


    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "highlight": "description"
    }

12) Effectuer une recherche dans l'index et retourner des documents triés du plus proche au plus éloigné par rapport à un emplacement de référence


    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "orderby": [ "geo.distance(location, geography'POINT(-122.12315 47.88121)')" ]
    }

13) Effectuer une recherche dans l'index en supposant qu'il existe un profil de calcul de score appelé « geo » avec deux fonctions de calcul de score de distance : l'une définit un paramètre appelé « currentLocation » et l'autre un paramètre appelé « lastLocation »


    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation:-122.123,44.77233&scoringParameter=lastLocation:-121.499,44.2113&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "something",
      "scoringProfile": "geo",
      "scoringParameters": [ "currentLocation:-122.123,44.77233", "lastLocation:-121.499,44.2113" ]
    }

14) Rechercher des documents dans l’index à l’aide d’une [syntaxe de requête simple](https://msdn.microsoft.com/library/dn798920.aspx). Cette requête renvoie les hôtels où les champs pouvant faire l'objet d'une recherche contiennent les termes « confort » et « emplacement », mais pas « motel » :


    GET /indexes/hotels/docs?search=comfort +location -motel&searchMode=all&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "comfort +location -motel",
      "searchMode": "all"
    }

Notez l'utilisation de `searchMode=all` ci-dessus. L'ajout de ce paramètre remplace la valeur par défaut `searchMode=any`, ce qui garantit que `-motel` signifie « ET PAS » au lieu de « OU PAS ». Sans `searchMode=all`, vous obtenez « OU PAS » qui étend au lieu de limiter les résultats de recherche, et cela peut être contraire à l'intuition pour certains utilisateurs.

15) Rechercher des documents dans l’index à l’aide d’une [syntaxe de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx). Cette requête renvoie les hôtels où le champ de catégorie contient le terme « budget » et tous les champs pouvant faire l’objet d’une recherche contenant l’expression « récemment rénové ». Les documents contenant l’expression « récemment rénové » sont mieux classés en raison de la valeur de renforcement du terme (3)

    GET /indexes/hotels/docs?search=category:budget AND "recently renovated"^3&searchMode=all&api-version=2015-02-28-Preview&querytype=full

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview
    {
      "search": "category:budget AND "recently renovated"^3",
      "queryType": "full",
      "searchMode": "all"
    }

<a name="LookupAPI"></a>
## Lookup Document

L'opération **Lookup Document** récupère un document dans Azure Search. Cela est utile quand un utilisateur clique sur un résultat de recherche en particulier et que vous voulez rechercher des détails spécifiques sur ce document.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Requête**

Le protocole HTTPS est requis pour les requêtes de service. La requête **Lookup Document** peut être construite comme suit.

    GET /indexes/[index name]/docs/key?[query parameters]

Vous pouvez aussi utiliser la syntaxe traditionnelle OData pour la recherche de clé :

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

L’URI de la requête inclut un [nom d’index] et une [clé], qui spécifient le document à extraire de l’index. Vous ne pouvez obtenir qu'un seul document à la fois. Utilisez **Search** pour obtenir plusieurs documents dans une requête unique.

**Paramètres de requête**

`$select=[string]` (facultatif) : liste de champs séparés par des virgules à récupérer. Si la valeur n'est pas spécifiée ou est `*`, tous les champs marqués comme récupérables dans le schéma sont inclus dans la projection.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Remarque : pour cette opération, `api-version` est spécifié en tant que paramètre de requête.

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `api-key` : l'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour l'URL de votre service. La requête **Lookup Document** peut spécifier une clé d'administration ou une clé de requête pour `api-key`.

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Aucun.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Exemple**

Rechercher le document qui contient la clé « 2 »

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Rechercher le document qui contient la clé « 3 » à l'aide de la syntaxe OData :

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>
## Nombre de documents

L'opération **Count Documents** récupère le nombre de documents dans un index de recherche. La syntaxe `$count` fait partie du protocole OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Requête**

Le protocole HTTPS est requis pour les requêtes de service. La requête **Count Documents** peut être construite à l'aide de la méthode GET.

Le [nom d’index] dans l’URI de la requête indique au service de retourner le nombre de tous les éléments de la collection de documents de l’index spécifié.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs.

- `Accept` : cette valeur doit être définie sur `text/plain`.
- `api-key` : l'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour l'URL de votre service. La requête **Count Documents** peut spécifier une clé d'administration ou une clé de requête pour `api-key`.

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Aucun.

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

Le corps de la réponse contient la valeur du nombre sous forme d'entier en texte brut.

<a name="Suggestions"></a>
## Suggestions

L'opération **Suggestions** récupère des suggestions basées sur une entrée de recherche partielle. Elle est généralement utilisée dans les zones de recherche pour fournir des suggestions à mesure que les utilisateurs entrent des termes de recherche.

Les requêtes de suggestions visent à suggérer des documents cibles, le texte suggéré peut donc être répété si plusieurs documents candidats correspondent à la même entrée de recherche. Vous pouvez utiliser `$select` pour récupérer d'autres champs du document (y compris la clé de document) afin de savoir quel document est la source de chaque suggestion.

Une opération **Suggestions** est publiée en tant que requête GET ou POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Utilisation de POST au lieu de GET**

Lorsque vous utilisez HTTP GET pour appeler l'API **Suggestions**, vous devez savoir que la longueur de l'URL de requête ne peut pas dépasser 8 Ko. Cela est généralement suffisamment pour la plupart des applications. Toutefois, certaines applications produisent des requêtes très volumineuses, en particulier les expressions de filtre OData. Pour ces applications, il est recommandé d'utiliser HTTP POST, car cela permet d'obtenir des filtres plus volumineux que GET. Avec POST, le nombre de clauses dans un filtre est le facteur limitant, pas la taille de la chaîne du filtre brut, car la limite de la taille de la requête POST est proche de 16 Mo.

> [AZURE.NOTE] Bien que la limite de la taille de la requête POST est très importante, les expressions de filtre ne peuvent pas être arbitrairement complexes. Consultez [Syntaxe d’expression OData](https://msdn.microsoft.com/library/dn798921.aspx) pour plus d'informations sur les limites de la complexité des filtres.

**Requête**

Le protocole HTTPS est requis pour les requêtes de service. La requête **Suggestions** peut être construite à l’aide des méthodes GET ou POST.

L'URI de la requête spécifie le nom de l'index à interroger. Les paramètres, tels que le terme de recherche partiellement entré, sont spécifiés dans la chaîne de requête pour les requêtes GET et dans le corps de la requête pour les requêtes POST.

Pendant la création de requêtes GET, il est recommandé d’[encoder par URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) les paramètres de requête spécifiques pour appeler l’API REST directement. Pour les opérations **Suggestions**, cela inclut :

- `$filter`
- `highlightPreTag`
- `highlightPostTag`
- `search`

L'encodage des URL est recommandé uniquement pour les paramètres de requête ci-dessus. Si vous encodez par URL par inadvertance la chaîne de requête entière (tout ce qui figure après ?), les requêtes sont interrompues.

En outre, l'encodage des URL est nécessaire uniquement lors de l'appel direct de l'API REST à l'aide de GET. Aucun encodage des URL n’est nécessaire pendant l’appel de **Suggestions** à l’aide de POST, ou quand vous utilisez la [bibliothèque cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx) qui gère l’encodage des URL pour vous.

**Paramètres de requête**

La requête **Suggestions** accepte plusieurs paramètres qui fournissent les critères de la requête et qui spécifient également le comportement de la recherche. Vous fournissez ces paramètres dans la chaîne de requête de l’URL pendant l’appel de **Suggestions** via GET et en tant que propriétés JSON dans le corps de la requête pendant l’appel de **Suggestions** via POST. La syntaxe de certains paramètres est légèrement différente entre GET et POST. Ces différences sont indiquées ci-dessous :

`search=[string]` : texte de recherche à utiliser pour suggérer des requêtes. Doit comprendre 1 caractère au minimum et 100 caractères au maximum.

`highlightPreTag=[string]` (facultatif) : balise de chaîne qui ajoute un préfixe aux résultats de la recherche. Doit être défini avec `highlightPostTag`.

> [AZURE.NOTE] Pendant l’appel de **Suggestions** à l’aide de GET, les caractères réservés dans l’URL doivent être codés en pourcentage (par exemple, %23 au lieu de #).

`highlightPostTag=[string]` (facultatif) : balise de chaîne qui ajoute un élément aux résultats de la recherche. Doit être défini avec `highlightPreTag`.

> [AZURE.NOTE] Pendant l’appel de **Suggestions** à l’aide de GET, les caractères réservés dans l’URL doivent être codés en pourcentage (par exemple, %23 au lieu de #).

`suggesterName=[string]` : nom du générateur de suggestions, comme spécifié dans la collection `suggesters` qui fait partie de la définition d'index. Un `suggester` détermine les champs qui sont analysés pour les termes de requête suggérés. Pour plus d'informations, consultez [Générateurs de suggestions](#Suggesters).

`fuzzy=[boolean]` (facultatif, valeur par défaut = false) : quand elle est définie avec la valeur true, cette API trouve des suggestions même si un caractère est remplacé ou manquant dans le texte de recherche. Bien qu'il en résulte une meilleure expérience, dans certains cas cela a un impact négatif sur les performances, car les recherches de suggestions approximatives sont plus lentes et consomment davantage de ressources.

`searchFields=[string]` (facultatif) : liste des noms de champs séparés par des virgules dans lesquels rechercher le texte spécifié. Les champs cibles doivent être activés pour les suggestions.

`$top=#` (facultatif, valeur par défaut = 5) : nombre de suggestions à récupérer. Doit être un nombre compris entre 1 et 100.

> [AZURE.NOTE] Pendant l’appel de **Suggestions** à l’aide de POST, ce paramètre est nommé `top` au lieu de `$top`.

`$filter=[string]` (facultatif) : expression qui filtre les documents considérés comme des suggestions.

> [AZURE.NOTE] Pendant l’appel de **Suggestions** à l’aide de POST, ce paramètre est nommé `filter` au lieu de `$filter`.

`$orderby=[string]` (facultatif) : liste d'expressions séparées par des virgules selon lesquelles les résultats doivent être triés. Chaque expression peut être un nom de champ ou un appel à la fonction `geo.distance()`. Chaque expression peut être suivie par `asc` pour indiquer l'ordre croissant, et par `desc` pour indiquer l'ordre décroissant. La valeur par défaut est l'ordre croissant. Il existe une limite de 32 clauses pour `$orderby`.

> [AZURE.NOTE] Pendant l’appel de **Suggestions** à l’aide de POST, ce paramètre est nommé `orderby` au lieu de `$orderby`.

`$select=[string]` (facultatif) : liste de champs séparés par des virgules à récupérer. Si aucune valeur n’est spécifiée, seuls la clé du document et le texte de suggestion sont retournés. Vous pouvez demander explicitement tous les champs en définissant ce paramètre avec la valeur `*`.

> [AZURE.NOTE] Pendant l’appel de **Suggestions** à l’aide de POST, ce paramètre est nommé `select` au lieu de `$select`.

`minimumCoverage` (facultatif, valeur par défaut 80) : nombre compris entre 0 et 100 indiquant le pourcentage de l’index qui doit être couvert par une requête de suggestions afin que la requête soit déclarée comme un succès. Par défaut, au moins 80 % de l’index doit être disponible ou `Suggest` retourne le code d’état HTTP 503. Si vous définissez `minimumCoverage` et que `Suggest` réussit, le code HTTP 200 est retourné et inclut une valeur `@search.coverage` dans la réponse indiquant le pourcentage de l’index inclus dans la requête.

> [AZURE.NOTE] La définition de ce paramètre à une valeur inférieure à 100 peut être utile pour garantir la disponibilité de la recherche même pour les services ayant un seul réplica. Cependant, il n’y a pas de garantie que toutes les suggestions correspondantes seront présentes dans les résultats. Si le rappel est plus important pour votre application que la disponibilité, il est préférable de ne pas baisser `minimumCoverage` en-dessous de sa valeur par défaut 80.

`api-version=[string]` (obligatoire). La version préliminaire est `api-version=2015-02-28-Preview`. Pour plus d'informations, y compris sur d'autres versions, consultez [Contrôle de version de service Azure Search](http://msdn.microsoft.com/library/azure/dn864560.aspx).

Remarque : pour cette opération, la `api-version` est spécifiée comme paramètre de requête dans l’URL, que vous appeliez **Suggestions** à l’aide de POST ou de GET.

**En-têtes de requête**

La liste suivante décrit les en-têtes de requête obligatoires et facultatifs

- `api-key` : l'en-tête `api-key` est utilisé pour authentifier la requête auprès de votre service de recherche. Il s'agit d'une valeur de chaîne, unique pour l'URL de votre service. La requête **Suggestions** peut spécifier une clé d'administration ou une clé de requête pour `api-key`.

Vous avez également besoin du nom du service pour construire l'URL de requête. Vous pouvez obtenir le nom du service et l'en-tête `api-key` à partir de votre tableau de bord de service dans le portail Azure. Pour obtenir de l'aide sur la navigation dans les pages, consultez [Création d'un service Azure Search dans le portail](search-create-service-portal.md).

**Corps de la requête**

Pour GET : aucun.

Pour POST :

    {
      "filter": "odata_filter_expression",
	  "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
	  "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Réponse**

Code d'état : 200 OK est retourné pour une réponse correcte.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Si l'option de projection est utilisée pour récupérer des champs, ils sont inclus dans chaque élément du tableau :

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Exemple**

Récupérer 5 suggestions pour lesquelles l'entrée de recherche partielle est « lux »

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }

<!------HONumber=AcomDC_0309_2016-->