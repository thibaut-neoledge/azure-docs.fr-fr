<properties 
	pageTitle="Sélection des paramètres permettant d’optimiser des algorithmes dans Microsoft Azure Machine Learning | Microsoft Azure" 
	description="Explique comment choisir l’ensemble de paramètres optimal pour un algorithme dans Microsoft Azure Machine Learning." 
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
	ms.date="05/27/2016" 
	ms.author="bradsev" />


# Sélection des paramètres permettant d’optimiser des algorithmes dans Microsoft Azure Machine Learning

Cette rubrique explique comment choisir le bon ensemble d’hyperparamètres pour un algorithme dans Microsoft Azure Machine Learning. La plupart des algorithmes ML dépendent de différents paramètres. Lorsque nous gérons l’apprentissage d’un modèle, nous devons fournir des valeurs pour ces paramètres. L’efficacité du modèle formé dépend des paramètres de modèle choisis. Le processus de recherche de l’ensemble optimal de paramètres est connu sous le nom de « sélection du modèle ».

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Cette sélection peut être effectuée de différentes manières. Dans ML, la méthode de validation croisée est l’une des plus largement utilisées pour la sélection de modèle. Il s’agit du mécanisme par défaut utilisé à cette fin dans Microsoft Azure Machine Learning. Comme les langages R et Python sont pris en charge par Microsoft Azure Machine Learning, les utilisateurs peuvent toujours implémenter leur propre mécanisme de sélection de modèle, via l’un ou l’autre de ces langages.

Le processus de recherche de l’ensemble de paramètres idéal comprend quatre étapes.

1.	**Définir l’espace de paramètre** : pour l’algorithme, nous devons d’abord déterminer les valeurs de paramètres exactes que nous souhaitons prendre en compte. 
2.	**Définir les paramètres de validation croisée** : pour le jeu de données, nous devons déterminer comment choisir les plis de validation croisée. 
3.	**Définir la mesure** : nous devons à présent déterminer la mesure à utiliser pour évaluer l’ensemble de paramètres le plus approprié (exactitude, erreur quadratique moyenne, précision, rappel ou f-score). 
4.	**Apprentissage, évaluation et comparaison** : pour chaque combinaison unique de valeurs de paramètres, la validation croisée est effectuée. Le modèle le plus performant peut alors être choisi selon la mesure d’erreur définie par l’utilisateur.

L’expérience ci-dessous indique comment effectuer cette opération dans Microsoft Azure Machine Learning.

![image1](./media/machine-learning-algorithm-parameters-optimize/fig1.png)
 
## Définir l’espace de paramètre
L’ensemble de paramètres peut être défini lors de l’étape d’initialisation du modèle. Le volet de paramètres de l’ensemble des algorithmes ML propose deux modes de formation : **Paramètre unique** et **Plage de paramètres**. Nous devons choisir le mode **Plage de paramètres** (figure 1). Il est ainsi possible de saisir plusieurs valeurs pour chaque paramètre ; les valeurs séparées par une virgule peuvent être saisies dans la zone de texte. Vous pouvez également utiliser l’option **Utiliser le générateur de plage** pour définir les points minimum et maximum de la grille et le nombre total de points à générer. Par défaut, les valeurs de paramètre sont générées sur une échelle linéaire. Cependant, si la case à cocher **Échelle logarithmique** est activée, les valeurs sont générées sur une échelle logarithmique (selon laquelle le rapport entre les points adjacents est constant, et non leur différence). Pour les paramètres de type entier, une plage peut être définie avec un trait d’union « - », par exemple « 1-10 », ce qui signifie que tous les entiers compris entre 1 et 10 (tous deux inclus) forment le jeu de paramètres. Un mode mixte est également pris en charge. Exemple : « 1-10, 20, 50 ». Dans ce cas, les entiers 20 et 50 sont ajoutés à l’ensemble de paramètres, en plus de la plage d’entiers 1-10.
  
![image2](./media/machine-learning-algorithm-parameters-optimize/fig2.png) ![image3](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## Définition du pli de validation croisée
Le module [Partition and Sample][partition-and-sample] peut être utilisé pour affecter des plis aux données, de manière aléatoire. Dans la figure suivante, nous avons représenté un exemple de configuration de ce module, dans lequel nous allons définir 5 plis et affecter au hasard le nombre de plis aux exemples d’instances.

![image4](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## Définir la mesure
Le module [Sweep Parameters][sweep-parameters] assure la prise en charge de la sélection empirique du meilleur ensemble de paramètres, pour un algorithme et un jeu de données spécifiques. Le volet des propriétés de ce module comprend la mesure à utiliser pour déterminer le meilleur ensemble de paramètres, en plus d’autres informations concernant l’apprentissage du modèle. Il présente deux listes déroulantes différentes pour les algorithmes de classification et de régression, respectivement. Si l’algorithme en question est un algorithme de classification, la mesure de régression est ignorée, et vice versa. Dans cet exemple, nous avons choisi la mesure **Exactitude**.
 
![image5](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## Apprentissage, évaluation et comparaison  
Le même module [Sweep Parameters][sweep-parameters] gère l’apprentissage de tous les modèles correspondant à l’ensemble de paramètres, évalue diverses mesures et renvoie ensuite le meilleur modèle formé, en fonction de la mesure choisie par l’utilisateur. Ce module dispose de deux entrées obligatoires :

* apprenant non formé, 
* jeu de données, 

ainsi qu’un jeu de données en entrée. Nous allons connecter le jeu de données incluant des informations sur les plis au jeu de données en entrée obligatoire. Si le jeu de données n’est associé à aucune information sur les plis, une validation croisée de 10 plis est exécutée automatiquement, par défaut. Si l’affectation de plis n’est pas effectuée et si un jeu de données de validation est fourni au port de jeu de données facultatif, un mode de test de formation est sélectionné et le premier jeu de données est utilisé pour gérer l’apprentissage du modèle pour chaque combinaison de paramètres. Ensuite, il est évalué sur le jeu de données de validation. Le port de sortie de gauche du module affiche des mesures différentes en fonction des valeurs de paramètres. Le port de sortie de droite indique le modèle formé correspondant au modèle le plus performant, conformément à la mesure choisie par l’utilisateur (dans ce cas, l’exactitude).

![image6](./media/machine-learning-algorithm-parameters-optimize/fig6a.png) ![image7](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)
 
Nous pouvons voir les paramètres exacts choisis en visualisant le port de sortie de droite. Ce modèle peut être utilisé lors du calcul de la notation d’un ensemble de test ou dans un service web mis en œuvre après l’enregistrement en tant que modèle formé.


<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[sweep-parameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
 

<!---HONumber=AcomDC_0601_2016-->