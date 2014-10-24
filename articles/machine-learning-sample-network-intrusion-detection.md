<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning Sample: Network intrusion detection | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Exemple Azure Machine Learning : Détection d'intrusions dans un réseau

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiment - Network Intrusion Detection - Development

## Description du problème

Cette démonstration met en œuvre différentes caractéristiques réseau afin de détecter les activités réseau associées à une intrusion ou à une attaque. Ce problème de classification binaire n'est pas très difficile, car les caractéristiques sont assez révélatrices d'une classe et les classes sont relativement bien équilibrées. Comme plusieurs caractéristiques font partie de l'étiquette, il est important de supprimer toutes les parties de l'étiquette avant l'apprentissage par le classifieur.

## Données

1.  Les données, qui viennent de KDD cup 1999, comprennent à la fois les jeux de données d'apprentissage et de test. La précision de la classification sur le jeu de données d'apprentissage est normalement plus élevée que sur le nouveau jeu de données, car de nouvelles intrusions réseau sont ajoutées au jeu de test. Le jeu de données d'apprentissage comporte environ 126 000 lignes et 43 colonnes, étiquettes comprises. Comme trois colonnes font partie des informations d'étiquette, il reste 40 colonnes pour l'apprentissage du modèle. Ces 40 colonnes sont pour la plupart numériques, avec quelques chaînes et caractéristiques de catégorisation. Les données de test contiennent environ 22 500 exemples (avec les mêmes 43 colonnes que les données d'apprentissage).
2.  Les données ont été chargées sur un objet blob public pour en faciliter l'accès. AzureML comprend des modules de lecture permettant de lire les données provenant de différents stockages comme les objets blob en quelques clics, sans code supplémentaire.

## Modèle

1.  La première étape du modèle est de définir les conditions des données pour leur classification ultérieure. La colonne « Class » contient les étiquettes à prédire. Ces étiquettes sont soit « normal » (aucune intrusion), soit le nom d'une attaque. Certaines attaques ne présentent pas beaucoup d'exemples dans les données d'apprentissage et le jeu de données de test comporte des attaques nouvelles. Nous convertissons donc ces étiquettes de classe en étiquettes de classe binaire, afin de faciliter la résolution du problème. Studio comporte un module intégré qui facilite cette étape de prétraitement. Le module de valeur d'indicateur est utilisé pour définir de façon binaire les étiquettes de classe, puis le module de colonnes du projet est utilisé pour sélectionner cette colonne d'étiquette de classe binaire (à l'exception de la colonne d'étiquette de la classe d'origine).
2.  Dans le cadre du développement de cette expérience, nous constatons via la sélection et la corrélation des caractéristiques que de nombreuses caractéristiques ont une corrélation forte avec l'étiquette. C'est courant dans les jeux de données synthétiques. Cloud ML permet de visualiser cet aspect en quelques clics. Pour cela, sélectionnez l'option de visualisation dans le résultat du module projet, puis cliquez sur l'en-tête d'une des caractéristiques pour lancer le visualiseur. Sélectionnez ensuite l'étiquette de classe dans la liste déroulante de comparaison.
3.  Comme un petit nombre de caractéristiques est fortement corrélé à l'étiquette, nous avons testé des combinaisons de classifieur linéaire et non linéaire, avec ou sans sélection de la caractéristique, afin de déterminer le meilleur modèle. Cette comparaison se fait via le module Évaluer le modèle.

## Résultats

1.  Le classifieur non linéaire (arbre de décision optimisé) offre des résultats légèrement meilleurs que le classifieur linéaire (régression logistique) :

![][]

Nous souhaitons maintenant comparer un arbre de décision optimisé avec sélection des caractéristiques. Les performances de classification sont assez similaires, mais l'arbre de décision optimisé sur toutes les caractéristiques se montre légèrement meilleur, et sera utilisé pour le processus. Notez que ce classement d'intégration élevée est typique de ce jeu de données.

![][1]

<!-- Removed until this part is fixed ## Operationalization ##   We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio:   1. First step is to save the learned model (by right clicking on the classifier module output)  1. Now create a new experiment and search for saved model and drop it in the panel for new experiment  1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment  1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service  1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio    -->

  []: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
  [1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
