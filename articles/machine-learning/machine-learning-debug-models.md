<properties 
	pageTitle="Déboguer votre modèle dans Azure Machine Learning | Microsoft Azure" 
	description="Décrit la procédure de débogage de votre modèle dans Azure Machine Learning." 
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
	ms.date="03/02/2016" 
	ms.author="bradsev;garye" />

# Déboguer votre modèle dans Azure Machine Learning

Cet article explique comment déboguer vos modèles dans Microsoft Azure Machine Learning. Plus précisément, il décrit les raisons possibles pour lesquelles l’exécution d’un modèle peut donner lieu à l’un des deux scénarios d’échec suivants :

* le module [Former le modèle][train-model] génère une erreur ; 
* le module [Noter le modèle][score-model] produit des résultats incorrects ; 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Le module Former le modèle génère une erreur

![image1](./media/machine-learning-debug-models/train_model-1.png)

le module [Former le modèle][train-model] attend les 2 entrées suivantes :

1. type de modèle de classification/régression issu de la collection de modèles fournie par Azure Machine Learning ;
2. données d’apprentissage avec une colonne Étiquette spécifiée. La colonne Étiquette spécifie la variable à prédire. Les autres colonnes incluses sont supposées être des fonctionnalités.

Ce module génère une erreur dans les cas suivants :

1. La colonne Étiquette est spécifiée de manière incorrecte, car plusieurs colonnes sont sélectionnées en tant qu’étiquette, ou un index de colonne incorrect a été sélectionné. Par exemple, le second cas s’applique si un index de colonne de 30 a été utilisé avec un jeu de données d’entrée qui ne comportait que 25 colonnes.

2. Le jeu de données ne contient aucune colonne Fonctionnalité. Par exemple, si le jeu de données d’entrée ne comporte qu’une seule colonne, identifiée en tant que colonne Étiquette, il n’existe aucune fonctionnalité avec laquelle générer le modèle. Dans ce cas, le module [Former le modèle][train-model] génère une erreur.

3. Le jeu de données d’entrée (Fonctionnalités ou Étiquette) contient une valeur Infini.


## Le module Noter le modèle produit des résultats incorrects

![image2](./media/machine-learning-debug-models/train_test-2.png)

Dans un graphique d’apprentissage/de test classique pour un apprentissage supervisé, le module [Fractionner les données][split] divise le jeu de données en deux parties : la partie utilisée pour effectuer l’apprentissage du modèle et la partie réservée pour noter l’efficacité du comportement du modèle formé vis-à-vis des données sur lesquelles il n’a pas été formé. Le modèle formé est ensuite utilisé pour noter les données de test, après quoi les résultats sont évalués afin de déterminer la précision du modèle.

Le module [Noter le modèle][score-model] nécessite deux entrées :

1. une sortie du modèle formé à partir du module [Former le modèle][train-model] ;
2. un jeu de données de notation sur lequel le modèle n’a pas été formé.

Même si l’expérience réussit, il est possible que le module [Noter le modèle][score-model] produise des résultats incorrects. Plusieurs scénarios peuvent donner lieu aux situations suivantes :

1. Si l’étiquette spécifiée est catégorielle et qu’un modèle de régression est formé sur les données, le module [Noter le modèle][score-model] produit une sortie incorrecte. Ceci est dû au fait que la régression requiert une variable dépendante continue. Dans ce cas, il est plus judicieux d’utiliser un modèle de classification. 
2. De la même façon, si un modèle de classification est formé sur un jeu de données comportant des nombres à virgule flottante dans la colonne Étiquette, il peut produire des résultats indésirables. Ceci s’explique par le fait que la classification requiert une variable dépendante discontinue qui autorise uniquement les valeurs couvrant un ensemble de classes fini et généralement plutôt restreint.
3. Si le jeu de données de notation ne contient pas toutes les fonctionnalités utilisées pour effectuer l’apprentissage du modèle, le module [Noter le modèle][score-model] génère une erreur.
4. Le module [Noter le modèle][score-model] ne produit aucune sortie correspondant à une ligne du jeu de données de notation qui présente une valeur manquante ou infinie pour l’une de ses fonctionnalités.
5. Le module [Noter le modèle][score-model] peut produire des sorties identiques pour toutes les lignes du jeu de données de notation. Ceci peut notamment se produire lors d’une tentative de classification à l’aide de forêts d’arbres de décision si le nombre minimal d’échantillons par nœud terminal est défini comme étant supérieur au nombre d’exemples d’apprentissage disponibles.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=AcomDC_0309_2016-->