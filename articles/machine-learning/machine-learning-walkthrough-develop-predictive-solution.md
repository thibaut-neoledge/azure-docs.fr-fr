---
title: "Solution prédictive des risques de crédit avec Machine Learning| Microsoft Docs"
description: "Guide pas à pas détaillé indiquant comment créer une solution d&quot;analyse prédictive pour l&quot;évaluation des risques de crédit dans Azure Machine Learning Studio."
keywords: "risque de crédit, solution d’analyse prédictive, évaluation des risques"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>Guide pas à pas : développer une solution d'analyse prédictive pour l'évaluation des risques de crédit dans Azure Machine Learning

Dans cette procédure détaillée, nous étudierons de manière approfondie le processus de développement d’une solution dans Machine Learning Studio. Nous allons développer un modèle de prévision analytique dans Machine Learning Studio, puis le déployer dans le service web Azure Machine Learning, où le modèle peut effectuer des prévisions à l’aide de nouvelles données. 

> [!TIP]
> Cette procédure détaillée suppose que vous avez utilisé Machine Learning Studio au moins une fois auparavant et que vous comprenez les concepts de Machine Learning, mais que vous n’êtes pas un expert de ces domaines.
> 
>Si vous n’avez jamais utilisé **Azure Machine Learning Studio** auparavant, commencez par le didacticiel [Création de votre première expérience de science des données dans Azure Machine Learning Studio](machine-learning-create-experiment.md). Ce didacticiel vous permet de bénéficier d’un premier aperçu de Machine Learning Studio, vous montre les principes fondamentaux de glisser-déplacer des modules vers votre expérience, et vous explique comment les connecter, réaliser votre expérience et étudier les résultats.
>
>Si vous ne connaissez pas le machine learning, la série de vidéos [Science des données pour les débutants](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) peut être un bon point de départ. Cette série de vidéos constitue une excellente introduction au machine learning, qui utilise le langage et les concepts courants.
> 

## <a name="the-problem"></a>Le problème

Supposons que vous deviez prédire le risque lié à l'octroi d'un crédit à un individu sur la base des informations fournies lors d'une demande de crédit.  

L’évaluation des risques de crédit est un problème complexe, bien sûr, mais nous allons simplifier un peu les paramètres de la question. Ensuite, nous pourrons l’utiliser comme exemple de la façon dont Microsoft Azure Machine Learning avec Machine Learning Studio et le service web Machine Learning peuvent vous aider à créer une solution d’analyse prédictive.  

## <a name="the-solution"></a>La solution

Dans cette procédure détaillée, nous allons commencer avec des données de risque de crédit disponibles publiquement, nous développerons et formerons un modèle prédictif à partir de ces données, puis nous déploierons le modèle sous forme de service web pouvant être utilisé par d’autres personnes pour l’évaluation des risques de crédit.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Pour créer une solution d'évaluation des risques de crédit, nous allons suivre ces étapes :  

1. [Créer un espace de travail Machine Learning](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3. [Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

Cette procédure pas à pas repose sur une version simplifiée de l’exemple d’expérience [Binary Classification: Credit risk prediction](http://go.microsoft.com/fwlink/?LinkID=525270) (Classification binaire : prédiction du risque de crédit) inclus dans la [galerie Cortana Intelligence](http://gallery.cortanaintelligence.com/).


> [!TIP]
> Pour télécharger et imprimer un diagramme offrant une vue d’ensemble des fonctionnalités de Machine Learning Studio, consultez [Diagramme de vue d’ensemble des fonctionnalités d’Azure Machine Learning Studio](machine-learning-studio-overview-diagram.md).
> 
> 



<!--HONumber=Dec16_HO3-->


