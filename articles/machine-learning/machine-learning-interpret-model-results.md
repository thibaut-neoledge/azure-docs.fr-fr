<properties 
	pageTitle="Interprétation des résultats de modèle dans Machine Learning | Microsoft Azure" 
	description="Sélection du paramètre optimal défini pour un algorithme en utilisant et visualisant des sorties de modèle de notation." 
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
	ms.date="07/14/2015" 
	ms.author="bradsev" />


# Interprétation des résultats de modèle dans Azure Machine Learning 
 
**Compréhension et visualisation de la sortie « Noter le modèle »** Cette rubrique vous explique comment visualiser et interpréter les résultats de prédiction dans Azure Machine Learning Studio. Après avoir formé un modèle et effectué des prédictions sur celui-ci (c'est-à-dire « noté le modèle »), vous avez besoin de comprendre et d'interpréter les résultats de prédiction que vous avez obtenus.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Il existe quatre principaux types de modèles d'apprentissage automatique dans Azure Machine Learning :

* classification ;
* clustering.
* régression ;
* systèmes de recommandation

Les modules utilisés pour réaliser une prédiction sur ces modules, appelée « notation », compte tenu de certaines données de test, sont les suivants :

* [Noter le modèle][score-model] pour la classification et la régression, 
* [Attribuer aux clusters][assign-to-clusters] pour le clustering 
* [Noter la recommandation Matchbox][score-matchbox-recommender] pour les systèmes de recommandation 
 
Ce document vous explique comment interpréter les résultats de prédiction pour chacun de ces modules. Pour obtenir une vue d'ensemble de ces types de modèles, consultez la rubrique [Sélection des paramètres permettant d'optimiser des algorithmes dans Azure Machine Learning](machine-learning-algorithm-parameters-optimize.md).

Cette rubrique aborde l'interprétation des prédictions et non l'évaluation du modèle. Pour plus d'informations sur la manière d'évaluer votre modèle, reportez-vous à la rubrique [Évaluation des performances d'un modèle dans Azure Machine Learning ](machine-learning-evaluate-model-performance.md).

Si vous débutez avec Azure Machine Learning et souhaitez découvrir comment créer une expérience simple, consultez la rubrique [Création d'une expérience simple dans Azure Machine Learning Studio](machine-learning-create-experiment.md) dans Azure Machine Learning Studio.

##Classification
Il existe deux sous-catégories de problèmes de classification :

* problèmes avec uniquement deux classes (classification double classe ou binaire) 
* problèmes avec plus de deux classes (classification multiclasse) 

Azure Machine Learning dispose de différents modules permettant de traiter chacun de ces types de classification. Les manières d'interpréter leurs résultats de prédiction sont cependant très similaires. Nous aborderons tout d'abord les problèmes de classification double classe, et nous poursuivrons ensuite avec les problèmes de classification multiclasse.

###Classification double classe.
**Exemple d'expérience**

Prenons le problème de classification double classe, la classification de fleurs d'Iris à titre d'exemple : la tâche consiste à classer les fleurs d'Iris en fonction de leurs fonctionnalités. Notez que l'ensemble des données d'Iris fourni dans Azure Machine Learning constitue un sous-ensemble de ce fameux [Ensemble de données Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) contenant des instances d'uniquement 2 espèces de fleur (classes 0 et 1). Il existe quatre fonctionnalités pour chaque fleur (la longueur des sépales, la largeur des sépales, la longueur des pétales et la largeur des pétales).

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1.png)

Figure 1 : Expérience d'un problème de classification double classe

Une expérience a été réalisée pour résoudre ce problème, comme mentionnée dans la Figure 1. Un modèle d'arbre de décision augmentée, double classe a été formé et noté. Vous pouvez dès à présent visualiser les résultats de prédiction du module [Noter le modèle][score-model] en cliquant sur le port de sortie du module [Modèle de notation][score-model], puis cliquer sur **Visualiser** dans le menu qui s'affiche. Cela fera apparaître les résultats de notation, comme mentionné dans la Figure 2.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/2.png)

Figure 2 : Visualisation du résultat du modèle de notation dans la classification double classe

**Interprétation du résultat**

La table des résultats est constituée de six colonnes. Les quatre colonnes de gauche représentent les quatre fonctionnalités. Les deux colonnes de droite, Étiquettes notées et Probabilités notées, représentent les résultats de prédiction. La colonne Probabilités notées indique la probabilité qu'une fleur appartienne à la classe positive (classe 1). Par exemple, le premier numéro 0,028571 indiqué dans la colonne signifie qu'il y a une probabilité de 0,028571 que la première fleur appartienne à la classe 1. La colonne Étiquettes notées indique la classe prédite pour chaque fleur. Elle est déterminée en fonction de la colonne Probabilités notées. Si la probabilité notée d'une fleur est supérieure à 0,5, elle est prédite en tant que classe 1, sinon, elle est prédite en tant que classe 0.

**Publication du service web**

Une fois que les résultats de prédiction ont été compris et considérés comme pertinents, l'expérience peut être publiée en tant que service web pour nous permettre de la déployer sur différentes applications et d'être sollicités pour obtenir des prédictions de classe sur n'importe quelle nouvelle fleur d'Iris. Pour découvrir comment convertir une expérience de formation en expérience de notation et la publier sous forme de service web, consultez la rubrique [Publication du service web Azure Machine Learning](machine-learning-walkthrough-5-publish-web-service.md). Cette procédure vous permet de bénéficier d'une expérience de notation, comme indiqué dans la Figure 3.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/3.png)

Figure 3 : Expérience de notation d'un problème de classification double classe Iris

Nous devons à présent définir l'entrée et la sortie du service web. L'entrée est, bien entendu, caractérisée par le port d'entrée droit de [Noter le modèle][score-model], qui représente l'entrée des fonctionnalités de la fleur d'Iris. La sortie est, quant à elle, définie en fonction de l'intérêt que nous portons à la classe prédite (étiquette notée), à la probabilité notée ou bien aux deux. On considère ici que nous sommes intéressés par les deux. Pour sélectionner les colonnes de sortie souhaitées, nous devons utiliser un module [Colonnes de projet][project-columns]. Nous cliquons sur le module [Colonnes de projet][project-columns], sur **Lancer le sélecteur de colonne** situé dans le volet droit, puis nous sélectionnons **Étiquettes notées** et **Probabilités notées**. Une fois le port de sortie du module [Colonnes de projet][project-columns] configuré et exécuté à nouveau, nous devons être en mesure de publier l'expérience de notation en tant que service web en cliquant sur le bouton du bas, intitulé **PUBLISH WEB SERVICE**. L'expérience finale est semblable à celle mentionnée dans la Figure 4.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4.png)

Figure 4 : Expérience de notation finale d'un problème de classification double classe Iris

Une fois le service web exécuté et certaines valeurs caractéristiques d'une instance de test saisies, le résultat renvoyé fournit deux nombres. Le premier nombre représente l'étiquette notée, le deuxième la probabilité notée. Cette fleur est prédite en tant que classe 1 avec une probabilité de 0,9655.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5.png)

Figure 5 : Résultats du service web de classification double classe Iris

###Classification multiclasse.
**Exemple d'expérience**

Lors de cette expérience, vous réaliserez une tâche de reconnaissance de lettres en tant qu'exemple de classification multiclasse. Le classificateur essaiera de prédire une lettre (classe), compte tenu de certaines valeurs d'attribut écrites, extraites d'images écrites. Dans les données de formation, seize caractéristiques sont extraites d'images de lettres écrites. Les vingt-six lettres représentent nos vingt-six classes. Une expérience a été mise en place pour former un modèle de classification multiclasse de reconnaissance de lettres et établir des prédictions sur le même ensemble de fonctionnalités d'un ensemble de données de test, comme mentionné dans la Figure 6.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/6.png)

Figure 6 : Expérience d'un problème de classification multiclasse de reconnaissance de lettres

Visualisation des résultats du module [Noter le modèle][score-model] en cliquant avec le bouton droit ou gauche sur le port de sortie du module [Noter le modèle][score-model], sur **Visualiser**, vous devez voir apparaître une fenêtre, telle que mentionné dans la Figure 7.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/7.png)

Figure 7 : Visualisation du résultat de modèle de notation dans la classification multiclasse

**Interprétation du résultat**

Les seize colonnes de gauche représentent les valeurs caractéristiques de l'ensemble du test. Les colonnes intitulées Probabilités notées de classe « XX » sont similaires à la colonne Probabilités notées dans le cas d'utilisation d'une classification double classe. Elles indiquent la probabilité que l'entrée correspondante est comprise dans une certaine classe. Par exemple, pour la première entrée, il y a une probabilité de 0,003571 que ce soit un « A », une probabilité de 0,000451 que ce soit un « B », et ainsi de suite. La dernière colonne Étiquettes notées est identique aux Étiquettes notées dans le cas d'utilisation d'une classification double classe. Elle sélectionne la classe présentant la probabilité notée la plus élevée en tant que classe prédite de l'entrée correspondante. Par exemple, pour la première entrée, l'étiquette notée est un « F », car elle présente la probabilité la plus élevée que ce soit un « F » (0,916995).

**Publication du service web**

Cette fois, au lieu d'utiliser [Colonnes de projet][project-columns] pour sélectionner certaines colonnes en tant que sortie de notre service web, nous souhaitons obtenir l'étiquette notée pour chaque entrée, de même que la probabilité de l'étiquette notée. La logique de base consiste à trouver la probabilité la plus élevée parmi toutes les probabilités notées. Pour ce faire, nous devons utiliser le module [Exécuter le script R][execute-r-script]. Le code R est mentionné dans la Figure 8 et l'expérience dans la Figure 9.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/8.png)

Figure 8 : Code R permettant d'extraire les étiquettes notées et les probabilités associées des étiquettes
  
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9.png)

Figure 9 : Expérience de notation finale d'un problème de classification multiclasse de reconnaissance de lettres

Une fois le service web publié et exécuté, et certaines valeurs caractéristiques d'une instance de test saisies, le résultat renvoyé est semblable à celui mentionné dans la Figure 10. Cette lettre écrite, accompagnée de ses seize fonctionnalités extraites, est prédite pour être un « T » avec une probabilité de 0,9715.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/10.png)

Figure 10 : Résultat du service web de la classification double classe Iris

##Régression

Les problèmes de régression sont différents des problèmes de classification. Dans un problème de classification, nous essayons de prédire les classes discrètes, telles que la classe à laquelle appartient une fleur d'Iris. Mais dans un problème de régression, nous essayons de prédire une variable continue, telle que le prix d'une voiture, comme indiqué dans l'exemple suivant.

**Exemple d'expérience**

Nous utilisons la prédiction du prix d'une voiture comme exemple de régression. Nous essayons de prédire le prix d'une voiture en fonction de ses fonctionnalités telles que la marque, le type de carburant, le type de voiture, le type de traction, etc. L'expérience est indiquée dans la Figure 11.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/11.png)

Figure 11 : Expérience d'un problème de régression du prix d'une voiture

Visualisation du module [Noter le modèle][score-model], le résultat est semblable à celui mentionné dans la Figure 12.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/12.png)

Figure 12 : Visualisation du résultat de notation d'un problème de prédiction du prix d'une voiture

**Interprétation du résultat**

Dans ce résultat de notation, les étiquettes notées représentent la colonne de résultat. Les nombres constituent le prix prédit pour chaque voiture.

**Publication du service web**

Nous pouvons publier l'expérience de régression dans un service web et la solliciter pour prédire le prix d'une voiture de la même manière que dans le cas d'utilisation d'une classification double classe.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13.png)

Figure 13 : Expérience de notation d'un problème de régression du prix d'une voiture

Exécution du service web, le résultat renvoyé est semblable à celui indiqué dans la Figure 14. Le prix prédit pour cette voiture est de 15 085,52.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/14.png)

Figure 14 : Résultat du service web d'un problème de régression du prix d'une voiture

##Clustering

**Exemple d'expérience**

Réutilisons l'ensemble de données Iris pour réaliser une expérience de clustering Nous filtrons ici les étiquettes de classe dans l'ensemble de données de manière à ce qu'il dispose de fonctionnalités et puisse être utilisé uniquement pour le clustering. Dans ce cas d'utilisation Iris, lorsque nous définissons 2 clusters lors du processus de formation, cela signifie que nous souhaitons regrouper les fleurs en deux classes. L'expérience est indiquée dans la Figure 15.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/15.png)

Figure 15 : Expérience d'un problème de clustering Iris

Le clustering diffère de la classification, car l'ensemble de données de formation ne dispose pas lui-même des étiquettes réelles. Au lieu de cela, nous nous intéressons à la méthode nous permettant de regrouper les instances d'ensembles de données de formation en groupes distincts. Lors du processus de formation, le modèle étiquette les entrées en prenant compte des différences existantes entre leurs fonctionnalités. Le modèle formé peut être ensuite utilisé pour classifier les entrées futures. Deux parties du résultat nous intéressent au sein d'un problème de clustering. La première partie est la manière dont est étiqueté l'ensemble de données de formation, la deuxième est la manière dont est classé un nouvel ensemble de données avec le modèle formé.

La première partie du résultat peut être visualisée en cliquant sur le port de sortie gauche du module [Former le modèle de clustering][train-clustering-model], puis sur **Visualiser**. La fenêtre de visualisation est indiquée dans la Figure 16.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/16.png)

Figure 16 : Visualisation du résultat de clustering pour l'ensemble de données de formation

Le résultat de la deuxième partie, regroupant de nouvelles entrées avec le modèle de clustering formé, est indiqué dans la Figure 17.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/17.png)

Figure 17 : Visualisation du résultat de clustering sur le nouvel ensemble de données

**Interprétation du résultat**

Bien que provenant d'étapes d'expérience différentes, les résultats des deux parties sont similaires et interprétés de la même manière. Les quatre premières colonnes représentent les caractéristiques. La dernière colonne Affectations représente les résultats de prédiction. Les entrées affectées du même numéro sont prédites dans le même groupe, c'est-à-dire qu'elles partagent d'une certaine façon des similitudes (nous avons utilisé la mesure de la distance euclidienne par défaut dans cette expérience). Rappelez-vous que nous avons défini 2 clusters. Par conséquent, dans la colonne Affectations, les entrées sont étiquetées d'un 0 ou d'un 1.

**Publication du service web**

Nous pouvons publier l'expérience de clustering dans un service web et la solliciter pour des prédictions de clustering de la même manière que dans le cas d'utilisation d'une classification double classe.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18.png)

Figure 18 : Expérience de notation d'un problème de clustering Iris

Une fois le service web exécuté, le résultat renvoyé est semblable à celui indiqué dans la Figure 19. Cette fleur est prédite dans le cluster 0.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19.png)

Figure 19 : Résultat du service web de classification double classe Iris

##Système de recommandation
**Exemple d'expérience**

Pour les systèmes de recommandation, nous utiliserons le problème de recommandation de restaurants à titre d'exemple : recommander des restaurants aux clients en fonction de leur historique de notation. Les données d'entrée sont composées de trois parties :

* les notations de restaurants attribuées par les clients 
* les données caractéristiques des clients 
* les données caractéristiques des restaurants 

Plusieurs tâches peuvent être effectuées via le module intégré [Former la recommandation Matchbox][train-matchbox-recommender] d'Azure Machine Learning, à savoir :

- Prédire des notations pour un utilisateur et un élément donné
- Recommander des éléments à un utilisateur donné
- Trouver des utilisateurs associés à un utilisateur donné
- Trouver des éléments associés à un utilisateur donné

Nous pouvons choisir ce que nous voulons en sélectionnant parmi les quatre options du menu **Type de prédiction de recommandation** du volet droit. Nous vous guiderons ici à travers ces quatre scénarios. Une expérience type Azure Machine Learning d'un système de recommandation est semblable à celui indiqué dans la Figure 20. Pour plus d'informations sur l'utilisation de ces modules de système de recommandation, consultez la page d'aide, aux rubriques [Former la recommandation Matchbox][train-matchbox-recommender] et [Noter la recommandation Matchbox][score-matchbox-recommender].
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/20.png)

Figure 20 : Expérience du système de recommandation

**Interprétation du résultat**

*Prédire des notations pour un utilisateur et un élément donné*

En sélectionnant Prédiction de notation dans le menu **Type de prédiction de recommandation**, nous demandons au système de recommandation de prédire la notation pour un utilisateur et un élément donné. La visualisation de la sortie [Noter la recommandation Matchbox][score-matchbox-recommender] est semblable à celle indiquée dans la Figure 21.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/21.png)

Figure 21 : Visualisation du résultat de notation du système de recommandation - Prédiction de notation

Il y a trois colonnes. Les deux premières colonnes représentent les paires utilisateur-élément fournies par les données d'entrée. La troisième colonne représente la notation prédite d'un utilisateur pour un élément donné. Par exemple, dans la première ligne, le client U1048 est censé attribuer au restaurant 135 026 la note de 2.

*Recommander des éléments à un utilisateur donné*

En sélectionnant ** Recommandation d'éléments** dans le menu **Type de prédiction de recommandation**, nous demandons au système de recommandation de recommander des éléments à un utilisateur donné. Il existe un paramètre supplémentaire, que nous devons choisir dans ce scénario : la sélection d'éléments recommandés. L'option **À partir d'éléments notés (pour l'évaluation de modèle)** est principalement utilisée pour l'évaluation de modèle lors du processus de formation. Pour cette étape de prédiction, nous choisirons **À partir de tous les éléments**. La visualisation de la sortie [Noter la recommandation Matchbox][score-matchbox-recommender] est semblable à celle mentionnée dans la Figure 22.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/22.png)

Figure 22 : Visualisation du résultat de notation du système de recommandation - Recommandation d'éléments

Il y a six colonnes. La première colonne représente le nom de l'utilisateur donné pour lequel nous recommandons des éléments, fournis par les données d'entrée. Les cinq colonnes restantes représentent les éléments recommandés à l'utilisateur, classés par ordre décroissant en fonction de leur pertinence. Par exemple, dans la première ligne, le restaurant le plus recommandé au client U1048 est le restaurant 134 986, suivi des restaurants 135 018, 134 975, 135 021 et 132 862.

*Trouver des utilisateurs associés à un utilisateur donné*

En sélectionnant Utilisateurs associés dans « Type de prédiction de recommandation », nous demandons au système de recommandation de trouver des utilisateurs associés à un utilisateur donné. Les utilisateurs associés sont les utilisateurs qui ont des préférences similaires. Il existe un paramètre supplémentaire, que nous devons choisir dans ce scénario : la sélection d'utilisateurs associés. L'option « À partir d'utilisateurs qui ont noté des éléments (pour l'évaluation de modèle) » est principalement utilisée pour l'évaluation de modèle lors du processus de formation. Pour cette étape de prédiction, nous choisissons « À partir de tous les utilisateurs ». La visualisation de la sortie [Noter la recommandation Matchbox][score-matchbox-recommender] est semblable à celle mentionnée dans la Figure 23.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/23.png)

Figure 23 : Visualisation du résultat de notation du système de recommandation - Utilisateurs associés

Il y a six colonnes. La première colonne représente les ID des utilisateurs donnés pour lesquels nous recherchons des utilisateurs associés, fournis par les données d'entrée. Les cinq colonnes restantes répertorient les utilisateurs associés prédits, classés par ordre décroissant en fonction de leur pertinence. Par exemple, dans la première ligne, le client le plus pertinent pour le client U1048 est le client U1051, suivi des clients U1066, U1044, U1017 et U1072.

**Trouver des éléments associés à un élément donné**

En sélectionnant **Éléments associés** dans le menu **Type de prédiction de recommandation**, nous demandons au système de recommandation de trouver des éléments associés à un élément donné. Les éléments associés sont les éléments les plus susceptibles d'être appréciés par le même utilisateur. Il existe un paramètre supplémentaire, que nous devons choisir dans ce scénario : la sélection d'éléments associés. L'option **À partir d'éléments notés (pour l'évaluation de modèle)** est principalement utilisée pour l'évaluation de modèle lors du processus de formation. Pour cette étape de prédiction, nous choisissons **À partir de tous les éléments**. La visualisation de la sortie [Noter la recommandation Matchbox][score-matchbox-recommender] est semblable à celle indiquée dans la Figure 24.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/24.png)

Figure 24 : Visualisation du résultat de notation du système de recommandation - Éléments associés

Il y a six colonnes. La première colonne représente le nom de l'élément donné pour lequel nous recherchons des éléments associés, fournis par les données d'entrée. Les cinq colonnes restantes répertorient les éléments associés prédits, classés par ordre décroissant en fonction de leur pertinence. Par exemple, dans la première ligne, l'élément le plu pertinent pour l'élément 135 026 est l'élément 135 074, suivi des éléments 135 035 132 875 135 055 et 134 992. Publication du service web

Le processus de publication de ces expériences en tant que services web permettant d'obtenir des prédictions est similaire pour chacun des quatre scénarios. Nous nous attarderons ici sur le second scénario, la recommandation d'éléments à un utilisateur donné, à titre d'exemple. Vous pouvez suivre la même procédure pour les trois autres.

Enregistrement du système de recommandation formé en tant que modèle formé, filtrage des données d'entrée dans une colonne d'ID utilisateur unique (tel que cela l'a été demandé), nous pouvons raccorder l'expérience, comme mentionné dans la Figure 25, et la publier en tant que service web.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25.png)
 
Figure 25 : Expérience de notation d'un problème de recommandation de restaurants

Exécution du service web, le résultat renvoyé est semblable à celui indiqué dans la Figure 14. Les cinq restaurants recommandés pour l'utilisateur U1048 sont les restaurants 134 986, 135 018, 134 975, 135 021 et 132 862.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/26.png)

Figure 26 : Résultat du service web d'un problème de recommandation de restaurants


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
 

<!---HONumber=August15_HO6-->