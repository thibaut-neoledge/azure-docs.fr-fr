<properties 
	pageTitle="Utilisation des exemples de jeux de données dans Machine Learning Studio | Microsoft Azure" 
	description="Descriptions des jeux de données utilisés dans les exemples de modèles inclus dans ML Studio. Vous pouvez utiliser ces exemples de jeux de données pour vos expériences." 
	keywords="data sets,datasets,ml studio,sample data sets"
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
	ms.date="04/22/2015" 
	ms.author="garye"/>


# Utilisation des exemples de jeux de données dans Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Quand vous créez un espace de travail dans Azure Machine Learning, vous disposez par défaut d'un certain nombre d'exemples de jeux de données et d'expériences. Plusieurs de ces exemples de jeux de données sont utilisés par les exemples de modèles dans la [galerie de modèles Azure Machine Learning ](http://azure.microsoft.com/documentation/services/machine-learning/models/), tandis que d'autres illustrent les différents types de données utilisés dans l'apprentissage automatique.

Certains de ces jeux de données sont disponibles dans le stockage d'objets blob Azure. Les tableaux ci-dessous fournissent un lien direct vers ces jeux de données. Vous pouvez utiliser ces jeux de données dans vos expériences à l'aide du module [Lecteur][reader] : pour savoir comment accéder à un jeu de données, affichez les propriétés du module [Lecteur][reader] dans l'exemple d'expérience associé à ce jeu de données.

Les autres exemples de jeux de données sont répertoriés sous **Jeux de données enregistrés** dans la palette de modules à gauche du canevas de l'expérience quand vous ouvrez ou créez une expérience dans ML Studio. Vous pouvez utiliser un de ces jeux de données dans votre propre expérience en le faisant glisser sur le canevas de l'expérience.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Exemple de jeux de données associés aux modèles dans la galerie Machine Learning

Les jeux de données suivants sont utilisés par des exemples de modèles dans la [galerie de modèles Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/models/). Pour plus d'informations sur le modèle et l'expérience correspondante, suivez le lien dans le tableau pour voir les détails du modèle.

<table>

<tr>
  <th align=left>Exemple de modèle</th>
  <th align=left>Expérience associée</th>
  <th align=left>Nom du jeu de données</th>
  <th align=left>Description du jeu de données</th>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-color-quantization-using-k-means-clustering/">Quantification des couleurs à l'aide de l'algorithme des k-moyennes</a></td>
  <td valign=top>Exemple d'expérience - Compression d'image via les couleurs à l'aide de l'algorithme des k-moyennes - Développement</td>
  <td valign=top>Image RVB de Bill Gates</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-credit-risk-prediction/">Prédiction du risque de crédit</a></td>
  <td valign=top>Exemple d'expérience - Crédit allemand - Développement</td>
  <td valign=top>Jeu de données d'UCI pour une carte de crédit allemande</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td rowspan=4 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-crm-task/">Tâche CRM</a></td>
  <td rowspan=4 valign=top>Exemples d'expériences - CRM - Développement</td>
  <td valign=top>Jeu de données CRM partagé</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>
<tr ID=crm-appetency-labels-shared>
  <td valign=top>Étiquettes de l'appétence CRM partagées</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-churn-labels-shared>
  <td valign=top>Étiquettes de l'attrition CRM partagées</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-upselling-labels-shared>
  <td valign=top>Étiquettes de vente incitative CRM partagées</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-finding-similar-companies/">Recherche d'entreprises apparentées</a></td>
  <td valign=top>Exemple d'expérience - Création de clusters pour les sociétés du S&amp;P&#160;500 - Développement</td>
  <td valign=top>Jeu de données Wikipedia concernant le SP&#160;500</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td rowspan=3 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-flight-delay-prediction/">Prédiction des retards de vol</a></td>
  <td rowspan=3 valign=top>Exemple d'expérience - Prédiction de retard de vol - Développement</td>
  <td valign=top>Jeu de données des codes de l'aéroport</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Données relatives aux vols retardés</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Jeu de données météorologiques</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top> <a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-network-intrusion-detection/">Détection d'intrusions dans un réseau</a></td>
  <td valign=top>Exemple d'expérience - Détection d'intrusions dans un réseau - Développement</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-number-of-bike-rentals/">Prédiction du nombre de locations de vélos</a></td>
  <td valign=top>Exemple d'expérience - Prévision de la demande de vélos</td>
  <td valign=top>Jeu de données de location de vélos de l'UCI</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-student-performance/">Prédiction des performances des étudiants</a></td>
  <td valign=top>Exemple d'expérience - Performances des étudiants - Développement</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-sentiment-analysis/">Analyse de sentiments</a></td>
  <td valign=top>Exemple d'expérience - Classification de sentiment - Développement</td>
  <td valign=top>Critiques de livres d'Amazon</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

</table>



## Jeux de données utilisés dans des exemples d'expérience Machine Learning Studio

Les jeux de données suivants sont utilisés par des exemples d'expérience inclus dans [Machine Learning Studio](https://studio.azureml.net/Home).

Ces jeux de données sont répertoriés sous **Jeux de données enregistrés** dans la palette de modules à gauche du canevas de l'expérience quand vous ouvrez ou créez une expérience dans ML Studio. Vous pouvez utiliser un de ces jeux de données dans votre propre expérience en le faisant glisser sur le canevas de l'expérience.

Tous les exemples d'expériences sont répertoriés sous l'onglet **Exemples** de la page **Expériences** dans ML Studio. Pour créer une copie d'un exemple d'expérience en vue de le modifier, ouvrez l'expérience dans ML Studio et cliquez sur **Enregistrer sous**.


<table>

<tr>
  <th align=left>Nom du jeu de données</th>
  <th align=left>Exemple d'expérience</th>
  <th align=left>Description du jeu de données</th>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Caractéristiques du cancer du sein
  <td valign=top>Exemple d'expérience - Cancer du sein - Développement</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informations sur le cancer du sein</td>
  <td valign=top><i>identique</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>Exemple d'expérience - Marketing direct - Développement - Formation</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Titres de films IMDB</td>
  <td valign=top>Exemple d'expérience - Système de recommandation de film - Développement</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets de films</td>
  <td valign=top>Exemple d'expérience - Classement de tweets de films en fonction de leur popularité future - Développement</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>Exemple d'expérience - Catégorisation de nouvelles - Développement</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top><i>identique</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top><i>identique</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

</table>



## Divers exemples de jeux de données dans Machine Learning Studio

Les jeux de données suivants sont inclus dans [Machine Learning Studio](https://studio.azureml.net/Home) pour illustrer les différents types de données utilisés dans l'apprentissage automatique. Ils sont répertoriés sous **Jeux de données enregistrés** dans la palette de modules à gauche du canevas de l'expérience quand vous ouvrez ou créez une expérience dans ML Studio. Vous pouvez utiliser un de ces jeux de données dans votre propre expérience en le faisant glisser sur le canevas de l'expérience.

<table>
<tr>
  <th align=left>Nom du jeu de données</th>
  <th align=left>Description</th>
</tr>

<tr>
  <td valign=top>Jeu de données Adult Census Income Binary Classification</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur le prix des véhicules automobiles (brutes)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur le don de sang</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur le cancer du sein</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur la régression de l'efficacité énergétique</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr>
  <td valign=top>Performance concernant les vols à l'heure (brutes)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les feux de forêt</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur deux classes d'iris</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur la quantité de litres au&#160;100 pour différents véhicules automobiles</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>Jeu de données sur la classification binaire du diabète chez les indiens Pima</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les clients de restaurant</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les caractéristiques de restaurants</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Notations de restaurants</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>Jeu de données multiclasse de recuit d'acier</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>Données sur les télescopes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>


</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->