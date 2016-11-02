<properties
    pageTitle="Exemples de requêtes Lucene pour Azure Search | Microsoft Azure Search"
    description="Syntaxe de requête Lucene pour la recherche partielle, la recherche de proximité, l’amélioration de termes, la recherche d’expressions régulières et la recherche par caractères génériques."
    services="search"
    documentationCenter=""
	authors="LiamCa"
	manager="pablocas"
	editor=""
    tags="Lucene query analyzer syntax"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="liamca"
/>

# Exemples de syntaxe de requête Lucene pour créer des requêtes dans Azure Search

Lors de la construction de requêtes pour Azure Search, vous pouvez utiliser la [syntaxe de requête simple](https://msdn.microsoft.com/library/azure/dn798920.aspx) par défaut ou bien [l’analyseur de requêtes Lucene dans Azure Search](https://msdn.microsoft.com/library/azure/mt589323.aspx). L’Analyseur de requêtes Lucene prend en charge des constructions de requêtes plus complexes, telles que les requêtes portant sur des champs, la recherche partielle, la recherche de proximité, la promotion de termes et la recherche d’expression régulière.

Cet article contient des exemples qui affichent la syntaxe de requête Lucene et les résultats côte à côte. Les exemples s’exécutent sur un index de recherche préchargé dans [JSFiddle](https://jsfiddle.net/), éditeur de code en ligne pour le test de script et HTML.

Cliquez avec le bouton droit sur les URL des exemples de requêtes pour ouvrir JSFiddle dans une nouvelle fenêtre de navigateur.

> [AZURE.NOTE] Les exemples suivants utilisent un index de recherche composé de postes à pourvoir sur la base d’un jeu de données fourni par l’initiative [City of New York OpenData](https://nycopendata.socrata.com/). Ces données ne doivent pas être considérées comme étant à jour ou complètes. L’index est sur un service de bac à sable (sandbox) fourni par Microsoft. Vous n’avez pas besoin d’abonnement Azure ou d’Azure Search pour essayer ces requêtes.

## Affichage des exemples de cet article

Tous les exemples de cet article spécifient l’analyseur de requêtes Lucene dans le paramètre de recherche **queryType**. Lorsque vous utilisez l’analyseur de requêtes Lucene à partir de votre code, vous devez spécifier le **queryType** à chaque requête. Les valeurs valides sont **simple**|**full**, **simple** étant la valeur par défaut et **full** la valeur faisant référence à l’analyseur de requêtes Lucene. Pour plus d’informations sur la spécification des paramètres de requêtes, consultez [Rechercher des documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx).

**Exemple 1** -- Cliquez avec le bouton droit sur l’extrait de requête suivant pour l’ouvrir sur une nouvelle page de navigateur qui charge JSFiddle et exécute la requête :
- [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

Cette requête retourne des documents à partir de notre index Jobs (chargé sur un service de bac à sable (sandbox))

Dans la nouvelle fenêtre de navigateur, vous verrez le JavaScript source et la sortie HTML côte à côte. Le script fait référence à une requête, qui est fournie par les exemples d’URL dans cet article. Par exemple, dans **l’exemple 1**, la requête sous-jacente est la suivante :

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

Notez que la requête utilise un index Azure Search préconfiguré appelé nycjobs. Le paramètre **searchFields** restreint la recherche au champ de titre d’entreprise. Le **queryType** est défini sur **full**, qui donne l’instruction à Azure Search d’utiliser l'analyseur de requêtes Lucene pour cette requête.

### Opération de requête portant sur un champ

Vous pouvez modifier les exemples de cet article en spécifiant une construction **fieldname:searchterm** pour définir une opération de requête portant sur un champ, où le champ est un mot unique et le terme de recherche est également un mot ou une expression unique, éventuellement avec des opérateurs booléens. Voici quelques exemples :

- business\_title:(senior NOT junior)
- state:("New York" AND "New Jersey")

Veillez à placer les chaînes entre guillemets si vous souhaitez que les deux chaînes soient évaluées comme une seule entité, comme ici où deux villes distinctes sont recherchées dans le champ d’emplacement. Vérifiez également que l’opérateur est en majuscules, comme c’est le cas ici avec NOT et AND.

Le champ spécifié dans **fieldname:searchterm** doit être un champ pouvant faire l’objet d’une recherche. Pour plus d’informations sur l’utilisation des attributs d’index dans les définitions de champs, consultez [Créer un index (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx).

## Recherche partielle

Une recherche partielle recherche des correspondances dans les termes qui ont une construction similaire. D’après la [documentation Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), les recherches partielles sont basées sur la [Distance Levenshtein-Damerau](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Pour effectuer une recherche partielle, utilisez le signe tilde « ~ » à la fin d’un mot avec un paramètre facultatif, une valeur comprise entre 0 et 2, qui spécifie la distance de modification. Par exemple, "blue~" ou "blue~1" retournent blue, blues et glue.

**Exemple 2** -- Cliquez avec le bouton droit sur l’extrait de requête suivant pour l’essayer. Cette requête recherche les titres de fonctions contenant le terme « senior » mais pas « junior » :

- [&queryType=full&search= business\_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## Recherche de proximité

Les recherches de proximité servent à rechercher des termes qui sont proches les uns des autres dans un document. Insérez un signe tilde « ~ » à la fin d’une expression, suivi du nombre de mots qui créent la limite de proximité. Par exemple, "hotel airport"~5 recherche les termes hotel et airport distants de cinq mots ou moins dans un document.

**Exemple 3** -- Cliquez avec le bouton droit sur l’extrait de requête suivant. Cette requête recherche les postes à pourvoir contenant le terme « associate » (où il est mal orthographié) :

- [&queryType=full&search= business\_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

**Exemple 4** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes contenant le terme « senior analyst » où les deux mots sont séparés au plus par un mot :

- [&queryType=full&search=business\_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Exemple 5** -- Réessayez, mais en supprimant les mots entre « senior analyst ».

- [&queryType=full&search=business\_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## Promotion de termes

La promotion de termes signifie que vous pouvez accorder à un document un rang plus élevé s’il contient le terme promu, par rapport aux documents qui ne contiennent pas ce terme. À ne pas confondre avec les profils de score qui promeuvent certains champs, plutôt que des termes spécifiques. L’exemple suivant permet d’illustrer les différences entre les deux.

Prenez un profil de score qui promeut les correspondances figurant dans un certain champ, par exemple **genre** dans l’exemple musicstoreindex. En utilisant la promotion de termes, vous pouvez promouvoir encore plus certains termes de recherche. Par exemple, "rock^2 electronic" promeut les documents qui contiennent les termes de recherche dans le champ **genre**, par rapport aux autres champs de recherche de l’index. Par ailleurs, les documents qui contiennent le terme de recherche « rock » bénéficient d’un classement plus élevé que ceux qui contiennent le terme de recherche « electronic » en raison de la valeur de promotion du terme (2).

Pour promouvoir un terme, utilisez le signe « ^ » avec un facteur de promotion (un nombre) à la fin du terme recherché. Plus le facteur de promotion est élevé, plus le terme est pertinent par rapport aux autres termes de recherche. Par défaut, le facteur de promotion est égal à 1. Ce facteur doit être positif, mais il peut être inférieur à 1 (par exemple 0,2).

**Exemple 6** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes à pourvoir contenant le terme "computer analyst". Nous constatons l’absence de résultat avec les deux mots « computer » et « analyst ». Par contre, les postes d’analyste figurent en haut des résultats.

- [&queryType=full&search=business\_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Exemple 7** -- Réessayez cette fois en promouvant les résultats contenant le terme « computer » par rapport au terme « analyst » si les deux mots ensemble n’existent pas.

- [&queryType=full&search=business\_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## Expression régulière

Une recherche d’expression régulière trouve une correspondance en fonction du contenu placé entre des barres obliques « / », comme le décrit la [classe RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Exemple 8** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes à pourvoir contenant le terme « Senior » ou « Junior ».

- `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

L’URL de cet exemple ne s’affiche pas correctement dans la page. En guise d’alternative, copiez l’URL ci-dessous et collez-la dans la barre d’adresse URL du navigateur : `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`


## Recherche par caractères génériques

Vous pouvez utiliser la syntaxe généralement reconnue pour effectuer des recherches avec plusieurs caractères génériques (*) ou un caractère générique unique (?). Notez que l’Analyseur de requêtes Lucene prend en charge l’utilisation de ces symboles avec un terme unique, et non une expression.

**Exemple 9** -- Cliquez avec le bouton droit sur la requête. Recherchez les postes à pourvoir qui contiennent le préfixe « prog », par exemple ceux contenant les termes « programmation » et « programmeur ».

- [&queryType=full&$select=business\_title&search=business\_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26queryType=full%26$select=business_title%26search=business_title:prog*)

Vous ne pouvez pas utiliser un signe * ou ? comme premier caractère d’une recherche.


## Étapes suivantes

Essayez de spécifier l’Analyseur de requêtes Lucene dans votre code. Les liens suivants expliquent comment configurer des requêtes de recherche pour .NET et l’API REST. Ces liens utilisent la syntaxe simple par défaut. Vous devrez donc appliquer ce que vous avez appris dans cet article pour spécifier le **queryType**.

- [Interroger un index Azure Search à l’aide du Kit de développement logiciel (SDK) .NET](search-query-dotnet.md)
- [Interroger votre index Azure Search à l’aide de l’API REST](search-query-rest-api.md)


 

<!---HONumber=AcomDC_0907_2016-->