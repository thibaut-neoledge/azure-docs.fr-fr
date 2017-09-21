---
title: "Exemples de requêtes Lucene pour la Recherche Azure | Microsoft Docs"
description: "Syntaxe de requête Lucene pour la recherche partielle, la recherche de proximité, l’amélioration de termes, la recherche d’expressions régulières et la recherche par caractères génériques."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: Lucene query analyzer syntax
ms.assetid: 147f360d-a5ce-4d7b-a909-c8b65bfb748c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: liamca
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 1faed621039ecd04064cb074e6b9011418e6ec47
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Exemples de syntaxe de requête Lucene pour créer des requêtes dans la Recherche Azure
Lors de la construction de requêtes pour la Recherche Azure, vous pouvez utiliser la [syntaxe de requête simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) par défaut ou bien [l’analyseur de requêtes Lucene de la Recherche Azure](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). L’analyseur de requêtes Lucene prend en charge des constructions de requêtes plus complexes, telles que des requêtes portant sur des champs, la recherche partielle, la recherche de proximité, la promotion de termes et la recherche d’expression régulière.

Dans cet article, vous pouvez parcourir des exemples décrivant les opérations de requête disponibles lors de l’utilisation de la syntaxe complète.

## <a name="viewing-the-examples-in-jsfiddle"></a>Affichage des exemples dans JSFiddle

Tous les exemples de cet article sont des requêtes exécutables qui s’exécutent sur un index de recherche préchargé dans [JSFiddle](https://jsfiddle.net), éditeur de code en ligne pour le script de test et HTML. 

Pour les exécuter, cliquez avec le bouton droit sur les URL des exemples de requêtes afin d’ouvrir JSFiddle dans une nouvelle fenêtre de navigateur.

> [!NOTE]
> Les exemples suivants utilisent un index de recherche composé de postes à pourvoir sur la base d’un jeu de données fourni par l’initiative [City of New York OpenData](https://nycopendata.socrata.com/) . Ces données ne doivent pas être considérées comme étant à jour ou complètes. L’index est sur un service de bac à sable (sandbox) fourni par Microsoft. Vous n’avez pas besoin d’abonnement Azure ni de Recherche Azure pour essayer ces requêtes.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Appel de l’analyse Lucene complète

Tous les exemples de cet article spécifient le paramètre de requête **queryType=full**, ce qui indique la syntaxe complète est traitée par l’analyseur de requêtes Lucene. 

**Exemple 1** -- Cliquez avec le bouton droit sur l’extrait de requête suivant pour l’ouvrir sur une nouvelle page de navigateur qui charge JSFiddle et exécute la requête :

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Dans la nouvelle fenêtre de navigateur, le JavaScript source et la sortie HTML sont présentés côte à côte. Le script fait référence à une requête complète (pas seulement à l’extrait, comme indiqué dans le lien). La requête complète est indiquée dans l’URL de chaque exemple. 

Cette requête retourne des documents à partir de notre index New York City Jobs (nycjobs, chargé sur un service de bac à sable (sandbox)). Par souci de concision, la requête spécifie que seuls les titres de fonctions sont retournés. La requête sous-jacente complète est la suivante :

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Le paramètre **searchFields** restreint la recherche au champ de titre d’entreprise. Le **queryType** est défini sur **full**, qui donne l’instruction à la Recherche Azure d’utiliser l’analyseur de requêtes Lucene pour cette requête.

> [!NOTE]
> Pour des informations sur le traitement des requêtes, consultez [Fonctionnement de la recherche en texte intégral dans Recherche Azure](search-lucene-query-architecture.md). Pour plus d’informations sur les paramètres de recherche, consultez [Rechercher des documents (API REST du service Recherche Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Opération de requête portant sur un champ
Vous pouvez modifier les exemples de cet article en spécifiant une construction **fieldname:searchterm** pour définir une opération de requête portant sur un champ, où le champ est un mot unique et le terme de recherche est également un mot ou une expression unique, éventuellement avec des opérateurs booléens. Voici quelques exemples :

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

Veillez à placer les chaînes entre guillemets si vous souhaitez que les deux chaînes soient évaluées comme une seule entité, comme ici où deux villes distinctes sont recherchées dans le champ d’emplacement. Vérifiez également que l’opérateur est en majuscules, comme c’est le cas ici avec NOT et AND.

Le champ spécifié dans **fieldname:searchterm** doit être un champ pouvant faire l’objet d’une recherche. Pour plus d’informations sur l’utilisation des attributs d’index dans les définitions de champs, consultez [Créer un index (API REST du service Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index) .

**Exemple 2** - Cliquez avec le bouton droit sur l’extrait de requête suivant. Cette requête recherche les titres de fonctions contenant le terme « senior » mais pas « junior » :

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Exemple de recherche partielle
Une recherche partielle recherche des correspondances dans les termes qui ont une construction similaire. D’après la [documentation Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), les recherches partielles sont basées sur la [Distance Levenshtein-Damerau](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Pour effectuer une recherche partielle, ajoutez le signe tilde « ~ » à la fin d’un mot avec un paramètre facultatif, une valeur comprise entre 0 et 2, qui spécifie la distance de modification. Par exemple, "blue~" ou "blue~1" retournent blue, blues et glue.

**Exemple 3** -- Cliquez avec le bouton droit sur l’extrait de requête suivant. Cette requête recherche les postes à pourvoir contenant le terme « associate » (où il est mal orthographié) :

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Les requêtes partielles ne sont pas [analysées](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), ce qui peut être surprenant si vous vous attendez à une racinisation ou lemmatisation. L’analyse lexicale est effectuée uniquement sur des termes complets (requête sur un terme ou une expression). Les types de requête avec des termes incomplets (requête de préfixe, de caractère générique, d’expression régulière, partielle) sont ajoutés directement à l’arborescence de requête, en ignorant la phase d’analyse. La seule transformation effectuée sur les termes de requête incomplets est l’utilisation de minuscules.
>

## <a name="proximity-search-example"></a>Exemple Recherche de proximité
Les recherches de proximité servent à rechercher des termes qui sont proches les uns des autres dans un document. Insérez un signe tilde « ~ » à la fin d’une expression, suivi du nombre de mots qui créent la limite de proximité. Par exemple, "hotel airport"~5 recherche les termes hotel et airport distants de cinq mots ou moins dans un document.

**Exemple 4** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes contenant le terme « senior analyst » où les deux mots sont séparés au plus par un mot :

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemple 5** -- Réessayez, mais en supprimant les mots entre « senior analyst ».

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Exemple Promotion de termes
La promotion de termes signifie que vous pouvez accorder à un document un rang plus élevé s’il contient le terme promu, par rapport aux documents qui ne contiennent pas ce terme. À ne pas confondre avec les profils de score qui promeuvent certains champs, plutôt que des termes spécifiques. L’exemple suivant permet d’illustrer les différences entre les deux.

Prenez un profil de score qui promeut les correspondances figurant dans un certain champ, par exemple **genre** dans l’exemple musicstoreindex. En utilisant la promotion de termes, vous pouvez promouvoir encore plus certains termes de recherche. Par exemple, "rock^2 electronic" promeut les documents qui contiennent les termes de recherche dans le champ **genre** , par rapport aux autres champs de recherche de l’index. Par ailleurs, les documents qui contiennent le terme de recherche « rock » bénéficient d’un classement plus élevé que ceux qui contiennent le terme de recherche « electronic » en raison de la valeur de promotion du terme (2).

Pour promouvoir un terme, utilisez le signe « ^ » avec un facteur de promotion (un nombre) à la fin du terme recherché. Plus le facteur de promotion est élevé, plus le terme est pertinent par rapport aux autres termes de recherche. Par défaut, le facteur de promotion est égal à 1. Ce facteur doit être positif, mais il peut être inférieur à 1 (par exemple 0,2).

**Exemple 6** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes à pourvoir contenant le terme "computer analyst". Nous constatons l’absence de résultat avec les deux mots « computer » et « analyst ». Par contre, les postes d’analyste figurent en haut des résultats.

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemple 7** -- Réessayez cette fois en promouvant les résultats contenant le terme « computer » par rapport au terme « analyst » si les deux mots ensemble n’existent pas.

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Exemple Expression régulière
Une recherche d’expression régulière trouve une correspondance en fonction du contenu placé entre des barres obliques « / », comme le décrit la [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemple 8** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes à pourvoir contenant le terme « Senior » ou « Junior ».

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

L’URL de cet exemple ne s’affiche pas correctement dans la page. En guise d’alternative, copiez l’URL ci-dessous et collez-la dans la barre d’adresse URL du navigateur : `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Exemple Recherche par caractères génériques
Vous pouvez utiliser la syntaxe généralement reconnue pour effectuer des recherches avec plusieurs caractères génériques (\*) ou un caractère générique unique (?). Notez que l’Analyseur de requêtes Lucene prend en charge l’utilisation de ces symboles avec un terme unique, et non une expression.

**Exemple 9** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes à pourvoir qui contiennent le préfixe « prog », par exemple ceux contenant les termes « programmation » et « programmeur ».

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

Vous ne pouvez pas utiliser un signe * ou ? comme premier caractère d’une recherche.

## <a name="next-steps"></a>Étapes suivantes
Essayez de spécifier l’Analyseur de requêtes Lucene dans votre code. Les liens suivants expliquent comment configurer des requêtes de recherche pour .NET et l’API REST. Ces liens utilisent la syntaxe simple par défaut. Vous devrez donc appliquer ce que vous avez appris dans cet article pour spécifier le **queryType**.

* [Interroger un index Recherche Azure à l’aide du kit .NET SDK](search-query-dotnet.md)
* [Interroger votre index Recherche Azure à l’aide de l’API REST](search-query-rest-api.md)

## <a name="see-also"></a>Voir aussi

 [Fonctionnement de la recherche en texte intégral dans la Recherche Azure](search-lucene-query-architecture.md)
