---
title: Cycle de vie du processus TDSP (Team Data Science Process) | Microsoft Docs
description: "Les étapes du cycle de vie et les composants pour structurer vos projets de science des données."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: bradsev;hangzh;gokuma
translationtype: Human Translation
ms.sourcegitcommit: 1796f7a7cd174d7ed6582878d72c59995aac41cb
ms.openlocfilehash: 995ba0dc3ffd2bc78625db7d1176ca0d5e1611a0
ms.lasthandoff: 02/08/2017


---
# <a name="team-data-science-process-lifecycle"></a>Cycle de vie du processus TDSP (Team Data Science Process)
Le processus de sciences des données (TDSP) fournit un cycle de vie recommandé que vous pouvez utiliser pour structurer le développement de vos projets de sciences des données. Le cycle de vie présente les étapes, du début à la fin, que les projets suivent généralement quand ils sont exécutés. Si vous utilisez un autre cycle de vie de sciences des données, tel que [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) ou le processus personnalisé de votre organisation, vous pouvez toujours utiliser le processus TDSP basé sur les tâches dans le contexte de ces cycles de vie de développement. 

Ce cycle de vie a été conçu pour les projets de sciences des données destinés à faire partie d’applications intelligentes. Ces applications déploient des modèles d’apprentissage automatique ou d’intelligence artificielle pour l’analytique prédictive. Les projets de sciences des données exploratoires et les projets d’analytique ad hoc ou on-off peuvent également tirer parti de ce processus, mais dans ces cas certaines étapes décrites peuvent s’avérer superflues.    

Voici une représentation visuelle du **cycle de vie du processus TDSP (Team Data Science Process)**. 

![Cycle de vie du processus TDSP](./media/data-science-process-overview/tdsp-lifecycle.png) 

Le cycle de vie du processus TDSP se compose de cinq phases majeures qui sont exécutées de manière itérative. Ces raisons peuvent être les suivantes :

* **Présentation de l’entreprise**
* **Acquisition de données et compréhension**
* **Modélisation**
* **Déploiement**
* **Acceptation du client**

Pour chaque phase, nous fournissons les informations suivantes :

* **Objectifs** : objectifs spécifiques détaillés.
* **Marche à suivre** : tâches spécifiques décrites et conseils fournis pour les effectuer.
* **Artefacts** : livrables et prise en charge de leur production.

## <a name="1-business-understanding"></a>1. Présentation de l’entreprise
### <a name="goals"></a>Objectifs
* Sont spécifiées les **variables clés** devant servir de **cibles du modèle** et dont les mesures associées permettent de déterminer la réussite du projet.
* Sont identifiées les **sources de données** accessibles à l’entreprise ou provenant d’autres sources si nécessaire.

### <a name="how-to-do-it"></a>Marche à suivre
Deux tâches principales sont traitées dans cette phase : 

* **Définir les objectifs** : collaborez avec votre client et autres parties prenantes pour comprendre et identifier les problèmes métier. Formulez des questions qui définissent les objectifs commerciaux et que les techniques de sciences des données peuvent cibler.
* **Identifier les sources de données** : recherchez les données pertinentes qui vous aident à répondre aux questions qui définissent les objectifs du projet.

#### <a name="11-define-objectives"></a>1.1 Définir les objectifs
1. Un objectif central de cette étape consiste à identifier les **variables d’activité** clés que l’analyse doit prédire. Ces variables sont appelées **cibles du modèle** et les mesures qui leur sont associées sont utilisées pour déterminer la réussite du projet. Les prévisions de ventes ou la probabilité qu’une commande soit frauduleuse sont des exemples de ces cibles.
2. Définissez les **objectifs du projet** en posant et affinant des questions « précises » qui sont à la fois pertinentes, spécifiques et non équivoques. La science des données est le processus consistant à utiliser des noms et des nombres pour répondre à ces questions. « *Quand vous choisissez votre question, imaginez que vous vous approchez d’un oracle qui peut vous indiquer n’importe quoi dans l’univers, dans la mesure où la réponse est un nombre ou un nom* ». Pour plus d’informations, consultez la section **Ask a Sharp Question** (Poser une question précise) du blog [How to do Data Science](https://blogs.technet.microsoft.com/machinelearning/2016/03/28/how-to-do-data-science/) (Guide pratique de la science des données).   La science des données et l’apprentissage automatique sont généralement utilisés pour répondre à cinq types de questions :
   * Quelle quantité (ou combien) ? (régression)
   * Quelle catégorie ? (classification)
   * Quel groupe ? (clustering)
   * Est-ce étrange ? (détection des anomalies)
   * Quelle est l’option appropriée ? (recommandation)
3. Définissez **l’équipe de projet** en spécifiant les rôles et responsabilités de ses membres. Développez un plan à étapes général auquel vous vous référez au fil de la découverte de nouvelles informations.  
4. **Définissez des mesures de réussite**. Par exemple : atteindre la précision de prédiction de la désinscription des clients de X % à la fin de ce projet de 3 mois, afin de pouvoir proposer des promotions pour réduire la désinscription. Les mesures doivent satisfaire à l’acronyme **SMART** : 
   * **S**pécifiques 
   * **M**esurables
   * **A**tteignables 
   * **R**elevant (pertinentes) 
   * **T**ime-bound (associées à un délai) 

#### <a name="12-identify-data-sources"></a>1.2 Identifier les sources de données
Identifiez les sources de données qui contiennent des exemples connus de réponses à vos questions précises. Recherchez les données suivantes :

* Données **pertinentes** pour la question. Disposons-nous de mesures de la cible et de caractéristiques qui sont associées à la cible ?
* Données qui constituent une **mesure exacte** de notre cible de modèle et des caractéristiques dignes d’intérêt.

Il n’est pas rare, par exemple, de constater que les systèmes existants doivent collecter et consigner d’autres types de données pour résoudre le problème et atteindre les objectifs du projet. Dans ce cas, vous pouvez rechercher des sources de données externes ou mettre à jour vos systèmes pour collecter des données plus récentes.

### <a name="artifacts"></a>Artefacts
Voici les livrables de cette phase :

* [**Charte**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md) : un modèle standard est fourni dans la définition de la structure de projet TDSP. Il s’agit d’un document actif qui est mis à jour tout au long du projet en fonction des nouvelles découvertes et de l’évolution des exigences pour l’entreprise. Il est important que vous vous référiez à ce document, en ajoutant des détails, à mesure que vous progressez dans le processus de découverte. Veillez à ce que les clients et autres parties prenantes demeurent impliqués dans la réalisation des modifications et communiquez-leur clairement les raisons des modifications.  
* [**Sources de données**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#raw-data-sources) : cela fait partie du rapport de données qui se trouve dans la structure de projet TDSP. Il décrit les sources des données brutes. Au cours de phases ultérieures, vous renseignez des détails supplémentaires, tels que des scripts permettant de déplacer les données vers votre environnement d’analyse.  
* [**Données**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/DataDictionaries) : ce document fournit les descriptions et le schéma (types de données, informations sur les règles de validation, le cas échéant) pour les données qui sont utilisées pour répondre à la question. Les descriptions ou diagrammes entité/relation sont inclus également, s’ils sont disponibles.

## <a name="2-data-acquisition-and-understanding"></a>2. Acquisition de données et compréhension
### <a name="goals"></a>Objectifs
* Jeu de données propre, de grande qualité et pouvant être modélisé, dont les relations avec les variables cibles qui se trouvent dans l’environnement d’analytique sont comprises.
* Développement d’une architecture de solution du pipeline de données pour actualiser et évaluer les données régulièrement.

### <a name="how-to-do-it"></a>Marche à suivre
Trois tâches principales sont traitées dans cette phase :

* **Ingérer les données** dans l’environnement d’analyse cible.
* **Explorer les données** pour déterminer si la qualité des données est suffisante pour répondre à la question. 
* **Configurer un pipeline de données** pour évaluer les données nouvelles ou régulièrement actualisées.

#### <a name="21-ingest-the-data"></a>2.1 Ingérer les données
Configurez le processus permettant de déplacer les données des emplacements sources vers les emplacements cibles où doivent être exécutées les opérations d’analytique telles que l’apprentissage et les prédictions. Pour connaître les détails techniques et les options disponibles pour effectuer cette opération avec plusieurs services de données Azure, consultez [Charger des données dans des environnements de stockage à des fins d’analyse](machine-learning-data-science-ingest-data.md). 

#### <a name="22-explore-the-data"></a>2.2 Explorer les données
Avant de former vos modèles, vous devez développer une parfaite compréhension des données. Les jeux de données réels sont souvent parasités, ne contiennent pas certaines valeurs ou comportent une multitude d’autres anomalies. La visualisation et la synthèse des données permettent d’auditer la qualité de vos données et de fournir les informations nécessaires pour traiter les données en vue de leur modélisation. Pour obtenir de l’aide sur le nettoyage des données, consultez [Tâches de préparation des données pour l’apprentissage automatique amélioré](machine-learning-data-science-prepare-data.md). Ce processus est souvent itératif. 

Le processus TDSP fournit un utilitaire automatisé appelé [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils), qui aide à visualiser les données et à préparer des rapports de synthèse de données. Nous vous recommandons de commencer avec IDEAR pour explorer les données afin de développer une compréhension initiale des données de manière interactive sans codage, puis d’écrire du code personnalisé pour l’exploration et la visualisation des données. 

Une fois que vous êtes satisfait de la qualité des données nettoyées, l’étape suivante consiste à mieux comprendre les modèles inhérents aux données grâce auxquels vous pouvez choisir et développer un modèle de prédiction approprié pour votre cible. Recherchez des éléments de preuve sur la qualité de la connexion des données à la cible et indiquant s’il existe suffisamment de données pour passer aux étapes de modélisation suivantes. Là encore, ce processus est souvent itératif. Vous devrez peut-être rechercher de nouvelles sources de données avec des données plus précises ou plus appropriées pour compléter le jeu de données identifié au cours de la phase précédente.  

#### <a name="23-set-up-a-data-pipeline"></a>2.3 Configurer un pipeline de données
Outre l’ingestion et le nettoyage initiaux des données, vous devez généralement définir un processus pour noter les nouvelles données ou actualiser les données régulièrement dans le cadre d’un processus de formation continue. Cette opération est possible grâce à la configuration d’un pipeline de données ou d’un flux de travail. Voici un [exemple](machine-learning-data-science-move-sql-azure-adf.md) de configuration d’un pipeline avec [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). Une architecture de solution du pipeline de données est développée dans cette phase. Le pipeline est également développé parallèlement aux phases suivantes du projet de science des données. Le pipeline peut être traité par lot, en streaming/temps réel ou de façon hybride, suivant les besoins de votre entreprise et les contraintes de vos systèmes existants auxquels cette solution est en cours d’intégration. 

### <a name="artifacts"></a>Artefacts
Voici les livrables de cette phase.

* [**Rapport de qualité des données**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/DataSummaryReport.md) : ce rapport contient des résumés de données, les relations entre chaque attribut et cible, le classement des variables, etc. L’outil [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) fourni dans le cadre du processus TDSP permet de générer rapidement ce rapport sur n’importe quel jeu de données tabulaire tel qu’un fichier CSV ou une table relationnelle. 
* **Architecture de la solution** : il peut s’agir d’un diagramme ou d’une description de votre pipeline de données utilisé pour exécuter une évaluation ou des prédictions sur de nouvelles données une fois que vous avez créé un modèle. Elle contient également le pipeline avec lequel former le modèle en fonction des nouvelles données. Le document est stocké dans ce [répertoire](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) quand vous utilisez le modèle de structure de répertoire TDSP.
* **Décision de point de contrôle** : avant de procéder à l’ingénierie des caractéristiques complète et à la création de modèles, vous pouvez réévaluer le projet pour déterminer si cela vaut la peine de le poursuivre. Vous pouvez, par exemple, être en mesure de continuer, avoir besoin de collecter des données supplémentaires ou abandonner le projet s’il n’existe pas de données permettant de répondre à la question.

## <a name="3-modeling"></a>3. Modélisation
### <a name="goals"></a>Objectifs
* Caractéristiques de données optimales pour le modèle d’apprentissage automatique.
* Modèle d’apprentissage automatique informatif qui prédit la cible avec le plus de précision.
* Modèle d’apprentissage automatique qui convient pour la production.

### <a name="how-to-do-it"></a>Marche à suivre
Trois tâches principales sont traitées dans cette phase :

* **Ingénierie des caractéristiques** : créez des caractéristiques de données à partir des données brutes pour faciliter l’apprentissage du modèle.
* **Apprentissage du modèle** : recherchez le modèle qui répond le plus précisément à la question en comparant leurs mesures de réussite.
* Déterminez si votre modèle est **approprié pour la production**.

#### <a name="31-feature-engineering"></a>3.1 Ingénierie des caractéristiques
L’ingénierie des caractéristiques implique l’inclusion, l’agrégation et la transformation de variables brutes pour créer les caractéristiques utilisées dans l’analyse. Pour savoir ce qui influence un modèle, vous devez comprendre comment les caractéristiques sont liées entre elles et comment les algorithmes d’apprentissage automatique sont susceptibles d’utiliser ces caractéristiques. Cette étape nécessite une combinaison inédite d’expertise dans le secteur et d’informations obtenues à partir de l’étape d’exploration de données. Elle consiste à trouver un équilibre entre, d’une part, rechercher et inclure des variables informatives et, d’autre part, éviter un nombre trop élevé de variables non liées. Les variables informatives améliorent nos résultats, alors que les variables non liées introduisent des bruits superflus dans le modèle. Vous devez également générer ces caractéristiques pour toutes les nouvelles données obtenues au cours de l’évaluation. Ainsi, la génération de ces caractéristiques ne peut dépendre que des données qui sont disponibles au moment de l’évaluation. Pour obtenir des conseils techniques sur l’ingénierie des caractéristiques durant l’utilisation de diverses technologies de données Azure, consultez [Feature engineering in the Data Science Process](machine-learning-data-science-create-features.md) (Ingénierie des caractéristiques dans le processus de sciences des données). 

#### <a name="32-model-training"></a>3.2 Apprentissage du modèle
Selon le type de question auquel vous essayez de réponse, il existe de nombreux algorithmes de modélisation. Pour plus d’informations sur le choix des algorithmes, consultez [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md). Bien que cet article soit écrit pour Azure Machine Learning, l’aide qu’il fournit est utile pour les autres infrastructures d’apprentissage automatique. 

Le processus d’apprentissage du modèle comprend les étapes suivantes : 

* Fractionner les données d’entrée de manière aléatoire en vue d’une modélisation dans un jeu de données d’apprentissage et un jeu de données de test.
* Créer les modèles à l’aide du jeu de données d’apprentissage.
* Évaluer une série d’algorithmes d’apprentissage automatique concurrents (apprentissage et jeu de données de test), ainsi que les divers paramètres associés (balayage de paramètres), destinés à répondre à la question digne d’intérêt avec les données actuelles.
* Déterminer la solution « optimale » pour répondre à la question en comparant les mesures de réussite entre les autres méthodes.

> [!NOTE]
> **Éviter la fuite** : la fuite de données peut résulter de l’inclusion de données en dehors du jeu de données d’apprentissage, permettant à un modèle ou un algorithme d’apprentissage automatique d’effectuer des prédictions anormalement correctes. La nervosité qui gagne les scientifiques de données quand ils obtiennent des résultats prédictifs semblant trop beaux pour être vrais est souvent liée à une fuite. Ces dépendances peuvent être difficiles à détecter. Pour éviter ce problème, il est souvent nécessaire de jongler entre la création d’un jeu de données d’analyse, la création d’un modèle et l’évaluation de la précision. 
> 
> 

Nous fournissons avec le processus TDSP un [outil de modélisation et de création de rapports automatisé](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling) capable d’exécuter plusieurs algorithmes et balayages de paramètres pour produire un modèle de référence. Il génère également un rapport de modélisation de base qui résume les performances de chaque combinaison de modèle et de paramètre, y compris l’importance des variables. Ce processus est également itératif, car il peut influer sur l’ingénierie des caractéristiques. 

### <a name="artifacts"></a>Artefacts
Les artefacts générés au cours de cette phase sont les suivants :

* [**Jeux de caractéristiques**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets) : les caractéristiques développées pour la modélisation sont décrites dans la section Feature Set (Jeu de caractéristiques) du rapport Data Definition (Définition des données). Elle contient des pointeurs vers le code permettant de générer les caractéristiques et indique comment la caractéristique a été générée.
* [**Rapport de modélisation**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md) : pour chaque modèle tenté, un rapport standard suivant un modèle TDSP spécifié est généré.
* **Décision de point de contrôle** : déterminez si le modèle est suffisamment performant pour être déployé sur un système de production. Voici certaines questions clés que vous devez vous poser :
  * Le modèle répond-il à la question avec une fiabilité suffisante compte tenu des données de test ? 
  * Est-il nécessaire d’essayer d’autres approches : collecter des données supplémentaires, poursuivre l’ingénierie des caractéristiques ou faire des essais avec d’autres algorithmes ?

## <a name="4-deployment"></a>4. Déploiement
### <a name="goal"></a>Objectif
* Les modèles et pipeline sont déployés sur un environnement de production ou similaire en vue de l’acceptation de l’utilisateur final. 

### <a name="how-to-do-it"></a>Marche à suivre
Une tâche principale est traitée dans cette phase :

* **Faire fonctionner le modèle** : déployez le modèle et le pipeline sur un environnement de production ou similaire en vue de l’utilisation de l’application.

#### <a name="41-operationalize-a-model"></a>4.1 Faire fonctionner un modèle
Une fois que votre ensemble de modèles fonctionne correctement, il peut être mis en œuvre et utilisé par d’autres applications. En fonction des exigences pour l’entreprise, les prédictions sont faites en temps réel ou par lot. Pour être mis en œuvre, les modèles doivent être exposés avec une interface d’API ouverte facilement utilisable à partir de diverses applications, par exemple, un site web en ligne, des feuilles de calcul, des tableaux de bord ou des applications métier et de serveur principal. Pour obtenir des exemples de mise en œuvre de modèle avec un service web Azure Machine Learning, consultez [Déploiement d’un service web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md). Il est également judicieux de générer des données de télémétrie et de surveillance dans le modèle de production et le pipeline de données déployés pour faciliter la création de rapports sur l’état du système et le dépannage de ce dernier.  

### <a name="artifacts"></a>Artefacts
* Tableau de bord d’état de mesures clés et d’intégrité du système.
* Rapport de modélisation final avec les détails du déploiement.
* Document final de l’architecture de la solution.

## <a name="5-customer-acceptance"></a>5. Acceptation du client
### <a name="goal"></a>Objectif
* **Finaliser les livrables du projet** : vérifiez que le pipeline, le modèle et leur déploiement dans un environnement de production sont conformes aux objectifs des clients.

### <a name="how-to-do-it"></a>Marche à suivre
Trois tâches principales sont traitées dans cette phase :

* **Validation du système** : vérifiez que le modèle et le pipeline déployés répondent aux besoins des clients.
* **Remise du projet** à l’entité chargée d’exécuter le système dans l’environnement de production.

Le client valide que le système répond à ses besoins professionnels et que les réponses apportées aux questions sont suffisamment précises pour permettre le déploiement du système sur l’environnement de production en vue de son utilisation par son application cliente. Toute la documentation est finalisée et révisée. Le projet est remis à l’entité responsable des opérations. Il peut s’agir d’une équipe de science des données au sein du service informatique ou chez le client ou d’un agent du client chargé d’exécuter le système dans l’environnement de production. 

### <a name="artifacts"></a>Artefacts
L’artefact principal produit au cours de cette étape finale est le [**rapport final du projet**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md). Il s’agit du rapport technique contenant tous les détails du projet qui facilitent la formation et l’utilisation du système. Un [modèle](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) est fourni par le processus TDSP qui peut être utilisé en l’état ou personnalisé en fonction des besoins du client. 

## <a name="summary"></a>Résumé
Le [cycle de vie du processus TDSP](http://aka.ms/datascienceprocess) est construit comme une suite d’étapes itérées qui fournissent des conseils sur les tâches indispensables à l’utilisation de modèles prédictifs. Ces modèles peuvent être déployés dans un environnement de production afin d’être exploités pour créer des applications intelligentes. Ce cycle de vie a pour objectif de faire progresser un projet de science des données vers un point final d’engagement clair. S’il est vrai que la science des données est un exercice de recherche et de découverte, pouvoir en faire prendre conscience votre équipe et vos clients à l’aide d’un ensemble bien défini d’artefacts qui utilise des modèles standardisés peut aider à éviter les malentendus et à augmenter les chances de réussite d’un projet de science des données complexe.

## <a name="next-steps"></a>Étapes suivantes
Des procédures pas à pas complètes illustrant toutes les étapes de **scénarios spécifiques** sont également fournies. La rubrique [Team Data Science Process walkthroughs](data-science-process-walkthroughs.md) (Procédures pas à pas du processus TDSP (Team Data Science Process)) les répertorie et les décrit brièvement, en les accompagnant de liens.


