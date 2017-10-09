---
title: "Structurer des projets avec un modèle de processus TDSP (Team Data Science Process) | Microsoft Docs"
description: "Guide pratique pour instancier des modèles de processus TDSP (Team Data Science Process) dans Azure ML qui structurent des projets à des fins de collaboration."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e03b6bcb1bc5a617234c7801f22f8207a9f5a4e
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="structure-projects-with-team-data-science-process-template"></a>Structurer des projets avec un modèle de processus TDSP (Team Data Science Process)

Ce document fournit des instructions sur la création de projets de science des données dans Azure Machine Learning avec des modèles de processus TDSP (Team Data Science Process) qui structurent des projets à des fins de collaboration et de reproductibilité. 


## <a name="what-is-team-data-science-process"></a>Qu’est-ce que le processus TDSP (Team Data Science Process) ?
Le processus TDSP (Team Data Science Process) est un processus de science des données agile et itératif qui permet d’exécuter et de proposer des solutions d’analytique avancée. Il est conçu pour améliorer la collaboration et l’efficacité des équipes de science des données au sein des entreprises. Il tient ces objectifs avec quatre composants clés :

1. Une définition du [cycle de vie de la science des données](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) standard.
2. Une structure de projet standardisée, des [modèles de documentation et de rapports de projet](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
3. Une infrastructure et des ressources pour l’exécution du projet, comme une infrastructure de calcul et de stockage, ainsi que des dépôts de code.
4. Des [outils et utilitaires](https://github.com/Azure/Azure-TDSP-Utilities) pour les tâches du projet de science des données, comme la gestion de versions et la revue de code, l’exploration et la modélisation des données, ainsi que la planification du travail.

Pour obtenir une description plus complète du processus TDSP, consultez [Vue d’ensemble du processus TDSP (Team Data Science Process)](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md).

## <a name="why-should-you-use-tdsp-structure-and-templates"></a>Pourquoi utiliser une structure et des modèles de processus TDSP ?
La normalisation de la structure, du cycle de vie et de la documentation des projets de science des données est primordiale pour favoriser une collaboration efficace au sein des équipes de science des données. Créer des projets Azure Machine Learning avec le modèle de processus TDSP permet d’offrir un framework pour un travail d’équipe coordonné.

Nous avions précédemment publié un [dépôt GitHub pour la structure et les modèles du projet TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) dans le but d’atteindre ces objectifs. Mais il n’était pas possible, avant aujourd’hui, d’instancier la structure et les modèles TDSP dans un outil de science des données. Il est désormais possible de créer un projet Azure Machine Learning qui instancie la structure et les modèles de documentation TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>Points à noter *avant* de créer un projet
Voici les points à noter ou à passer en revue *avant* de créer un projet :
* [Modèle](https://aka.ms/tdspamlgithubrepo) TDSP Azure Machine Learning.
* La taille du contenu (autre que celui du dossier « docs ») doit être inférieure à 25 Mo. Consultez la **remarque** ci-dessous.
* Le dossier sample\_data sert uniquement aux petits fichiers de données (de taille inférieure à 5 Mo) avec lesquels vous pouvez tester votre code ou effectuer les premières phases du développement.
* Le stockage de fichiers de type Office Word, PowerPoint, etc. peut augmenter sensiblement la taille du dossier « docs ». Nous vous conseillons de trouver un Wiki collaboratif, une ressource [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) ou toute autre ressource collaborative pour stocker ces fichiers.
* Pour gérer des fichiers et sorties de grande taille dans Azure Machine Learning, lisez [ceci](http://aka.ms/aml-largefiles).

> [!NOTE]
> Vérifiez qu’en dehors du fichier readme.md, tout le contenu lié à la documentation (texte, fichiers Markdown, images, autres fichiers de document) qui n’est PAS utilisé pendant l’exécution du projet, réside dans le dossier nommé « docs » (tout en minuscules). Il s’agit d’un dossier spécial ignoré par l’exécution Azure Machine Learning afin que le contenu de ce dossier ne soit pas copié dans la cible de calcul inutilement. Les objets inclus dans ce dossier ne sont également pas comptabilisés dans la limite de 25 Mo pour la taille du projet, si bien que vous pouvez y stocker les fichiers image volumineux utiles à votre documentation, par exemple. Ils sont quand même suivis par Git par le biais de l’historique des exécutions. 

## <a name="instantiating-tdsp-structure-and-templates-from-the-azure-machine-learning-template-gallery"></a>Instanciation de la structure et des modèles TDSP à partir de la galerie de modèles Azure Machine Learning
Pour créer un projet avec la structure et les modèles de processus de documentation TDSP, effectuez les procédures suivantes : 

### <a name="click-on-new-project"></a>Clic sur « Nouveau projet »
Ouvrez Azure Machine Learning. Sous **Projets** dans le coin supérieur gauche, cliquez sur **+** et sélectionnez **Nouveau projet** pour créer un projet.

![Nouveau projet](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="creating-a-new-tdsp-structured-project"></a>Création d’un projet à structure TDSP
Spécifiez les paramètres et les informations dans les zones appropriées :

- Nom du projet
- Répertoire du projet
- Description du projet
- Chemin de dépôt Git vide
- Nom de l’espace de travail

Ensuite, dans le zone de **recherche**, tapez *TDSP*. Quand le modèle **Structurer un projet avec TDSP** s’affiche, cliquez dessus pour le sélectionner. Puis cliquez sur le bouton **Créer** pour créer votre projet avec la structure TDSP. Si vous indiquez un dépôt Git vide pendant la création du projet (dans la zone appropriée), ce dépôt est renseigné avec la structure de projet et le contenu une fois le projet créé.

![Créer un projet TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Examiner la structure de projet TDSP
Une fois votre nouveau projet créé, vous pouvez examiner sa structure (panneau gauche dans la figure ci-dessous). Il contient tous les aspects de la documentation normalisée pour la présentation de l’entreprise, les étapes du cycle de vie TDSP, l’emplacement, la définition et l’architecture des données dans ce modèle de documentation. Cette structure est dérivée de la structure TDSP publiée [ici](https://github.com/Azure/Azure-TDSP-ProjectTemplate), avec quelques modifications. Par exemple, plusieurs des modèles de document sont fusionnés dans un seul fichier Markdown, à savoir, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Structure de dossiers du projet
Le modèle de projet TDSP contient les dossiers de niveau supérieur suivants :
1. **code** : contient le code.
2. **docs** : contient la documentation nécessaire sur le projet (par exemple, les fichiers Markdown, les médias associés, etc.).
3. **sample_data** : contient des **(petits) EXEMPLES** de données qui peuvent être utilisés dans le cadre du développement initial ou à des fins de test. En règle générale, ces données n’excèdent pas quelques Mo/s (5). Ce dossier ne convient pas à des jeux de données complets ou volumineux.

![Exemples de données](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="using-the-tdsp-structure-and-templates"></a>Utilisation de la Structure et des modèles TDSP
La structure et les modèles doivent être renseignés avec des informations propres au projet. Vous êtes censé les renseigner avec le code et les informations nécessaires à l’exécution et à la remise de votre projet. Le fichier [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) est un modèle qui doit être modifié directement à l’aide des informations relatives à votre projet. Il est fourni avec un ensemble de questions qui vous aident à remplir les informations à chacune des quatre phases du [cycle de vie du processus TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md).

Un exemple de structure de projet telle qu’elle apparaît pendant l’exécution ou une fois terminée est donné ci-dessous (panneau gauche dans la figure ci-dessous). Il provient de l’exemple de projet [Team Data Science Process Sample Project: Classify incomes from US Census data in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) (Exemple de projet TDSP : Classifier des revenus à partir de données US Census dans Azure Machine Learning).

![Exemple de structure de projet](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="documenting-your-project"></a>Documentation de votre projet
Reportez-vous aux [modèles de documentation TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) pour documenter votre projet. Dans le modèle de documentation TDSP Azure Machine Learning actuel, nous vous conseillons d’inclure toutes les informations contenues dans le fichier [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Ce modèle doit être rempli avec des informations propres à votre projet. 

Nous fournissons également un autre modèle [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) pour inclure des informations qui ne sont pas incluses dans le document de projet principal, mais qui sont quand même utiles à documenter. 

### <a name="example-project-report"></a>Exemple de rapport de projet
Un exemple de rapport de projet se trouve [ici](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliveralbe_docs/ProjectReport.md). Voici le rapport de projet de l’[exemple de projet de classification des revenus US](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome), qui montre comment le modèle TDSP peut être instancié et utilisé pour un projet de science des données.

## <a name="next-steps"></a>Étapes suivantes
Pour faciliter votre compréhension de la façon dont la structure et les modèles TDSP peuvent être utilisés dans des projets Azure Machine Learning, nous fournissons plusieurs exemples de projet complets dans la documentation d’Azure Machine Learning.

- Pour voir un exemple montrant comment créer un projet TDSP dans Azure Machine Learning, consultez [Team Data Science Process Sample Project: Classify incomes from US Census data in Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) (Exemple de projet TDSP : Classifier des revenus à partir de données US Census dans Azure Machine Learning). 
- Pour voir un exemple qui utilise l’apprentissage profond et le traitement en langage naturel dans un projet instancié en TDSP dans Azure Machine Learning, consultez [Bio-medical entity recognition using Natural Language Processing with Deep Learning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction) (Reconnaissance d’entité bio-médicale à l’aide d’un traitement en langage naturel avec apprentissage profond).

