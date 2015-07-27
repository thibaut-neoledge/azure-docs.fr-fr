<properties
	pageTitle="Solution prédictive des risques de crédit avec Machine Learning| Microsoft Azure"
	description="Guide pas à pas détaillé indiquant comment créer une solution d'analyse prédictive pour l'évaluation des risques de crédit dans Azure Machine Learning Studio."
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
	ms.topic="get-started-article" 
	ms.date="07/10/2015"
	ms.author="garye"/>


# Guide pas à pas : développer une solution d'analyse prédictive pour l'évaluation des risques de crédit dans Azure Machine Learning

Supposons que vous deviez prédire le risque lié à l'octroi d'un crédit à un individu sur la base des informations fournies lors d'une demande de crédit.

L'évaluation des risques de crédit est un problème complexe, bien sûr, mais nous allons simplifier un peu les paramètres de la question. Ensuite, nous pouvons l'utiliser comme exemple de la façon dont Microsoft Azure Machine Learning avec Machine Learning Studio et le service web Machine Learning peuvent vous aider à créer une solution d'analyse prédictive.

Dans ce guide pas à pas détaillé, nous allons suivre le processus de développement d'un modèle d'analyse prédictive dans Machine Learning Studio, puis nous le publierons en tant que service web Azure Machine Learning. Nous allons commencer avec des données de risque de crédit disponibles publiquement, nous développerons et formerons un modèle prédictif à partir de ces données, puis nous publierons le modèle sous forme de service web pouvant être utilisé par d'autres personnes pour l'évaluation des risques de crédit.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ouvrez Machine Learning Studio : [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Pour en savoir plus sur la prise en main de Machine Learning Studio, consultez la [page d'accueil de Microsoft Azure Machine Learning Studio](https://studio.azureml.net/).

Pour créer une solution d'évaluation des risques de crédit, nous allons suivre ces étapes :

1.	[Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3.	[Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Publier le service web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

Cette procédure pas à pas est basée sur une version simplifiée de l'[Exemple d'expérience de prédiction du risque de crédit](../machine-learning-sample-credit-risk-prediction.md) inclus dans Machine Learning Studio.
 

<!---HONumber=July15_HO3-->