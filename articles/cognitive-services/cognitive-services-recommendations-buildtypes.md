<properties
	pageTitle="Guide de démarrage rapide : API Machine Learning Recommendations | Microsoft Azure"
	description="Azure Machine Learning Recommendations - Guide de démarrage rapide"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

#  Types de build et qualité du modèle #

<a name="TypeofBuilds"></a>
## Quel type de build de recommandations dois-je utiliser ? ##

Nous prenons actuellement en charge deux types de build : les builds *Recommandation* et *FBT*. Chacune d’elles sont créées à l’aide de différents algorithmes et offrent des fonctionnalités bien distinctes. Ce document décrit chacune de ces builds, ainsi que les techniques permettant de comparer la qualité des modèles générés.


> Si ce n’est déjà fait, nous vous encourageons à suivre le [guide de démarrage rapide](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### Build de type recommandation ###

Le type de build *recommandation* utilise la factorisation de matrice pour fournir des recommandations. La version courte utilise les transactions de l’utilisateur pour générer des vecteurs de [fonctionnalité latente](https://en.wikipedia.org/wiki/Latent_variable) afin de décrire chaque élément, et utilise ensuite ces vecteurs latents pour comparer les éléments similaires.

En supposant que vous formiez le modèle en fonction des achats effectués dans votre boutique électronique, et en utilisant comme donnée d’entrée du modèle le moment auquel vous fournissez un téléphone Lumia 650, vous obtiendrez en retour un ensemble d’articles pouvant potentiellement intéresser les personnes sont susceptibles d’acheter un téléphone Lumia 650. Notez que les articles ne sont peut-être pas complémentaires. Dans cet exemple, il est possible que d’autres téléphones soient retournés dans la mesure où les personnes qui apprécient le Lumia 650 peuvent aussi aimer d’autres téléphones.

La build de recommandation possède deux fonctionnalités intéressantes :

-	Elle prend en charge le positionnement d’articles froids.

 Les *articles froids* désignent les articles qui ne sont pas utilisés de façon intensive. Par exemple, imaginez que vous venez de recevoir le tout nouveau téléphone Lumia. Étant donné que vous n’en n’avez jamais vendu, le système ne peut pas déduire les recommandations pour ce produit uniquement sur la base des transactions. Autrement dit, le système doit se renseigner à partir des informations concernant le produit lui-même.

 Si vous souhaitez utiliser le positionnement d’articles froids, vous devez fournir les informations relatives aux fonctionnalités de chaque article figurant dans le catalogue. Voici la manière dont peuvent se présenter les premières lignes du catalogue (notez le format clé=valeur utilisé pour les fonctionnalités) :

> 6CX-00001,Surface Pro2, Surface, Type=Matériel, Stockage=128 Go, Mémoire=4G, Fabricant=Microsoft

> 73H-00013,Wake Xbox 360,Gaming, Type=Logiciel, Langue=Anglais, Classification=Mature

> WAH-0F05,Minecraft Xbox 360,Gaming, * Type=Logiciel, Langue=Espagnol, Classification=Récent

> ...

 En outre, dans le cadre des paramètres de build, vous devez définir les paramètres de build suivants :

| Paramètre de build | Remarques
|------------------     |-----------
|useFeaturesInModel | Défini sur true. Indique si des caractéristiques peuvent être utilisées pour améliorer le modèle de recommandation. 
|allowColdItemPlacement | Défini sur true. Indique si la recommandation doit également placer les éléments froids selon la similarité des caractéristiques.
| modelingFeatureList | Liste de noms de caractéristiques séparés par des virgules à utiliser dans la build de recommandation pour améliorer les recommandations. Par exemple, « Langue, Stockage » dans l’exemple ci-dessus.

-	Elle prend en charge les recommandations utilisateur.

 Le type de build Recommandation prend en charge les [recommandations de l’utilisateur](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Cela signifie qu’elle peut s’appuyer sur l’historique des transactions d’un utilisateur afin de proposer à cet utilisateur des recommandations personnalisées. Pour les recommandations de l’utilisateur, vous pouvez fournir l’ID d’utilisateur et/ou l’historique de transactions récent de cet utilisateur.

 Il peut être intéressant d’appliquer les recommandations de l’utilisateur lorsque, par exemple, l’utilisateur se connecte à votre boutique/site, sur la page d’accueil. Vous pouvez alors promouvoir le contenu qui s’applique à cet utilisateur spécifique.
 
 Vous pouvez également appliquer un type de build de recommandation lorsque l’utilisateur est sur le point de procéder au paiement. À ce stade, vous avez accès à la liste des articles que le client est sur le point d’acheter ; l’occasion idéale de fournir des recommandations compte tenu du panier en cours.
 
#### Paramètres de génération de recommandation 
 
| Nom | 	Description |	 Type, <br> Valeurs valides <br> (valeur par défaut)
|-------|-------------------|------------------
| NumberOfModelIterations |	Le nombre d'itérations effectuées par le modèle est déterminé par le temps de calcul total et la précision du modèle. Un nombre élevé signifie une plus grande précision, mais aussi des temps de calcul plus longs. |	 Entier, <br> 10 à 50 <br>Par défaut : 40 
| NumberOfModelDimensions |	Le nombre de dimensions correspond au nombre de « caractéristiques » que le modèle tente de trouver dans vos données. Le fait d'augmenter le nombre de dimensions permet de mieux ajuster les résultats en clusters plus petits. Toutefois, si le nombre de dimensions est trop important, le modèle ne pourra pas trouver de corrélations entre les éléments. |	Entier, <br> 10 à 40 <br>Par défaut : 20 |
| ItemCutOffLowerBound |	Définit le nombre minimal de points d’utilisation où un élément doit se trouver pour qu’il soit pris en compte dans le modèle. |		Entier, <br> 2 ou Plus. <br> Par défaut : 2 |
| ItemCutOffUpperBound | 	Définit le nombre maximal de points d’utilisation où un élément doit se trouver pour qu’il soit pris en compte dans le modèle. | Entier, <br> 2 ou Plus.<br> Par défaut : 2147483647 |
|UserCutOffLowerBound |	Définit le nombre minimal de transactions qu’un utilisateur doit avoir effectuées pour être pris en compte dans le modèle. |	Entier, <br> 2 ou Plus. <br> Par défaut : 2 
| ItemCutOffUpperBound |	Définit le nombre maximal de transactions qu’un utilisateur doit avoir effectuées pour être pris en compte dans le modèle. |	Entier, <br>2 ou Plus. <br> Par défaut : 2147483647|
| UseFeaturesInModel |	Indique si des caractéristiques peuvent être utilisées pour améliorer le modèle de recommandation. | 	 Booléen<br> Par défaut : True 
|ModelingFeatureList |	Liste de noms de caractéristiques séparés par des virgules à utiliser dans la build de recommandation pour améliorer les recommandations. (Dépend des fonctionnalités importantes) |	Chaîne, 512 caractères maximum
| AllowColdItemPlacement |	Indique si la recommandation doit également placer les éléments froids selon la similarité des caractéristiques. | Booléen <br> Par défaut : False	
| EnableFeatureCorrelation | Indique si des caractéristiques peuvent être utilisées dans le raisonnement. |	Booléen <br> Par défaut : False
| ReasoningFeatureList |	Liste de noms de caractéristiques séparés par des virgules à utiliser pour générer des phrases de raisonnement (par exemple, pour expliquer les recommandations). (Dépend des fonctionnalités importantes pour les clients) | Chaîne, 512 caractères maximum
| EnableU2I |	Autoriser la recommandation personnalisée alias U2I (recommandations d’éléments selon un utilisateur). | Booléen <br>Par défaut : True
|EnableModelingInsights |	Définit si l’évaluation hors connexion doit être effectuée pour collecter des informations sur la modélisation (par exemple, les mesures de précision et de diversité). Si cette valeur est définie sur True, un sous-ensemble des données ne sera pas utilisé pour l’apprentissage car il sera réservé pour le test du modèle. En savoir plus sur les [évaluations hors connexion](#OfflineEvaluation) | Booléen <br> Par défaut : False
| SplitterStrategy | Si enable modeling insights (Activer les informations de modélisation) est définie sur True, la méthode selon laquelle les données doivent être fractionnées à des fins d’évaluation | Chaîne, *RandomSplitter* ou *LastEventSplitter* <br>Par défaut : RandomSplitter 


<a name="FBTBuild"></a>
### Build de type FBT ###

*FBT* est l’acronyme de « Frequently-Bought-Together », qui désigne des produits fréquemment achetés ensemble. La build FBT effectue une analyse qui compte le nombre de fois où deux ou trois produits différents sont achetés ensemble, puis trie les ensembles obtenus sur la base d’une fonction de similarité (Co-occurrences, Jaccard, Lift).

Jaccard et Lift sont des techniques de normalisation des occurrences simultanées. Autrement dit, les articles sont renvoyés uniquement s’ils sont effectivement achetés avec l’élément de départ.

Dans notre exemple de téléphone Lumia 650, un téléphone X est renvoyé si et seulement si le téléphone X a été acheté au cours de la même session que le téléphone Lumia 650. Étant donné que cette situation est peu probable, il est préférable de retourner des articles complémentaires du Lumia 650, par exemple un protecteur d’écran ou un adaptateur compatible.

On suppose actuellement que deux articles sont achetés au cours de la même session si l’achat est effectué dans une transaction associée au même ID utilisateur et au même horodatage.

Les builds FBT ne gèrent pas les articles froids pour le moment, puisque, par définition, deux articles doivent être effectivement achetés au cours de la même transaction. Bien que les builds FBT puissent retourner des jeux d’articles (triplets), elles ne gèrent pas les recommandations personnalisées puisqu’elles acceptent un seul article de départ comme donnée d’entrée.


#### Paramètres de build FBT 
 
| Nom | 	Description |		Type, <br> Valeurs valides <br> (valeur par défaut)
|-------|---------------|-----------------------
| FbtSupportThreshold | Niveau de conservatisme du modèle. Nombre de co-occurrences d'éléments à prendre en compte pour la modélisation. | Entier, <br> 3-50 <br> Par défaut : 6 
| FbtMaxItemSetSize | Limite le nombre d'éléments dans un ensemble fréquent.| Entier <br> 2-3 <br> Par défaut : 2
| FbtMinimalScore | Score minimal d'un jeu fréquent pour que celui-ci soit inclus dans les résultats retournés. Plus le score est élevé, mieux c'est. | Double <br> 0 et supérieur <br> Par défaut : 0
| FbtSimilarityFunction | Définit la fonction de similarité à utiliser par le build. Élévation favorise la sérendipité, cooccurence favorise la prévisibilité, et Jaccard est un bon compromis entre les deux. | Chaîne, <br> <i>cooccurrence, lift, jaccard</i><br> Par défaut : <i>jaccard</i> 

<a name="SelectBuild"></a>
## Comment choisir la build exacte à utiliser ? ##

Les instructions ci-dessus peuvent suffire en soi à vous aider à déterminer si vous devez utiliser une build de recommandation ou une build FB... mais elles n’offrent aucune réponse affirmative pour les situations où les deux versions peuvent convenir. Même si vous savez que vous souhaitez utiliser une build de type FBT, il vous reste encore peut-être à décider si vous souhaitez utiliser Jaccard ou Lift comme fonction de similarité.

La meilleure approche consiste à tester les deux builds en environnement réel (version d’évaluation en ligne) et à suivre un taux de conversion pour les différentes builds. Le taux de conversion peut être mesuré en fonction du nombre de clics de recommandation, du nombre d’achats réels effectués à partir des recommandations affichées, ou même du montant réel des ventes lorsque les différentes recommandations sont affichées. Vous pouvez sélectionner votre mesure du taux de conversion en fonction de vos objectifs métier.

Dans certains cas, vous souhaiterez peut-être évaluer le modèle en mode hors connexion avant de le mettre en production. Bien qu’une évaluation hors connexion ne puisse pas remplacer une évaluation en ligne, elle peut vous servir de mesure.

<a name="OfflineEvaluation"></a>
## Évaluation hors connexion  ##

L’objectif d’une évaluation en mode hors connexion est de prédire la précision (le nombre d’utilisateurs qui achèteront réellement l’un des articles recommandés) et la diversité des recommandations (le nombre réel d’articles recommandés). Dans le cadre de l’évaluation des mesures de précision et de diversité, le système identifie un groupe d’utilisateurs échantillon, puis répartit en deux groupes les transactions de ces utilisateurs : le jeu de données d’apprentissage et le jeu de données de test.

> Remarque :
>
> Pour utiliser des mesures en mode hors connexion, vos données d’utilisation doivent contenir des horodatages (car les données temporelles sont indispensables pour fractionner correctement l’utilisation entre les jeux de données d’apprentissage et de test).

> En outre, une évaluation hors connexion peut ne pas produire de résultats pour les fichiers peu utilisés, pour la simple raison que le jeu de données de test doit comporter au moins 1 000 points d’utilisation pour que l’évaluation soit précise.

<a name="Precision"></a>
### Précision à k ###
Le tableau ci-dessous représente la sortie de l’évaluation de la précision à k en mode hors connexion.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|Pourcentage |	13,75 |	18,04 | 21 |	24,31 |	26,61
|Nombre d’utilisateurs testés |	10 000 |	10 000 |	10 000 |	10 000 |	10 000
|Nombre d’utilisateurs considérés |	10 000 |	10 000 |	10 000 |	10 000 |	10 000
|Nombre d’utilisateurs non considérés |	0 |	0 |	0 |	0 |	0

#### K
Dans le tableau ci-dessus, *K* représente le nombre de recommandations présentées au client. Vous devez donc interpréter le tableau de la manière suivante : « Si, pendant le test, une seule recommandation est présentée aux clients, seuls 13,75 % des utilisateurs ont réellement acheté l’article recommandé ». (En supposant que le modèle a été formé avec les données d’achat). On peut aussi simplement dire que la « précision à 1 » est de 13,75.

Vous remarquerez que la probabilité qu’un client achète un article recommandé augmente est proportionnelle au nombre d’articles présentés au client. Dans l’expérience ci-dessus, la probabilité passe à 26,61 % lorsque 5 articles sont recommandés.

#### Pourcentage
Pourcentage d’utilisateurs qui ont réagi à au moins une des K recommandations indiquées. Le pourcentage est calculé en divisant le nombre d’utilisateurs ayant réagi à au moins une recommandation par le nombre total d’utilisateurs considérés. (Voir la définition des utilisateurs considérés).

#### Nombre d’utilisateurs testés
Nombre total d’utilisateurs dans le jeu de données de test.

#### Nombre d’utilisateurs considérés
Un utilisateur est pris en compte uniquement si le système a recommandé au moins K éléments à partir du modèle généré à l’aide du jeu de données d’apprentissage.

#### Nombre d’utilisateurs non considérés
Tous les utilisateurs non pris en compte, c’est-à-dire les utilisateurs n’ayant pas reçu au moins K articles recommandés.

Nombre d’utilisateurs non considérés = Nombre d’utilisateurs testés – Nombre d’utilisateurs considérés

<a name="Diversity"></a>
### Diversité ###
Les mesures de diversité permettent d’évaluer le type d’éléments recommandés. Le tableau ci-dessous représente la sortie de l’évaluation de la diversité en mode hors connexion.

|Plage de percentiles |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|Pourcentage | 34,258 | 55,127| 10,615


Nombre total d’articles recommandés : 100 000

Nombre d’articles uniques recommandés : 954

#### Plages de percentiles
Chaque plage de percentiles est représentée par une étendue (valeurs min/max comprises entre 0 et 100). Les articles proche de 100 représentent les articles les plus populaires et les articles proches de 0 représentent les articles les moins populaires. Par exemple, si la valeur de pourcentage correspondant à la plage de percentiles 99-100 est de 10,6, cela signifie que 10,6 % des recommandations ont retourné uniquement 1 % des articles les plus populaires. La valeur minimale de la plage de percentiles est incluse, et la valeur maximale est exclusive à l’exception de 100.
#### Nombre d’articles uniques recommandés
Nombre d’articles distincts retournés pour évaluation.
#### Nombre total d’articles recommandés
Nombre total d’articles recommandés (doublons possibles)


<a name="ImplementingEvaluation"></a>
### Comment obtenir des évaluations hors connexion ? ###
Les mesures de précision et de diversité en mode hors connexion peuvent vous aider à choisir la build que vous devrez utiliser. Pour obtenir des mesures en mode hors connexion, vous devez effectuer les opérations suivantes :

Au moment de la build, dans le cadre des paramètres de build FBT ou de recommandation :
1.	Définissez le paramètre de build enableModelingInsights sur true.

2.	Vous pouvez éventuellement sélectionner *splitterStrategy* (*RandomSplitter* ou *LastEventSplitter*). *RandomSplitter* fractionne les données d’utilisation dans les jeux de données d’apprentissage et de test à partir des pourcentages de test et des valeurs de départ aléatoires *randomSplitterParameters*. *LastEventSplitter* fractionne les données d’utilisation dans les jeux de données d’apprentissage et de test en fonction de la dernière transaction de chaque utilisateur.

Ceci déclenche une build qui utilise uniquement un sous-ensemble des données pour l’apprentissage, en réservant les autres données pour calculer les mesures d’évaluation. Une fois la build terminée, vous devez simplement appeler [l’API Get build metrics](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/573e43bb3e9d4627a8c4bd3e/console) en transmettant les valeurs *modelId* et *buildId* correspondantes pour obtenir le résultat de l’évaluation.

 Voici le résultat JSON obtenu pour l’exemple d’évaluation que nous avons effectuée :


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }

<!---HONumber=AcomDC_0706_2016-->