<properties pageTitle="Recommandations « Fréquemment achetés ensemble » d'Azure Search | Microsoft Azure | Apache Mahout | Azure Machine Learning" description="Exemple de code pour l'ajout de recommandations Fréquemment achetés ensemble, Nouveautés pour vous ou A également acheté à une application Azure Search à l'aide d'Apache Mahout ou d'Azure Machine Learning" services="search" documentationCenter="" authors="liamca" manager="pablocas" editor="" tags="machine learning"/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="11/12/2015"
   ms.author="liamca"/>

# Recommandations « Fréquemment achetés ensemble » d'Azure Search

Si vous avez déjà effectué des achats en ligne, vous avez probablement remarqué une liste de type « Fréquemment achetés ensemble » ou « Les clients ayant acheté cet article ont également acheté » ou même « Nouveautés pour vous » qui présente d'autres marchandises ou services susceptibles de vous intéresser. En règle générale, ces données sont compilées par des détaillants en ligne en fonction de l'historique des achats de nombreux clients, ou uniquement des données préférentielles qui vous sont uniques. La possibilité de proposer des recommandations pertinentes, centrées sur le client via la recherche est un ajout puissant non seulement pour les applications commerciales, mais également pour les sites qui fournissent des informations ou du contenu multimédia.

Dans les applications Azure Search, vous pouvez mettre en œuvre des recommandations en écrivant du code supplémentaire qui intègre des données et des services permettant d'identifier des modèles et des relations dans les données, puis de les transformer en recommandations pertinentes pour vos utilisateurs.

[Cet exemple illustre comment](https://github.com/liamca/azure-search-recommendations), en utilisant [Apache Mahout]() pour le moteur de recommandations.

Dans le reste de cet article, je vais vous présenter l'exemple de code qui ajoute des recommandations de type « Fréquemment louées ensemble » à l'aide d'exemples de données sur les locations de films.

![1](./media/search-fbt-recommendations/product_recommendations.png)

## Qu'est-ce qu'une recommandation ?

Les *recommandations* constituent une technique permettant d'exposer plusieurs éléments d'un catalogue en fonction d'une activité de l'utilisateur de recherche existante (par exemple les journaux web) utilisée pour recommander des éléments et améliorer la conversion.

Les moteurs de recommandations sont souvent formés à l'aide de l'activité précédente du client ou en collectant les données directement à partir d'un magasin numérique. Dans cet exemple, j'utilise Apache Mahout pour compiler les données de recommandation.

Les recommandations courantes incluent : - Fréquemment achetés ensemble : un client qui a acheté ceci a également acheté cela - Recommandations client pour élément : un client comme vous a également acheté cela

## Création de l'index Azure Search

- Ouvrez la solution AzureSearchMovieRecommendations.sln et définissez ImportAzureSearchIndexData comme projet par défaut.  
- Ouvrez Program.cs dans ImportAzureSearchIndexData et modifiez SearchServiceName et SearchApiKey pour pointer vers votre service Azure Search.
- Téléchargez hetrec2011-movielens-2k.zip à partir de http://grouplens.org/datasets/hetrec-2011/ et copiez les fichiers Movies.dat et user\_ratedmovies.dat dans \\ImportAzureSearchIndexData\\data
- Exécutez le projet pour créer un index et charger des données de film. 
- À la fin, l'application exécute un test de recherche.

## Création d'une simple application HTML pour la recherche dans les films

Vous trouverez une application web JavaScript complète vous permettant d'interroger l'index Azure Search sous : \\WebSite\\starter-template-complete

Si vous souhaitez parcourir la démonstration depuis le début, le CSS d'origine se trouve ici : \\WebSite\\starter-template

Ouvrez le fichier search.js dans \\WebSite\\starter-template-complete et mettez à jour la clé API et azureSearchService avec les détails de votre service Azure Search.

Vous devez pouvoir ouvrir ce fichier dans un navigateur tel que Chrome pour afficher désormais les films en tapant dans la zone de recherche.

## Commande d'exécution de Création de recommandations à l'aide de Mahout

- Téléchargez le fichier data\\movie\_usage.txt dans le stockage d'objets Blob Azure 
- Créez une instance HDInsight (activation du Bureau à distance) et connectez-vous à l’ordinateur via le Bureau à distance (disponible à partir du portail Azure Classic)
- À partir de l'ordinateur HDInsight, ouvrez la « ligne de commande Hadoop »
- Passez au répertoire bin de Mahout dans c:\\apps\\dist. Le mien ressemble à ceci, mais vous pouvez obtenir une version plus récente de Mahout C:\\apps\\dist\\mahout-1.0.0.2.3.3.0-2992\\bin
- Exécutez la ligne de commande suivante, où vous remplacez [CONTAINER] et [STORAGEACT] par les détails de votre stockage Azure (où vous avez placé le fichier movie\_usage.txt) :

    mahout itemsimilarity -s SIMILARITY\_COSINE --input "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/movie\_usage.txt" --output "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/output/" --tempDir "wasb://[CONTAINER]@[STORAGEACT].blob.core.windows.net/temp" -m 5

Ceci doit prendre plusieurs minutes, mais une fois l'opération terminée, votre conteneur de stockage doit contenir le fichier suivant qui inclut vos recommandations de films : /movies/output/part-r-00000

Ce fichier comporte 3 colonnes : [ID d'élément de film] [ID d'élément de recommandations relatives à ce film], [Pourcentage de similarité]

## Importation de données à partir de Mahout vers Azure Search

Le programme qui a créé l'index Azure Search a également créé un champ appelé `Recommendations` qui est de type Collection (qui se présente comme un ensemble de chaînes séparées par des virgules). Nous allons fusionner les données créées à l'étape précédente avec cet index Azure Search.

- À partir de la solution Visual Studio AzureSearchMovieRecommendations.sln, ouvrez Program.cs dans MahoutOutputLoader.
- Mettez à jour SearchServiceName et SearchApiKey avec les détails de votre service Azure Search
- Mettez à jour StorageApiKey et StorageAccountName avec les détails de votre compte Azure Storage pour lequel vous avez stocké le fichier de recommandations produit de Mahout
- Exécutez l'application pour fusionner les données
 
## Visualisation des recommandations
À ce stade, vous devez être en mesure de revenir à l'application web et de cliquer sur l'un des films pour voir les recommandations.

Si vous voulez voir comment les recommandations ont été retournées lorsque vous avez cliqué sur cette image, ouvrez Search.js et examinez la fonction openMovieDetails().

## Crédit

Données fournies par GroupLens (http://grouplens.org/datasets/hetrec-2011/)

Consultez cette page pour plus d'informations sur les licences de ces données : http://files.grouplens.org/datasets/hetrec2011/hetrec2011-movielens-readme.txt

<!---HONumber=AcomDC_1203_2015-->