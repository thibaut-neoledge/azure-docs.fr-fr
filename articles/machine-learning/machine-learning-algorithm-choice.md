<properties 
	pageTitle="Comment choisir les algorithmes d'apprentissage automatique | Microsoft Azure" 
	description="Comment choisir les algorithmes Azure Machine Learning pour l'apprentissage supervisé et non supervisé dans les expériences de clustering, classification ou régression." 	
	services="machine-learning"
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# Comment choisir des algorithmes Azure Machine Learning pour le clustering, la classification ou la régression

Cette rubrique décrit certains aspects de base de l'approche d'apprentissage automatique. En particulier, vous découvrirez comment choisir les algorithmes d'apprentissage automatique appropriés pour analyser des types de données spécifiques, répondre aux questions posées, accomplir des tâches spécifiées ou fournir des critères pour prendre des décisions.

> [AZURE.TIP]L'[aide-mémoire d'algorithme Microsoft Azure Machine Learning](machine-learning-algorithm-cheat-sheet.md) est une référence pratique pour accompagner cet article.

Lorsque nous utilisons l’apprentissage automatique pour effectuer des analyses, nous devons souvent répondre à deux questions :

* Quel type d’analyse devons-nous effectuer pour atteindre nos objectifs au moyen des données disponibles ? 
* Quel est l’algorithme ou le modèle le plus approprié pour y parvenir ?

Nous allons étudier trois types d'analyse d'apprentissage automatique et comparer leurs différents cas d'usage :

* **Clustering**
* **Classification** 
* **Régression** 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## Les algorithmes d'apprentissage automatique apprennent à partir des données

L'apprentissage automatique est une discipline qui étudie une classe d'algorithmes conçus pour apprendre à partir des données et ne pas imposer un modèle spécifique prédéterminé à tester sur ces données. L'objectif est d'acquérir des connaissances de manière plus inductive, en examinant les modèles d'un jeu de données au lieu d'appliquer la *méthode hypothético-déductive* selon laquelle vous essayez d'abord de deviner le modèle approprié pour l'ensemble du jeu de données avant de le tester de manière empirique.

Il existe deux types d'apprentissage automatique (ou encore apprentissage à partir des données) : l'*apprentissage supervisé* et l'*apprentissage non supervisé*.

<a name="anchor-2"></a>
## Apprentissage supervisé  

Ce type d'apprentissage nécessite une définition correcte de la variable cible, ainsi que la mise à disposition d'un nombre suffisant de ses valeurs.

L'apprentissage supervisé est le type d'apprentissage automatique effectué quand les résultats de sortie corrects (ou variables cibles) des instances d'apprentissage à entrer sont connus. L'objectif de la formation d'un algorithme d'apprentissage automatique est de trouver le modèle (c'est-à-dire une règle ou une fonction) qui mappe les entrées aux valeurs de sortie connues. Cela revient à demander à un superviseur d'indiquer à l'agent algorithmique s'il mappe correctement ou non les entrées aux sorties. Une fois que le processus d’apprentissage est terminé et que nous disposons d’un modèle utilisable, nous pouvons appliquer ce dernier aux nouvelles données d’entrée pour prédire la sortie attendue, dans laquelle la valeur cible n’est pas connue à l’avance, contrairement à celle du jeu de données d’apprentissage.

Le type de modèle est déterminé par la nature de la variable cible.

![Diagramme d'apprentissage supervisé : modéliser à partir de données étiquetées et utiliser le modèle pour prédire les résultats des nouvelles données.](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

Il existe deux grandes catégories d'analyse qui reposent sur l'apprentissage supervisé : la *classification* et la *régression*. Ce type d'apprentissage est fréquemment utilisé dans les problèmes de classification, car l'objectif consiste souvent à demander à l'ordinateur d'apprendre un système de classification que nous avons créé. Les réponses sont généralement fournies sous la forme de quelques variables connues (étiquettes), comme « true » ou « false », « high », « medium » ou « low ». Les algorithmes de classification s'appliquent aux valeurs nominales et non aux valeurs de réponse ordinales. L’algorithme de reconnaissance des chiffres est un exemple courant d’apprentissage de classification. En général, l'apprentissage de classification est adapté à tout problème pour lequel il est utile et facile de déterminer la classification.

Dans le cadre de l’apprentissage supervisé, les variables étudiées peuvent être réparties en deux groupes : variables explicatives (ou variables predictor) et variables dépendantes (variables de réponse). L'objectif de l'analyse est de définir une relation entre les variables explicatives et les variables dépendantes, comme c'est le cas dans l'*analyse de régression*. Les valeurs de la variable dépendante doivent être connues pour une partie suffisante importante du jeu de données. Dans une régression, les variables de sortie ou de réponse incluent des valeurs continues (nombre de milles au galon d’une voiture spécifique, âge d’une personne, etc.).

L'apprentissage supervisé est également la technique la plus courante pour former des réseaux neuronaux et des arbres de décision :

> Ces deux techniques dépendent beaucoup des informations fournies par les classifications prédéfinies. Dans le cas de réseaux neuronaux, la classification est d'abord utilisée pour déterminer l'erreur du réseau avant d'ajuster le réseau pour la réduire, et dans les arbres de décision, les classifications sont utilisées pour déterminer les attributs qui fournissent le plus d'informations pouvant être utilisées pour résoudre le puzzle de classification. Ces deux exemples se développent avec une certaine « supervision » sous la forme de classifications prédéfinies.

>  La reconnaissance vocale qui utilise les modèles Markov masqués et les réseaux bayésiens repose également sur certains éléments de supervision pour ajuster les paramètres afin de, comme d'habitude, réduire l'erreur sur les entrées données. [[Apprentissage automatique, 2ème partie : apprentissage supervisé et non supervisé](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/)]


<a name="anchor-3"></a>
##Apprentissage non supervisé

Dans le domaine de l'apprentissage automatique, le problème de l'apprentissage non supervisé est de déterminer s'il existe des modèles ou une structure masquée dans les données sans étiquette. Le modèle n’est pas fourni avec les « résultats corrects » d’un jeu de données sur lequel effectuer l’apprentissage. Comme des exemples sans étiquette sont donnés à l'apprenant, il n'y a pas de commentaires (ni erreur ni récompense) permettant d'évaluer une solution potentielle. L'objectif est de permettre à l'ordinateur d'apprendre à effectuer une action sans que nous ayons à lui expliquer, de manière explicite, comment procéder. Dans une situation d’apprentissage non supervisé, toutes les variables sont traitées de la même façon. Il n'existe aucune distinction entre les variables explicatives et les variables dépendantes. Toutefois, il existe toujours un objectif à atteindre, qui peut être un objectif général, comme la réduction des données, ou un objectif plus spécifique comme la recherche de clusters.

Dans Azure Machine Learning, nous pouvons effectuer à la fois un apprentissage supervisé et non supervisé via le **Clustering**, la **Classification** et la **Régression**.

   ![screenshot\_of\_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##Clustering
Le clustering est un exemple d’apprentissage non supervisé. Dans ce type d’apprentissage, l’objectif consiste à rechercher des similitudes dans les données d’apprentissage et à partitionner le jeu de données en sous-ensembles, délimités par ces similitudes. Bien souvent (même si ce n’est pas systématique), les clusters les plus significatifs découverts par ces procédures pilotées par les données sont cohérents avec notre classification intuitive, selon nos attentes.

Même si l'algorithme de clustering n'affecte pas de noms appropriés à ces clusters, il peut les produire et les utiliser pour anticiper les similitudes attendues dans les nouveaux exemples, en les classant dans le cluster le plus approprié. Cette approche, pilotée par les données, peut offrir de bons résultats, tant que la quantité de données est suffisante. Par exemple, les algorithmes de filtrage d’informations sociales (tels que ceux qu’utilise le site Amazon.com pour recommander des ouvrages) sont basés sur la recherche de groupes de personnes similaires, puis sur l’affectation des nouveaux utilisateurs à ces groupes, dans l’objectif de proposer des recommandations.

L'algorithme de clustering disponible dans Azure Machine Learning est [K-Means Clustering][k-means-clustering].

![Expérience de l'algorithme K-Means Clustering : capture d'écran](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

K-means est l'un des algorithmes d'apprentissage non supervisés les plus simples, qui permet de résoudre efficacement les problèmes de clustering connus. L'algorithme K-means met les données en cluster en s'efforçant de répartir les échantillons en N groupes de variance égale, ce qui permet de réduire l'impact du critère « inertie » ou du critère « somme des carrés intra-cluster » (WCSS, Within-Cluster Sum of Squares). Cet algorithme requiert la spécification d’un nombre de clusters. Il s'adapte à un grand nombre d'échantillons et a été utilisé dans une gamme étendue de domaines d'application, dans des champs très différents.

Le module d'algorithme [K-Means Clustering][k-means-clustering] retourne un modèle de clustering K-means non formé qui peut être transmis au module [Train Clustering Model][train-clustering-model] à des fins de formation.

![screenshot\_of\_experiment](./media/machine-learning-algorithm-choice/k4.png)

Cette illustration indique les options à configurer pour l'utilisation du module K-Means Clustering. La méthode K-means recherche un certain nombre de clusters pour un ensemble de points de données présentant d dimensions. En commençant par un *ensemble initial de centroïdes K*, la méthode utilise l'algorithme de Lloyd pour affiner les emplacements des centroïdes, de manière itérative. L'algorithme se termine quand les centroïdes sont stabilisés ou quand un *nombre spécifié d'itérations* est effectué. Le module initialise un tableau K-par-D avec les centroïdes finals, qui définissent les K clusters trouvées dans les N points de données. Cet algorithme utilise également un vecteur de longueur N, avec l’assignation de chaque point de données à l’un des K clusters. En cas de spécification d’un nombre précis de clusters à rechercher (K), le module affecte les premiers K points de données aux K clusters, dans l’ordre.


De plus, ce module accepte ou génère des points initiaux pour définir la configuration initiale du cluster. Le paramètre *Mesure* définit la méthode utilisée pour mesurer la distance entre un point de données et le centroïde. Chaque point de données est affecté au cluster présentant le centroïde le plus proche de ce point de données. Par défaut, la méthode utilise la *mesure Euclidienne*. Vous pouvez spécifier la *mesure Cosinus* en tant que mesure alternative à cette méthode. Notez que la méthode K-means peut uniquement rechercher une configuration de cluster optimale pour un jeu de données, en local. La méthode peut rechercher une configuration différente, voire supérieure, à laquelle est associée une autre configuration initiale.

<a name="anchor-5"></a>
##Classification 
Dans l'analyse de classification, nous divisons les exemples en différentes classes et utilisons un ensemble formé de données précédemment étiquetées. Cette technique est utilisée pour prédire l’appartenance à un groupe des instances de données. Microsoft Azure Machine Learning propose les algorithmes de classification suivants.

![screenshot\_of\_experiment](./media/machine-learning-algorithm-choice/help3.png)

Les *algorithmes à deux classes* sont utilisés pour les variables de réponse binaires (de type oui ou non, 0 ou 1, true ou false, etc.), alors que les *algorithmes multiclasses* sont utilisés pour toute variable de réponse nominale qui classifie les instances dans plus de deux classes.

### Instructions de sélection d’un algorithme de classification
Cette longue liste d’algorithmes donne lieu à une série de questions :

* Comment savoir quel est le classifieur le plus adapté pour un jeu de données spécifique ? 
* Existe-t-il des cas permettant d'identifier un classifieur comme un choix « logique » ? 
* Quels sont les principes de sélection d'un classifieur ?

Une approche recommandée consiste à tester plusieurs classifieurs différents, ainsi que différents jeux de paramètres au sein de chaque algorithme, puis à sélectionner le plus efficace à l’aide de la méthode de validation croisée.

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) vous permet d'essayer plusieurs algorithmes côte à côte sur les mêmes données et de comparer les résultats. Voici un exemple de la [Galerie Azure Machine Learning](http://gallery.azureml.net/) : [Comparer les classifieurs multiclasses : reconnaissance de lettres](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

Voici quelques indications générales pouvant faciliter la sélection. Tenez compte des points suivants lors du choix de l'algorithme à utiliser : [plan suggéré par le billet de blog [Choix d'un classifieur d'apprentissage automatique](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

**Quelle est la taille de vos données d'apprentissage ?** Si votre ensemble de formation est de petite taille, et si vous comptez former un classifieur supervisé, la théorie d'apprentissage automatique stipule que vous devez vous en tenir à un classifieur avec une forte compensation/faible variance, comme le modèle bayésien naïf. Ces classifieurs présentent un avantage par rapport aux classifieurs associés à un écart peu élevé/décalage élevé, comme kNN, qui présente des tendances au dépassement. Mais les classifieurs à faible compensation/forte variance sont plus appropriés si vous disposez d'un ensemble de formation plus important, car ils ont une erreur asymptotique plus petite. Dans ce cas, un classifieur à forte compensation n'est pas suffisamment puissant pour fournir un modèle précis. Il existe des résultats théoriques et empiriques qui indiquent que l’algorithme Naive Bayes fonctionne correctement dans de telles circonstances. Toutefois, notez que généralement il peut être plus avantageux d'avoir de meilleures données et de bonnes fonctionnalités plutôt qu'un meilleur algorithme. En outre, si vous disposez d'un très grand jeu de données, les performances de classification peuvent ne pas être très affectées par l'algorithme que vous utilisez. Dans ce cas, il est alors préférable de choisir votre algorithme en fonction de paramètres comme son extensibilité, sa vitesse ou sa simplicité d'utilisation.

**Devez-vous effectuer la formation de façon incrémentielle ou en mode par lot ?** Si vous disposez d'un lot de données ou que vos données sont souvent mises à jour, utilisez les algorithmes bayésiens qui se mettent à jour correctement. Les réseaux neuronaux et les machines à vecteur de support doivent utiliser les données d'apprentissage en mode par lot.

**Vos données sont-elles exclusivement catégoriques ou numériques, ou une combinaison des deux types ?** Les algorithmes bayésiens fonctionnent mieux avec des données catégoriques/binomiales. Les arbres de décision ne peuvent pas fournir des valeurs numériques dans leurs prédictions.

**Est-ce que vous ou votre audience avez besoin de comprendre le fonctionnement du classifieur ?** Les arbres de décision ou l'algorithme bayésien sont expliqués plus facilement. Il est beaucoup plus difficile de voir ou d'expliquer comment les réseaux neuronaux et les machines à vecteur de support classifient les données.

**À quelle vitesse la classification doit-elle être générée ?** Les arbres de décision peuvent être lents quand l'arbre est complexe. En revanche, les machines à vecteur de support classifient plus rapidement, car elles ont uniquement besoin de déterminer de quel côté de la « ligne » se trouvent les données.

**Quel est le degré de complexité que le problème présente ou exige ?** Les réseaux neuronaux et les machines à vecteur de support peuvent gérer une classification non linéaire complexe.

### Avantages et inconvénients des algorithmes de classification
Chaque algorithme de classification présente certains avantages et inconvénients :

<a name="anchor-5a"></a> **Avantages et inconvénients de la régression logistique :** « l'analyse de régression logistique repose sur le calcul des probabilités du résultat comme étant le rapport entre la probabilité d'obtenir le résultat et la probabilité de ne pas l'obtenir ». [[Régression logistique et analyses discriminatoires linéaires dans l'évaluation des facteurs associés à la prévalence de l'asthme chez les enfants de 10 à 12 ans : divergence et similitude des deux méthodes statistiques](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, et coll. (Journal international de pédiatrie, 2009) ID d'article 952042]
 
Le modèle logistique est paramétrique, il présente donc un avantage certain : il apporte des informations sur l'impact de chaque variable prédictives sur la variable de réponse.


Grâce aux interprétations probabilistes naturelles disponibles (contrairement aux arbres de décision ou aux machines à vecteur de support), vous pouvez facilement mettre à jour votre modèle pour qu'il incorpore les nouvelles données. Il existe de nombreuses façons de régler votre modèle et, contrairement au modèle bayésien naïf, vous n'avez pas besoin de vous soucier autant de la corrélation de vos fonctions. La régression logistique est utile si vous voulez :

* une infrastructure probabiliste pour ajuster les seuils de classification ;
* incorporer rapidement des données d'apprentissage supplémentaires ;  

L’analyse de régression logistique fonctionne mieux que les arbres de décision en cas de données volumineuses. Par exemple, il se peut qu’une classification de texte porte sur plus de 100 000 documents, de 500 000 mots chacun. L'adoption d'une règle simple, comme l'apprentissage d'un hyperplan linéaire, est préférable, car les arbres de décision présentent beaucoup trop de degrés de liberté et sont sujets au surapprentissage. Vous pouvez effectuer une sélection des fonctionnalités de façon à utiliser un arbre de décision sur les données de texte, mais vous risquez de perdre un grand nombre d'informations précieuses pour la classification du texte si vous sélectionnez un sous-ensemble réduit des fonctionnalités. L'utilisation de modèles d'apprentissage avec des données incluant de nombreuses dimensions favorisent la prolifération des erreurs de variance. Dans ce cas, il est préférable d'utiliser des modèles simples avec une compensation plus forte.

L'un des inconvénients de la régression logistique est qu'elle devient instable quand une variable prédictive peut quasiment expliquer la variable de réponse, car le coefficient de cette variable devient très important.

<a name="anchor-5b"></a> **Avantages et inconvénients des arbres de décision :** les [arbres de décision](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) sont faciles à interpréter et expliquer.

> Les [arbres de décision] gèrent facilement les interactions entre les fonctionnalités et ne sont pas paramétriques. Ainsi, vous n'avez pas à vous soucier des observations aberrantes ou de savoir si les données sont séparables de manière linéaire (par exemple, les arbres de décision gèrent facilement les cas où la classe A figure à l'extrémité inférieure d'une fonction x quelconque ; la classe B, sur la plage moyenne de cette fonction x et de nouveau la classe A, à l'extrémité supérieure). L'un des inconvénients est qu'ils ne prennent pas en charge l'apprentissage en ligne. Vous devez donc régénérer votre arbre quand de nouveaux exemples apparaissent. Un autre inconvénient est une tendance au surapprentissage. C'est à ce niveau que les méthodes d'ensembles (comme les forêts aléatoires ou les arbres boostés) peuvent s'avérer utiles. De plus, les forêts aléatoires sont souvent le vainqueur d'un grand nombre de problèmes de classification (généralement légèrement devant les machines à vecteur de support, je pense) : elles sont rapides et évolutives, et vous n'avez pas à vous soucier d'ajuster un ensemble de paramètres comme avec les machines à vecteur de support. [[Choix d'un classifieur d'apprentissage automatique](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

Les arbres de décision génèrent la sortie sous forme de règles accompagnées de mesures telles que la *prise en charge*, la *confiance* et l'*élévation*.


<a name="anchor-5c"></a> **Avantages et inconvénients des machines à vecteur de support :** les machines à vecteur de support sont efficaces dans les espaces incluant de nombreuses dimensions, même quand le nombre de dimensions est supérieur au nombre d'échantillons. Toutefois, les performances de la méthode sont susceptibles de s'avérer médiocres si le nombre de fonctionnalités est nettement supérieur au nombre d'échantillons. Cette méthode utilise également une faible quantité de mémoire, car elle exploite un sous-ensemble de points de formation dans la fonction de prise de décision (connu sous le nom de vecteurs de support). Elle est très polyvalente : différentes fonctions de noyau, à la fois courantes et personnalisées, peuvent être spécifiées pour la fonction de prise de décision. Les fonctions de noyau permettent de transformer les exemples de formation incluant un faible nombre de dimensions en exemples incluant davantage de dimensions, ce qui peut être utile pour résoudre des problèmes de séparabilité linéaire.

Cependant, les SVM ne proposent aucune estimation directe des probabilités. Celles-ci sont calculées via une validation croisée à cinq plis, qui s’avère exigeante.

>[Avec] une haute précision, des garanties théoriques intéressantes concernant le surapprentissage et un noyau approprié, elles peuvent fonctionner parfaitement même si vos données ne sont pas séparables de façon linéaire dans l'espace de la fonctionnalité de base. [Les SVM sont] particulièrement reconnues pour résoudre les problèmes de classification de texte, où les espaces incluant plusieurs dimensions sont la norme. [[Choix d'un classifieur d'apprentissage automatique](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]

Contrairement aux forêts, les SVM étaient à l’origine des classifieurs à deux classes. Cependant, ils ont récemment été adaptés pour pouvoir travailler avec plusieurs classes. Nous pouvons utiliser un apprentissage de type « one-vs-rest » pour créer un classifieur capable de gérer des classes multiples, ce peut s’avérer moins performant. Puisque les SVM peuvent uniquement gérer les sorties à deux classes (c’est-à-dire, une variable de sortie catégorique avec une variété de 2), avec N  classes, l’apprentissage concerne N SVM (SVM apprend « Output==1 » vs « Output != 1 », SVM 2 apprend « Output==2 » vs « Output != 2 », ..., et SVM N apprend « Output==N » vs “Output != N »). Pour prédire la sortie d'une nouvelle entrée, il crée simplement la prédiction avec chaque SVM, puis détecte celle qui place la prédiction le plus loin dans la région positive. [[Machines à vecteur de support](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University 2001)]

<a name="anchor-5d"></a> **Avantages et inconvénients du modèle bayésien naïf :** les classifieurs [bayésiens naïfs (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) sont souvent choisis pour résoudre les problèmes de classification. Ils partent du principe que les fonctionnalités sont indépendantes, d'où le nom de technique « naïve ».

> Si l'hypothèse de l'indépendance conditionnelle du classifieur NB est avérée, ce dernier converge plus vite que les modèles discriminatoires, comme la régression logistique, et permet un nombre moins important de données d'apprentissage. Même si cette hypothèse n'est pas avérée, le classifieur NB est souvent très efficace, en pratique. Son inconvénient principal est qu'il ne peut pas apprendre les interactions entre les fonctionnalités (par exemple, il ne peut pas comprendre que vous puissiez aimer les films avec Brad Pitt et les films avec Tom Cruise, mais détester les films où ils apparaissent ensemble). [[Choix d'un classifieur d'apprentissage automatique](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen]


<a name="anchor-5e"></a> **One-vs-all :** il s'agit d'une stratégie permettant de réduire le problème de la classification multiclasse à un ensemble de plusieurs problèmes de classification binaire. Cette stratégie implique la formation d'un seul classifieur par classe, les exemples de cette classe étant considérés comme des exemples positifs, alors que tous les autres sont perçus comme négatifs. Cette stratégie nécessite des classifieurs de base pour produire un score de confiance réelle pour sa décision, au lieu d'une simple étiquette de classe. Les étiquettes de classe discrètes seules peuvent entraîner des ambiguïtés, car plusieurs classes sont prédites pour un seul échantillon. [[Classification multiclasse](http://en.wikipedia.org/wiki/Multiclass_classification) (Wikipedia 2006)]


<a name="anchor-6"></a>
##Régression
 
L’analyse de régression nous permet de prédire de nouvelles valeurs en fonction de l’inférence passée. Les nouvelles valeurs d’une variable dépendante sont calculées en fonction de la valeur d’un ou de plusieurs attributs mesurés. Les différents algorithmes de régression disponibles dans Microsoft Azure Machine Learning sont les suivants :

![screenshot\_of\_experiment](./media/machine-learning-algorithm-choice/help4.png)

Selon le cas d'usage et les données à portée de main, nous sélectionnons un algorithme plutôt qu'un autre. À présent, nous allons décrire certains algorithmes de régression et leurs cas d’usage principaux.

<a name="anchor-6b"></a> **[Régression linéaire bayésienne][bayesian-linear-regression]** Ce type de régression est une approche selon laquelle l'analyse statistique est entreprise dans le contexte de l'inférence bayésienne. Des résultats explicites sont disponibles pour les distributions de probabilité ultérieures des paramètres du modèle, lorsque le modèle de régression comporte des erreurs distribuées normales et qu'on peut partir sur la base d'une forme particulière de distribution antérieure. [[Régression linaire](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6f"></a> **[Régression des arbres de décision boostés][boosted-decision-tree-regression]** Ce type de régression calcule une relation entre la variable prédictive et les variables de réponse. La structure d’un arbre de régression est similaire à celle d’un arbre de classification. Les nœuds terminaux sont des valeurs de fonction prédite (modèle). Les valeurs prédites sont limitées aux valeurs dans les nœuds terminaux. Voici quelques-uns des avantages liés à l’utilisation des arbres de décision :

* L'interprétation des règles de prise de décision est simple. 
* Ces arbres ne sont pas paramétriques, il est donc facile d'utiliser une plage de couches de données numériques ou catégoriques, et les données d'apprentissage unimodal ne sont pas nécessaires.
* Ils sont robustes en termes de traitement des observations aberrantes dans les données d’apprentissage. 
* La classification peut être effectuée rapidement une fois que les règles sont développées. 

Toutefois, l'utilisation des arbres de décision présente certains inconvénients :

* Ils ont tendance à surapprendre les données d'apprentissage, ce qui entraîne des résultats médiocres sur l'ensemble du jeu de données.
* Il n'est pas possible de faire des prédictions au-delà des limites minimales et maximales de la variable de réponse dans les données d'apprentissage.


<a name="anchor-6g"></a> **[Régression des forêts de décision][decision-forest-regression]** Les forêts de décision sont utilisées dans des applications de classification (variables catégorielles) ou de régression (variables continues). Les forêts de régression peuvent être utilisées pour la régression non linéaire des variables dépendantes bénéficiant d'entrées indépendantes, et aussi bien les entrées que les sorties peuvent contenir plusieurs dimensions. Les forêts de régression ne sont pas aussi souvent utilisées que leurs homologues de classification. La principale différence est que les étiquettes de sortie des forêts de décision associées aux données d'entrée (ainsi que les étiquettes d'apprentissage) doivent être continues. La fonction d'objectif doit donc être adaptée de manière appropriée. Les forêts de régression partagent un grand nombre des avantages des forêts de classification, tels que l'efficacité et la flexibilité.

<a name="anchor-6a"></a> **[Régression linéaire][linear-regression]** La régression linéaire est largement utilisée pour modéliser la relation entre une variable dépendante scalaire Y et une ou plusieurs variables explicatives (X). Elle peut être appliquée (et l'est souvent) à des fins de prédiction, de prévision ou de réduction. Il peut servir à insérer un modèle prédictif dans un jeu de données observé regroupant les valeurs X et Y. La régression linéaire suppose que la structure sous-jacente de l’élément Y est une combinaison linéaire des variables X. Si une valeur supplémentaire de l’élément X est ensuite transmise sans sa valeur Y correspondante, le modèle de régression linéaire ajustée peut servir à prédire cette valeur Y. Les modèles de régression linéaire sont généralement ajustés à l'aide de la méthode des moindres carrés, mais il existe d'autres options permettant de mesurer la valeur qui convient le mieux. [[Régression linéaire](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org))]

<a name="anchor-6c"></a> **[Régression des réseaux neuronaux][neural-network-regression]** Les réseaux neuronaux sont des outils statistiques utiles pour la régression non paramétrique. La régression non paramétrique résout le problème consistant à tenter d'ajuster un modèle pour une variable Y sur un ensemble de variables explicatives possibles X1; : : : ;Xp, et où la relation entre X et Y peut être plus compliquée qu'une simple relation linéaire. [[Une infrastructure pour la régression non paramétrique utilisant les réseaux neuronaux](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee (ISDS, Duke University)]

<a name="anchor-6d"></a> **[Régression ordinale][ordinal-regression]** Ce type d'analyse de régression est utilisé pour modéliser ou prédire une variable dépendante ordinale. En ce qui concerne les variables dépendantes ordinales, vous pouvez classer les valeurs, mais la distance réelle entre les catégories est inconnue. Seul l’ordre relatif entre les différentes valeurs est significatif. Les étiquettes ou les valeurs cibles présentant un classement ou un ordre naturel, toute colonne numérique peut être utilisée en tant que cible ordinale. L’ordre naturel des numéros est utilisé pour ce classement. Par exemple, des maladies sont classées sur une échelle du moins grave au plus grave. Des participants à un sondage choisissent des réponses entre « tout à fait d'accord » et « pas du tout d'accord ». Des étudiants sont classés sur une échelle de A à F. Fondamentalement, la régression ordinale est une extension de la régression logistique et est basée sur le modèle à rapport des *chances proportionnelles*.


<a name="anchor-6e"></a> **[Régression de Poisson][poisson-regression]** La régression de Poisson est souvent utilisée pour modéliser les valeurs numériques. Elle suppose que la variable de réponse suit une distribution de Poisson. Les données soumises à une distribution de Poisson correspondent par essence à des valeurs entières (discrètes et positives), ce qui s’avère pertinent pour les données numériques. Sur un jeu de données d’apprentissage, la régression de Poisson tente de trouver les valeurs optimales en optimisant la vraisemblance du journal des paramètres sur la base des entrées. La probabilité des paramètres correspond à la probabilité que les données d’apprentissage aient été échantillonnées à partir d’une distribution avec ces paramètres. Cette régression peut être utile dans les cas suivants :

* modélisation du nombre de rhinites en fonction du nombre de vols en avion ; 
* estimation du nombre d’appels liés à un événement ou à une promotion ; 
* création de tables d’urgence.

## Références

Pour obtenir la liste complète des types d'algorithmes d'apprentissage automatique disponibles dans Machine Learning Studio, consultez [Initialiser le modèle](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/) dans [Machine Learning Studio : aide sur les algorithmes et les modules](https://msdn.microsoft.com/library/azure/dn905974.aspx).

Vous trouverez des informations plus détaillées sur tous les types d'algorithmes d'apprentissage automatique dans les références suivantes, qui ont été utilisées pour la création de cet article.

* [Choix d'un classifieur d'apprentissage automatique](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen.

* [Forêts de décision pour la classification, la régression, l'estimation de densité, l'apprentissage des variétés et l'apprentissage semi-supervisé](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf), A. Criminisi1, J. Shotton2 et E. Konukoglu (Microsoft Research, 2011) rapport technique TR-2011-114.

* [Une infrastructure pour la régression non paramétrique utilisant les réseaux neuronaux](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee (ISDS, Duke University).

* Manuel de statistiques informatiques : concepts et méthodes édité par James E. Gentle, Wolfgang Karl Härdle, Yuichi Mori (Springer-Verlag Berlin Heidelberg New York, 2004).

* [Régression logistique et analyses discriminatoires linéaires dans l'évaluation des facteurs associés à la prévalence de l'asthme chez les enfants de 10 à 12 ans : divergence et similitude des deux méthodes statistiques](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, Demosthenes B. Panagiotakos, Kostas N. Priftis et Anastasia Tzonou (Journal international de pédiatrie, 2009) ID de l'article 952042.

* [Optimalité du modèle bayésien naïf](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (Université de New Brunswick, 2004) Harry Zhang.

* [Machines à vecteur de support](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University, 2001).

* [Apprentissage automatique, 2ème partie : apprentissage supervisé et non supervisé](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/).

* [Quels sont les avantages de la régression logistique sur les arbres de décision ?](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees) ([Quora](http://www.quora.com/)).

* [Quelle est la différence entre l'apprentissage supervisé et l'apprentissage non supervisé ?](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning) ([Stackoverflow](http://stackoverflow.com/)).

* [Quand choisir le classifieur d'apprentissage automatique ?](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier) ([Stackoverflow](http://stackoverflow.com/)).

* [Wikipédia](http://en.wikipedia.org) :
	* [Régression linéaire bayésienne](http://en.wikipedia.org/wiki/Bayesian_linear_regression)
	* [Régression linéaire](http://en.wikipedia.org/wiki/Linear_regression)
	* [Classification multiclasse](http://en.wikipedia.org/wiki/Multiclass_classification)
	* [Apprentissage non supervisé](http://en.wikipedia.org/wiki/Unsupervised_learning)

Voir aussi :

* [Aide-mémoire d'algorithme Microsoft Azure Machine Learning](machine-learning-algorithm-cheat-sheet.md) (Microsoft).

* [Choix de l'estimateur approprié](http://scikit-learn.org/stable/tutorial/machine_learning_map/) ([scikit-learn](http://scikit-learn.org/stable/index.html)).


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=August15_HO6-->