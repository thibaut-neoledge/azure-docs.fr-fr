<properties
	pageTitle="Expérience simple dans Machine Learning Studio | Microsoft Azure"
	description="Ce didacticiel sur l’apprentissage automatique vous guidera tout au long d’une expérience de science des données simple. Nous allons prédire le prix d’une voiture à l’aide d’un algorithme de régression."
	keywords="expérience,régression linéaire,algorithmes d’apprentissage automatique,didacticiel d’apprentissage automatique,techniques de modélisation prédictive,expérience de science de données"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="garye"/>

# Didacticiel sur l’apprentissage automatique : Création de votre première expérience de science des données dans Azure Machine Learning Studio

Ce didacticiel sur l’apprentissage automatique vous guidera tout au long d’une expérience de science des données simple. Nous allons créer un modèle de régression linéaire qui prédit le prix d'une voiture selon différentes variables, comme la marque et les caractéristiques techniques. Pour ce faire, nous allons utiliser Microsoft Azure Machine Learning Studio (ML Studio) pour développer et effectuer une itération sur une expérience d’analyse prédictive simple.

*L’analyse prédictive* est un type de science des données qui utilise les données actuelles pour prédire les résultats futurs. Pour obtenir un exemple très simple d’analyse prédictive, regardez la quatrième vidéo de la série « Science des données pour les débutants » : [Prédire une réponse à l’aide d’un modèle simple](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) (durée : 7:42).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## En quoi Machine Learning Studio est-il utile ?

Machine Learning Studio facilite la configuration d’une expérience à l’aide de modules à glisser-déplacer préprogrammés avec des techniques de modélisation prédictive. Pour exécuter votre expérience et prédire une réponse, vous allez utiliser Machine Learning Studio pour *créer un modèle*, *former le modèle* et *noter et évaluer le modèle*.

Accédez à Machine Learning Studio : [https://studio.azureml.net](https://studio.azureml.net). Si vous vous êtes déjà connecté à Machine Learning Studio, cliquez sur **Connectez-vous ici**. Sinon, cliquez sur **S’inscrire** et faites votre choix parmi les options gratuites et payantes.

Pour obtenir des informations générales sur Machine Learning Studio, consultez [Présentation de Machine Learning Studio](machine-learning-what-is-ml-studio.md).

## Cinq étapes pour créer une expérience

Dans ce didacticiel sur l'apprentissage automatique, vous allez suivre cinq étapes de base pour créer une expérience dans Machine Learning Studio afin de créer, former et noter votre modèle :

- Création d’un modèle
	- [Étape 1 : obtention des données]
	- [Étape 2 : traitement préalable des données]
	- [Étape 3 : définition des fonctionnalités]
- Formation du modèle
	- [Étape 4 : sélection et application d’un algorithme d’apprentissage]
- Attribution d’une notation et test du modèle
	- [Étape 5 : prédiction des nouveaux prix des voitures]

[Étape 1 : obtention des données]: #step-1-get-data
[Étape 2 : traitement préalable des données]: #step-2-preprocess-data
[Étape 3 : définition des fonctionnalités]: #step-3-define-features
[Étape 4 : sélection et application d’un algorithme d’apprentissage]: #step-4-choose-and-apply-a-learning-algorithm
[Étape 5 : prédiction des nouveaux prix des voitures]: #step-5-predict-new-automobile-prices


## Étape 1 : obtention des données

Vous avez le choix entre plusieurs exemples de jeux de données sont inclus dans ML Studio. Vous pouvez importer des données à partir de nombreuses sources. Pour les besoins de cet exemple, nous allons utiliser un jeu de données inclus, **Données sur le prix des véhicules automobiles (brutes)**. Ce jeu de données comprend des entrées pour plusieurs véhicules, notamment des informations sur la marque, le modèle, les caractéristiques techniques et le prix.

1. Démarrez une nouvelle expérience en cliquant sur l’option **+ NOUVEAU** située en bas de la fenêtre de Machine Learning Studio, sélectionnez **EXPÉRIENCE**, puis **Expérience vide**. Sélectionnez le nom d’expérience par défaut, situé en haut de la zone de dessin, et remplacez-le par un nom significatif, par exemple : **Prédiction de prix d’une voiture**.

2. Sur la gauche de la zone de dessin de l’expérience se trouve une palette de jeux de données et de modules. Saisissez la valeur **automobile** dans la zone de recherche se trouvant en haut de cette palette, afin de rechercher le jeu de données **Données sur le prix des véhicules automobiles (brutes)**.

	![Palette search][screen1a]

3. Faites glisser ce jeu de données sur le canevas de l’expérience.

	![Jeu de données][screen1]

Pour voir à quoi ressemblent ces données, cliquez sur le port de sortie situé en bas du jeu de données d’automobile, puis sélectionnez **Visualiser**.

![Port de sortie de module][screen1c]

Les variables du jeu de données sont affichées sous forme de colonnes, tandis que les types d'automobiles sont affichés sous forme de lignes. La colonne située le plus à droite (colonne 26, intitulée « Price ») est la variable cible, dont nous allons essayer de prédire la valeur.

![Dataset visualization][screen1b]

Fermez la fenêtre de visualisation en cliquant sur le symbole « **x** » dans le coin supérieur droit.

## Étape 2 : traitement préalable des données

Pour pouvoir être analysé, un jeu de données nécessite généralement un traitement préalable. Vous avez peut-être remarqué l'absence de certaines valeurs dans les colonnes des différentes lignes. Pour que vous puissiez analyser les données correctement, ces valeurs manquantes doivent être nettoyées. Dans le cas qui nous occupe, nous allons supprimer toutes les lignes dans lesquelles des valeurs sont manquantes. De plus, la colonne **normalized-losses** contient une grande quantité de valeurs manquantes. Nous allons donc l’exclure du modèle.

> [AZURE.TIP] Le nettoyage des valeurs manquantes des données d’entrée est un prérequis pour l’utilisation de la plupart des modules.

Commençons par supprimer la colonne **normalized-losses**, puis chaque ligne ayant des données manquantes.

1. Dans la zone de recherche située sur la partie supérieure de la palette de modules, saisissez la chaîne **sélectionner des colonnes** afin de rechercher le module [Sélectionner des colonnes dans le jeu de données][select-columns], puis faites-le glisser vers la zone de dessin de l’expérience et connectez-le au jeu de données sur le **prix des véhicules automobiles (brutes)**. Ce module permet de sélectionner les colonnes de données à inclure ou exclure du modèle.

2. Sélectionnez le module [Sélectionner des colonnes dans le jeu de données][select-columns], puis cliquez sur **Lancer le sélecteur de colonne** dans le volet **Propriétés**.

	- Sur la gauche, cliquez sur **With rules** (À l’aide de règles).
	- Sous **Commencer par**, cliquez sur **Toutes les colonnes**. Vous indiquez ainsi au module [Sélectionner des colonnes dans le jeu de données][select-columns] de transmettre toutes les colonnes, sauf celles que nous nous apprêtons à exclure.
	- Dans les listes déroulantes, sélectionnez **Exclure** et **Noms des colonnes**, puis cliquez dans la zone de texte. Une liste de colonnes s’affiche. Sélectionnez la colonne **normalized-losses**, qui est alors ajoutée à la zone de texte.
	- Cliquez sur le bouton en forme de coche (OK) pour fermer le sélecteur de colonne.

    ![Select columns][screen3]

	Le volet de propriétés du module **Sélectionner des colonnes dans le jeu de données** indique qu’il transmettra toutes les colonnes du jeu de données, à l’exception de **normalized-losses**.

    ![Propriétés du module Select Columns in Dataset][screen4]

    > [AZURE.TIP] Vous pouvez ajouter un commentaire dans un module en double-cliquant sur ce module, puis en saisissant du texte. Ceci peut vous aider à voir d'un seul coup d'œil ce que fait chaque module dans votre expérience. Dans ce cas, double-cliquez sur le module [Sélectionner des colonnes dans le jeu de données][select-columns] et saisissez le commentaire suivant : « Exclure les pertes normalisées ».

3. Faites glisser le module [Nettoyer les données manquantes][clean-missing-data] vers la zone de dessin de l’expérience et connectez-le au module [Sélectionner des colonnes dans le jeu de données][select-columns]. Dans le volet **Propriétés**, sélectionnez **Supprimer toute la ligne** sous **Mode de nettoyage**, afin de nettoyer les données en supprimant les lignes contenant des valeurs manquantes. Double-cliquez sur le module et saisissez le commentaire suivant : « Supprimer les lignes de valeur manquantes ».

	![Clean Missing Data properties][screen4a]

4. Démarrez l’expérience en cliquant sur l’option **DÉMARRER** sous la zone de dessin de l’expérience.

Une fois l'expérience terminée, une coche verte s'affiche en regard de chaque module pour indiquer la réussite de leurs opérations. Notez que le statut **Exécution terminée** s’affiche dans le coin supérieur droit de la fenêtre.

![First experiment run][screen5]

Pour l’instant, nous n’avons exécuté que l’action de nettoyage dans l’expérience. Si vous souhaitez afficher le jeu de données nettoyé, cliquez sur le port de sortie gauche du module [Nettoyer les données manquantes][clean-missing-data] \(« Jeu de données nettoyé ») et sélectionnez **Visualiser**. Vous pouvez constater que la colonne **normalized-losses** n’est plus là et qu’il ne manque plus de données.

Maintenant que les données sont nettoyées, nous pouvons indiquer les fonctionnalités que nous allons utiliser dans le modèle de prévision.

## Étape 3 : définition des fonctionnalités

Dans Machine Learning, les *fonctionnalités* sont des propriétés individuelles mesurables d’un élément qui vous intéresse. Dans notre jeu de données, chaque ligne représente un véhicule et chaque colonne une fonctionnalité de ce véhicule.

La recherche du jeu de fonctionnalités adéquat pour la création d’un modèle de prévision requiert certaines expériences et des connaissances sur le problème qui se pose. Certaines fonctionnalités sont mieux adaptées à la prévision que d’autres. De même, certaines fonctionnalités présentent une forte corrélation avec d'autres fonctionnalités, comme city-mpg par rapport à highway-mpg. Elles n'apporteront donc pas beaucoup de nouvelles informations au modèle. Vous pouvez les supprimer.

Nous allons développer un modèle utilisant un sous-ensemble de ces fonctionnalités pour notre jeu de données. Vous pouvez revenir en arrière et sélectionner différentes fonctionnalités, relancer l'expérience et voir si vous obtenez de meilleurs résultats. Mais pour commencer, nous allons essayer les fonctionnalités suivantes :

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Faites glisser un autre module [Sélectionner des colonnes dans le jeu de données][select-columns] vers la zone de dessin de l’expérience et connectez-le au port de sortie gauche du module [Nettoyer les données manquantes][clean-missing-data]. Double-cliquez sur le module et saisissez le commentaire suivant : « Sélection des fonctionnalités pour la prévision ».

2. Cliquez sur l’option **Lancer le sélecteur de colonne** figurant dans le volet **Propriétés**.

3. Cliquez sur **With rules** (À l’aide de règles).

4. Sous **Commencer par**, cliquez sur **Aucune colonne**, puis sélectionnez **Inclure** et **Noms des colonnes** dans la ligne de filtre. Entrez votre liste de noms de colonnes. Vous indiquez aussi au module qu’il doit transmettre les colonnes spécifiées uniquement.

	> [AZURE.TIP] Lors de l’exécution de l’expérience, nous nous sommes assurés que les définitions de colonne de nos données aient été transmises à partir du jeu de données d’origine, via le module [Nettoyer les données manquantes][clean-missing-data]. Cela signifie que les autres modules que vous connecterez contiendront également les informations du jeu de données.

5. Cliquez sur le bouton en forme de coche (OK) pour continuer.

![Select columns][screen6]

Cette opération génère le jeu de données qui sera utilisé dans l’algorithme d’apprentissage lors des étapes suivantes. Plus tard, vous pouvez reprendre la procédure en utilisant une autre sélection de fonctionnalités.

## Étape 4 : sélection et application d’un algorithme d’apprentissage

À présent que les données sont prêtes, la construction d'un modèle de prévision passe par la formation et le test. Nous allons utiliser nos données pour former le modèle, puis tester le modèle pour voir dans quelle mesure il peut prévoir les prix. Pour l’instant, ne vous préoccupez pas des raisons pour lesquelles nous devons former puis tester le modèle.

La *classification* et la *régression* sont deux types de techniques d'apprentissage automatique supervisé. La classification permet de prédire une réponse à partir d'un jeu de catégories défini, comme une couleur (rouge, bleu ou vert). La régression est utilisée pour prédire un nombre.

Étant donné que nous voulons prédire un prix, qui est un nombre, nous allons utiliser un modèle de régression. Pour cet exemple, nous allons former un modèle de *régression linéaire* simple, que nous testerons à l’étape suivante.

1. Nous allons utiliser nos données pour la formation et le test en les divisant en jeux distincts de formation et de test. Sélectionnez et faites glisser le module [Fractionner les données][split] sur le canevas d’expérience et connectez-le à la sortie du dernier module [Sélectionner des colonnes dans le jeu de données][select-columns]. Définissez **Fraction de lignes dans le premier jeu de données de sortie** sur 0.75. Ainsi, nous allons utiliser 75 % des données pour former le modèle, et 25 % pour le tester.

	> [AZURE.TIP] En modifiant le paramètre **Valeur de départ aléatoire**, vous pouvez produire différents échantillons aléatoires pour la formation et le test. Ce paramètre contrôle la valeur de départ du générateur de nombres pseudo-aléatoire.

2. Exécutez l’expérience. Cela permet aux modules [Sélectionner des colonnes dans le jeu de données][select-columns] et [Fractionner les données][split] de transmettre des définitions de colonne aux modules que nous allons ajouter par la suite.

3. Pour sélectionner l’algorithme d’apprentissage, développez la catégorie **Machine Learning** dans la palette des modules, à gauche de la zone de dessin, puis développez **Initialiser le modèle**. Différentes catégories de modules s'affichent, permettant d'initialiser des algorithmes d'apprentissage automatique.

	Pour les besoins de cet exemple, sélectionnez le module [Régression linéaire][linear-regression] sous la catégorie **Régression** (vous pouvez également rechercher le module en saisissant l’expression « linear regression » dans la zone de recherche de la palette), puis faites-le glisser vers la zone de dessin de l’expérience.

4. Recherchez et faites glisser le module [Effectuer le traitement de données pour apprentissage du modèle][train-model] jusqu’à la zone de dessin de l’expérience. Connectez le port d’entrée de gauche à la sortie du module [Régression linéaire][linear-regression]. Connectez le port d’entrée de droite à la sortie des données d’apprentissage (port de gauche) du module [Split Data][split].

5. Sélectionnez le module [Effectuer le traitement de données pour apprentissage du modèle][train-model], cliquez sur l’option **Lancer le sélecteur de colonne** du volet **Propriétés** et sélectionnez la colonne **Price**. Il s’agit de la valeur que notre modèle va prévoir.

	![Select "price" column][screen7]

6. Exécutez l’expérience.

Le résultat est un modèle de régression formé qui permet de noter de nouveaux exemples pour effectuer des prédictions.

![Applying the machine learning algorithm][screen8]

## Étape 5 : prédiction des nouveaux prix des voitures

À présent que nous avons formé le modèle à l'aide de 75 % de nos données, nous pouvons l'utiliser pour la notation du reste de nos données (25 %), afin de voir s'il fonctionne correctement.

1. Recherchez et faites glisser le module [Score Model][score-model] vers la zone de dessin de l’expérience, puis connectez le port d’entrée de gauche de ce dernier à la sortie du module [Train Model][train-model]. Connectez le port d’entrée de droite à la sortie des données de test (port de droite) du module [Split Data][split].

	![Score Model module][screen8a]

2. Pour exécuter l’expérience et afficher la sortie du module [Score Model][score-model], cliquez sur le port de sortie, puis sélectionnez **Visualiser**. La sortie affiche les valeurs de prévision associées au prix, ainsi que les valeurs connues des données de test.

3. Enfin, pour tester la qualité des résultats, sélectionnez et faites glisser le module [Evaluate Model][evaluate-model] vers la zone de dessin de l’expérience, puis connectez le port d’entrée de gauche à la sortie du module [Score Model][score-model]. (Il existe deux ports d’entrée, car le module [Evaluate Model][evaluate-model] peut être utilisé pour comparer deux modèles.)

4. Exécutez l’expérience.

Pour afficher la sortie du module [Evaluate Model][evaluate-model], cliquez sur le port de sortie, puis sélectionnez **Visualiser**. Les statistiques suivantes s’affichent pour notre modèle :

- **Erreur d’absolue moyenne** (EAM) : la moyenne des erreurs absolues (une *erreur* correspond à la différence entre la valeur prévue et la valeur réelle).
- **Racine de l’erreur quadratique moyenne** (RMSE) : la racine carrée de la moyenne des erreurs carrées des prévisions effectuées sur le jeu de données de test.
- **Erreur absolue relative** : la moyenne des erreurs absolues relative à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
- **Erreur carrée relative** : la moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
- **Coefficient de détermination** : aussi nommé « **valeur R au carré** », il s’agit d’une mesure statistique indiquant à quel point un modèle correspond aux données.

Pour chacune des statistiques liées aux erreurs, les valeurs les plus petites sont privilégiées. En effet, une valeur plus petite indique un degré de correspondance plus étroit avec la valeur réelle. Plus la valeur du **Coefficient de détermination**, est proche de un (1.0), plus la prévision est correcte.

![Evaluation results][screen9]

Au final, l'expérience doit ressembler à ceci :

![Didacticiel sur l'apprentissage automatique : effectuer une expérience de régression linéaire qui utilise des techniques de modélisation prédictive.][screen10]

## Étapes suivantes

À présent que vous avez terminé un premier didacticiel sur l'apprentissage automatique et que vous avez configuré votre expérience, vous pouvez répéter l'expérience pour essayer d'améliorer le modèle. Par exemple, vous pouvez modifier les fonctionnalités que vous utilisez pour votre prévision. Ou vous pouvez modifier les propriétés de l'algorithme [Régression linéaire][linear-regression], ou essayer un autre algorithme. Vous pouvez même ajouter plusieurs algorithmes d'apprentissage automatique à la fois à votre expérience et les comparer (deux par deux) à l'aide du module [Evaluate Model][evaluate-model].

> [AZURE.TIP] Utilisez le bouton **ENREGISTRER SOUS** qui figure sous la zone de dessin de l’expérience afin de copier toute itération de votre expérience. Vous pouvez afficher toutes les itérations de votre expérience en cliquant sur l’option **AFFICHER L’HISTORIQUE D’EXÉCUTION**, sous la zone de dessin. Pour en savoir plus, consultez la section [Gérer les itérations des expériences dans Microsoft Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

Une fois satisfait de votre modèle, vous pouvez le déployer en tant que service web permettant de prévoir l’évolution des prix dans le secteur automobile, en utilisant de nouvelles données. Consultez la section [Déployer un service web Microsoft Azure Machine Learning][publish] pour en savoir plus.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Pour obtenir un guide pas à pas plus complet et détaillé des techniques de modélisation prédictive pour la création, la formation, la notation et le déploiement d’un modèle, consultez [Développement d’une solution prédictive avec Azure Machine Learning][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen1c]: ./media/machine-learning-create-experiment/screen1c.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!---HONumber=AcomDC_0914_2016-->