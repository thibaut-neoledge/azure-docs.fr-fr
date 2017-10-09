--- 
title: "Scénario réel de maintenance prédictive | Microsoft Docs"
description: "Scénario réel de maintenance prédictive avec PySpark"
services: machine-learning
author: jaymathe
ms.author: jaymathe
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 09/25/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7bb5672a2b9de0a62af8b6155bfdead6a0105eb5
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

--- 

# <a name="predictive-maintenance-real-world-scenario"></a>Scénario réel de maintenance prédictive


L’impact des temps d’arrêt non planifiés de l’équipement peut nuire aux entreprises. Il est par conséquent essentiel de maintenir les équipements opérationnels afin d’optimiser les performances et l’utilisation, et de réduire les temps d’arrêt non planifiés et coûteux. Cette identification précoce des problèmes peut aider à allouer des ressources de maintenance limitées de façon économique et à améliorer la qualité et les processus de la chaîne d’approvisionnement. 

Pour ce scénario, nous utilisons un [ensemble de données relativement important](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) pour guider l’utilisateur à travers les principales étapes, de l’ingestion des données, l’ingénierie des fonctionnalités et la création d’un modèle à l’opérationnalisation et au déploiement du modèle. Le code de l’ensemble du processus est écrit dans PySpark et implémenté à l’aide de blocs-notes Jupyter dans Azure ML Workbench. Pour terminer, le meilleur modèle est opérationnalisé à l’aide de Gestion des modèles Azure Machine Learning en vue de son utilisation dans un environnement de production pour effectuer des prédictions de défaillance en temps réel.   

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie

Le lien vers le dépôt GitHub public est le suivant : [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

L’un des problèmes majeurs auxquels sont confrontées les entreprises des secteurs gourmands en ressources n’est autre que les coûts importants associés aux retards dus à des problèmes mécaniques. La plupart des entreprises aimeraient être en mesure de prédire quand ces problèmes surviendront pour éviter de façon proactive qu’ils ne se produisent. Cela permet de réduire les coûts en réduisant les temps d’arrêt et, dans certains cas, en améliorant la sécurité. Consultez le [playbook sur la maintenance prédictive](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance) pour une explication détaillée des cas d’usage communs et de l’approche de modélisation pour la maintenance prédictive.

Ce scénario tire parti des idées du playbook dans le but de décrire les étapes nécessaires pour implémenter un modèle prédictif pour un scénario basé sur une synthèse de divers problèmes métier du monde réel. Cet exemple regroupe des éléments de données communs observés dans de nombreux cas d’usage de maintenance prédictive.

Le problème métier pour ces données simulées consiste à prédire les problèmes causés par les échecs de composants. La question métier est par conséquent la suivante : « *Quelle est la probabilité qu’une machine tombe en panne en raison d’un échec de composant* ? » Ce problème est formulé sous forme de problème de classification multiclasse (plusieurs composants par machine) et un algorithme Machine Learning est utilisé pour créer le modèle prédictif. L’apprentissage du modèle est effectué à partir de données d’historique collectées sur des machines. Dans ce scénario, l’utilisateur réalise les différentes étapes d’implémentation d’un modèle de ce type dans l’environnement Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Composants requis

* Un [compte Azure](https://azure.microsoft.com/en-us/free/) (des comptes d’essai gratuit sont disponibles).
* Une copie [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installée selon les instructions du [guide d’installation et de démarrage rapide](./quickstart-installation.md) pour installer le programme et créer un espace de travail.
* Les résultats intermédiaires à utiliser via des blocs-notes Jupyter dans ce scénario sont stockés dans un conteneur de stockage Blob Azure. Des instructions pour configurer un compte de stockage Azure sont disponibles à travers ce [lien](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-python-how-to-use-blob-storage). 
* Pour l’[opérationnalisation](https://github.com/Azure/Machine-Learning-Operationalization) du modèle, il est préférable que l’utilisateur exécute un [moteur Docker](https://www.docker.com/) installé et exécuté localement. Dans le cas contraire, vous pouvez utiliser l’option de cluster, mais tenez compte du fait que l’exécution d’un cluster [Azure Container Service (ACS)](https://azure.microsoft.com/en-us/services/container-service/) est souvent coûteuse.
* Ce scénario part du principe que l’utilisateur exécute Azure ML Workbench sur une machine Windows 10 avec le moteur Docker installé localement. 
* Le scénario a été créé et testé sur une machine Windows 10 avec les spécifications suivantes : processeur Intel Core i7-4600U de 2,10 GHz, 8 Go de RAM, système d’exploitation 64 bits, processeur x64 avec Docker version 17.06.0-ce-win19 (12801). 
* L’opérationnalisation du modèle a été effectuée à l’aide de la version suivante de l’interface CLI Azure ML : azure-cli-ml==0.1.0a22

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Maintenance prédictive » et sélectionnez le modèle
5.  Cliquez sur **Créer**

## <a name="data-description"></a>Description des données

Les [données simulées](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sont constituées de cinq fichiers de valeurs séparées par une virgule (.csv). 

* [Machines](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv) : fonctionnalités qui différencient chaque machine. Par exemple, l’âge et le modèle.
* [Erreur](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv) : le journal des erreurs contient des erreurs sans rupture levées alors que l’ordinateur est toujours opérationnel. Ces erreurs ne sont pas considérées comme des échecs, même si elles peuvent prédire un événement d’échec futur. La date et l’heure de l’erreur sont arrondies à l’heure la plus proche étant donné que les données de télémétrie sont collectées toutes les heures.
* [Maintenance](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv) : le journal de maintenance contient à la fois les enregistrements de la maintenance planifiée et de la maintenance non planifiée. La maintenance planifiée correspond à l’inspection régulière des composants, tandis que la maintenance non planifiée peut être due à une défaillance mécanique ou autre dégradation des performances. La date et l’heure de la maintenance sont arrondies à l’heure la plus proche étant donné que les données de télémétrie sont collectées toutes les heures.
* [Télémétrie](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv) : les données de série chronologique de télémétrie sont constituées de mesures de capteur de tension, rotation, pression et vibration collectées sur chaque machine en temps réel. Le système effectue la moyenne par heure des données et stocke ces dernières dans les journaux de télémétrie.
* [Échecs](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv) : les échecs correspondent aux remplacements de composants dans le journal de maintenance. Chaque enregistrement contient l’ID de machine, le type de composant et les date et heure de remplacement. Ces enregistrements sont utilisés pour créer les étiquettes Machine Learning que le modèle tente de prédire.

Consultez le scénario d’[ingestion des données](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) à l’aide de bloc-notes Jupyter dans la section Code pour télécharger les jeux de données brutes à partir du dépôt GitHub et créer les jeux de données PySpark pour cette analyse.

## <a name="scenario-structure"></a>Structure du scénario
Le contenu pour le scénario est disponible dans le [dépôt GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Le référentiel contient un fichier [Lisez-moi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) qui décrit les processus, de la préparation des données à la création de plusieurs modèles et l’opérationnalisation de l’un des meilleurs modèles. Les quatre blocs-notes Jupyter sont disponibles dans le dossier [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) du référentiel.   

Décrivons à présent le flux de travail du scénario étape par étape. Le scénario de bout en bout est écrit dans PySpark et divisé en quatre blocs-notes, comme indiqué ci-dessous :

* [Ingestion des données](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) : ce bloc-notes gère l’ingestion des données des cinq fichiers .csv d’entrée, procède à un nettoyage préliminaire, crée des graphiques de synthèse pour vérifier le téléchargement des données, puis stocke les jeux de données obtenus dans un conteneur d’objets blob Azure en vue de les utiliser dans le bloc-notes suivant.

* [Ingénierie des fonctionnalités](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb) : le jeu de données nettoyé à l’étape précédente permet de créer les fonctionnalités de décalage pour les capteurs de télémétrie, ainsi que l’ingénierie de fonctionnalités supplémentaires pour créer des variables telles que des jours depuis le dernier remplacement. Enfin, les échecs sont marqués au niveau des enregistrements correspondants pour créer un jeu de données final, qui est enregistré dans un objet blob Azure pour l’étape suivante. 

* [Création du modèle](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb) : le jeu de données final auquel a été appliquée l’ingénierie des fonctionnalités est ensuite divisé en deux, à savoir un jeu de données d’apprentissage et un jeu de données de test basé sur un horodatage. Deux modèles (un classifieur de forêts aléatoires et un classifieur d’arbres de décision) sont ensuite créés au niveau du jeu de données d’apprentissage, puis notés au niveau du jeu de données de test. 

* [Opérationnalisation et déploiement du modèle](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb) : le meilleur modèle créé à l’étape précédente est ensuite enregistré en tant que fichier .model avec le fichier de schéma .json correspondant pour le déploiement. Les fonctions init() et run() sont d’abord testées localement avant l’opérationnalisation du modèle à l’aide de l’environnement Gestion des modèles Azure Machine Learning en vue de son utilisation dans un environnement de production pour effectuer des prédictions de défaillance en temps réel.  

## <a name="conclusion"></a>Conclusion

Ce scénario offre au lecteur une vue d’ensemble de la création d’une solution de maintenance prédictive de bout en bout à l’aide de PySpark dans l’environnement de blocs-notes Jupyter dans Azure ML Workbench. Le scénario oriente également le lecteur sur la façon d’opérationnaliser et de déployer le meilleur modèle à l’aide de l’environnement Gestion des modèles Azure Machine Learning en vue de son utilisation dans un environnement de production pour effectuer des prédictions de défaillance en temps réel. Le lecteur peut ensuite modifier les parties pertinentes du scénario pour l’adapter à ses besoins métier.  

## <a name="references"></a>Références

Ce cas d’usage a été précédemment développé sur plusieurs plateformes, comme indiqué ci-dessous :

* [Modèle de solution de maintenance prédictive](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide de modélisation de maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guide de modélisation de maintenance prédictive avec les services SQL R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guide de modélisation de maintenance prédictive - Bloc-notes Python](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Maintenance prédictive avec PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)



