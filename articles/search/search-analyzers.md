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
ms.date: 09/11/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 70a869ac428efa0af93adbb5ee78ebc83a13a785
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="analyzers-in-azure-search"></a>Analyseurs dans Recherche Azure

Un *analyseur* est un composant de [recherche en texte intégral](search-lucene-query-architecture.md) chargé de traiter le texte des chaînes de requête et le contenu des documents indexés. Pendant l’indexation, un analyseur transforme le texte en jetons, qui sont écrits sous forme de termes dans l’index. Pendant la recherche, un analyseur effectue les mêmes transformations sur les termes de requête employés pour récupérer les documents avec les termes correspondants de l’index.

Les transformations suivantes se produisent généralement au cours d’une analyse :

+ Les mots non essentiels (mots vides) et la ponctuation sont supprimés.
+ Les expressions et les mots avec tirets sont segmentés en différents composants.
+ Les mots en majuscules sont mis en minuscules.
+ Seule la racine des mots est conservée pour qu’une correspondance soit trouvée, quels que soient le temps ou la forme utilisés.

La Recherche Azure fournit un analyseur par défaut. Vous pouvez le remplacer pour chaque champ par un autre analyseur. Cet article a pour objectif de décrire les différents analyseurs qu’il est possible d’utiliser et d’indiquer les bonnes pratiques pour personnaliser l’analyse lexicale pour un champ donné. Il montre également des exemples de configuration pour des scénarios clés.

## <a name="supported-analyzers"></a>Analyseurs pris en charge

La liste suivante contient les analyseurs pris en charge par la Recherche Azure.

| Catégorie | Description |
|----------|-------------|
| [Analyseur Lucene standard](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Par défaut. Aucune spécification ou configuration n’est nécessaire. Cet analyseur à usage général est efficace pour la plupart des scénarios et des langues.|
| Analyseurs prédéfinis | Proposés comme un produit fini destiné à être utilisé tel quel, avec une personnalisation limitée. <br/>Il en existe deux types : spécialisé et linguistique. Ils sont dits « prédéfinis », car vous les référencez par leur nom, sans personnalisation. <br/><br/>[Analyseurs spécialisés (non dépendants de la langue)](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable) : convient aux entrées de texte nécessitant un traitement spécialisé ou un traitement minimal. Les analyseurs prédéfinis qui ne dépendent pas de la langue sont les suivants : **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop**, **Whitespace**.<br/><br/>[Analyseurs linguistiques](https://docs.microsoft.com/rest/api/searchservice/language-support) : fournissent une prise en charge linguistique avancée pour différentes langues. La Recherche Azure prend en charge 35 analyseurs linguistiques Lucene et 50 analyseurs de traitement en langage naturel Microsoft. |
|[Analyseurs personnalisés](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Configuration définie par l’utilisateur d’une combinaison d’éléments existants, comprenant un générateur de jetons (obligatoire) et des filtres facultatifs (caractères ou jetons).|

Vous pouvez personnaliser un analyseur prédéfini, tel que **Pattern** ou **Stop**, pour utiliser les autres options qui sont documentées dans [Référence sur les analyseurs prédéfinis](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable). Seuls quelques analyseurs prédéfinis peuvent être configurés. Comme pour toute personnalisation, nommez votre nouvelle configuration (par exemple, *MonAnalyseurDePatterns*) pour la distinguer de l’analyseur de patterns Lucene.

## <a name="how-to-specify-analyzers"></a>Comment spécifier des analyseurs

1. Pour les analyseurs personnalisés uniquement, créez une section `analyzer` dans la définition de l’index. Pour plus d’informations, consultez [Créer un index](https://docs.microsoft.com/rest/api/searchservice/create-index) et [Analyseurs personnalisés > Créer](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer).

2. Pour chaque champ pouvant faire l’objet d’une recherche et sur lequel vous voulez utiliser l’analyseur, définissez la propriété `analyzer` sur le nom d’un analyseur cible dans la [définition du champ de l’index](https://docs.microsoft.com/rest/api/searchservice/create-index). Les valeurs valides sont les suivantes : un analyseur prédéfini, un analyseur linguistique ou un analyseur personnalisé défini précédemment dans le schéma de l’index.

  Au lieu de définir une seule propriété `analyzer`, vous pouvez également définir des analyseurs différents pour l’indexation et l’interrogation à l’aide des paramètres de champ `indexAnalyzer` et `searchAnalyzer`. 

3. L’analyse se produit pendant l’indexation. Si vous ajoutez un `analyzer` à un index existant, prenez note des étapes suivantes :
 
 | Scénario | Étapes |
 |----------|-------|
 | Ajout d’un nouveau champ (pas encore indexé). | Une analyse se produit quand vous ajoutez ou mettez à jour les documents qui fournissent le contenu du nouveau champ. Utilisez [Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index) et [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pour cette tâche.|
 | Ajout d’un analyseur à un champ indexé existant. | L’index inversé doit être entièrement recréé pour ce champ et le contenu de document de ce dernier doit être réindexé. <br/> <br/>Pour un index en cours de développement, [supprimez](https://docs.microsoft.com/rest/api/searchservice/delete-index) et [créez](https://docs.microsoft.com/rest/api/searchservice/create-index) l’index pour sélectionner la nouvelle définition de champ. <br/> <br/>Pour un index en production, vous devez créer un champ pour fournir la définition modifiée et commencer à l’utiliser. Utilisez [Mettre à jour l’index](https://docs.microsoft.com/rest/api/searchservice/update-index) et [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) pour incorporer le nouveau champ. Par la suite, pendant l’opération de maintenance planifiée de l’index, vous pouvez le nettoyer de façon à supprimer les champs obsolètes. |

## <a name="best-practices"></a>Meilleures pratiques

Cette section offre des conseils pour utiliser les analyseurs.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Un même analyseur pour les opérations de lecture-écriture, sauf besoins spécifiques

La recherche Azure vous permet de spécifier différents analyseurs pour l’indexation et la recherche via les paramètres de champ supplémentaires `indexAnalyzer` et `searchAnalyzer`. Par défaut, l’analyseur défini avec la propriété `analyzer` est utilisé pour l’indexation et la recherche. Si `analyzer` n’est pas spécifié, l’analyseur Lucene standard est utilisé par défaut.

En règle générale, il est préférable d’utiliser le même analyseur pour l’indexation et l’interrogation, sauf si des besoins spécifiques vous obligent à faire autrement. Veillez à effectuer des tests approfondis. Quand il existe une divergence de traitement de texte pendant la recherche et l’indexation, le risque est que les termes de la requête et les termes indexés ne correspondent pas si la configuration de l’analyseur de recherche et celle de l’analyseur d’indexation ne sont pas conformes.

### <a name="test-during-active-development"></a>Effectuer des tests pendant le développement actif

La substitution de l’analyseur standard nécessite une regénération de l’index. Si possible, choisissez les analyseurs à utiliser pendant le développement actif, avant de déployer l’index dans un environnement de production.

### <a name="compare-analyzers-side-by-side"></a>Comparer des analyseurs côte à côte

Nous vous recommandons d’utiliser [l’API Analyse](https://docs.microsoft.com/rest/api/searchservice/test-analyzer). La réponse se compose de jetons, qui sont générés par un analyseur spécifique pour le texte que vous fournissez. 

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

Les API comprennent des attributs d’index supplémentaires qui permettent de spécifier des analyseurs différents pour l’indexation et la recherche. Les attributs `searchAnalyzer` et `indexAnalyzer` doivent être spécifiés comme une paire, en remplacement de l’attribut `analyzer`.


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
           "indexAnalyzer":"whitespace",
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

