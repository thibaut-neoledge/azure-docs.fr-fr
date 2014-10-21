<properties title="Create a simple experiment in Azure Machine Learning Studio" pageTitle="Create a simple experiment in Machine Learning Studio | Azure" description="How to create an experiment to train and test a simple model in Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Création d'une expérience simple dans Azure Machine Learning Studio

Une expérience d'analyse prédictive est essentiellement constituée de composants pour *créer un modèle*, *former un modèle* et *noter et évaluer un modèle*. Vous pouvez les combiner pour créer une expérience qui extrait des données, forme un modèle et applique ce modèle à de nouvelles données. Vous pouvez également ajouter des modules pour prétraiter des données et sélectionner des fonctionnalités, diviser des données en ensembles de formation et de test ou effectuer une validation croisée de la qualité de votre modèle.

Dans cet article, nous allons utiliser Microsoft Azure Machine Learning Studio pour développer et effectuer une itération sur une expérience d'analyse prédictive simple.

## Cinq étapes pour la création d'une expérience

Les cinq étapes de base à suivre pour développer une expérience dans ML Studio vous permettent de créer, de former et d'évaluer votre modèle :

-   Création d'un modèle

    -   [Étape 1 : Obtention des données][]
    -   [Étape 2 : Prétraitement des données][]
    -   [Étape 3 : Définition des fonctionnalités][]
-   Formation du modèle
    -   [Étape 4 : Sélection et application d'un algorithme d'apprentissage][]
-   Évaluation et test du modèle
    -   [Étape 5 : Établir une prévision sur des nouvelles données][]

Dans cet exemple, nous allons voir la création d'un modèle de régression en utilisant des exemples de données automobiles. L'objectif est de prédire le prix d'une automobile à l'aide de différentes variables telles que les spécifications techniques et de fabrication.

### Étape 1 : Obtention des données

Plusieurs exemples de jeux de données sont inclus avec ML Studio et vous pouvez importer des données à partir de différentes sources. Pour cet exemple, nous allons utiliser un jeu de données inclus, **Automobile price data (Raw)**, qui représente les données des prix automobiles.

1.  Démarrez une nouvelle expérience en cliquant sur **+NOUVEAU** en bas de la fenêtre ML Studio et en sélectionnant **EXPÉRIENCE**. Remplacez le nom de l'expérience (« Sans titre ») par un nom plus significatif, comme « Prévision sur les prix automobiles ».

2.  ﻿À gauche du canevas de l'expérience se trouve une palette de jeux de données et de modules. Tapez « automobile » dans la zone de recherche en haut de cette palette pour rechercher le jeu de données nommé **Automobile price data (Raw)**.

    ![Palette search][]

3.  Faites glisser ce jeu de données sur le canevas de l'expérience.

    ![Dataset][]

Pour voir à quoi ressemblent ces données, double-cliquez sur le port de sortie situé en bas du jeu de données sur les automobiles et sélectionnez **Visualiser**. Les variables du jeu de données sont affichées sous forme de colonnes, tandis que les types d'automobiles sont affichés sous forme de lignes. La colonne la plus à droite, nommée « price » (colonne 26) est la variable cible que nous allons prédire.

![Dataset visualization][]

Fermez la fenêtre de visualisation en cliquant sur le symbole « **x** » dans le coin supérieur droit.

### Étape 2 : Prétraitement des données

Un jeu de données requiert un prétraitement avant de pouvoir être analysé. Vous avez peut-être déjà remarqué les valeurs manquantes dans les colonnes des différentes lignes : pour analyser les données, ces valeurs manquantes doivent être supprimées. Ici, nous allons simplement supprimer toutes les lignes ayant des valeurs manquantes. De même, la colonne « normalized-losses » contient une grande quantité de valeurs manquantes, nous allons donc l'exclure du modèle.

> **Conseil** : la suppression des valeurs manquantes des données d'entrée est un prérequis pour la plupart des modules.

Commençons par supprimer la colonne « normalized-losses »; puis chaque ligne ayant des données manquantes.

1.  Faites glisser le module **Colonnes de projet** vers le canevas d'expérience et connectez-le au port de sortie du jeu de données **Automobile price data (Raw)**. Ce module permet de sélectionner les colonnes de données à inclure ou exclure du modèle.

2.  Sélectionnez le module **Colonnes de projet** et cliquez sur **Lancer le sélecteur de colonne** dans le volet Propriétés.

    -   Assurez-vous que l'option **Toutes les colonnes** est sélectionnée dans la liste déroulante **Commence par**. Ceci force **Colonnes de projet** à transmettre toutes les colonnes (à l'exception de celles que nous allons exclure).
    -   Dans la ligne suivante, sélectionnez **Exclure** et **Noms des colonnes**, puis cliquez dans la zone de texte. Une liste des colonnes s'affiche. Sélectionnez « normalized-losses », qui s'ajoute dans la zone de texte.
    -   Cliquez sur le bouton **OK** pour fermer le sélecteur de colonne.

    ![Select columns][]

    Le volet Propriétés pour **Colonnes de projet** indique qu'il transmettra toutes les colonnes du jeu de données à l'exception de « normalized-losses ».

    ![Project Columns properties][]

    > **Conseil** : vous pouvez ajouter un commentaire sur un module en double-cliquant sur ce module, puis en entrant du texte. Ceci peut vous aider à voir d'un seul coup d'œil ce que fait chaque module dans votre expérience. Dans ce cas, double-cliquez sur le module **Colonnes de projet** et entrez le commentaire « Exclure les pertes normalisées ».

3.  Faites glisser le module **Programme de nettoyage des valeurs manquantes** vers le canevas de l'expérience et connectez-le au module **Colonnes de projet**. Dans le volet Propriétés, sélectionnez **Supprimer toute la ligne** sous **Pour les valeurs manquantes** pour nettoyer les données en supprimant les lignes contenant des valeurs manquantes. Double-cliquez sur le module et entrez le commentaire « Supprimer les lignes de valeur manquantes ».

    ![Missing Values Scrubber properties][]

4.  Démarrez l'expérience en cliquant sur **DÉMARRER** sous le canevas d'expérience.

Une fois l'expérience terminée, une coche verte s'affiche en regard de chaque module pour indiquer la réussite de leurs opérations. Notez que le statut « Exécution terminée » s'affiche dans le coin supérieur droit.

![First experiment run][]

Pour l'instant, nous avons juste nettoyé les données de l'expérience. Pour afficher le jeu de données nettoyé, double-cliquez sur le port de sortie du module **Programme de nettoyage des valeurs manquantes** et sélectionnez **Visualiser**. Notez que la colonne « perte normalisées » n'est plus là et qu'il ne manque plus de données.

Maintenant que les données sont nettoyées, nous pouvons indiquer les fonctionnalités que nous allons utiliser dans le modèle de prévision.

### Étape 3 : Définition des fonctionnalités

Dans Machine Learning, les *fonctionnalités* sont des propriétés mesurables individuelles d'un élément qui vous intéresse. Dans notre jeu de données, chaque ligne représente une automobile, et chaque colonne une fonctionnalité de cette automobile. La recherche du jeu de fonctionnalités adéquat pour la création d'un modèle de prévision requiert de l'expérimentation et des connaissances sur le problème qui se pose. Certaines fonctionnalités sont mieux adaptées à la prévision que d'autres. Ainsi, certaines fonctionnalités ont une forte corrélation avec d'autres fonctionnalités, comme city-mpg par rapport à highway-mpg. Elles n'apporteront donc pas beaucoup de nouvelles informations au modèle. Vous pouvez les supprimer.

Nous allons développer un modèle utilisant un sous-ensemble de ces fonctionnalités pour notre jeu de données. Vous pouvez revenir en arrière et sélectionner différentes fonctionnalités, relancer l'expérience et voir si vous obtenez de meilleurs résultats. En guise de test, sélectionnez les fonctionnalités suivantes (colonnes) avec le module **Colonnes de projet**. Notez que pour la formation du modèle, vous devez inclure la valeur *price*, car c'est ce que nous voulons prévoir.

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1.  Faites glisser un autre module **Colonnes de projet** vers le canevas de l'expérience et connectez-le au module **Programme de nettoyage des valeurs manquantes**. Double-cliquez sur le module et entrez le commentaire « Sélection des fonctionnalités pour la prévision ».

2.  Cliquez sur **Lancer le sélecteur de colonne** dans le volet Propriétés.

3.  Dans le sélecteur de colonne, sélectionnez **Aucune colonne** pour **Commence par**, puis sélectionnez **Inclure** et **noms des colonnes** dans la ligne de filtre. Entrez votre liste de noms de colonnes. Ceci force le module à transmettre uniquement les colonnes spécifiées.

    > **Conseil** : à ce point de l'expérience, les définitions de colonnes de nos données sont transmises du jeu de données d'origine vers le module **Programme de nettoyage des valeurs manquantes**. Lorsque vous connectez **Colonnes de projet** à **Programme de nettoyage des valeurs manquantes**, le module **Colonnes de projet** intègre les définitions de colonnes dans nos données. Lorsque vous cliquez sur la zone des noms de colonnes, une liste de colonnes s'affiche, vous permettant de sélectionner les colonnes une à une pour les ajouter à la liste.

4.  Cliquez sur **OK**.

![Select columns][1]

Ceci produira le jeu de données que nous allons utiliser dans l'algorithme d'apprentissage durant les prochaines étapes. Plus tard, vous pouvez le renvoyer et réessayer avec une autre sélection de fonctionnalités.

### Étape 4 : Sélection et application d'un algorithme d'apprentissage

﻿À présent que les données sont prêtes, la construction d'un modèle de prévision passe par la formation et le test. La *classification* et la *régression* sont deux types de techniques d'apprentissage automatique supervisé. La classification permet d'établir une prévision à partir d'un jeu de valeurs défini, comme une couleur (rouge, bleu ou vert). La régression permet d'établir une prédiction à partir d'un jeu de valeurs continu, comme l'âge d'une personne.

Nous voulons prévoir le prix d'une automobile, qui peut être de n'importe quelle valeur. Nous allons donc utiliser le modèle de régression. Pour cet exemple, nous allons former un simple modèle de *régression linéaire*, que nous testerons à l'étape suivante.

1.  Divisez les données en jeux de formation et de test. Sélectionnez et faites glisser le module **Fractionner** sur le canevas d'expérience et connectez-le à la sortie du dernier module **Colonnes de projet**. Définissez **Fraction de lignes dans le premier jeu de données de sortie** sur 0.75. Ainsi, nous allons utiliser 75 % des données pour former le modèle, et 25 % pour le tester.

    > **Conseil** : en modifiant le paramètre **Valeur de départ aléatoire**, vous pouvez produire différents exemples aléatoires pour la formation et le test. Ce paramètre contrôle la valeur de départ du générateur de nombres pseudo-aléatoire.

2.  Exécutez l'expérience. Ceci permet aux modules **Colonnes de projet** et **Fractionner** de transmettre des définitions de colonnes aux modules que nous allons ajouter par la suite.

3.  Pour sélectionner l'algorithme d'apprentissage, développez la catégorie **Machine Learning** dans la palette des modules à gauche du canevas, puis développez **Initialiser le modèle**. Ceci affiche différentes catégories de modules permettant d'initialiser un algorithme d'apprentissage.

    Pour cet exemple d'expérience, sélectionnez le module **Régression linéaire** sous la catégorie **Régression** (vous pouvez également trouver le module en tapant « Régression linéaire » dans la zone de recherche de la palette), puis faites-le glisser sur le canevas d'expérience.

4.  Recherchez et faites glisser le module **Effectuer le traitement de données pour apprentissage du modèle** vers l'expérience. Cliquez sur **Lancer le sélecteur de colonne**, puis sélectionnez la colonne *price*. Il s'agit de la valeur que notre modèle va prévoir.

    ![Select "price" column][]

5.  Connectez le port d'entrée gauche à la sortie du module **Régression linéaire** et le port d'entrée droit à la sortie des données de formation (port gauche) du module **Fractionner**.

6.  Exécutez l'expérience.

Le résultat est un modèle de régression formé qui permet de noter de nouveaux exemples pour effectuer des prédictions.

![Applying the learning algorithm][]

### Étape 5 : Établir une prévision sur des nouvelles données

﻿À présent que nous avons formé le modèle, nous pouvons l'utiliser pour noter le reste de nos données (25 %) pour voir si notre modèle fonctionne correctement.

1.  Recherchez et faites glisser le module **Noter le modèle** vers le canevas d'expérience et connectez le port d'entrée gauche à la sortie du module **Effectuer le traitement de données pour apprentissage du modèle**, et le port d'entrée droit à la sortie des données de test (port droit) du module **Fractionner**.

    ![Score Model module][]

2.  Exécutez l'expérience et affichez la sortie du module **Noter le modèle** (double-cliquez sur le port de sortie et sélectionnez **Visualiser**). La sortie affiche les valeurs prévues pour le prix, ainsi que les valeurs connues des données de test.

3.  Enfin, pour tester la qualité des résultats, sélectionnez et faites glisser le module **Évaluer le modèle** vers le canevas d'expérience et connectez le port d'entrée gauche à la sortie du module **Noter le modèle** (il y a deux ports d'entrée, car le module **Évaluer le modèle** permet de comparer deux modèles).

4.  Exécutez l'expérience et affichez la sortie du module **Évaluer le modèle** (double-cliquez sur le port de sortie et sélectionnez **Visualiser**). Les statistiques suivantes s'affichent pour notre modèle.

    -   **Erreur d'absolue moyenne** (EAM) : la moyenne des erreurs absolues (une *erreur* correspond à la différence entre une valeur prévue et une valeur réelle).
    -   **Racine d'erreur moyenne carrée** (RMSE) : la racine carrée de la moyenne des erreurs carrées des prévisions effectuées sur le jeu de données de test.
    -   **Erreur d'absolu relative** : la moyenne des erreurs absolues relative à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
    -   **Erreur carrée relative** : la moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
    -   **﻿Coefficient de détermination** : aussi nommé « valeur R carré », il s'agit d'une mesure statistique indiquant à quel point un modèle correspond aux données.

    Pour chaque erreur de statistique, plus le nombre est petit, meilleur est le résultat : une petite valeur indique que la prévision est plus proche de la valeur réelle. Plus la valeur du **Coefficient de détermination**, est proche de un (1.0), plus la prévision est correcte.

    ![Evaluation results][]

Au final, l'expérience doit ressembler à ceci :

![Complete experiment][]

### Et ensuite ?

Maintenant que votre expérience est configurée, vous pouvez créer une itération pour améliorer le modèle. Par exemple, vous pouvez modifier les fonctionnalités que vous utilisez pour votre prévision. Ou vous pouvez modifier les propriétés de l'algorithme **Régression linéaire**, ou essayer un autre algorithme. Vous pouvez même ajouter plusieurs algorithmes à votre expérience en même temps et les comparer (deux à deux) en utilisant le module **Évaluer le modèle**.

> **Conseil** : utilisez le bouton **ENREGISTRER SOUS** situé sous le canevas d'expérience pour copier la moindre itération de votre expérience. Vous pouvez afficher toutes les itérations de votre expérience en cliquant sur **AFFICHER L'HISTORIQUE D'EXÉCUTION**, sous le canevas. Pour plus d'informations, consultez la rubrique d'aide de ML Studio, **Affichage de l'historique d'exécution**.

Une fois satisfait de votre modèle, vous pouvez le publier en tant que service web permettant de prévoir des prix automobiles en utilisant de nouvelles données. Pour plus d'informations, consultez la rubrique d'aide de ML Studio, **Publication d'expériences**.

Pour obtenir un didacticiel pas à pas plus avancé et détaillé sur la création, la formation, la notation et la publication d'un modèle de prévision, consultez [Didacticiel pas à pas : Développement d'une solution prédictive avec Azure Machine Learning][].

<!-- Images -->

  [Étape 1 : Obtention des données]: #step-1-get-data
  [Étape 2 : Prétraitement des données]: #step-2-pre-process-data
  [Étape 3 : Définition des fonctionnalités]: #step-3-define-features
  [Étape 4 : Sélection et application d'un algorithme d'apprentissage]: #step-4-choose-and-apply-a-learning-algorithm
  [Étape 5 : Établir une prévision sur des nouvelles données]: #step-5-predict-over-new-data
  [Palette search]: ./media/machine-learning-create-experiment/screen1a.png
  [Dataset]: ./media/machine-learning-create-experiment/screen1.png
  [Dataset visualization]: ./media/machine-learning-create-experiment/screen1b.png
  [Select columns]: ./media/machine-learning-create-experiment/screen3.png
  [Project Columns properties]: ./media/machine-learning-create-experiment/screen4.png
  [Missing Values Scrubber properties]: ./media/machine-learning-create-experiment/screen4a.png
  [First experiment run]: ./media/machine-learning-create-experiment/screen5.png
  [1]: ./media/machine-learning-create-experiment/screen6.png
  [Select "price" column]: ./media/machine-learning-create-experiment/screen7.png
  [Applying the learning algorithm]: ./media/machine-learning-create-experiment/screen8.png
  [Score Model module]: ./media/machine-learning-create-experiment/screen8a.png
  [Evaluation results]: ./media/machine-learning-create-experiment/screen9.png
  [Complete experiment]: ./media/machine-learning-create-experiment/screen10.png
  [Didacticiel pas à pas : Développement d'une solution prédictive avec Azure Machine Learning]: http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/
