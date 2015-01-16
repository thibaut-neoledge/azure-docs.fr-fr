<properties title="What Is Azure Machine Learning Studio?" pageTitle="Azure Machine Learning Studio - De quoi s'agit-il ? | Azure" description="Vue d'ensemble de Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/11/2014" ms.author="garye" />

# Azure Machine Learning Studio - De quoi s'agit-il ?

Microsoft Azure Machine Learning Studio est un environnement de développement visuel et collaboratif qui vous permet de générer, de tester et de déployer des solutions d'analyse prédictive d'après vos données. Le service Machine Learning et l'environnement de développement sont basés sur le cloud, offrent des ressources de calcul et une flexibilité de la mémoire, et éliminent les problèmes d'installation et de configuration du fait que vous travaillez par l'intermédiaire de votre navigateur web. 

ML Studio : là où convergent votre connaissance des données, l'analyse prédictive, les ressources cloud et vos données !

## Espace de travail interactif ML Studio

Pour développer un modèle d'analyse prédictive, vous utilisez généralement des données d'une ou plusieurs sources que vous transformez et analysez par diverses manipulations et fonctions statistiques. Vous créez ensuite un ensemble de résultats. Le développement d'un tel modèle est un processus itératif : lorsque vous modifiez les diverses fonctions et leurs paramètres, vos résultats convergent jusqu'à ce que l'efficacité du modèle formé vous donne satisfaction.

**ML Studio** offre un espace de travail visuel et interactif qui vous permet de générer, de tester et d'affiner facilement un modèle d'analyse prédictive. Vous faites glisser des ***jeux de données*** et des ***modules d'analyse*** sur un ***canevas interactif***, en les connectant ensemble pour former une ***expérience***, que vous ***exécutez*** sous ML Studio. Pour affiner votre modèle, vous ***modifiez*** l'expérience, ***enregistrez*** une copie si vous le souhaitez et l'exécuter à nouveau. Lorsque vous êtes prêt, vous pouvez ***publier*** votre expérience en tant que ***service web*** afin que votre modèle soit accessible par d'autres utilisateurs. 

Aucune programmation n'est nécessaire : il suffit de visualiser la connexion des jeux de données et des modules pour construire votre modèle d'analyse prédictive.

![ML Studio Overview][ml-studio-overview]

## Prise en main de ML Studio

Lorsque vous ouvrez ML Studio pour la première fois, les onglets suivants se trouvent à gauche :

- **Accueil Studio** : un ensemble de liens vers la documentation et autres ressources
- **EXPÉRIENCES** : expériences qui ont été créées, exécutées et enregistrées comme brouillons. 
- **SERVICES WEB** : liste des expériences que vous avez publiées. 
- **PARAMÈTRES** : ensemble des paramètres que vous pouvez utiliser pour configurer votre compte et vos ressources. 

>[WACOM.NOTE] Lorsque vous construisez une expérience, une liste de travail des jeux de données et des modules disponibles	s'affiche à gauche du canevas. Il s'agit de la liste des composants que vous utilisez pour générer votre modèle.

## Composants d'une expérience

Une expérience se compose de jeux de données qui fournissent des données aux modules d'analyse que vous connectez ensemble pour construire un modèle d'analyse prédictive. En particulier, une expérience valide a les caractéristiques suivantes :

- Elle comporte au moins un jeu de données et un module. 
- Il est possible de connecter des jeux de données uniquement à des modules. 
- Les modules peuvent être connectés à des jeux de données ou à d'autres modules. 
- Tous les ports d'entrée des modules doivent comporter une connexion au flux de données. 
- Tous les paramètres obligatoires d'un module doivent être configurés. 

Pour obtenir un exemple de création d'une expérience simple, consultez la rubrique [Création d'une expérience simple dans Azure Machine Learning Studio](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-create-experiment/). 
Pour une description plus complète de la création d'une solution d'analyse prédictive, consultez la rubrique [Développement d'une solution prédictive avec Azure Machine Learning](http://azure.microsoft.com/fr-fr/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).

### Jeux de données

Un jeu de données représente des données téléchargées dans ML Studio de façon à les utiliser dans la procédure de modélisation.  ML Studio fournit divers exemples de jeux de données utilisables pour vos expériences ; vous pouvez télécharger vers le serveur d'autres jeux de données si vous en avez besoin.  Voici quelques exemples de jeux de données fournis :

- **Données sur la quantité de litres au 100 pour différents véhicules automobiles** : valeurs de quantité de litres au 100 pour des automobiles identifiées par leur nombre de cylindres, leur puissance, etc. 
- **Données sur le cancer du sein** : données de diagnostics sur le cancer du sein 
- **Données sur les feux de forêts** : tailles des incendies de forêts au nord-est du Portugal 

Lorsque vous créez une expérience, la liste des jeux de données utilisables se trouve à gauche du canevas. 

### Modules

Un module est un algorithme que vous appliquez à vos données.  ML Studio comporte divers modules, allant de fonctions de saisie des données à des procédures de formation, de notation et de validation.  Voici quelques exemples de modules fournis :

- **Conversion au format ARFF** : convertit un jeu de données sérialisé .NET au format ARFF 
- **Statistiques élémentaires** : calcule des statistiques élémentaires (ex. moyenne, écart-type, etc.). 
- **Régression linéaire** : crée en ligne un modèle de régression linéaire à gradient décroissant 
- **Noter le modèle** : note une classification formée ou un modèle de régression 

Lorsque vous créez une expérience, la liste des modules utilisables se trouve à gauche du canevas. 

Un module peut comporter un ensemble de paramètres utilisables pour configurer les algorithmes internes du module. Lorsque vous sélectionnez un module dans le canevas,  ses paramètres sont affichés dans le volet à droite du canevas. Vous pouvez modifier les paramètres figurant dans ce volet pour affiner votre modèle.


[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/context.jpg
