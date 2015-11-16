<properties
	pageTitle="Comment rechercher des données StackExchange à l’aide de la recherche Azure | Microsoft Azure"
	description="Apprenez à effectuer des recherches REST à l'aide du service Azure Search, un service de recherche cloud hébergé sur Microsoft Azure."
	services="search"
	documentationCenter=""
	authors="liamca"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="liamca"/>

# Comment rechercher des données StackExchange avec Azure Search

Cet article est une procédure pas à pas qui met en évidence certaines des fonctionnalités de base de recherche en texte intégral qui peuvent être exécutées avec [Azure Search](https://azure.microsoft.com/fr-FR/services/search/). Les données Stack Exchange [disponibles](https://archive.org/details/stackexchange) sont utilisées par Creative Commons avec l’[attribution](http://blog.stackoverflow.com/2009/06/attribution-required/) suivante.

## Prise en main

Pour mettre en évidence certaines de ces fonctionnalités, j’ai créé un index de recherche Azure contenant des données de programmeur StackExchange pour que vous puissiez jouer à partir du 14 octobre 2015. Remarque : je vous montrerai également comment créer vos propres index Azure Search avec ces données ultérieurement.

Commençons par une requête de recherche de texte intégral vraiment simple dans laquelle les utilisateurs peuvent taper le mot « azure » pour rechercher tous les billets StackExchange relatifs à Azure. À présent,faisons un essai en cliquant sur ce lien pour le voir en fonctionnement :

> [http://fiddle.jshell.NET/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&Query=API-version=2015-02-28%26search=Azure](http://fiddle.jshell.net/liamca/gkvfLe6s/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure)

Dans cet exemple, nous allons simplement transférer le mot « azure » en tant que paramètre de recherche et d’affichage les résultats mis en forme JSON renvoyés. Voici quelques autres exemples de requêtes, que vous pouvez essayer.

-	`Faceting` : une fois que l’utilisateur a recherché le jeu de données, la possibilité de filtrer les données est un excellent moyen d’accéder aux résultats. Pour avoir confirmation, vous allez démarrer avec un ensemble de catégories (facettes) qui s’affichent sur l’utilisateur. Voici quelques exemples de facettes que nous pouvons exploiter :
  -	**Balises** : de nombreuses questions sont associées à des balises pour permettre aux utilisateurs d’examiner des catégories spécifiques
  -	**Dates** : un utilisateur peut souhaiter afficher uniquement les questions qui ont été posées ou ont reçu une réponse pendant une période spécifique
  -	**Utilisateur** : il se peut que vous souhaitiez voir ou limiter des résultats d’utilisateurs spécifiques. Dans cet exemple, que nous allons rechercher « azure », mais retourner le nombre de facettes correspondant aux noms d’utilisateur tagsCollection et acceptedAnswerDisplayName.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26facet=tagsCollection%26facet=acceptedAnswerDisplayName>

-	`Filtering` : Une fois que l’utilisateur a choisi une facette, vous devez effectuer une autre recherche, mais utiliser un filtre pour limiter les résultats de cette valeur de facette. Par exemple, pour rechercher « Azure », mais limiter les résultats aux emplacements où il existe une balise d’« architecture », dans l’ordre décroissant viewCount :

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28%26search=azure%26$filter=tagsCollection/any(t:+t+eq+'architecture')%26$orderby=viewCount+desc>

-	`Spelling Mistakes` : notre nouvelle assistance (version préliminaire) d’[Expressions de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx) vous permet également de lancer certaines requêtes plutôt sophistiquées, par exemple la mise en correspondance partielle des résultats et la limitation de recherche à des champs spécifiques. L’exemple suivant recherche le mot « visualiser » dans le champ titre, mais le ~ indique une correspondance approximative, ce qui signifie que les résultats tels que « visualiser » et « visualisation » sont également renvoyés.

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28&search%3Dtitle%3Avisualise~%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

-	`Scoring and Tuning` : les [profils de score](https://msdn.microsoft.com/library/azure/dn798928.aspx) sont extrêmement utiles lorsqu’il s’agit d’optimiser les résultats retournés par le service de recherche. En fait, maintenant, nous pouvons également utiliser les [expressions de requête Lucene](https://msdn.microsoft.com/library/mt589323.aspx) pour appliquer l’évaluation à des champs individuels et des conditions à la volée. Par exemple, si nous voulions rechercher les mots « visualiser » ou « graphique » dans le champ titre, ou encore donner plus de poids aux éléments contenant le mot « graphique », nous pourrions procéder comme suit :

> <http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=stackexchange&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2015-02-28-Preview%26search%3Dtitle%3Avisualise+OR+title%3Achart%5E3+%26querytype%3Dfull%26searchMode%3Dall%26%24select%3Dtitle>

  Il existe dans ce jeu de données de nombreux autres champs pouvant être utilisés pour obtenir des résultats pertinents pour les utilisateurs. Par exemple, je peux utiliser :

  -	**Magnitude** évaluation des champs numériques tels qu’answerCount, commentCount, favoriteCount et viewCount pour améliorer les résultats de recherche si leur nombre est élevé.
  -	**Fraîcheur** évaluation de champs d’horodatage tels que creationDate et lastActivityDate pour améliorer les éléments qui ont été créés ou activés plus récemment
  -	**Pondérations de champ** pour indiquer si le texte de recherche est dans le corps de la question. Il est alors plus pertinent que s’il se trouve dans la réponse.

Parmi les autres choses que vous pouvez souhaiter lire, vous trouverez :

-	[`Suggestions`](https://msdn.microsoft.com/library/azure/mt131377.aspx) : comme les utilisateurs tapent dans la zone de recherche, il conviendra d’utiliser des champs tels que Titre, Balises et Nom d’utilisateur pour la saisie automatique.  

-	`Recommendations` : vous aurez souvent besoin d’outils tels que Apache Mahout ou Azure Machine pour vous aider à créer des recommandations permettant d’afficher des questions similaires pouvant intéresser les utilisateurs, mais heureusement, ce jeu de données contient déjà des recommandations.

N’hésitez pas à jouer avec cette page JSFiddle pour essayer différents types de requêtes. Si vous souhaitez en savoir plus sur la création de cet index, poursuivez la lecture. N’hésitez pas également à me joindre directement sur mon compte twitter [@liamca](https://twitter.com/liamca).

## Comment cet index de recherche Azure a-t-il été créé ?

Brent a déjà accompli la majeure partie du travail montrant comment organiser les données dans une base de données SQL. Si vous souhaitez exécuter cette procédure d’organisation des données vous-même, consultez le [didacticiel ici](http://www.brentozar.com/archive/2014/01/how-to-query-the-stackexchange-databases/). Autrement, vous pouvez simplement exploiter la base de données Azure SQL déjà renseignée avec certaines des données du 15 octobre 2015, ou faire un essai avec l’index Azure Search que vous avez créé. Ces détails sont expliqués ci-dessous dans la section « Importation de données intermédiaire à partir de la base de données Azure SQL ». La seule opération effectuée qui n’était pas prévue par Brent a été de créer une vue dans la case de données Azure SQL qui sera utilisée par le [indexeur de recherche Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx) pour analyser et acquérir des données à partir d’un groupe de tables que je souhaite utiliser. Voici comment cet écran est défini.

    CREATE VIEW StackExchangeSearchData as
    SELECT
          PQ.[Id]
          ,PQ.[AcceptedAnswerId]
          ,PQ.[AnswerCount]
          ,PQ.[Body]
          ,PQ.[ClosedDate]
          ,PQ.[CommentCount]
          ,PQ.[CommunityOwnedDate]
          ,PQ.[CreationDate]
          ,PQ.[FavoriteCount]
          ,PQ.[LastActivityDate]
          ,PQ.[LastEditDate]
          ,PQ.[LastEditorDisplayName]
    	  ,PUQ.DisplayName OwnerDisplayName
    	  ,PUQ.Reputation OwnerReputation
          ,PQ.[Score]
          ,reverse(REPLACE(LTRIM(REPLACE(reverse(REPLACE(REPLACE (PQ.[Tags], '>' , '],' ), '<' , '[' )), ISNULL(',', '0'), ' ')), ' ', ISNULL(',', '0'))) TagsCollection
          ,PQ.[Title]
          ,PQ.[ViewCount]
    	  ,PA.[Body] AcceptedAnswerBody
    	  ,PA.[Score] AcceptedAnswerScore
    	  ,PUQ.DisplayName AcceptedAnswerDisplayName
    	  ,PUQ.Reputation AcceptedAnswerReputation
    	  ,PA.[FavoriteCount] AcceptedAnswerFavoriteCount
    	  ,PL.[RelatedPostId]
      FROM [StackExchange].[dbo].[Posts] PQ
      LEFT OUTER JOIN [StackExchange].[dbo].[Posts] PA
      and PQ.AcceptedAnswerId = PA.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[PostLinks] PL
      on PQ.Id = PL.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUQ
      on PQ.OwnerUserId = PUQ.Id
      LEFT OUTER JOIN [StackExchange].[dbo].[Users] PUA
      on PA.[OwnerUserId] = PUA.Id
      WHERE PQ.PostTypeId = 1

Une fois cette opération terminée, vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour « Importer des données » de la vue de SQL Azure, ce qui crée par la suite un index basé sur Azure Search et obéissant au schéma des champs de la vue. Si vous souhaitez utiliser la base de données SQL Azure que j’ai préparée, voici la chaîne de connexion en lecture seule que vous pouvez utiliser :

    Server=tcp:azs-playground.database.windows.net,1433;Database=StackExchange;User ID=reader@azs-playground;
    Password=EdrERBt3j6mZDP;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

<!---HONumber=Nov15_HO2-->