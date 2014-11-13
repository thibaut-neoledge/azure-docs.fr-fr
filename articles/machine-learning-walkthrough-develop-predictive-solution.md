<properties title="D&eacute;veloppement d'une solution pr&eacute;dictive avec Azure Machine Learning" pageTitle="D&eacute;veloppement d'une solution pr&eacute;dictive avec Machine Learning | Azure" description="Proc&eacute;dure pas &agrave; pas pour cr&eacute;er une exp&eacute;rience d'analyse pr&eacute;dictive dans Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />

# Développement d'une solution prédictive avec Azure Machine Learning

Supposons que vous deviez prédire le risque lié à l'octroi d'un crédit à un individu sur la base des informations fournies lors d'une demande de crédit.

Bien sûr, il s'agit d'un problème complexe, mais simplifions quelque peu les paramètres de la question et utilisons-la comme exemple de la façon dont vous pouvez utiliser Microsoft Azure Machine Learning avec ML Studio et le service API ML pour créer une solution d'analyse prédictive.

Au cours de cette procédure pas à pas, nous suivrons le processus de développement d'un modèle d'analyse prédictive dans ML Studio, puis nous le publierons sur le service API ML. Nous commencerons avec des données de risque de crédit disponibles publiquement, nous développerons un modèle prédictif à partir de ces données, puis nous publierons le modèle sous forme de service web pouvant être utilisé par d'autres personnes.

Nous suivrons les étapes suivantes :

1.  [Création d'un espace de travail ML][Création d'un espace de travail ML]
2.  [Téléchargement de données existantes][Téléchargement de données existantes]
3.  [Création d'une expérience][Création d'une expérience]
4.  [Formation et évaluation des modèles][Formation et évaluation des modèles]
5.  [Publication du service web][Publication du service web]
6.  [Accès au service web][Accès au service web]

Cette procédure pas à pas est basée sur une version simplifiée de l'
[Exemple d'expérience de prédiction du risque de crédit][Exemple d'expérience de prédiction du risque de crédit] inclus dans ML Studio.

  [Création d'un espace de travail ML]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Téléchargement de données existantes]: ../machine-learning-walkthrough-2-upload-data/
  [Création d'une expérience]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Formation et évaluation des modèles]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Publication du service web]: ../machine-learning-walkthrough-5-publish-web-service/
  [Accès au service web]: ../machine-learning-walkthrough-6-access-web-service/
  [Exemple d'expérience de prédiction du risque de crédit]: ../machine-learning-sample-credit-risk-prediction/
