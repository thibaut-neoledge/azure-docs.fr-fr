<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Exemple sous Machine Learning : prédiction du risque de crédit | Azure" description="Un exemple d'expérience Azure Machine Learning permettant de développer un modèle de classification binaire qui prédit si un candidat présente un risque de crédit faible ou élevé." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Exemple sous Azure Machine Learning : prédiction du risque de crédit 

>[AZURE.NOTE]
>L'[exemple d'expérience] et l'[exemple de jeu de données] associés à ce modèle sont disponibles dans ML Studio. Pour plus d'informations, consultez la section ci-dessous.
[Exemple d'expérience]: #exemple-expérience
[Exemple de jeu de données]: #exemple-jeudedonnées

*Pour un didacticiel plus détaillé sur la création et l'utilisation d'une version simplifiée de cette expérience, consultez la page [Développement d'une solution prédictive avec Azure Machine Learning](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).*

L'objectif de cette expérience est de prévoir le risque d'un crédit en fonction des informations fournies sur une demande de crédit. La prévision est une valeur binaire : risque faible ou élevé. 


<!-- Removed until the Training and Scoring parts are fixed
This example is divided into 3 sample experiments:

- Development Experiment - for experimenting with different models
- Training Experiment - to train the one chosen model
- Scoring Experiment - to set up a web service using the trained model
-->

<!-- Removed because we added a section at the bottom describing the dataset
##Dataset Description

The experiment uses the UCI Statlog (German Credit Card) dataset which can be found here: 
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>. 
Nous utilisons le fichier german.data de ce site web.

Le jeu de données classe les gens, décrits par un ensemble d'attributs, par risque de crédit (faible ou élevé). Chaque exemple représente une personne. Il y a 20 caractéristiques, numériques et catégoriques, ainsi qu'une étiquette binaire (la valeur du risque de crédit). Les entrées de risque de crédit élevé ont l'étiquette = 2 ; un risque de crédit faible a l'étiquette = 1. Le coût d'une erreur de classement d'un exemple de risque faible en risque élevé est de 1, tandis que le coût d'une erreur de classement d'un risque élevé en risque faible est de 5.
-->

##Expérience de développement

Le jeu de données d'origine utilise un format séparé par des espaces. Nous avons transformé le jeu de données au format CSV et l'avons téléchargé dans ML Studio. Cette transformation peut être effectuée avec Powershell : 

	cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

Ou en utilisant la commande Unix sed :

	sed 's/ /,/g' german.data > german.csv

Nous débutons en utilisant l'**Éditeur de métadonnées** pour ajouter des noms de colonne afin de remplacer les noms par défaut du jeu de données par des noms plus pertinents tirés de la description du jeu de données sur le site UCI. Les nouveaux noms de colonne sont séparés par des virgules dans le champ **Nouveau nom de colonne** de l'**Éditeur de métadonnées**.

Ensuite, nous générons des jeux d'apprentissage et de test qui seront utilisés pour élaborer le modèle de prédiction des risques. Nous divisons le jeu de données d'origine en jeux d'apprentissage et de test de la même taille grâce au module **Fractionner** avec le paramètre **Ratio des premières lignes de sortie vers l'entrée** défini sur 0,5.
 
Le coût d'une erreur de classement d'un risque élevé en risque faible étant 5 fois plus important que celui d'une erreur de classement d'un risque faible en risque élevé, nous générons un nouveau jeu de données reflétant cette fonction de coût. Dans le nouveau jeu de données, chaque exemple de risque élevé est répliqué 5 fois, tandis que les exemples de risque faible sont conservés tels quels. La division des jeux de données d'apprentissage et de test est effectuée avant cette réplication afin d'éviter de retrouver les mêmes exemples dans les deux jeux. 

Cette réplication est effectuée en suivant le code R exécuté à l'aide du module **Exécuter un script R** :

	dataset1 <- maml.mapInputPort(1)
	data.set<-dataset1[dataset1[,21]==1,]
	pos<-dataset1[dataset1[,21]==2,]
	for (i in 1:5) data.set<-rbind(data.set,pos)
	maml.mapOutputPort("data.set")

Dans cette expérience, nous comparons deux méthodes de génération de modèles : l'apprentissage sur le jeu de données d'origine et l'apprentissage sur le jeu de données répliqué. Pour ces deux méthodes, nous testons le jeu de test avec réplication afin d'être en alignement avec la fonction de coût du problème. Le dernier flux de travail de fractionnement et de réplication est décrit ci-après. Dans ce flux de travail, la sortie gauche du module **Fractionner** est un jeu d'apprentissage et celle de droite est un jeu de test. Notez que le jeu d'apprentissage est ensuite utilisé avec et sans le module **Exécuter un script R**, c'est-à-dire avec et sans réplication.

![Splitting training and test data][screen1]
 
Outre la vérification de l'effet de la réplication des exemples dans le jeu d'apprentissage, nous comparons également les performances de deux algorithmes : machine à vecteurs de support (Support Vector Machine, SVM) et arbre de décision optimisé. Cela nous permet de générer efficacement 4 modèles :

- SVM, formé avec les données d'origine
- SVM, formé avec les données répliquées
- Arbre de décision optimisé, formé avec les données d'origine
- Arbre de décision optimisé, formé avec les données répliquées

Les arbres de décision optimisés fonctionnent bien avec tout type de caractéristique. Toutefois, le module SVM générant un classifieur linéaire, le modèle qu'il génère obtient la meilleure erreur de test quand toutes les caractéristiques sont à la même échelle. Pour convertir toutes les caractéristiques à la même échelle, nous utilisons le module **Transformer les données par mise à l'échelle** avec une transformation tanh. Cette transformation change toutes les caractéristiques numériques à l'échelle [0,1]. Notez que les caractéristiques de chaîne sont converties en caractéristiques catégoriques par le module SVM, puis en caractéristiques 0/1 binaires. Il n'est donc pas nécessaire de les transformer manuellement. 

Nous initialisons l'algorithme d'apprentissage à l'aide du module **Machine à vecteurs de support à deux classes** ou du module **Arbre de décision optimisé à deux classes**, puis nous utilisons le module **Former le modèle** pour créer le modèle. Ces modèles sont utilisés par les modules **Noter le modèle** pour évaluer des exemples de test. Un exemple de flux de travail combinant ces modules et utilisant SVM et le jeu d'apprentissage répliqué est décrit ci-dessous. Notez que le module **Former le modèle** est connecté au jeu d'apprentissage, tandis que le module **Noter le modèle** est connecté au jeu de test.

![Training and scoring a model][screen2]

Lors de l'évaluation de l'expérience, nous calculons l'exactitude de chacun des 4 modèles ci-dessus. Pour ce faire, nous utilisons le module **Évaluer le modèle**. Remarque : ce module ne calcule l'exactitude que lorsque tous les exemples ont le même coût d'erreur de classification. Cependant, puisque nous avons répliqué les exemples positifs, l'exactitude calculée par **Évaluer le modèle** est sensible au coût et est la suivante : 

![Accuracy computation][formula]

où *n+* et *n-* représentent le nombre d'exemples positifs et négatifs du jeu de données d'origine, et où *e+* et *e-* représentent le nombre d'exemples positifs et négatifs mal classés dans le jeu de données d'origine.

Le module **Évaluer le modèle** compare 2 modèles évalués. Nous en utilisons donc **un** pour comparer les deux modèles SVM et un autre pour comparer les deux modèles d'arbre de décision optimisé. Nous les combinerons dans un tableau pour afficher les 4 résultats. **Évaluer le modèle** produit un tableau avec une seule ligne contenant différentes mesures. Nous utilisons le module **Ajouter des lignes** pour combiner tous les résultats dans un seul tableau. Nous annotons ensuite le tableau avec les exactitudes des 4 modules utilisant un script R dans le module **Exécuter le script R**, dans lequel nous saisissons manuellement le nom des lignes du tableau final. Enfin, nous supprimons les colonnes contenant des mesures non pertinentes à l'aide du module **Colonnes de projet**. 

Les résultats finaux de l'expérience, obtenus en faisant un clic droit sur la sortie **Jeu de données des résultats** de **Colonnes de projet** sont :

![Results][results] 

où la première colonne est le nom de l'algorithme d'apprentissage automatique utilisé pour générer un modèle, la deuxième colonne indique le type du jeu d'apprentissage et la troisième représente l'exactitude en fonction du coût. Dans cette expérience, le modèle SVM, qui fonctionne avec le jeu de données d'apprentissage répliqué, fournit la plus grande exactitude.

<!-- Removed until the Training and Scoring parts are fixed
##Training Experiment

The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model. 

Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.

![Azure storage parameters][screen3] 

##Scoring Experiment

The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model. 

After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**. 

After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.

When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.

![Web service ready for production][screen4] 
-->

## Exemple d'expérience

L'exemple d'expérience suivant associé à ce modèle est disponible dans ML Studio dans la rubrique **EXPÉRIENCES** de l'onglet **EXEMPLES**.

> **Exemple d'expérience - Crédit allemand - Développement**


## Exemple de jeu de données

L'exemple de jeu de données suivant utilisé dans cette expérience est disponible dans ML Studio dans la palette de module sous **Jeux de données enregistrés**.

###Jeu de données d'UCI pour une carte de crédit allemande
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]



[screen1]:./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
[screen2]:./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
[formula]:./media/machine-learning-sample-credit-risk-prediction/formula.jpg
[results]:./media/machine-learning-sample-credit-risk-prediction/results.jpg
[screen3]:./media/machine-learning-sample-credit-risk-prediction/screen3.jpg
[screen4]:./media/machine-learning-sample-credit-risk-prediction/screen4.jpg
