<properties
	pageTitle="Analyseurs personnalisés (API REST Azure Search version 2015-02-28-Preview) | Microsoft Azure"
	description="Analyseurs personnalisés (API REST Azure Search version 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# Analyseurs (API REST Azure Search version 2015-02-28-Preview)

> [AZURE.NOTE] La prise en charge des analyseurs personnalisés est actuellement en version préliminaire. Pour tirer parti de cette fonctionnalité, vous devez utiliser la version 2015-02-28-Preview de l’API REST du service Azure Search. Notez que les fonctionnalités préliminaires ne sont actuellement pas ajoutées au Kit de développement logiciel (SDK) .NET. L’utilisation de la version préliminaire de l’API REST est donc la seule option de programmation pour le moment.

## Vue d'ensemble

En termes simples, le rôle d’un moteur de recherche en texte intégral est de traiter et de stocker des documents afin de permettre une interrogation et une récupération efficaces. À un niveau élevé, tout cela se résume à extraire les mots importants des documents, à les placer dans un index, puis à utiliser ce dernier pour rechercher les documents qui correspondent aux mots d’une requête donnée. Le processus d’extraction de mots des documents et de requêtes de recherche est appelé analyse lexicale. Les composants qui effectuent une analyse lexicale sont appelés des analyseurs. Dans Azure Search, vous pouvez choisir parmi un ensemble d’[analyseurs sans langue prédéfinie](#Analyzers) et d’[analyseurs propres à une langue](https://msdn.microsoft.com/library/azure/dn879793.aspx). Vous pouvez également définir vos propres analyseurs personnalisés. Un analyseur personnalisé vous permet de contrôler le processus de conversion de texte en jetons d’indexation/de recherche. Il s’agit d’une configuration définie par l’utilisateur constituée d’un seul générateur de jetons prédéfini et d’un ou plusieurs filtres de jeton. Le générateur de jetons est chargé de segmenter le texte en jetons tandis que les filtres de jeton modifient les jetons émis par le générateur de jetons.

Les scénarios courants permis par les analyseurs personnalisés sont les suivants :

- Recherche phonétique : ajoutez un filtre phonétique pour permettre les recherches basées sur le son d’un mot et non sur son orthographe.
- Désactivez l’analyse lexicale : utilisez l’analyseur de mots-clés pour créer des champs pouvant faire l’objet de recherche qui ne sont pas analysés.
- Recherche de préfixes/suffixes rapide : ajoutez le filtre de jeton Edge N-gram pour indexer les préfixes des mots afin de permettre une correspondance de préfixes rapide. Combinez-le avec le filtre de jeton Reverse pour effectuer une correspondance de suffixes.
- Création personnalisée de jetons : utilisez par exemple le générateur de jetons Whitespace pour segmenter les phrases en jetons en utilisant l’espace blanc comme délimiteur.
- Conversion ASCII : ajoutez le filtre de conversion ASCII pour normaliser les signes diacritiques comme ö ou ê dans les termes de recherche.

Vous pouvez définir plusieurs analyseurs personnalisés pour varier la combinaison de filtres, mais chaque champ ne peut utiliser qu’un analyseur pour l’analyse de l’indexation et qu’un analyseur pour l’analyse de la recherche.
 
Cette page répertorie la liste des analyseurs, des générateurs de jetons et des filtres de jeton pris en charge. Vous trouverez également la description des modifications apportées à la définition d’index avec un exemple d’utilisation. Consultez l’introduction et l’exploration de scénarios dans l’article [Analyseurs personnalisés dans Azure Search](link). Pour plus d’informations sur la technologie sous-jacente exploitée dans l’implémentation d’Azure Search, consultez l’article [Analysis package summary (Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html) (Résumé du package d’analyse [Lucene]).


## Analyseur par défaut

L’analyseur par défaut est l’analyseur [Apache Lucene Standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html).

## Règles de validation

Les noms des analyseurs, générateurs de jetons et filtres de jeton personnalisés doivent être uniques et ne peuvent pas être identiques à ceux des analyseurs, générateurs de jetons ou filtres de jeton prédéfinis.
 

## Définition d’index avec des analyseurs personnalisés 

Définissez des analyseurs personnalisés au moment de la création d’index. Vous trouverez ci-dessous la syntaxe des composants d’analyse personnalisée dans la définition d’index. Un exemple complet est fourni [ici](#Example).

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] Les analyseurs personnalisés que vous créez ne sont pas exposés dans le portail Azure. La seule façon d’ajouter un analyseur personnalisé est de le faire dans le code, qui effectue des appels vers l’API REST lors de la définition d’un index.

##Attributs d’index pour les analyseurs, générateurs de jetons et filtres de jeton personnalisés 

Cette section spécifie les propriétés de configuration de la section des analyseurs, générateurs de jetons et filtres de jeton d’une définition d’index.

###Analyseurs

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Limité à 128&#160;caractères, il doit contenir uniquement des lettres, des chiffres, des espaces, des tirets ou des traits de soulignement, et ne peut commencer et se terminer que par des caractères alphanumériques.
      </td>
    </tr>
    <tr>
      <td>
        <b>Type</b>
      </td>
      <td>
		Les valeurs valides sont «&#160;#Microsoft.Azure.Search.CustomAnalyzer&#160;» ou un nom d’analyseur provenant de la liste des analyseurs pris en charge. Consultez la colonne <b>analyzer_type</b> dans le tableau Analyseurs indiqué ci-dessous.
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenizer</b> (valide uniquement pour #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Obligatoire. Doit être l’un des générateurs de jetons prédéfinis, indiqués dans le tableau Générateurs de jetons ci-dessous ou l’un des générateurs de jetons personnalisés définis dans la définition d’index.
      </td>
	</tr>
    <tr>
      <td>
        <b>TokenFilters</b> (valides uniquement pour #Microsoft.Azure.Search.CustomAnalyzer)
      </td>
      <td>
		Le filtre de jeton est l’un des filtres de jeton prédéfinis répertoriés dans le tableau Filtres de jeton ou l’un des filtres de jeton personnalisés définis dans la définition d’index.
      </td>
	</tr>
    <tr>
      <td>
        <b>Options</b>
      </td>
      <td>
		Options valides d’un analyseur prédéfini.
      </td>
	</tr>
  </tbody>
</table>

###Générateurs de jetons

Un générateur de jetons divise un texte continu en une séquence de jetons, par exemple en divisant une phrase en mots. Vous pouvez spécifier un seul générateur de jetons par analyseur personnalisé. Si vous avez besoin de plusieurs générateurs de jetons, vous pouvez créer plusieurs analyseurs personnalisés et les affecter champ par champ dans votre schéma d’index. Un analyseur personnalisé peut utiliser un générateur de jetons prédéfini avec des options personnalisées ou par défaut.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Limité à 128&#160;caractères, il doit contenir uniquement des lettres, des chiffres, des espaces, des tirets ou des traits de soulignement, et ne peut commencer et se terminer que par des caractères alphanumériques.
      </td>
    </tr>
    <tr>
      <td>
        <b>Type</b>
      </td>
      <td>
		Nom du générateur de jetons provenant de la liste des générateurs de jetons pris en charge. Consultez la colonne <b>tokenizer_type</b> dans le tableau Générateurs de jetons ci-dessous.
      </td>
	</tr>
     <tr>
      <td>
        <b>Options</b>
      </td>
      <td>
		Options valides d’un type donné de générateur de jetons.
      </td>
	</tr>
  </tbody>
</table>

###Filtres de jeton

Un filtre de jeton permet de filtrer ou de modifier les jetons générés par un générateur de jetons. Par exemple, vous pouvez spécifier un filtre Lowercase qui convertit tous les caractères en minuscules. Un analyseur personnalisé peut compter plusieurs filtres de jeton, qui s’exécutent dans l’ordre dans lequel ils sont répertoriés.

<table>
  <tbody>
    <tr>
      <td>
        <b>Name</b>
      </td>
      <td>
		Limité à 128&#160;caractères, il doit contenir uniquement des lettres, des chiffres, des espaces, des tirets ou des traits de soulignement, et ne peut commencer et se terminer que par des caractères alphanumériques.
      </td>
    </tr>
    <tr>
      <td>
        <b>Type</b>
      </td>
      <td>
		Nom du filtre de jeton provenant de la liste des filtres de jeton pris en charge. Consultez la colonne <b>token_filter_type</b> dans le tableau Filtres de jeton ci-dessous.
      </td>
	</tr>
     <tr>
      <td>
        <b>Options</b>
      </td>
      <td>
		Options valides d’un type donné de filtre de jeton.
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##Exemple de définition d’index

Cet exemple de définition d’index inclut un champ utilisant un analyseur personnalisé « my\_analyzer », qui à son tour utilise un générateur de jetons standard personnalisé « my\_standard\_tokenizer » et deux filtres de jeton : filtre lowercase et asciifolding personnalisé « my\_asciifolding ».

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
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
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

<a name="Analyzers"></a>
##Analyseurs

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>Description</td>
		<td>Options</td>
	</tr>
	</thead>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>Traite l’ensemble du contenu d’un champ comme un jeton unique. Cet analyseur est utile pour les données telles que les codes postaux, les&#160;ID et certains noms de produit.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>Sépare le texte de façon flexible en termes via un modèle d’expression régulière.</td>
	  <td>
		- lowercase - type&#160;: booléen - si les termes sont en minuscules, la valeur par défaut est true - [modèle](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - type&#160;: chaîne - modèle d’expression régulière pour faire correspondre des séparateurs de jeton. Par défaut&#160;: \w+ - [indicateurs](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - type&#160;: chaîne - indicateurs d’expression régulière. Par défaut&#160;: chaîne vide - stopwords - type&#160;: tableau de chaînes - liste de mots vides. Par défaut&#160;: liste vide.
	  </td>
    </tr>
    <tr>
      <td>[simple](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>Divise le texte en caractères autres que des lettres et les convertit en minuscules.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>Analyseur standard avec filtre de recherche de radical Snowball (http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
	  <td>
		- language - type&#160;: chaîne. Valeurs autorisées&#160;: danish, dutch, english, finnish, french, german, hungarian, italian, norwegian, portuguese, russian, spanish, swedish - stopwords - type&#160;: tableau de chaînes - liste de mots vides. Par défaut&#160;: liste vide.
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>Analyseur Lucene standard composé du générateur de jetons standard et des filtres Minuscule et Arrêt.</td>
	  <td>
		- maxTokenLength - type&#160;: entier - longueur de jeton maximale. Par défaut&#160;: 255. Les jetons dont la longueur est supérieure à la longueur maximale sont fractionnés. - stopwords - type&#160;: tableau de chaînes - liste de mots vides. Par défaut&#160;: liste vide.
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>Analyseur standard avec le filtre de conversion&#160;ASCII</td>
	  <td></td>
    </tr>
    <tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>Divise le texte en caractères autres que des lettres, et applique les filtres de jeton de minuscule et de mot vide.</td>
	  <td>
		- stopwords - type&#160;: tableau de chaînes - liste de mots vides. Par défaut&#160;: liste vide.
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>Analyseur qui utilise le générateur de jetons Whitespace.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##Générateurs de jetons

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>Description</td>
		<td>Options</td>
	</tr>
	</thead>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>Générateur de jetons basé sur la grammaire qui convient au traitement de la plupart des documents de langue européenne.</td>
	  <td>
		- maxTokenLength - type&#160;: entier - longueur de jeton maximale. Par défaut&#160;: 255. Les jetons dont la longueur est supérieure à la longueur maximale sont fractionnés. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>Segmente l’entrée d’une arête en n-grammes de tailles données.</td>
	  <td>
		- minGram - type&#160;: entier. Par défaut&#160;: 1 - maxGram - type&#160;: entier. Par défaut&#160;: 2 - tokenChars - type&#160;: tableau de chaînes - classes de caractères à conserver dans les jetons. Valeurs autorisées&#160;: letter, digit, whitespace, punctuation, symbol.
	  </td>	  
    </tr>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>Émet l’ensemble de l’entrée sous la forme d’un jeton unique.</td>
	  <td>
		- bufferSize - type&#160;: entier - taille du tampon de lecture. Par défaut&#160;: 256.
	  </td>
    </tr>
    <tr>
      <td>[letter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>Divise le texte en caractères autres que des lettres.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>Divise le texte en caractères autres que des lettres et les convertit en minuscules.</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>Segmente l’entrée en n-grammes de tailles données.</td>
	  <td>
		- minGram - type&#160;: entier. Par défaut&#160;: 1 - maxGram - type&#160;: entier. Par défaut&#160;: 2 - tokenChars - type&#160;: tableau de chaînes - classes de caractères à conserver dans les jetons. Valeurs autorisées&#160;: letter, digit, whitespace, punctuation, symbol.
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>Générateur de jetons pour des hiérarchies de type chemin d’accès</td>
	  <td>
		- delimiter - type&#160;: chaîne. Par défaut&#160;: «&#160;/&#160;» - replacement - type&#160;: chaîne - si défini, remplace le caractère délimiteur. Par défaut&#160;: délimiteur - bufferSize - type&#160;: entier. Par défaut&#160;: 1024 - reverse - type&#160;: booléen - si défini sur true, génère les jetons dans l’ordre inverse. Par défaut&#160;: true - skip - type&#160;: booléen - jetons initiaux à ignorer. Par défaut&#160;: 0.
	  </td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>Ce générateur de jetons utilise la correspondance de modèle regex pour construire des jetons distincts.</td>
	  <td>
		 - pattern - type&#160;: chaîne - modèle d’expression régulière. Par défaut&#160;: \w+ - [indicateurs](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - type&#160;: chaîne - indicateurs d’expression régulière. Par défaut&#160;: chaîne vide - group - type&#160;: entier - groupe à extraire en jetons. Par défaut&#160;: -1 (split).
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>Segmente le texte en suivant les règles de segmentation de texte Unicode (http://unicode.org/reports/tr29/).</td>
	  <td>
		 - maxTokenLength - type&#160;: entier - longueur de jeton maximale. Par défaut&#160;: 255. Les jetons dont la longueur est supérieure à la longueur maximale sont fractionnés. 
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>Segmente les&#160;URL et les messages électroniques en un jeton.</td>
	  <td>
		- maxTokenLength - type&#160;: entier - longueur de jeton maximale. Par défaut&#160;: 255. Les jetons dont la longueur est supérieure à la longueur maximale sont fractionnés.
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>Divise le texte à l’espace blanc.</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##Filtres de jeton

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>Description</td>
		<td>Options</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>Supprime tous les caractères après une apostrophe (y compris l'apostrophe elle-même)</td>
    </tr>
    <tr>
      <td>[apostrophe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>Supprime tous les caractères après une apostrophe</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>Convertit les caractères Unicode alphabétiques, numériques et symboliques qui ne font pas partie des 127&#160;premiers caractères&#160;ASCII (bloc Unicode «&#160;latin de base&#160;») en équivalents&#160;ASCII s’ils existent.</td>
	  <td>
		- preserveOriginal - type&#160;: booléen - si défini sur true, le jeton d’origine est conservé. Par défaut&#160;: false.
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>Forme des bigrammes des termes&#160;CJC qui sont générés à partir de StandardTokenizer.</td>
	  <td>
		- ignoreScripts - type&#160;: booléen - scripts à ignorer. Valeurs autorisées&#160;: han, hiragana, katakana, hangul. Par défaut&#160;: liste vide - type outputUnigrams&#160;: booléen -- défini sur true si vous souhaitez toujours sortir des unigrammes et des bigrammes. Par défaut&#160;: false.
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>Normalise les différences de largeur&#160;CJC. Convertit les variantes&#160;ASCII pleine chasse en variantes katakana pleine chasse et latin de base équivalentes et en kana équivalent.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>Supprime les possessifs anglais, et les points des acronymes.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>Crée des bigrammes pour les termes fréquents lors de l’indexation. Les termes uniques sont toujours indexés également, avec des bigrammes superposés.</td>
	  <td>
		- commonWords - type&#160;: tableau de chaînes - ensemble de mots courants. Par défaut&#160;: liste vide - ignoreCase - type&#160;: booléen - si défini sur true, les mots courants correspondants ne respectent pas la casse. Par défaut&#160;: false - queryMode - type&#160;: booléen - génère des bigrammes, puis supprime les mots courants et les termes uniques suivis d’un mot courant. Par défaut&#160;: false.
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>Les caractères placés avant le délimiteur sont le «&#160;jeton&#160;», tandis que ceux qui sont placés après sont la charge utile. Exemple&#160;: si le délimiteur est «&#160;|&#160;», alors pour la chaîne «&#160;hello|world&#160;», «&#160;hello&#160;» est le jeton et «&#160;world&#160;» la charge utile. Vous pouvez également inclure un encodeur pour convertir la charge utile de façon appropriée (caractères en octets).</td>
	  <td>
		- delimiter - type&#160;: caractère. Par défaut&#160;: «&#160;|&#160;» - encoding - type&#160;: chaîne. Valeurs autorisées&#160;: int, float, identity. Par défaut&#160;: flottant.
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>Décompose les mots composés trouvés dans de nombreux langages germaniques.</td>
	  <td>
		- wordList - type&#160;: tableau de chaînes - liste de mots avec lesquels effectuer une correspondance. Par défaut&#160;: liste vide - minWordSize - type&#160;: entier - seuls les mots dont la longueur est supérieure sont traités. Par défaut&#160;: 5 - minSubwordSize - type&#160;: entier - seuls les sous-mots dont la longueur est supérieure sont générés. Par défaut&#160;: 2 - maxSubwordSize - type&#160;: entier - seuls les sous-mots dont la longueur est inférieure sont générés. Par défaut&#160;: 15 - onlyLongestMatch - type&#160;: booléen - ajoute uniquement le sous-mot correspondant le plus long à la sortie. Par défaut&#160;: false. 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>Génère n-grammes de tailles données en commençant à partir du début ou de la fin d’un jeton d’entrée.</td>
	  <td>
		- minGram - type&#160;: entier. Par défaut&#160;: 1 - maxGram - type&#160;: entier. Par défaut&#160;: 2 - side - type&#160;: chaîne - spécifie le côté de l’entrée à partir duquel le n-gramme doit être généré. Valeurs autorisées&#160;: front, back
	  </td>	  
    </tr>   
    <tr>
      <td>[elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>Supprime les élisions. Exemple&#160;: «&#160;l’avion&#160;» (the plane) est converti en «&#160;avion&#160;» (plane).</td>
	  <td>
		- articles - type&#160;: tableau de chaînes - ensemble d’articles à supprimer. Par défaut&#160;: liste vide.
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>Normalise les caractères allemands en fonction de l’heuristique de l’algorithme Snowball German2 (http://snowball.tartarus.org/algorithms/german2/stemmer.html).</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>Normalise le texte en hindi pour supprimer des différences dans les variations orthographiques.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>Normalise la représentation Unicode du texte dans les langues indiennes.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[keep](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>Conserve uniquement les jetons comportant le texte contenu dans une liste spécifiée de mots.</td>
	  <td>
		- keepWords - type&#160;: tableau de chaînes - liste de mots à conserver. Par défaut&#160;: liste vide - keepWordsCase - type&#160;: booléen - si défini sur true, d’abord tous les mots en minuscules. Par défaut&#160;: false.
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>Conserve les jetons dont les types s’affichent dans la liste donnée de types autorisés.</td>
	  <td>
		- types - type&#160;: tableau de chaînes - liste des types à conserver.
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>Marque les termes en tant que mots-clés.</td>
	  <td>
		- keywords - type&#160;: tableau de chaînes - liste de mots à marquer comme mots-clés. Par défaut&#160;: liste vide - ignoreCase - type&#160;: booléen - si défini sur true, tous les mots en minuscules sont d’abord mis en minuscules. Par défaut&#160;: false.
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>Émet deux&#160;fois chaque jeton entrant une fois en tant que mot clé et une fois en tant que mot différent d’un mot clé.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>Filtre kstem haute performance pour l’anglais.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[length](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>Supprime les mots trop longs ou trop courts.</td>
	  <td>
		- min - type&#160;: entier - nombre minimal. Par défaut&#160;: 0 - max - type&#160;: int - nombre maximal. Par défaut&#160;: valeur entière maximale.
	  </td>	  
    </tr>
    <tr>
      <td>[limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>Limite le nombre de jetons lors de l’indexation.</td>
	  <td>
		- maxTokenCount - type&#160;: entier - nombre maximal de jetons à produire. Par défaut&#160;: 1 - consumeAllTokens - type&#160;: booléen - indique si tous les jetons de l’entrée doivent être utilisés même si le nombre maxTokenCount est atteint. Par défaut&#160;: false.
	  </td>	  
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>Normalise le texte de jeton en minuscules.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>Génère des n-grammes des tailles données.</td>
	  <td>
		- minGram - type&#160;: entier. Par défaut&#160;: 1 - maxGram - type&#160;: entier. Par défaut&#160;: 2
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Utilise des expressions régulières Java pour émettre plusieurs jetons&#160;; un par groupe de capture dans un ou plusieurs modèles.</td>
	  <td>
		- patterns - type&#160;: tableau de chaînes - liste des modèles avec lesquels faire correspondre chaque jeton - preserveOriginal - type&#160;: booléen - défini sur true pour retourner le jeton d’origine même si l’un des modèles correspond.
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>Applique la normalisation pour le persan.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[phonetic]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>Crée des jetons pour les correspondances phonétiques.</td>
	  <td>
		- encoder - type&#160;: chaîne&#160; - encodeur phonétique à utiliser. Valeurs autorisées&#160;: metaphone, doublemetaphone, soundex, refinedsoundex, caverphone1, caverphone2, cologne, nysiis, koelnerphonetik, haasephonetik, beidermorse. Par défaut&#160;: metaphone - replace - type&#160;: booléen - true si les jetons encodés doivent remplacer les jetons d’origine, false s’ils doivent être ajoutés en tant que synonymes. Par défaut&#160;: true.
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>Transforme le flux de jetons conformément à l’algorithme de recherche de radical Porter (http://tartarus.org/~martin/PorterStemmer/).</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[reverse](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>Inverse la chaîne de jeton.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>Normalise l’utilisation des caractères scandinaves interchangeables.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>Convertit les caractères scandinaves åÅäæÄÆ->a et öÖøØ->o. Il empêche également l’utilisation des doubles voyelles aa, ae, ao, oe et oo, en laissant uniquement la première.</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>Crée des combinaisons de jetons en tant que jeton unique.</td>
	  <td>
		- maxShingleSize - type&#160;: entier. Par défaut&#160;: 2 - minShingleSize - type&#160;: entier. Par défaut&#160;: 2 - outputUnigrams - type&#160;: booléen - si true, le flux de sortie contient les jetons d’entrée (unigrammes) ainsi que des tuiles. Par défaut&#160;: true - outputUnigramsIfNoShingles - type&#160;: booléen - si true, remplace le comportement du paramètre outputUnigrams==false pour les cas dans lesquels aucune tuile n’est disponible. Par défaut&#160;: false - tokenSeparator - type&#160;: chaîne - chaîne à utiliser lors de la jonction de jetons adjacents pour former une tuile. Par défaut&#160;: «&#160; &#160;» - filterToken - type&#160;: chaîne - chaîne à insérer pour chaque emplacement auquel il n’y a pas de jeton. Par défaut&#160;: «&#160;_&#160;».
	  </td>	  
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Filtre de jeton de recherche de radical Snowball.</td>
	  <td>
		- language - type&#160;: chaîne. Valeurs autorisées&#160;: Armenian, Basque, Catalan, Danish, Dutch, English, Finnish, French, German, German2, Hungarian, Italian, Kp, Lovins, Norwegian, Porter, Portuguese, Romanian, Russian, Spanish, Swedish, Turkish.
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>Normalise la représentation Unicode du texte en sorani.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>analyseur morpholoqique</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>Filtre de recherche de radical propre à une langue.</td>
	  <td>
		- language - type&#160;: chaîne. Valeurs autorisées&#160;: - [arabic](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [armenian](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [basque](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [brazilian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [bulgarian](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [catalan](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [czech](http://portal.acm.org/citation.cfm?id=1598600) - [danish](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [dutch](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [english](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [finnish](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [french](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [german](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [greek](http://sais.se/mthprize/2007/ntais2007.pdf) - [hindi](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [hungarian](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [indonesian](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [irish](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [italian](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [latvian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [norwegian](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [portuguese](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [romanian](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [russian](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [spanish](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [swedish](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [turkish](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>Les termes provenant des dictionnaires sont marqués comme mots-clés afin de ne pas être renvoyés avec les analyseurs morphologiques en bas de la chaîne. Doit être placé avant les filtres de recherche de radical.</td>
	  <td>
		- rules - type&#160;: tableau de chaînes - règles de recherche de radical au format suivant «&#160;word => stem&#160;». Exemple&#160;: «&#160;ran => run&#160;». Par défaut&#160;: liste vide.
	  </td>	  
    </tr>
	<tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>Retire les mots vides d’un flux de jetons.</td>
	  <td>
		- stopwords - type&#160;: tableau de chaînes - liste de mots vides. Par défaut&#160;: liste vide. - stopwords_list - type&#160;: chaîne - liste prédéfinie de mots vides. Valeurs autorisées&#160;: _arabic_, _armenian_, _basque_, _brazilian_, _bulgarian_, _catalan_, _czech_, _danish_, _dutch_, _english_, _finnish_, _french_, _galician_, _german_, _greek_, _hindi_, _hungarian_, _indonesian_, _irish_, _italian_, _latvian_, _norwegian_, _persian_, _portuguese_, _romanian_, _russian_, _sorani_, _spanish_, _swedish_, _thai_, _turkish_. Par défaut&#160;: _english_ - ignoreCase - type&#160;: booléen - si true, tous les mots sont d’abord mis en minuscules. Par défaut&#160;: false - removeTrailing - type&#160;: booléen - si true, ignore le dernier terme recherché s’il s’agit d’un mot vide. Par défaut&#160;: true.
	  </td>	  
    </tr>
	<tr>
      <td>[synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>Fait correspondre un ou plusieurs synonymes dans un flux de jetons.</td>
	  <td>
		- synonyms - type&#160;: tableau de chaînes - liste de synonymes en suivant l’un des deux&#160;formats&#160;: - incredible, unbelievable, fabulous => amazing - tous les termes situés à gauche du symbole => sont remplacés par tous les termes situés à droite - incredible, unbelievable, fabulous, amazing - liste de mots équivalents séparés par une virgule. Définit l’option expand pour modifier l’interprétation de cette liste - ignoreCase - type&#160;: booléen - convertit la casse de l’entrée pour la correspondance. Par défaut&#160;: false - expand - type&#160;: booléen - - si true, tous les mots de la liste de synonymes (si la notation => n’est pas utilisée) sont mappés les uns aux autres. La liste suivante, incredible, unbelievable, fabulous, amazing équivaut à&#160;: incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing - si false, la liste suivante, incredible, unbelievable, fabulous, amazing équivaut à&#160;: incredible, unbelievable, fabulous, amazing => incredible.   
	  </td>	  
    </tr>
	<tr>
      <td>[trim](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>Supprime les espaces de début et de fin dans les jetons.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>Tronque les termes selon une longueur spécifique.</td>
	  <td>
		- length - type&#160;: entier - option requise.
	  </td>	  
    </tr>
	<tr>
      <td>[unique](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>Filtre les jetons contenant le même texte que le jeton précédent.</td>
	  <td>
		- onlyOnSamePosition - type&#160;: booléen - si défini, supprime les doublons uniquement au même emplacement. Par défaut&#160;: true.
	  </td>	  
    </tr>
    <tr>
      <td>[uppercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>Normalise le texte de jeton en majuscules.</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>Fractionne les mots en sous-mots et effectue des transformations facultatives sur les groupes de sous-mots.</td>
	  <td>
		- generateWordParts - type&#160;: booléen - provoque la génération de parties de mots. Exemple&#160;: «&#160;AzureSearch&#160;» -> «&#160;Azure&#160;» «&#160;Search&#160;». Par défaut&#160;: true - generateNumberParts - type&#160;: booléen - provoque la génération de plusieurs sous-mots. Par défaut&#160;: true - catenateWords - type&#160;: booléen - provoque un nombre maximal d’exécutions des parties de mots à concaténer. Exemple&#160;: «&#160;Azure-Search&#160;» -> «&#160;AzureSearch&#160;». Par défaut&#160;: false - catenateNumbers - type&#160;: booléen - provoque un nombre maximal d’exécutions de parties de nombres à concaténer. Exemple&#160;: «&#160;1-2&#160;» -> «&#160;12&#160;». Par défaut&#160;: false - catenateAll - type&#160;: booléen - provoque la concaténation de toutes les parties de sous-mots. Exemple&#160;: «&#160;Azure-Search-1&#160;» -> «&#160;AzureSearch1&#160;». Par défaut&#160;: false - splitOnCaseChange - type&#160;: booléen - si true, fractionne les mots au changement de casse. Exemple&#160;: «&#160;AzureSearch&#160;» -> «&#160;Azure&#160;» «&#160;Search&#160;». Par défaut&#160;: true - preserveOriginal - provoque la conservation des mots d’origine et leur ajout à la liste de sous-mots. Par défaut&#160;: false - splitOnNumerics - type&#160;: booléen - si true, fractionne au niveau des chiffres. Exemple&#160;: «&#160;Azure1Search&#160;» -> «&#160;Azure&#160;» «&#160;1&#160;» «&#160;Search&#160;». Par défaut&#160;: true - stemEnglishPossessive - type&#160;: booléen - provoque la suppression du «&#160;s&#160;» de fin de chaque sous-mot. Par défaut&#160;: true - protectedWords - type&#160;: tableau de chaînes - jetons à protéger d’une délimitation. Par défaut&#160;: liste vide.
	  </td>	  
    </tr>
  </tbody>
</table>

**Voir aussi** [API REST du service Azure Search](http://msdn.microsoft.com/library/azure/dn798935.aspx) sur MSDN <br/> [Créer un index (API Azure Search)](http://msdn.microsoft.com/library/azure/dn798941.aspx) sur MSDN<br/>

<!---HONumber=AcomDC_0224_2016-->