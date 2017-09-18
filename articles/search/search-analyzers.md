---
title: Analyseurs de la Recherche Azure | Microsoft Docs
description: "Attribuez des analyseurs aux champs de texte d’un index pouvant faire l’objet d’une recherche, afin de remplacer l’instance de Lucene standard par défaut par des solutions alternatives personnalisées, prédéfinies ou spécifiques à une langue."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/03/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 6e6c4491b8f66011340d1246495dbded7caf2903
ms.contentlocale: fr-fr
ms.lasthandoff: 09/05/2017

---

# <a name="analyzers-in-azure-search"></a>Analyseurs dans Recherche Azure

Un *analyseur* est un composant de [traitement de recherche en texte intégral](search-lucene-query-architecture.md) qui est chargé des conversions de « texte en jetons » pour les charges de travail d’indexation et de requête. Lors de l’indexation, un analyseur transforme le texte en jetons (unités lexicales), qui sont ensuite écrits dans l’index. Au moment de la requête, un analyseur effectue les mêmes transformations (de texte en unités lexicales), mais cette fois, pour les opérations de lecture. 

Les transformations suivantes se produisent généralement au cours d’une analyse :

+ Les mots non essentiels (mots vides) et la ponctuation sont supprimés.
+ Les expressions et les termes avec tirets sont décomposés.
+ Les termes sont mis en minuscules.
+ Seule la racine des mots est conservée pour qu’une correspondance soit trouvée, quels que soient le temps ou la forme utilisés.

La Recherche Azure fournit un analyseur par défaut. Vous pouvez le remplacer pour chaque champ par l’une des options proposées. Cet article a pour objectif de décrire les différents analyseurs qu’il est possible d’utiliser, et de fournir les bonnes pratiques relatives à l’ajout d’un analyseur à vos opérations de recherche. Il montre également des exemples de configuration d’analyseur pour des scénarios clés.

## <a name="how-analysis-fits-into-full-text-search-processing"></a>Rôle de l’analyse dans le traitement de la recherche en texte intégral

Les analyseurs opèrent sur les entrées de termes passées par le parser de requête, et retournent les termes analysés qui sont ensuite ajoutés à un objet d’arborescence de requête.

 ![Diagramme d’architecture de requête Lucene dans la recherche Azure][1]

Les analyseurs sont utilisés uniquement sur les requêtes à terme unique et les requêtes d’expression. Ils ne sont pas utilisés pour les types de requêtes comprenant des termes incomplets : requête de préfixe, requête de caractère générique, requête d’expression régulière ou requête partielle. Pour ces types de requêtes, les termes sont ajoutés directement à l’arborescence de requête, sans passer par la phase d’analyse. La seule transformation effectuée sur les termes de requête de ce type est l’utilisation de minuscules.

## <a name="supported-analyzers"></a>Analyseurs pris en charge

La liste suivante contient les analyseurs pris en charge par la Recherche Azure.

| Catégorie | Description |
|----------|-------------|
| [Analyseur Lucene standard](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Par défaut. Utilisé automatiquement pour l’indexation et les requêtes. Aucune spécification ou configuration n’est nécessaire. Cet analyseur à usage général est efficace pour la plupart des scénarios et des langues.|
| Analyseurs prédéfinis | Proposés comme un produit fini destiné à être utilisé tel quel, avec une personnalisation limitée. <br/>Il en existe deux types : spécialisé et linguistique. Ils sont dits « prédéfinis », car vous les référencez par leur nom, sans personnalisation. <br/><br/>[Analyseurs spécialisés (non dépendants de la langue)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) : convient aux entrées de texte nécessitant un traitement spécialisé ou un traitement minimal. Les analyseurs prédéfinis qui ne dépendent pas de la langue sont les suivants : **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop**, **Whitespace**.<br/><br/>[Analyseurs linguistiques](https://docs.microsoft.com/rest/api/searchservice/language-support) : fournissent une prise en charge linguistique avancée pour différentes langues. La Recherche Azure prend en charge 35 analyseurs linguistiques Lucene et 50 analyseurs de traitement en langage naturel Microsoft. |
|[Analyseurs personnalisés](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Configuration définie par l’utilisateur d’une combinaison d’éléments existants, comprenant un générateur de jetons (obligatoire) et des filtres facultatifs (caractères ou jetons).|

Vous pouvez personnaliser un analyseur prédéfini, tel que **Pattern** ou **Stop**, pour utiliser les autres options qui sont documentées dans [Référence sur les analyseurs prédéfinis](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Seuls quelques analyseurs prédéfinis peuvent être configurés. Comme pour toute personnalisation, nommez votre nouvelle configuration (par exemple, *MonAnalyseurDePatterns*) pour la distinguer de l’analyseur de patterns Lucene.

## <a name="how-to-specify-analyzer"></a>Comment spécifier un analyseur

1. Pour les analyseurs personnalisés, créez une définition `analyzer` pour l’index. Pour plus d’informations, consultez [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index) et [Analyseurs personnalisés > Créer](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Pour chaque champ sur lequel vous souhaitez utiliser l’analyseur, définissez la propriété `analyzer` sur le nom d’un analyseur cible dans une définition de champ [ de l’index ](https://docs.microsoft.com/rest/api/searchservice/create-index). Les valeurs valides sont les suivantes : un analyseur prédéfini, un analyseur linguistique ou un analyseur personnalisé défini précédemment dans le schéma de l’index.

 Au lieu de définir une seule propriété `analyzer`, vous pouvez également définir des analyseurs différents pour l’indexation et l’interrogation à l’aide des paramètres de champ `indexAnalyzer` et `searchAnalyzer`. 

3. Regénérez l’index pour appeler les nouveaux comportements de traitement de texte.

## <a name="best-practices"></a>Meilleures pratiques

Cette section fournit des conseils pour utiliser plus efficacement les analyseurs.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Un même analyseur pour les opérations de lecture-écriture, sauf besoins spécifiques

La recherche Azure vous permet de spécifier différents analyseurs pour l’indexation et la recherche via les paramètres de champ supplémentaires `indexAnalyzer` et `searchAnalyzer`. Par défaut, l’analyseur défini avec la propriété `analyzer` est utilisé pour l’indexation et la recherche. Si `analyzer` n’est pas spécifié, l’analyseur Lucene standard est utilisé par défaut.

En règle générale, il est préférable d’utiliser le même analyseur pour l’indexation et l’interrogation, sauf si des besoins spécifiques vous obligent à faire autrement. En effet, il est plus efficace que l’analyseur qui crée le jeton soit le même que celui qui recherche les jetons au moment de la requête. 

### <a name="test-during-active-development"></a>Effectuer des tests pendant le développement actif

La substitution de l’analyseur standard nécessite une regénération de l’index. Si possible, choisissez les analyseurs à utiliser pendant le développement actif, avant de déployer l’index dans un environnement de production.

### <a name="compare-analyzers-side-by-side"></a>Comparer des analyseurs côte à côte

Nous vous recommandons d’utiliser [l’API Analyse](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). La réponse comprend des termes sous forme d’unités lexicales, qui sont générés par l’analyseur pour le texte que vous spécifiez. 

> [!Tip]
> La [démonstration de l’analyseur Recherche](http://alice.unearth.ai/) présente une comparaison côte à côte de l’analyseur Lucene standard, de l’analyseur linguistique anglais de Lucene et de l’outil de traitement en langage naturel anglais de Microsoft. Pour chaque recherche, les résultats de chaque analyseur sont affichés dans des volets adjacents.

## <a name="examples"></a>Exemples

Les exemples ci-dessous montrent des définitions d’analyseur pour quelques scénarios clés.

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>Exemple 1 : Options personnalisées

Cet exemple montre une définition d’analyseur avec des options personnalisées. Les options personnalisées pour les filtres de caractères, les générateurs de jetons et les filtres de jetons sont spécifiées séparément comme des constructions nommées, puis elles sont référencées dans la définition de l’analyseur. Les éléments prédéfinis sont utilisés tels quels et sont référencés par leur nom.

Si nous suivons cet exemple :

* Les analyseurs sont une propriété de la classe d’un champ pouvant faire l’objet d’une recherche.
* Un analyseur personnalisé fait partie d’une définition d’index. Il peut faire l’objet d’une légère personnalisation (par exemple, la personnalisation d’une option d’un filtre) ou d’une personnalisation à plusieurs endroits.
* Dans ce cas, l’analyseur personnalisé est « my_analyzer », qui utilise alors le générateur de jetons standard personnalisé « my_standard_tokenizer », et deux filtres de jetons : le filtre « lowercase » et le filtre asciifolding personnalisé « my_asciifolding ».
* Il définit également le filtre de caractères personnalisé « map_dash » pour remplacer tous les tirets par des traits de soulignement avant la segmentation du texte en unités lexicales (les tirets provoquent l’arrêt du générateur de jetons, mais pas les traits de soulignement).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>Exemple 2 : Remplacer l’analyseur par défaut

L’analyseur standard est celui qui est utilisé par défaut. Supposons que vous souhaitiez remplacer l’analyseur par défaut par un autre analyseur prédéfini, tel que l’analyseur de pattern. Si vous ne définissez pas d’options personnalisées, vous devez uniquement le spécifier par son nom dans la définition du champ.

L’élément « analyzer » remplace l’analyseur standard champ après champ. Aucun remplacement global n’est possible. Dans cet exemple, `text1` utilise l’analyseur de pattern, et `text2`, qui ne spécifie pas d’analyseur, utilise celui par défaut.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>Exemple 3 : Des analyseurs différents pour les opérations d’indexation et de recherche

Les API en préversion comprennent des attributs d’index supplémentaires qui permettent de spécifier des analyseurs différents pour l’indexation et la recherche. Les attributs `searchAnalyzer` et `indexAnalyzer` doivent être spécifiés comme une paire, en remplacement de l’attribut `analyzer`.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>Exemple 4 : Analyseur linguistique

Les champs qui contiennent des chaînes dans différentes langues peuvent utiliser un analyseur linguistique, tandis que les autres champs conservent l’analyseur par défaut (ou utilisent un autre analyseur prédéfini ou personnalisé). Si vous utilisez un analyseur linguistique, vous devez l’utiliser à la fois pour les opérations d’indexation et de recherche. Les champs qui utilisent un analyseur linguistique ne peuvent pas avoir des analyseurs différents pour l’indexation et la recherche.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "IndexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>Étapes suivantes

+ Lisez notre explication détaillée du [Fonctionnement de la recherche en texte intégral dans Recherche Azure](search-lucene-query-architecture.md). Cet article utilise des exemples pour expliquer les comportements qui, au premier abord, peuvent sembler contraires à la logique.

+ Essayez une syntaxe de requête supplémentaire à partir de la section Exemple [Rechercher des documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#examples) ou à partir de la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) dans l’Explorateur de recherche du portail.

+ Découvrez comment appliquer des [analyseurs lexicaux propres au langage](https://docs.microsoft.com/rest/api/searchservice/language-support).

+ [Configurez des analyseurs personnalisés](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) pour un traitement minimal ou pour un traitement spécialisé sur certains champs.

+ [Comparez les analyseurs standard et les analyseurs anglais](http://alice.unearth.ai/) dans des volets adjacents sur ce site web de démonstration. 

## <a name="see-also"></a>Voir aussi

 [API REST de recherche de documents](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Syntaxe de requête complète Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [Traiter les résultats de recherche](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png

