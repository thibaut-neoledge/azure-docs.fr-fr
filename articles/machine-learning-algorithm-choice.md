<properties 
	title="" 
	pageTitle="Comment choisir un algorithme dans Microsoft Azure Machine Learning | Azure" 
	description="Explique comment choisir un algorithme dans Microsoft Azure Machine Learning." 
	services="machine-learning"
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/9/2015" 
	ms.author="bradsev" />


# Comment choisir un algorithme dans Microsoft Azure Machine Learning

Cette rubrique décrit certains aspects fondamentaux de la méthode Machine Learning (ML) et de ses principes, notamment la sélection d'un algorithme approprié pour analyser un type de données spécifique, répondre à une question posée, accomplir une tâche donnée, ou fournir des critères permettant de prendre une décision. Lorsque nous utilisons l'apprentissage automatique pour effectuer des analyses, nous devons souvent répondre à deux questions : 

* Quel type d'analyse devons-nous effectuer pour atteindre nos objectifs au moyen des données disponibles ? 
* Quel est l'algorithme ou le modèle le plus approprié pour y parvenir ?

Nous allons étudier trois types d'analyse, en comparant leurs différents cas d'usage : 

* classification ; 
* régression  
* clustering.

Nous traiterons également des différents algorithmes associés à chaque type d'analyse que propose Microsoft Azure Machine Learning, ainsi que des modules contenant ces algorithmes. 


<a name="anchor-1"></a>
##**Apprentissage automatique**

L'apprentissage automatique est une discipline qui étudie une classe d'algorithmes pilotés par les données, en ce sens qu'ils sont conçus pour tirer des connaissances des données et n'imposent aucun modèle spécifique et prédéterminé à tester en fonction de ces dernières. L'objectif consiste à acquérir des connaissances de manière plus inductive, en examinant les modèles dans un jeu de données plutôt qu'en appliquant ce qu'on appelle généralement la " méthode hypothético-déductive ". Selon cette méthode, vous tentez d'abord de deviner le modèle approprié pour l'ensemble du jeu de données, puis vous le testez de manière empirique. Ce type d'apprentissage à partir des données existe en deux versions : supervisé et non supervisé. 

<a name="anchor-2"></a>
##**Apprentissage supervisé**  

Ce type d'apprentissage nécessite une définition correcte de la variable cible, ainsi que la mise à disposition d'un sous-ensemble suffisant de ses valeurs. 

L'apprentissage supervisé est le type d'apprentissage effectué lorsque les résultats de sortie corrects (ou variables cibles) des instances d'apprentissage à saisir en entrée sont connus. L'objectif de la formation d'un algorithme d'apprentissage automatique consiste à trouver le modèle (c'est-à-dire une règle ou une fonction) qui mappe les entrées sur les valeurs de sortie connues. Cela revient à demander à un superviseur d'indiquer à l'agent algorithmique s'il mappe correctement les entrées sur les sorties. Une fois que le processus d'apprentissage est terminé et que nous disposons d'un modèle utilisable, nous pouvons appliquer ce dernier aux nouvelles données d'entrée pour prédire la sortie attendue, dans laquelle la valeur cible n'est pas connue à l'avance, contrairement à celle du jeu de données d'apprentissage.

La nature de la variable cible détermine le type du modèle.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help9.png)

Il existe deux grandes catégories d'analyse reposant sur l'apprentissage supervisé : la classification et la régression. Ce type d'apprentissage est fréquemment utilisé dans les *classification problems*. En effet, l'objectif consiste souvent à demander à l'ordinateur d'apprendre un système de classification que nous avons créé. Les réponses sont généralement fournies sous la forme de quelques variables connues (étiquettes), comme  'true' ou 'false'. Les algorithmes de classification s'appliquent aux valeurs de réponse nominales, non ordinales. L'algorithme de reconnaissance des chiffres est un exemple courant d'apprentissage de classification. En général, l'apprentissage de classification est adapté à tout problème pour lequel la déduction d'une classification est utile, cette dernière étant facile à déterminer.

Dans le cadre de l'apprentissage supervisé, les variables étudiées peuvent être réparties en deux groupes : variables explicatives (ou variables predictor) et variables dépendantes (variables de réponse). L'objectif de l'analyse consiste à spécifier une relation entre les variables explicatives et les variables dépendantes, comme dans *regression analysis*. Les valeurs de la variable dépendante doivent être connues et ce, pour une partie suffisante du jeu de données. Dans une régression, les variables de sortie ou de réponse incluent des valeurs continues (nombre de milles au galon d'une voiture spécifique, âge d'une personne, etc.).

La méthode d'apprentissage supervisé est également la technique la plus courante pour les réseaux neuronaux d'apprentissage et les arbres de décision. Ces deux techniques dépendent beaucoup des informations fournies par les classifications prédéfinies. 

* Dans le cas des réseaux neuronaux, la classification est utilisée pour déterminer les erreurs du réseau et pour régler les paramètres du réseau afin de les réduire. 
* Dans le cas des arbres de décision, les classifications sont utilisées pour identifier les attributs qui fournissent le plus d'informations pouvant être utilisées pour résoudre le problème de classification.  

Ces deux exemples requièrent une certaine " supervision ", car ils reposent sur la fourniture de classifications prédéfinies. 

La fonction de reconnaissance vocale utilisant des réseaux bayésiens et des modèles de Markov masqués s'appuie également sur des éléments de supervision, afin d'ajuster les paramètres et de réduire les erreurs sur les entrées données. 

<a name="anchor-3"></a>
##**Apprentissage non supervisé**

Dans le domaine de l'apprentissage automatique, le problème de l'apprentissage non supervisé consiste à rechercher des modèles ou une structure masquée dans les données sans étiquette. Le modèle n'est pas fourni avec les " résultats corrects " d'un jeu de données sur lequel effectuer l'apprentissage. Les exemples donnés à l'apprenant étant sans étiquette, il n'existe aucun signal de récompense ou d'erreur pour évaluer une solution éventuelle. L'objectif est de laisser l'ordinateur apprendre à effectuer une action sans que nous ayons besoin de lui expliquer, de manière explicite, comment l'accomplir ! Dans une situation d'apprentissage non supervisé, toutes les variables sont traitées de la même façon. Il n'existe aucune distinction entre les variables explicatives et les variables dépendantes. Toutefois, il existe toujours un objectif à atteindre. Cet objectif peut être général (réduction de données, par exemple) ou spécifique (comme une recherche de clusters). 

Dans Microsoft Azure Machine Learning, nous pouvons effectuer un apprentissage supervisé ou non supervisé via les analyses de type **classification**, **clustering** ou **régression**.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##**Clustering**  
Le clustering est un exemple d'apprentissage non supervisé. Dans ce type d'apprentissage, l'objectif consiste à rechercher des similitudes dans les données d'apprentissage et à partitionner le jeu de données en sous-ensembles, délimités par ces similitudes. Bien souvent (même si ce n'est pas systématique), les clusters les plus significatifs découverts par ces procédures pilotées par les données sont cohérents avec notre classification intuitive, selon nos attentes. 

Même si l'algorithme n'affecte pas de noms appropriés à ces clusters, il peut les produire et les utiliser pour anticiper la présence de similarités attendues dans les nouveaux exemples, en effectuant leur classification dans le cluster le plus approprié. Cette approche, pilotée par les données, peut offrir de bons résultats, tant que la quantité de données est suffisante. Par exemple, les algorithmes de filtrage d'informations sociales (tels que ceux qu'utilise le site Amazon.com pour recommander des ouvrages) sont basés sur la recherche de groupes de personnes similaires, puis sur l'affectation des nouveaux utilisateurs à ces groupes, dans l'objectif de proposer des recommandations.

L'algorithme de clustering proposé par Microsoft Azure Machine Learning s'appelle K-means.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help10.png)

K-means est l'un des algorithmes d'apprentissage les plus simples de la solution. Il est capable de résoudre efficacement les problèmes de clustering connus. L'algorithme K-means met les données en cluster en s'efforçant de répartir les échantillons en n groupes de variance égale, ce qui lui permet de réduire l'impact du critère appelé " inertie ", ou de la somme des carrés intra-cluster (WCSS, Within-Cluster Sum of Squares). Cet algorithme requiert la spécification d'un nombre de clusters. Il s'adapte bien à un grand nombre d'échantillons. Il a été utilisé dans un grand nombre de domaines d'application, dans des champs très différents.

Le module **K-Means Clustering** qui implémente l'algorithme K-means renvoie un modèle de clustering K-means pouvant être passé au module **Train Clustering Model**, à des fins de formation.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

Cette illustration indique les options à configurer pour l'utilisation du module K-means Clustering. La méthode K-means recherche un certain nombre de clusters pour un ensemble de points de données présentant d dimensions. En commençant par un *initial set of K centroids*, la méthode utilise l'algorithme de Lloyd pour affiner les emplacements des centroïdes, de manière itérative. L'algorithme se termine lorsque les centroïdes sont stabilisés ou lorsqu'un *specified number of iterations* est effectué.
Le module initialise un tableau K-par-D avec les centroïdes finals, qui définissent les K clusters trouvées dans les N points de données. Cet algorithme utilise également un vecteur de longueur N, avec l'assignation de chaque point de données à l'un des K clusters.
En cas de spécification d'un nombre précis de clusters à rechercher (K), le module affecte les premiers K points de données aux K clusters, dans l'ordre.
De plus, ce module accepte ou génère des points initiaux pour définir la configuration initiale du cluster.
*Metric* définit la méthode utilisée pour mesurer la distance entre un point de données et le centroïde.
Chaque point de données est affecté au cluster présentant le centroïde le plus proche de ce point de données. Par défaut, cette méthode utilise la *Euclidean metric*. Vous pouvez spécifier la *cosine metric* en tant que mesure secondaire de cette méthode.
Notez que la méthode K-means peut uniquement rechercher une configuration de cluster optimale pour un jeu de données, en local. La méthode peut rechercher une configuration différente, voire supérieure, à laquelle est associée une autre configuration initiale.

<a name="anchor-5"></a>
##**Classification**  
Dans l'analyse de classification, nous divisons les exemples en différentes classes et utilisons un ensemble formé constitué de données précédemment étiquetées. Cette technique est utilisée pour prédire l'appartenance à un groupe des instances de données. 
Microsoft Azure Machine Learning propose les algorithmes de classification suivants.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

Les *Two-Class algorithms* sont utilisés pour les variables de réponse binaires (de type oui ou non, 0 ou 1, true ou false, etc.) alors que les *Multiclass algorithms* sont utilisées pour toute variable de réponse nominale qui regroupe les instances en plus de deux classes.

### Instructions de sélection d'un algorithme de classification
Cette longue liste d'algorithmes donne lieu à une série de questions : 

* Comment savoir quel est le classifieur le plus adapté pour un jeu de données spécifique ? 
* Existe-t-il des cas permettant d'identifier l'une d'eux comme un choix " logique " ? 
* Quels sont les principes de sélection d'un classifieur ?

Une approche recommandée consiste à tester plusieurs classifieurs différents, ainsi que différents jeux de paramètres au sein de chaque algorithme, puis à sélectionner le plus efficace à l'aide de la méthode de validation croisée. Voici quelques indications générales pouvant faciliter la sélection. Tenez compte des points suivants lors du choix de l'algorithme à utiliser :

**Quelle est la taille des données d'apprentissage ?**
Si l'ensemble de données d'apprentissage est de taille réduite, et si vous comptez former un classifieur supervisé, la théorie d'apprentissage automatique stipule que vous devez vous en tenir à un classifieur présentant un écart peu élevé/décalage élevé, comme l'algorithme Naive Bayes. Ces classifieurs présentent un avantage par rapport aux classifieurs associés à un écart peu élevé/décalage élevé, comme kNN, qui présente des tendances au dépassement. Cependant, les classifieurs présentant un écart peu élevé/décalage élevé semblent plus attractifs lorsque l'ensemble de données d'apprentissage est volumineux (et est associé à une erreur asymptotique plus petite), car les classifieurs présentant un écart élevé ne sont pas capables de fournir des modèles précis. Il existe des résultats théoriques et empiriques qui indiquent que l'algorithme Naive Bayes fonctionne correctement dans de telles circonstances. Notez toutefois que la qualité des données est généralement plus importante que celle de l'algorithme et qu'une conception fonctionnelle efficace offre un avantage significatif. Si vous disposez d'un jeu de données volumineux, l'algorithme de classification que vous utilisez n'aura qu'une incidence réduite sur les performances de la classification, selon toute vraisemblance. Par conséquent, il peut être préférable de choisir votre algorithme en fonction  de son évolutivité, de sa rapidité ou de sa convivialité.

**Devez-vous effectuer l'apprentissage de façon incrémentielle ou en mode par lot ?** 
Si vous devez fréquemment mettre à jour votre classifieur avec de nouvelles données (ou si vous avez un grand nombre de données), vous souhaiterez sans doute utiliser des algorithmes bayésiens qui se mettent à jour correctement. Les réseaux neuronaux et SVM doivent être exécutés sur des données d'apprentissage en mode par lot.

**Les données sont-elles exclusivement catégoriques ou exclusivement numériques, ou une combinaison des deux types ?** 
Les algorithmes bayésiens fonctionnent mieux avec des données catégoriques/binomiales. Les arbres de décision ne peuvent pas fournir des valeurs numériques dans leurs prédictions.

**Est-ce que vous ou votre public avez besoin de comprendre le fonctionnement du classifieur ?**  Utilisez l'algorithme bayésien ou l'arbre de décision, car ces notions sont relativement simples à expliquer et sont compréhensibles pour la plupart des gens. Les réseaux neuronaux et SVM sont similaires à des " boîtes noires ", car vous ne pouvez pas réellement voir comment ils effectuent la classification des données.

**À quelle vitesse la classification doit-elle être générée ?** 
En termes de classification, les SVM sont rapides, car ils ont uniquement besoin de déterminer de quel côté de la " ligne " se trouvent les données. À l'inverse, les arbres de décision peuvent être lents, tout particulièrement s'ils sont complexes (par exemple, lorsqu'ils incluent un grand nombre de branches).

**Quel est le degré de complexité présenté ou requis par le problème ?** Les réseaux neuronaux et SVM peuvent gérer une classification complexe et non linéaire.

### Avantages et inconvénients des algorithmes de classification
Chaque algorithme de classification présente certains avantages et  inconvénients :

<a name="anchor-5a"></a>
**Avantages et inconvénients de la régression logistique :**   
L'analyse de régression logistique est basée sur le calcul des chances d'apparition du résultat, sous la forme du rapport de la probabilité d'apparition du résultat, divisée par la probabilité de non-production de ce résultat. Comme le modèle logistique  est paramétrique, il présente un avantage certain : il apporte des informations sur l'impact de chaque variable predictor sur la variable de réponse. Grâce aux interprétations probabilistes naturelles disponibles (contrairement aux arbres de décision ou aux SVM), nous pouvons facilement mettre à jour notre modèle, de façon à prendre en compte les nouvelles données. Il existe de nombreuses façons de régler notre modèle. Vous n'avez pas besoin de vous soucier autant de la corrélation de vos fonctions, comme avec l'algorithme Naive Bayes. La régression logistique est utile lorsque nous souhaitons 

* disposer d'une infrastructure probabiliste facilitant l'ajustement des seuils de classification indiquant que nous ne sommes pas sûrs ou pour bénéficier d'intervalles de confiance 
* si nous nous attendons à recevoir ultérieurement davantage de données d'apprentissage, que nous souhaitons pouvoir incorporer rapidement dans notre modèle. 

L'analyse de régression logistique fonctionne mieux que les arbres de décision en cas de données volumineuses. Par exemple, il se peut qu'une classification de texte porte sur plus de 100 000 documents, de 500 000 mots chacun. L'adoption d'une règle simple, comme l'apprentissage d'un hyperplan linéaire, est préférable, car les arbres de décision présentent beaucoup trop de degrés de liberté et sont sujets au dépassement. Vous pouvez effectuer une sélection des fonctions de façon à utiliser un arbre de décision sur les données de texte, mais vous risquez de perdre un grand nombre d'informations précieuses pour la classification du texte, suite à la sélection d'un sous-ensemble réduit de fonctionnalités. Lorsque des modèles d'apprentissage sont utilisés avec des données de dimensions élevées, il est très facile pour les erreurs basées sur l'écart de prendre de l'ampleur. Ainsi, le choix de modèles simples, avec un écart élevé, semble plus pertinent. 

La régression logistique présente un inconvénient : elle est instable lorsqu'une variable predictor est quasiment susceptible d'expliquer la variable de réponse, car le coefficient de cette variable devient très volumineux.

<a name="anchor-5b"></a>
**Avantages et inconvénients des arbres de décision :**   
[les arbres de décision](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) sont simples à interpréter et leur fonctionnement, simple à expliquer. Ils gèrent facilement les interactions entre les fonctionnalités et ne sont pas paramétriques. Ainsi, vous n'avez pas à vous soucier des observations aberrantes ou de savoir si les données sont séparables de manière linéaire (par exemple, les arbres de décision gèrent facilement les cas où la classe A figure à l'extrémité inférieure d'une fonction x quelconque ; la classe B, sur la plage moyenne de cette fonction x et de nouveau la classe A, à l'extrémité supérieure). Les arbres de décision génèrent la sortie sous forme de règles, ainsi que des mesures telles que la prise en charge, la confiance et l'élévation. 

Il existe un inconvénient : les arbres de décision ne prennent pas en charge l'apprentissage en ligne. Vous devez donc régénérer votre arborescence lorsque de nouveaux exemples apparaissent. Autre inconvénient : une tendance au dépassement. C'est à ce niveau que les méthodes d'ensembles (comme les forêts aléatoires, ou les " boosted trees ") peuvent s'avérer utiles. En outre, les forêts aléatoires sont souvent plus efficaces pour gérer de nombreux problèmes de classification. Elles offrent des résultats légèrement meilleurs que les SVM : elles sont rapides et évolutives, et vous n'êtes pas obligé d'ajuster un groupe de paramètres, comme dans les SVM.


<a name="anchor-5c"></a>
**Avantages et inconvénients des SVM :**   
Les SVM (Support Vector Machines) sont efficaces dans les espaces avec un grand nombre de dimensions et ce, même lorsque le nombre de dimensions est supérieur au nombre d'échantillons. Toutefois, si le nombre de fonctions est nettement supérieur au nombre d'échantillons, les performances de cette méthode seront probablement médiocres. Elle utilise une faible quantité de mémoire, car elle exploite un sous-ensemble de points d'apprentissage dans sa fonction de prise de décision (on parle de " vecteurs de support ").  Cette méthode est très souple : des fonctions de noyau différentes peuvent être spécifiées pour la fonction de prise de décision. Des noyaux courants sont fournis, mais il est également possible de spécifier des noyaux personnalisés. La fonction de noyau permet de transformer des exemples d'apprentissage incluant un faible nombre de dimensions en exemples incluant davantage de dimensions, ce qui peut être utile pour résoudre des problèmes de séparabilité linéaire. 

Cependant, les SVM ne proposent aucune estimation directe des probabilités. Celles-ci sont calculées via une validation croisée à cinq plis, qui s'avère exigeante. Grâce à une haute précision, des garanties théoriques solides en matière de dépassement et un noyau approprié, les SVM peuvent être efficaces, même si les données ne sont pas séparables de façon linéaire dans l'espace de fonction de base. Ils sont souvent utilisés lors de la résolution des problèmes de classification pour lesquels les espaces présentant un grand nombre de dimensions sont la norme. Contrairement aux forêts, les SVM étaient à l'origine des classifieurs à deux classes. Cependant, ils ont récemment été adaptés pour pouvoir travailler avec plusieurs classes. Nous pouvons utiliser un apprentissage de type " one-vs-rest " pour créer un classifieur capable de gérer des classes multiples, ce peut s'avérer moins performant. Puisque les SVM peuvent uniquement gérer les sorties à deux classes (c'est-à-dire, une variable de sortie catégorique avec une variété de 2), avec N  classes, l'apprentissage concerne N SVM (SVM apprend " Output==1 " vs " Output != 1 ", SVM 2 apprend " Output==2 " vs " Output != 2 ", ..., et SVM N apprend " Output==N " vs "Output != N "). Pour prédire la sortie d'une nouvelle entrée, il crée simplement la prédiction avec chaque SVM et recherche celui qui place la prédiction le plus loin dans la région positive.

<a name="anchor-5d"></a>
**Avantages et inconvénients du classifieur Naive Bayes :**   
[Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf) (NB) est un classifieur très souvent utilisé pour résoudre les problèmes de classification. Il part du principe que les fonctions sont indépendantes ; c'est pour cela qu'on parle d'une technique 'naive'. Si l'hypothèse d'indépendance conditionnelle du classifieur NB est avérée, ce dernier effectue la convergence plus rapidement que les modèles discriminatoires, comme la régression logistique. Ainsi, vous avez besoin d'un nombre moins important de données d'apprentissage, dans ce cas. 

Même si cette hypothèse n'est pas avérée, le classifieur NB est souvent très efficace, en pratique.  Il s'avère efficace pour les cas où les fonctions sont indépendantes, mais également lorsque les dépendances des fonctions les unes entre les autres sont similaires entre les fonctions. Ainsi, ce classifieur est un bon choix si vous avez besoin d'un mécanisme rapide et simple, qui offre les performances attendues.

Cependant, il présente un inconvénient majeur : il ne peut pas apprendre les interactions entre les fonctionnalités (par exemple, il ne peut pas comprendre que vous puissiez aimer les films avec Brad Pitt et les films avec Tom Cruise, mais détester les films où ils apparaissent ensemble).


<a name="anchor-5e"></a>
**Un contre tous :**  
il s'agit d'une stratégie permettant de réduire un problème de classification de classes multiples à un ensemble de problèmes multiples de classification binaire. Cette stratégie implique la formation d'un seul classifieur par classe, les exemples de cette classe étant considérés comme des exemples positifs, alors que tous les autres sont perçus comme négatifs. Elle nécessite la production, par les classifieurs de base, d'une notation de confiance à valeurs réelles pour la prise de décision, plutôt qu'une simple étiquette de classe. La seule utilisation d'étiquettes de classe discrètes peut entraîner des ambiguïtés, plusieurs classes étant prédites pour un seul et même échantillon.	


<a name="anchor-6"></a>
##**Régression**  
L'analyse de régression nous permet de prédire de nouvelles valeurs en fonction de l'inférence passée. Les nouvelles valeurs d'une variable dépendante sont calculées en fonction de la valeur d'un ou de plusieurs attributs mesurés. Les différents algorithmes de régression disponibles dans Microsoft Azure Machine Learning sont les suivants :

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

Selon le cas d'usage et les données à portée de main, nous sélectionnons un algorithme/module plutôt qu'un autre. À présent, nous allons décrire certains algorithmes de régression et leurs cas d'usage principaux.

<a name="anchor-6b"></a>
**Régression linéaire bayésienne**                      
Ce type de régression est une approche selon laquelle l'analyse statistique est entreprise dans le contexte de l'inférence bayésienne. Des résultats explicites sont disponibles pour les distributions de probabilité ultérieures des paramètres du modèle, lorsque le modèle de régression comporte des erreurs distribuées normales et qu'on peut partir sur la base d'une forme particulière de distribution antérieure.

<a name="anchor-6f"></a>
**Régression de l'arbre de décision optimisé**  
La régression calcule une relation entre la variable predictor et les variables de réponse. La structure d'un arbre de régression est similaire à celle d'un arbre de classification. Les nœuds terminaux sont des valeurs de fonction prédite (modèle). Les valeurs prédites sont limitées aux valeurs dans les nœuds terminaux. Voici quelques-uns des avantages liés à l'utilisation des arbres de décision : 

* L'interprétation des règles de prise de décision est simple. 
* Ces arbres ne sont pas paramétriques. Il est donc facile d'intégrer une plage de couches de données numériques ou catégoriques et il n'est pas nécessaire de sélectionner les données d'apprentissage unimodal. 
* Ils sont robustes en termes de traitement des observations aberrantes dans les données d'apprentissage. 
* La classification est rapide une fois que les règles sont développées. 

Ils présentent aussi certains inconvénients : ils ont tendance à entraîner un dépassement par rapport aux données d'apprentissage, ce qui peut occasionner des résultats peu satisfaisants en cas d'application à l'ensemble de données complet. De plus, il n'est pas possible d'effectuer de prédictions au-delà des limites minimales et maximales de la variable de réponse dans les données d'apprentissage.

<a name="anchor-6g"></a>
**Régression de la forêt de décision**  
Les forêts de décision sont utilisées pour les situations de classification (variables catégorielles) ou de régression (variables continues). Elles peuvent être utilisées pour la régression non-linéaire des variables dépendantes bénéficiant d'entrées indépendantes. Les entrées et les sorties peuvent être multidimensionnelles. Les forêts de régression ne sont pas aussi souvent utilisées que leurs homologues de classification. Il existe une différence majeure : l'étiquette de sortie des forêts de décision à associer aux données d'entrée (et aussi les étiquettes d'apprentissage) doit être continue. Par conséquent, la fonction d'objectif doit être adaptée de manière appropriée. Les forêts de régression présentent un grand nombre des avantages des forêts de classification, tels que l'efficacité et la flexibilité.

<a name="anchor-6a"></a>
**Régression linéaire**  
La régression linéaire est très souvent utilisée pour modéliser la relation entre une variable dépendante scalaire Y et une ou plusieurs variables explicatives (X). Elle peut être appliquée à des fins de prédiction, de prévision ou de réduction ; on l'utilise très souvent pour cela. Il peut servir à insérer un modèle prédictif dans un jeu de données observé regroupant les valeurs X et Y. La régression linéaire suppose que la structure sous-jacente de l'élément Y est une combinaison linéaire des variables X. Si une valeur supplémentaire de l'élément X est ensuite transmise sans sa valeur Y correspondante, le modèle de régression linéaire ajustée peut servir à prédire cette valeur Y. Les modèles de régression linéaire sont généralement adaptés à l'aide de la méthode des moindres carrés, mais il existe d'autres options permettant de mesurer la valeur qui convient le mieux.

<a name="anchor-6c"></a>
**Régression de réseau neuronal**  
Les réseaux neuronaux sont des outils statistiques utiles pour toute régression non paramétrique. La régression non paramétrique résout le problème consistant à ajuster un modèle associé à la variable Y par rapport à un ensemble de variables explicatives possibles  : X1; : : : ;XP, et lorsque la relation entre X et Y peuvent être plus compliquée qu'une relation linéaire simple.

<a name="anchor-6d"></a>
**Régression ordinale**   
Ce type d'analyse de régression est utilisé pour modéliser ou prédire une variable dépendante ordinale. En ce qui concerne les variables dépendantes ordinales, vous pouvez classer les valeurs, mais la distance réelle entre les catégories est inconnue. Seul l'ordre relatif entre les différentes valeurs est significatif. Les étiquettes ou les valeurs cibles présentant un classement ou un ordre naturel, toute colonne numérique peut être utilisée en tant que cible ordinale. L'ordre naturel des numéros est utilisé pour ce classement. Les maladies sont classées sur échelles partant du niveau de gravité le plus faible pour atteindre le niveau le plus élevé. Les personnes interrogées choisissent des réponses parmi différentes propositions, allant de l'acceptation complète au désaccord complet. Les élèves sont notés sur des échelles allant de A à F. Par essence, toutefois, la régression ordinale est une extension de la régression logistique qui repose sur le modèle de probabilités proportionnelles.


<a name="anchor-6e"></a>
**Régression de Poisson**  
La régression de Poisson est souvent utilisée pour modéliser les valeurs numériques. Elle suppose que la variable de réponse suit une distribution de Poisson. Les données soumises à une distribution de Poisson correspondent par essence à des valeurs entières (discrètes et positives), ce qui s'avère pertinent pour les données numériques. Sur un jeu de données d'apprentissage, la régression de Poisson tente de trouver les valeurs optimales en optimisant la vraisemblance du journal des paramètres sur la base des entrées. La probabilité des paramètres correspond à la probabilité que les données d'apprentissage aient été échantillonnées à partir d'une distribution avec ces paramètres. Cette régression peut être utile dans les cas suivants : 

* modélisation du nombre de rhinites en fonction du nombre de vols en avion ; 
* estimation du nombre d'appels liés à un événement ou à une promotion ; 
* création de tables d'urgence.



<!--HONumber=49-->