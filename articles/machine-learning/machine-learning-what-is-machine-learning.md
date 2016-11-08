---
title: Qu’est-ce que l’apprentissage automatique (Machine Learning) sur Azure ? | Microsoft Docs
description: Explique les concepts de base de l’apprentissage automatique dans le cloud, décrit à quoi il sert, et en définit les principaux termes.
keywords: qu’est-ce que l’apprentissage automatique,termes de l’apprentissage automatique,prédictive,qu’est-ce que l’analyse prédictive,rendre opérationnel
services: machine-learning
documentationcenter: ''
author: cjgronlund
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: cgronlun;tedway;olgali

---
# <a name="introduction-to-machine-learning-in-the-cloud"></a>Présentation de l’apprentissage automatique dans le cloud
## <a name="what-is-machine-learning?"></a>L’apprentissage automatique - De quoi s’agit-il ?
L’apprentissage automatique (Machine Learning) utilise des ordinateurs pour exécuter des modèles prédictifs qui apprennent à partir de données existantes afin de prévoir les tendances, résultats et comportements futurs.  

Ces prévisions ou prédictions générées à partir de l’apprentissage automatique peuvent rendre les applications et les appareils plus intelligents. Lorsque vous faites vos achats en ligne, l’apprentissage automatique permet de recommander d’autres produits que vous êtes susceptible d’aimer, en fonction de ce que vous avez acheté. Lorsque vous utilisez votre carte de crédit, l’apprentissage automatique compare la transaction à une base de données de transactions et aide la banque à détecter des fraudes. Lorsque votre robot aspirateur nettoie une pièce, l’apprentissage automatique l’aide à déterminer si le travail est terminé.

Pour une vue d’ensemble rapide, visionnez la série de vidéos [Science des données pour les débutants](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md). Sans utiliser de jargon ou de mathématiques, la série Science des données pour les débutants présente l’apprentissage automatique et décrit un modèle prédictif simple.

## <a name="what-is-machine-learning-in-the-microsoft-azure-cloud?"></a>Présentation de l’apprentissage automatique dans le cloud Microsoft Azure
Azure Machine Learning est un puissant service d’analyse prédictive sur le cloud qui permet de créer et de déployer rapidement des modèles prédictifs sous forme de solutions d’analyse. Lorsque vous utilisez le cloud Azure pour exécuter des expériences Machine Learning ou créer des solutions, vous n’êtes pas obligé d’acheter du matériel coûteux ou une infrastructure onéreuse.

![L’apprentissage automatique - De quoi s’agit-il ? Flux de travail de base pour configurer l'analyse prédictive dans Azure Machine Learning.](./media/machine-learning-what-is-machine-learning/machine-learning-service-parts-and-workflow.png)

Azure Machine Learning fournit non seulement des outils pour modéliser des analyses prédictives, mais également un service entièrement pris en charge, que vous pouvez utiliser pour déployer vos modèles prédictifs sous la forme de services web prêts à l’emploi. Azure Machine Learning fournit des outils pour créer des solutions complètes d’analyse prédictive sur le cloud : créer, tester, mettre en service et gérer rapidement des modèles prédictifs.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="what-is-predictive-analytics?"></a>Qu’est-ce qu’une analyse prédictive ?
L’analyse prédictive utilise différentes formules mathématiques, appelées algorithmes, qui analysent les données historiques ou actuelles des schémas ou des tendances afin de prévoir des événements futurs.

Azure Machine Learning est particulièrement efficace pour effectuer des analyses prédictives : vous pouvez utiliser une bibliothèque d’algorithmes prêts à l’emploi, créer des modèles sur un PC connecté à Internet et déployer rapidement votre solution prédictive. Pour obtenir des résultats rapidement, commencez avec des solutions et des exemples prêts à l’emploi dans la [galerie Cortana Intelligence](http://gallery.cortanaintelligence.com/).

## <a name="build-complete-machine-learning-solutions-in-the-cloud"></a>Génération de solutions complètes d’apprentissage automatique sur le cloud
Azure Machine Learning propose tout ce dont vous avez besoin pour créer des solutions d’analyse prédictive dans le cloud : une bibliothèque d’algorithmes volumineuse, un environnement pour générer des modèles et un outil pour déployer facilement votre modèle sous la forme d’un service web.

### <a name="machine-learning-studio:-create-predictive-models"></a>Machine Learning Studio : création de modèles prédictifs
Dans [Machine Learning Studio](machine-learning-what-is-ml-studio.md), vous pouvez créer rapidement des modèles prédictifs, en déplaçant les modules et en les connectant les uns aux autres. Vous pouvez essayer différentes combinaisons et les [tester gratuitement](https://studio.azureml.net/?selectAccess=true&o=2).

![Qu’est-ce que l’analyse prédictive : exemple d’une expérience d’analyse prédictive dans Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* Dans la [galerie Cortana Intelligence](machine-learning-gallery-how-to-use-contribute-publish.md), vous pouvez tester les solutions d’analyse créées par d’autres personnes ou créer la vôtre. Publiez vos questions ou commentaires sur vos expériences à l’intention de la communauté, ou partagez des liens vers des expériences via les réseaux sociaux tels que LinkedIn et Twitter.
* Utilisez une volumineuse bibliothèque d' [algorithmes et de modules Machine Learning](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) dans Machine Learning Studio afin de lancer vos modèles prédictifs. Faites votre choix parmi les exemples d’expérience, les packages R et Python, ainsi que les meilleurs algorithmes Microsoft tels que Xbox et Bing. Développez les modules Studio avec vos propres scripts personnalisés [R](machine-learning-r-quickstart.md) et [Python](machine-learning-execute-python-scripts.md).

    ![Essayez des expériences prédictives ou proposez la vôtre dans la galerie Azure Cortana Intelligence](./media/machine-learning-what-is-machine-learning/machine-learning-cortana-intelligence-gallery.png)

### <a name="operationalize-predictive-analytics-solutions:-purchase-web-services-or-publish-your-own"></a>Mise en service de solutions d’analyse prédictive : achat de services web ou publication de vos propres services web
* Achetez des services web prêts à l'emploi à partir de [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning), comme des recommandations, des analyses de texte ou une détection d'anomalie.
* Mettez en service vos modèles d’analyse prédictive :
  
  * [Déployez des services web](machine-learning-publish-a-machine-learning-web-service.md)
  * [Formez et reformez des modèles via les API](machine-learning-retrain-models-programmatically.md)
  * [Gérez les points de terminaison des services web](machine-learning-create-endpoint.md)
  * [Mettez à l’échelle un service web](machine-learning-scaling-webservice.md)
  * [Utilisez des services web](machine-learning-consume-web-services.md)

## <a name="key-machine-learning-terms-and-concepts"></a>Concepts clé et terminologie de l’apprentissage automatique
Les termes de l’apprentissage automatique peuvent prêter à confusion. Vous trouverez ici la définition des termes clés pour vous aider. Utilisez les commentaires suivants pour nous indiquer ceux dont vous souhaiteriez lire la définition.

### <a name="data-exploration,-descriptive-analytics,-and-predictive-analytics"></a>Exploration des données, analyse descriptive et analyse prédictive
**exploration des données** désigne le processus de collecte des informations sur un jeu volumineux de données, souvent non structurées, afin d’y trouver des caractéristiques utiles pour une analyse ciblée. **Data mining** fait référence à l'exploration automatisée des données.

**analyse descriptive** est le processus d'analyse d'un jeu de données afin de synthétiser ce qui s'est passé. La majeure partie des analyses commerciales, comme les rapports de ventes, les mesures du web et l’analyse de réseaux sociaux, est descriptive.

**analyse prédictive** est le processus de création de modèles à partir de données historiques ou actuelles afin de prévoir les futurs résultats.

### <a name="supervised-and-unsupervised-learning"></a>Apprentissage supervisé et non supervisé
 **apprentissage supervisé** sont formés avec des données étiquetées, c'est-à-dire des données composées d'exemples de réponses souhaitées. Par exemple, un modèle qui identifie l’utilisation frauduleuse d’une carte de crédit exploite un jeu de données qui contient des points de données indiquant des utilisations frauduleuses et valides connues. La plupart des apprentissages automatiques sont supervisés.

 L’**apprentissage non supervisé** est utilisé sur les données sans étiquette. L’objectif est alors de trouver des relations entre les données. Par exemple, vous voulez trouver des groupes de données démographiques de clients avec des habitudes d’achat similaires.

### <a name="model-training-and-evaluation"></a>Formation et évaluation du modèle
Un modèle d’apprentissage automatique est une abstraction de la question à laquelle vous essayez de répondre ou le résultat que vous souhaitez prédire. Les modèles sont formés et évalués à partir de données existantes.

#### <a name="training-data"></a>Données de formation
Lorsque vous formez un modèle, vous utilisez un jeu de données connu, puis vous adaptez le modèle en fonction des caractéristiques des données pour obtenir la réponse la plus précise. Dans Azure Machine Learning, un modèle est créé à partir d’un module d’algorithme qui traite les données d’apprentissage et à partir de modules fonctionnels, tels qu’un module d’évaluation.

Dans le cadre d’un apprentissage supervisé, si vous formez un modèle de détection des fraudes, vous utilisez un ensemble de transactions étiquetées comme frauduleuses ou valides. Vous fractionnez votre jeu de données de manière aléatoire et vous en utilisez une partie pour former le modèle et l’autre pour tester ou évaluer le modèle.

#### <a name="evaluation-data"></a>Données d’évaluation
Une fois que votre modèle est formé, évaluez-le en utilisant les autres données de test. Vous utilisez des données dont vous connaissez déjà les résultats afin de pouvoir déterminer si votre modèle prédit correctement.

## <a name="other-common-machine-learning-terms"></a>Autres termes courants relatifs à l’apprentissage automatique
* **Algorithme**: ensemble de règles utilisées pour résoudre les problèmes de traitement des données, de calcul mathématique ou de déduction automatisée.
* **Détection d’anomalies**: modèle qui signale les valeurs ou les événements hors normes et qui vous permet d’identifier les problèmes. Par exemple, la détection de fraudes à la carte de crédit consiste à rechercher les achats inhabituels.
* **Données catégorielles**: données organisées en catégories et pouvant être divisées en groupes. Par exemple, un jeu de données catégorielles relatif à des véhicules peut spécifier l’année, la marque, le modèle et le prix.
* **Classification**: modèle d'organisation des points de données en catégories basées sur un jeu de données dont les groupes de catégorie sont déjà connus.
* **Conception de caractéristiques**: processus d'extraction ou de sélection des caractéristiques liées à un jeu de données afin d'améliorer ce dernier et les résultats. Par exemple, les données relatives aux prix des billets d’avion peuvent être améliorées par jour de la semaine et par période de vacance. Consultez la page [Ingénierie et sélection de caractéristiques dans Azure Machine Learning](machine-learning-feature-selection-and-engineering.md).
* **Module**: élément fonctionnel d’un modèle Machine Learning Studio, comme le module Entrer des données, qui permet d’entrer et de modifier de petits jeux de données. Un algorithme est également un type de module dans Machine Learning Studio.
* **Modèle**: dans le cadre d’un apprentissage supervisé, un modèle est le résultat d’une expérience d’apprentissage automatique, comprenant des données de formation, un module d’algorithme et des modules fonctionnels, comme un module d’évaluation.
* **Données numériques**: données qui ont une signification sous forme de mesures (données continues) ou de compteurs (données discrètes). Également appelées *données quantitatives*.
* **Partition**: méthode permettant de diviser les données en échantillons. Consultez la page [Partition et échantillon](https://msdn.microsoft.com/library/azure/dn905960.aspx) pour plus d'informations.
* **Prédiction**: prévision d'une valeur ou de plusieurs valeurs, à partir d'un modèle d'apprentissage automatique. On rencontre parfois le terme « note prédite ». Toutefois, les notes prédites ne sont pas le résultat final d’un modèle. L’évaluation du modèle suit la note.
* **Régression**: modèle permettant de prédire une valeur en fonction de variables indépendantes, comme la prédiction du prix d’une voiture en fonction de son année et de sa marque.
* **Note**: valeur prédite générée à partir d'un modèle formé de régression ou de classification, à l'aide du module [Noter le modèle](https://msdn.microsoft.com/library/azure/dn905995.aspx) dans Machine Learning Studio. Les modèles de classification retournent également une note pour la probabilité de la valeur prédite. Une fois que vous avez généré les notes à partir d'un modèle, vous pouvez évaluer la précision du modèle à l'aide du module [Évaluer le modèle](https://msdn.microsoft.com/library/azure/dn905915.aspx).
* **Échantillon**: partie d'un jeu de données destinée à être représentative de l'ensemble. Les échantillons peuvent être sélectionnés de manière aléatoire ou sur la base de fonctionnalités spécifiques du jeu de données.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez apprendre les principes fondamentaux de l’analyse prédictive et de l’apprentissage automatique à l’aide d’un [didacticiel](machine-learning-create-experiment.md) et avec la [génération à partir d’exemples](machine-learning-sample-experiments.md).  

<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/



<!--HONumber=Oct16_HO2-->


