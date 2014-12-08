<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Exemple sous Machine Learning : détection d'intrusions dans un réseau | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />



# Exemple sous Azure Machine Learning : détection d'intrusions dans un réseau

>[AZURE.NOTE]
>L'[exemple d'expérience] et l'[exemple de jeu de données] associés à ce modèle sont disponibles dans ML Studio. Pour plus d'informations, consultez la section ci-dessous.
[Exemple d'expérience]: #exemple-expérience
[Exemple de jeu de données]: #exemple-jeudedonnées


## Description du problème ##

Cette démonstration met en œuvre différentes caractéristiques réseau afin de détecter les activités réseau associées à une intrusion ou à une attaque. Ce problème de classification binaire n'est pas très difficile, car les caractéristiques sont assez révélatrices d'une classe et les classes sont relativement bien équilibrées.  Comme plusieurs caractéristiques font partie de l'étiquette, il est important de supprimer toutes les parties de l'étiquette avant l'apprentissage par le classifieur. 

## Données ##

1. Les données, qui viennent de KDD cup 1999, comprennent à la fois les jeux de données d'apprentissage et de test.  La précision de la classification sur le jeu de données d'apprentissage est normalement plus élevée que sur le nouveau jeu de données, car de nouvelles intrusions réseau sont ajoutées au jeu de test.  Le jeu de données d'apprentissage comporte environ 126 000 lignes et 43 colonnes, étiquettes comprises.  Comme trois colonnes font partie des informations d'étiquette, il reste 40 colonnes pour l'apprentissage du modèle.  Ces 40 colonnes sont pour la plupart numériques, avec quelques chaînes et caractéristiques de catégorisation. Les données de test contiennent environ 22 500 exemples (avec les mêmes 43 colonnes que les données d'apprentissage). 
1. Les données ont été chargées sur un objet blob public pour en faciliter l'accès. AzureML comprend des modules de lecture permettant de lire les données provenant de différents stockages, comme les objets blob, en quelques clics, sans code supplémentaire. 

## Modèle ##

1. La première étape du modèle est de préparer les données pour leur classification ultérieure.  La colonne " Classe " contient les étiquettes à prédire.  Ces étiquettes sont " normales " (sans intrusion) ou contiennent le nom d'une attaque.  Certaines attaques ont peu d'exemples dans les données d'apprentissage et il existe de nouvelles attaques dans le jeu de données de test, donc nous convertissons ces étiquettes multiclasses en étiquettes de classe binaire pour rendre le problème possible.  Studio comporte un module intégré qui facilite cette étape de prétraitement. Le module de valeur d'indicateur est utilisé pour définir de façon binaire les étiquettes de classe, puis le module de colonnes du projet est utilisé pour sélectionner cette colonne d'étiquette de classe binaire (à l'exception de la colonne d'étiquette de la classe d'origine).  
1. Dans le cadre du développement de cette expérience, nous constatons via la sélection et la corrélation des caractéristiques que celles-ci sont souvent fortement liées à l'étiquette. C'est courant dans les jeux de données synthétiques.  Cloud ML permet de visualiser cet aspect en quelques clics. Pour cela, sélectionnez l'option de visualisation dans le résultat du module projet, puis cliquez sur l'en-tête de l'une des caractéristiques pour lancer le visualiseur. Sélectionnez ensuite l'étiquette de classe dans la liste déroulante de comparaison.
1. Comme un petit nombre de caractéristiques est fortement corrélé à l'étiquette, nous avons testé des combinaisons de classifieur linéaire et non linéaire, avec ou sans sélection de la caractéristique, afin de déterminer le meilleur modèle.  Cette comparaison se fait via le module Évaluer le modèle.    

## Résultats ##

1. Le classifieur non linéaire (arbre de décision optimisé) offre des résultats légèrement meilleurs que le classifieur linéaire (régression logistique) : 

![][1]


Nous souhaitons maintenant comparer un arbre de décision optimisé avec sélection des caractéristiques. Les performances de classification sont assez similaires, mais l'arbre de décision optimisé sur toutes les caractéristiques se montre légèrement meilleur, et sera utilisé pour le processus.  Notez que ce classement d'ASC élevé est typique pour ce jeu de données. 


![][2]

<!-- Removed until this part is fixed
## Operationalization ##


We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio: 

1. First step is to save the learned model (by right clicking on the classifier module output) 
1. Now create a new experiment and search for saved model and drop it in the panel for new experiment 
1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment 
1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service 
1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio   
-->


## Exemple d'expérience

L'exemple d'expérience suivant associé à ce modèle est disponible dans ML Studio dans la rubrique **EXPÉRIENCES** de l'onglet **EXEMPLES**.

> **Exemple d'expérience - Détection d'intrusions dans un réseau - Développement**


## Exemple de jeu de données

L'exemple de jeu de données suivant utilisé dans cette expérience est disponible dans ML Studio dans la palette de module sous **Jeux de données enregistrés**.

###network_intrusion_detection.csv
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../includes/machine-learning-sample-dataset-network-intrusion.md)]




[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[2]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
