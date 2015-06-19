<properties 
    pageTitle="Azure Machine Learning - De quoi s’agit-il ? | Microsoft Azure" 
    description="Vue d’ensemble du service Azure Machine Learning." 
    services="machine-learning" 
    documentationCenter="" 
    authors="tedway" 
    manager="neerajkh" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/22/2015" 
    ms.author="tedway;olgali"/>


# L’apprentissage automatique - De quoi s’agit-il ?
L’apprentissage automatique nous entoure. Lorsque vous faites vos achats en ligne, l’apprentissage automatique permet de recommander d’autres produits, en fonction de ce que vous avez acheté. Lorsque vous utilisez votre carte de crédit, l’apprentissage automatique aide les banques à détecter des fraudes et à vous avertir si la transaction semble suspecte. L’apprentissage automatique correspond au processus de création de modèles afin d’apprendre des données existantes et d’effectuer une analyse prédictive des futures données.

## Azure Machine Learning - De quoi s’agit-il ?
Azure Machine Learning est un puissant service d’analyse prédictive sur le cloud qui permet de créer rapidement des solutions d’analyse. Nous avons créé Azure Machine Learning afin de démocratiser l’apprentissage automatique : nous avons éliminé les plus gros travaux impliqués dans la création et le déploiement de la technologie d’apprentissage automatique, pour la rendre accessible à tout le monde. C’est un service entièrement géré, ce qui signifie que vous n’avez pas besoin d’acheter de matériel ni de gérer manuellement des machines virtuelles.

Vous pouvez utiliser l’outil sur navigateur [Machine Learning Studio](machine-learning-what-is-ml-studio.md) pour créer et automatiser rapidement les flux de travail d’apprentissage automatique. Vous pouvez glisser-déposer des centaines de [bibliothèques d’apprentissage automatique](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) existantes et lancer vos solutions d’analyse prédictive, puis ajouter vos propres scripts [R](machine-learning-r-quickstart.md) et [Python](machine-learning-execute-python-scripts.md) afin de les étendre. Studio fonctionne dans n’importe quel navigateur et vous permet de développer et d’itérer des solutions rapidement. ![Expériences d’analyse prédictive dans le cloud avec Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

Vous pouvez facilement découvrir et créer des [services Web](machine-learning-publish-a-machine-learning-web-service.md), [former et recycler vos modèles via des API](machine-learning-retrain-models-programmatically.md), [gérer les points de terminaison](machine-learning-create-endpoint.md) et [faire évoluer les services Web](machine-learning-scaling-endpoints.md) par client et configurer des diagnostics pour la surveillance et le débogage des services. Les nouvelles fonctionnalités sont les suivantes :

- La possibilité de créer un module R personnalisé configurable, d’incorporer vos propres scripts R de formation/prédiction et d’ajouter des scripts Python à l’aide d’un vaste écosystème de bibliothèques numpy, scipy, pandas ou scikit-learn. Vous pouvez désormais utiliser des téraoctets de données pour l’apprentissage à l’aide d’[Apprendre avec des comptes][learning-with-counts], utiliser PCA ou SVM à une classe pour la détection des anomalies, ainsi que modifier, filtrer et nettoyer facilement les données à l’aide de l’interface familière de SQLite. 
- La [galerie de la communauté Machine Learning](machine-learning-gallery-how-to-use-contribute-publish.md) vous permet de découvrir et d’utiliser des expériences intéressantes créées par d’autres utilisateurs. Vous pouvez poser des questions ou publier des commentaires sur les expériences de la [galerie](http://gallery.azureml.net) ou publier les vôtres. Vous pouvez partager des liens vers des expériences intéressantes via des canaux sociaux tels que LinkedIn et Twitter. La galerie est un excellent moyen pour les utilisateurs de prendre en main Azure Machine Learning et d’apprendre d’autres personnes de la communauté. ![Essayez des exemples d’expérimentation prédictive ou proposez le vôtre dans la galerie Azure Machine Learning](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)
- Vous pouvez acheter des [applications Marketplace](https://datamarket.azure.com/browse?query=machine+learning) via un abonnement Azure et utiliser des services Web terminés pour les recommandations, l’analyse de texte et la détection d’anomalies directement à partir d’Azure Marketplace. 
- Un guide pas à pas du parcours de la science des données, des données brutes à un service Web utilisable pour faciliter la science des données sur le cloud. Nous avons ajouté la possibilité d’utiliser des outils courants tels qu’iPython Notebook et Python Tools pour Visual Studio avec Azure Machine Learning.

## Commencer dès maintenant
Vous pouvez apprendre les principes fondamentaux de l’analyse prédictive et de l’apprentissage automatique à l’aide d’un [didacticiel](machine-learning-create-experiment.md) et avec la [génération à partir d’exemples](machine-learning-sample-experiments.md). Aucun abonnement Azure ou carte de crédit n’est nécessaire pour commencer à utiliser Machine Learning lorsque effectuez des expériences dans Studio.


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!--HONumber=54--> 