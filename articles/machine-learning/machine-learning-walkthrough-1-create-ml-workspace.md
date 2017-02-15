---
title: "Étape 1 : création d’un espace de travail Machine Learning | Microsoft Docs"
description: "Étape 1 de la procédure pas à pas de développement d’une solution prédictive : apprenez à configurer un nouvel espace de travail Azure Machine Learning Studio."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: a9ebbbdc431a34553de04e920efbbc8c2496ce5f
ms.openlocfilehash: 116dbf0ee30497d82c984b10e61ae02301e820a4


---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>Étape 1 de la procédure pas à pas : création d’un espace de travail Machine Learning
Voici la première étape de la procédure pas à pas [Développement d'une solution d'analyse prédictive dans Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

1. **Créer un espace de travail Machine Learning**
2. [Télécharger des données existantes](machine-learning-walkthrough-2-upload-data.md)
3. [Créer une expérience](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Former et évaluer les modèles](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Déployer le service web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Accéder au service web](machine-learning-walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Pour utiliser Machine Learning Studio, vous devez disposer d’un espace de travail Microsoft Azure Machine Learning. Cet espace de travail contient les outils dont vous avez besoin pour créer, gérer et publier des expériences.  

<!--
## To create a workspace
1. Sign in to the [Azure classic portal](https://manage.windowsazure.com).
2. In the  Azure services panel, click **MACHINE LEARNING**.  
   ![Create workspace][1]
3. Click **CREATE AN ML WORKSPACE**.
4. On the **QUICK CREATE** page, enter your workspace information and then click **CREATE AN ML WORKSPACE**.
-->

L’administrateur de votre abonnement Azure doit créer l’espace de travail, puis vous ajouter en tant que propriétaire ou collaborateur. Pour plus d’informations, consultez [Créer et partager un espace de travail Azure Machine Learning](machine-learning-create-workspace.md).

Une fois votre espace de travail créé, ouvrez Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). S’il s’agit de votre seul espace de travail, Studio l’ouvre automatiquement. Dans le cas contraire, vous pouvez sélectionner l’espace de travail dans la barre d’outils dans le coin supérieur droit de la fenêtre.

![Sélection de l’espace de travail dans Studio][2]

> [!TIP]
> Si vous avez été désigné comme propriétaire de l’espace de travail, vous pouvez partager les expériences sur lesquelles vous travaillez en invitant d’autres personnes dans l’espace de travail. Pour cela, dans Machine Learning Studio, ouvrez la page **PARAMÈTRES** . Vous avez simplement besoin du compte Microsoft ou du compte professionnel de chaque utilisateur.
> 
> Dans la page **PARAMÈTRES**, cliquez sur **UTILISATEURS**, puis cliquez sur **INVITER PLUS D’UTILISATEURS** en bas de la fenêtre.
> 
> 

- - -
**Suivant : [Téléchargement de données existantes](machine-learning-walkthrough-2-upload-data.md)**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/machine-learning-walkthrough-1-create-ml-workspace/open-workspace.png



<!--HONumber=Feb17_HO2-->


