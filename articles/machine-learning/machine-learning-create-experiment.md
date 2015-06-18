<properties 
	pageTitle="Création d'une expérience simple dans Machine Learning Studio | Azure" 
	description="Création d'une expérience pour effectuer l'apprentissage et tester un modèle simple dans Azure Machine Learning Studio" 
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
	ms.date="01/07/2015" 
	ms.author="garye"/>

#Création d'une expérience simple dans Azure Machine Learning Studio 
 
Fondamentalement, une expérience d'analyse prédictive comprend des composants permettant d'accomplir les actions suivantes : *créer un modèle*, *former un modèle* et *noter et évaluer un modèle*. Vous pouvez les combiner pour créer une expérience qui extrait des données, forme un modèle et applique ce modèle à de nouvelles données. Vous pouvez également ajouter des modules pour traiter des données au préalable et sélectionner des fonctionnalités, diviser des données en ensembles de formation et de test ou évaluer ou effectuer une validation croisée de la qualité de votre modèle.  

Dans cet article, nous allons utiliser Microsoft Azure Machine Learning Studio (ML Studio) pour développer et effectuer une itération sur une expérience d'analyse prédictive simple. Pour ouvrir ML Studio, cliquez sur ce lien : [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Pour en savoir plus sur la mise en route de ML Studio, consultez la page d'accueil de [Microsoft Azure Machine Learning Studio](https://studio.azureml.net/). 
 

##Cinq étapes pour créer une expérience 

Voici les cinq étapes de base que vous pouvez suivre pour créer une expérience dans ML Studio, afin de créer, de former et d'attribuer une notation à votre modèle :  

- Création d'un modèle 
	- [Étape 1 : Obtention des données]
	- [Étape 2 : Traitement préalable des données]
	- [Étape 3 : Définition des fonctionnalités]
- Formation du modèle 
	- [Étape 4 : Sélection et application d'un algorithme d'apprentissage]
- Attribution d'une notation et test du modèle 
	- [Étape 5 : Prédiction relative aux nouvelles données] 

[Étape 1 : Obtention des données]: #step-1-get-data
[Étape 2 : Traitement préalable des données]: #step-2-preprocess-data
[Étape 3 : Définition des fonctionnalités]: #step-3-define-features
[Étape 4 : Sélection et application d'un algorithme d'apprentissage]: #step-4-choose-and-apply-a-learning-algorithm
[Étape 5 : Prédiction relative aux nouvelles données]: #step-5-predict-over-new-data 

Dans cet exemple, nous allons étudier la création d'un modèle de régression utilisant des données issues du secteur automobile, à titre d'exemple. L'objectif est de prédire le prix d'une voiture à l'aide de différentes variables, telles que les spécifications techniques et de fabrication. 

### Étape 1 : Obtention des données

Plusieurs exemples de jeux de données sont inclus dans ML Studio. Vous pouvez importer des données à partir de nombreuses sources. Pour les besoins de cet exemple, nous allons utiliser un jeu de données inclus, **Données sur le prix des véhicules automobiles (brutes)**, qui représente les données sur les prix du secteur automobile.

1. Démarrez une nouvelle expérience en cliquant sur l'option **+ NOUVEAU** située en bas de la fenêtre de Machine Learning Studio, sélectionnez **EXPÉRIENCE**, puis " Expérience vide ". Sélectionnez le nom d'expérience par défaut, situé en haut de la zone de dessin, et remplacez-le par un nom significatif, par exemple : **Prédiction de prix d'une voiture**.

2. ﻿Sur la gauche de la zone de dessin de l'expérience se trouve une palette de jeux de données et de modules. Saisissez la valeur **automobile** dans la zone de recherche se trouvant en haut de cette palette, afin de rechercher le jeu de données **Données sur le prix des véhicules automobiles (brutes)**. 

	![Palette search][screen1a]

3. Faites glisser ce jeu de données sur le canevas de l'expérience. 

	![Dataset][screen1]

Pour voir à quoi ressemblent ces données, cliquez sur le port de sortie situé en bas du jeu de données d'automobile et sélectionnez **Visualiser**. Les variables du jeu de données sont affichées sous forme de colonnes, tandis que les types d'automobiles sont affichés sous forme de lignes. La colonne située le plus à droite (colonne 26, intitulée " Price ") est la variable cible, dont nous allons essayer de prédire la valeur. 

![Dataset visualization][screen1b]

Fermez la fenêtre de visualisation en cliquant sur la croix " **x** " située dans l'angle supérieur droit de la fenêtre.

### Étape 2 : Traitement préalable des données

Pour pouvoir être analysé, un jeu de données nécessite généralement un traitement préalable. Vous avez peut-être remarqué l'absence de certaines valeurs dans les colonnes des différentes lignes. Pour que vous puissiez analyser les données, ces valeurs manquantes doivent être nettoyées. Dans le cas qui nous occupe, nous allons supprimer toutes les lignes dans lesquelles des valeurs sont manquantes. De plus, la colonne **normalized-losses** contient une grande quantité de valeurs manquantes. Nous allons donc l'exclure du modèle. 

> [AZURE.TIP] Le nettoyage des valeurs manquantes des données d'entrée est un prérequis pour l'utilisation de la plupart des modules. 

Commençons par supprimer la colonne **normalized-losses**, puis chaque ligne ayant des données manquantes. 

1. Dans la zone de recherche située sur la partie supérieure de la palette de modules, saisissez la chaîne **project columns** afin de rechercher le module **Project Columns**, puis faites-le glisser vers la zone de dessin de l'expérience et connectez-le au jeu de données **Données sur le prix des véhicules automobiles (brutes)**. Ce module permet de sélectionner les colonnes de données à inclure ou exclure du modèle. 

2. Sélectionnez le module **Project Columns** et cliquez sur **Lancer le sélecteur de colonne** dans le volet des propriétés. 

	- Assurez-vous que l'option **Toutes les colonnes** est sélectionnée dans la liste déroulante de filtres, **Commencer par**. Vous indiquez ainsi au module **Project Columns** de transmettre toutes les colonnes, sauf celles que nous nous apprêtons à exclure. 
	- Dans la ligne suivante, sélectionnez **Exclure** et **Noms des colonnes**, puis cliquez dans la zone de texte. Une liste de colonnes s'affiche. Sélectionnez la colonne **normalized-losses**, qui est alors ajoutée à la zone de texte. 
	- Cliquez sur le bouton en forme de coche (OK) pour fermer le sélecteur de colonne.

    ![Select columns][screen3]
	
	Le volet de propriétés du module **Project Columns** indique qu'il transmettra toutes les colonnes du jeu de données, à l'exception de **normalized-losses**. 

    ![Project Columns properties][screen4]

    > [AZURE.TIP] Vous pouvez ajouter un commentaire dans un module en double-cliquant sur ce module, puis en saisissant du texte. Ceci peut vous aider à voir d'un seul coup d'œil ce que fait chaque module dans votre expérience. Dans ce cas, double-cliquez sur le module **Project Columns** et saisissez le commentaire suivant : " Exclure les pertes normalisées ". 

3. Faites glisser le module **Missing Values Scrubber** vers la zone de dessin de l'expérience et connectez-le au module **Project Columns**. Dans le volet **Propriétés**, sélectionnez **Supprimer toute la ligne** sous **Pour les valeurs manquantes**, afin de nettoyer les données en supprimant les lignes contenant des valeurs manquantes. Double-cliquez sur le module et saisissez le commentaire suivant : " Supprimer les lignes de valeur manquantes ".

	![Missing Values Scrubber properties][screen4a]

4. Démarrez l'expérience en cliquant sur l'option **DÉMARRER** sous la zone de dessin de l'expérience.

Une fois l'expérience terminée, une coche verte s'affiche en regard de chaque module pour indiquer la réussite de leurs opérations. Notez que le statut **Exécution terminée** s'affiche dans le coin supérieur droit de la fenêtre.

![First experiment run][screen5]

Pour l'instant, nous n'avons exécuté que l'action de nettoyage dans l'expérience. Pour afficher le jeu de données nettoyé, cliquez sur le port de sortie du module **Missing Values Scrubber** et sélectionnez **Visualiser**. Vous pouvez constater que la colonne **normalized-losses** n'est plus là et qu'il ne manque plus de données.

Maintenant que les données sont nettoyées, nous pouvons indiquer les fonctionnalités que nous allons utiliser dans le modèle de prévision.

### Étape 3 : Définition des fonctionnalités

Dans Machine Learning, *features* sont des propriétés mesurables individuelles d'un élément qui vous intéresse. Dans notre jeu de données, chaque ligne représente une automobile et chaque colonne, une fonctionnalité de cette automobile. La recherche du jeu de fonctionnalités adéquat pour la création d'un modèle de prévision requiert certaines expériences et des connaissances sur le problème qui se pose. Certaines fonctionnalités sont mieux adaptées à la prévision que d'autres. De même, certaines fonctionnalités présentent une forte corrélation avec d'autres fonctionnalités, comme city-mpg par rapport à highway-mpg. Elles n'apporteront donc pas beaucoup de nouvelles informations au modèle. Vous pouvez les supprimer.

Nous allons développer un modèle utilisant un sous-ensemble de ces fonctionnalités pour notre jeu de données. Vous pouvez revenir en arrière et sélectionner différentes fonctionnalités, relancer l'expérience et voir si vous obtenez de meilleurs résultats. En guise de test, nous allons sélectionner les fonctionnalités suivantes (colonnes) avec le module **Project Columns**. Notez que, pour l'apprentissage du modèle, nous devons inclure la valeur *price*, car c'est ce que nous voulons prévoir.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Faites glisser un autre module **Project Columns** vers la zone de dessin de l'expérience et connectez-le au module **Missing Values Scrubber**. Double-cliquez sur le module et saisissez le commentaire suivant : " Sélection des fonctionnalités pour la prévision ".

2. Cliquez sur l'option **Lancer le sélecteur de colonne** figurant dans le volet **Propriétés**. 

3. Dans le sélecteur de colonne, choisissez la valeur **Aucune colonne** pour **Commencer par**, puis sélectionnez **Inclure** et **Noms des colonnes** dans la ligne de filtre. Entrez votre liste de noms de colonnes. Vous indiquez aussi au module qu'il doit transmettre les colonnes spécifiées uniquement.

	> [AZURE.TIP] Étant donné que nous avons exécuté l'expérience, les définitions de colonne de nos données ont été transmises à partir du jeu de données d'origine, via le module **Missing Values Scrubber**. Lorsque vous connectez le module **Project Columns** au module **Missing Values Scrubber**, le module **Project Columns** prend connaissance des définitions de colonne dans nos données. Lorsque vous cliquez sur la zone **Noms des colonnes**, une liste de colonnes s'affiche, vous permettant de sélectionner celles que vous voulez ajouter à la liste. 

4. Cliquez sur le bouton en forme de coche (OK) pour continuer.

![Select columns][screen6]

Cette opération génère le jeu de données qui sera utilisé dans l'algorithme d'apprentissage lors des étapes suivantes. Plus tard, vous pouvez reprendre la procédure en utilisant une autre sélection de fonctionnalités. 

### Étape 4 : Sélection et application d'un algorithme d'apprentissage

Les données sont désormais prêtes. La création d'un modèle de prévision se compose d'un apprentissage et de tests. *Classification* et *regression* sont deux types de techniques d'apprentissage automatique supervisé. La classification permet d'établir une prévision à partir d'un jeu de valeurs défini, comme une couleur (rouge, bleu ou vert). La régression permet d'établir une prédiction à partir d'un jeu de valeurs continu, comme l'âge d'une personne.

Nous voulons prévoir le prix d'une automobile, qui peut être de n'importe quelle valeur. Nous allons donc utiliser le modèle de régression. Pour cet exemple, nous allons former un modèle de *linear regression* simple, que nous testerons à l'étape suivante. 

1. Divisez les données en jeux de formation et de test. Sélectionnez et faites glisser le module **Split** vers la zone de dessin de l'expérience et connectez-le à la sortie du dernier module **Project Columns**. Définissez la zone **Fraction de lignes dans le premier jeu de données de sortie** sur la valeur 0.75. Ainsi, nous allons utiliser 75 % des données pour former le modèle, et 25 % pour le tester.

	> [AZURE.TIP] En modifiant le paramètre **Valeur de départ aléatoire**, vous pouvez produire différents échantillons aléatoires pour la formation et le test. Ce paramètre contrôle la valeur de départ du générateur de nombres pseudo-aléatoire.
	
2. Exécutez l'expérience. Cela permet aux modules **Project Columns** et **Split** de transmettre des définitions de colonne aux modules que nous allons ajouter par la suite.  

3. Pour sélectionner l'algorithme d'apprentissage, développez la catégorie **Machine Learning** dans la palette des modules, à gauche de la zone de dessin, puis développez **Initialiser le modèle**. Ceci affiche différentes catégories de modules permettant d'initialiser un algorithme d'apprentissage. 

	Pour les besoins de cet exemple, sélectionnez le module **Linear Regression** sous la catégorie **Régression** (vous pouvez également rechercher le module en saisissant l'expression " Linear Regression " dans la zone de recherche de la palette), puis faites-le glisser vers la zone de dessin de l'expérience.

4. Recherchez et faites glisser le module **Train Model** vers l'expérience. Connectez le port d'entrée de gauche à la sortie du module **Linear Regression**. Connectez le port d'entrée de droite à la sortie des données d'apprentissage (port de gauche) du module **Split**.

5. Exécutez l'expérience pour transmettre les définitions de colonne au module **Train Model**.
 
6. Sélectionnez le module **Train Model**, cliquez sur l'option **Lancer le sélecteur de colonne** du volet **Propriétés** et sélectionnez la colonne **Price**. Il s'agit de la valeur que notre modèle va prévoir.

	![Select "price" column][screen7]

7. Exécutez l'expérience. 

Le résultat est un modèle de régression formé qui permet de noter de nouveaux exemples pour effectuer des prédictions. 

![Applying the learning algorithm][screen8]

### Étape 5 : Prédiction relative aux nouvelles données 

﻿À présent que nous avons formé le modèle, nous pouvons l'utiliser pour la notation du reste de nos données (25 %), afin de voir si notre modèle fonctionne correctement. 

1. Recherchez et faites glisser le module **Score Model** vers la zone de dessin de l'expérience, puis connectez le port d'entrée de gauche de ce dernier à la sortie du module **Train Model**. Connectez le port d'entrée de droite à la sortie des données de test (port de droite) du module **Split**.  

	![Score Model module][screen8a]

2. Pour exécuter l'expérience et afficher la sortie du module **Score Model**, cliquez sur le port de sortie et sélectionnez **Visualiser**. La sortie affiche les valeurs de prévision associées au prix, ainsi que les valeurs connues des données de test.  

3. Enfin, pour tester la qualité des résultats, sélectionnez et faites glisser le module **Evaluate Model** vers la zone de dessin de l'expérience, puis connectez le port d'entrée de gauche à la sortie du module **Score Model**. (Il existe deux ports d'entrée, car le module **Evaluate Model** peut être utilisé pour comparer deux modèles.)
 
4. Pour exécuter l'expérience et afficher la sortie du module **Evaluate Model**, cliquez sur le port de sortie et sélectionnez **Visualiser**. Les statistiques suivantes s'affichent pour notre modèle :

	- **Moyenne des erreurs absolues** : moyenne des erreurs absolues (une *error* correspond à la différence entre la valeur de prédiction et la valeur réelle).
	- **Erreur du carré moyen racine** : racine carrée de la moyenne des erreurs carrées associées aux prévisions effectuées sur le jeu de données de test.
	- **Moyenne relative des erreurs absolues** : moyenne des erreurs absolues par rapport à la différence absolue entre les valeurs réelles et la moyenne de l'ensemble des valeurs réelles.
	- **Erreur carrée relative** : moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
	- **Coefficient de détermination** : également nommé **valeur R au carré**. Il s'agit d'une mesure statistique indiquant à quel point un modèle correspond aux données.
	
	Pour chacune des statistiques liées aux erreurs, les valeurs les plus petites sont privilégiées. En effet, une valeur plus petite indique un degré de correspondance plus étroit avec la valeur réelle. Dans le cas du **coefficient de détermination**, plus la valeur sera proche de 1 (1.0), plus les prédictions seront précises.

	![Evaluation results][screen9]

Au final, l'expérience doit ressembler à ceci :

![Complete experiment][screen10]

### Et ensuite ?

Maintenant que votre expérience est configurée, vous pouvez créer une itération pour améliorer le modèle. Par exemple, vous pouvez modifier les fonctionnalités que vous utilisez pour votre prévision. Vous pouvez également modifier les propriétés de l'algorithme **Régression linéaire** ou utiliser un autre algorithme. Il est également possible d'ajouter plusieurs algorithmes à votre expérience en même temps et de les comparer (deux à deux) via le module **Evaluate Model**. 

> [AZURE.TIP] Utilisez le bouton **ENREGISTRER SOUS** qui figure sous la zone de dessin de l'expérience afin de copier toute itération de votre expérience. Vous pouvez afficher toutes les itérations de votre expérience en cliquant sur l'option **AFFICHER L'HISTORIQUE D'EXÉCUTION**, sous la zone de dessin. Pour en savoir plus, consultez la section [Gérer les itérations des expériences dans Microsoft Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

Une fois satisfait de votre modèle, vous pouvez le publier en tant que service web permettant de prévoir l'évolution des prix dans le secteur automobile, en utilisant de nouvelles données. Consultez la section [Publier un service web Microsoft Azure Machine Learning][publish] pour en savoir plus.

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Pour obtenir une présentation pas à pas plus complète et détaillée pour la création, la gestion de l'apprentissage, la notation et la publication d'un modèle de prédiction, voir [Développement d'une solution prédictive avec Azure Machine Learning ][walkthrough]. 

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/screen10.png

<!--HONumber=49--> 