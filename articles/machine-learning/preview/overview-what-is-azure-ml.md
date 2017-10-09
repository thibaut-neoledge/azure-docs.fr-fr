---
title: "Qu'est-ce qu’Azure Machine Learning ? | Microsoft Docs"
description: "Vue d’ensemble des services Expérimentation et Gestion des modèles Azure Machine Learning, une solution de science des données de bout en bout intégrée et destinée aux chercheurs de données professionnels pour développer, tester et déployer des applications d’analytique avancée à l’échelle du cloud."
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c4919fb679eeb4d25eb0066b9bf617b057d44354
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="what-is-azure-machine-learning"></a>Qu'est-ce qu’Azure Machine Learning ?

Azure Machine Learning est une solution d’analytique avancée et de science des données de bout en bout intégrée. Cette solution permet aux scientifiques des données de préparer des données, de développer des expérimentations et de déployer des modèles à l’échelle du cloud.

Les principaux composants d’Azure Machine Learning sont :
- Azure Machine Learning Workbench
- Service Expérimentation Azure Machine Learning
- Service Gestion des modèles Azure Machine Learning
- Bibliothèques Microsoft Machine Learning pour Apache Spark (bibliothèque MMLSpark)
- Visual Studio Code Tools pour IA

Ensemble, ces applications et services permettent d’accélérer considérablement le développement et le déploiement d’un projet de science des données. 

![Concepts Azure Machine Learning](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>Compatible open source

Azure Machine Learning prend entièrement en charge les technologies open source. Vous pouvez utiliser des dizaines de milliers de packages Python open source, comme les frameworks d’apprentissage automatique ci-dessous :

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [Spark ML](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Vous pouvez effectuer vos expérimentations dans les environnements gérés tels que les conteneurs Docker et les clusters Spark. Vous pouvez également utiliser un matériel avancé comme les [machines virtuelles prenant en charge GPU dans Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) pour accélérer votre exécution.

Azure Machine Learning repose sur les technologies open source suivantes :

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Il inclut également les propres technologies open source de Microsoft, comme la [bibliothèque Microsoft Machine Learning pour Apache Spark](https://github.com/Azure/mmlspark) et Cognitive Toolkit.

En outre, vous bénéficiez également de certaines des technologies Machine Learning éprouvées les plus avancées développées par Microsoft et conçues pour résoudre les problèmes à grande échelle. Elles sont testées dans de nombreux produits Microsoft, par exemple :

- Windows
- Bing
- Xbox
- Office
- SQL Server

Voici quelques-unes des technologies Microsoft Machine Learning incluses dans Azure Machine Learning :

- [PROSE](https://microsoft.github.io/prose/) (Program Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
Azure Machine Learning Workbench est constitué d’une application de bureau ainsi que d’outils en ligne de commande, et est pris en charge à la fois sur Windows et macOS. Il vous permet de gérer des solutions d’apprentissage automatique dans l’intégralité du cycle de vie de science des données :

- Ingestion et préparation des données
- Développement des modèles et gestion des expérimentations
- Déploiement des modèles dans différents environnements cibles

Voici les principales fonctionnalités proposées par Azure Machine Learning Workbench :

- Outil de préparation des données qui peut acquérir une logique de transformation des données par exemple.
- Abstraction de la source de données accessible par du code UX et Python.
- Kit SDK Python pour appeler des packages de préparation des données construits visuellement.
- Service Bloc-notes Jupyter intégré et UX client.
- Surveillance et gestion des expérimentations via des affichages de l’historique des exécutions.
- Contrôle d’accès en fonction du rôle qui permet le partage et la collaboration via Azure Active Directory.
- Instantanés de projets automatiques pour chaque exécution et gestion de versions explicite activée par l’intégration de Git native. 
- Intégration aux IDE Python populaires.

Pour plus d’informations, reportez-vous aux articles suivants :
- [Guide de l’utilisateur de préparation des données](data-prep-user-guide.md)
- [Utilisation de Git avec Azure Machine Learning](using-git-ml-project.md)
- [Utilisation du Bloc-notes Jupyter dans Azure Machine Learning](how-to-use-jupyter-notebooks.md)
- Itinérance et partage
- [Guide de l’historique des exécutions](how-to-use-run-history-model-metrics.md)
- [Intégration de l’IDE](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Service Expérimentation Azure Machine Learning
Le service Expérimentation gère l’exécution des expérimentations Machine Learning. Il prend également en charge Workbench en assurant la gestion de projets, l’intégration de Git, le contrôle d’accès, l’itinérance et le partage. 

Une configuration facile vous permet d’exécuter vos expérimentations sur diverses options d’environnement Compute :

- Natif local
- Conteneur Docker local
- Conteneur Docker sur une machine virtuelle distante
- Cluster Spark avec montée en charge dans Azure

Le service Expérimentation construit des environnements virtuels pour garantir que votre script peut être exécuté de manière isolée avec des résultats reproductibles. Il enregistre les informations de l’historique des exécutions et vous présente cet historique visuellement. Vous pouvez facilement sélectionner le meilleur modèle parmi vos exécutions d’expérimentations. 

Pour plus d’informations, reportez-vous à [Configuration de l’exécution d’une expérimentation](experiment-execution-configuration.md).

## <a name="azure-machine-learning-model-management-service"></a>Service Gestion des modèles Azure Machine Learning

Le service Gestion des modèles permet aux chercheurs de données et aux équipes des opérations de développement de déployer des modèles prédictifs dans un large éventail d’environnements. Le suivi du lignage et des versions de modèle est assuré depuis les exécutions d’apprentissage jusqu’aux déploiements. Les modèles sont stockés, inscrits et gérés dans le cloud. 

À l’aide de commandes CLI simples, vous pouvez mettre en conteneur vos modèle, scripts de notation et dépendances dans des images Docker. Ces images sont inscrites dans votre propre registre Docker hébergé dans Azure (Azure Container Registry). Elles peuvent être déployées de manière fiable sur les cibles suivantes :

- Ordinateurs locaux
- Serveurs locaux
- Cloud
- Appareils IoT Edge

Kubernetes exécuté dans ACS (Azure Container Service) est utilisé pour le déploiement avec montée en charge dans le cloud. Les données de télémétrie d’exécution du modèle sont capturées dans AppInsights pour une analyse visuelle. 

Pour plus d’informations sur le service Gestion des modèles, reportez-vous à [Présentation de la gestion de modèles](model-management-overview.md)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Bibliothèque Microsoft Machine Learning pour Apache Spark

La bibliothèque [MMLSpark](https://github.com/Azure/mmlspark) (Microsoft Machine Learning pour Apache Spark) est un package Spark open source qui fournit des outils d’apprentissage profond et de science des données pour Apache Spark. Elle intègre [Spark Machine Learning Pipelines](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) à [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) et la bibliothèque [OpenCV](http://opencv.org/) bibliothèque. Elle vous permet de créer rapidement de puissants modèles analytiques et prédictifs hautement scalables pour les volumineux jeux de données de textes et d’images. Les fonctions clés sont notamment les suivantes :

- Ingérer facilement les images depuis HDFS dans Spark DataFrame
- Prétraiter les données d’image à l’aide de transformations à partir d’OpenCV
- Caractériser des images à l’aide de réseaux DNN préformés avec Microsoft Cognitive Toolkit 
- Utiliser des LSTM bidirectionnels préformés de Keras pour l’extraction de l’entité médicale
- Former des modèles de classification d’images basés sur des réseaux DNN sur des machines virtuelles GPU série N sur Azure
- Caractériser des données de texte au format libre à l’aide d’API pratiques sur les primitives dans SparkML via un transformateur unique
- Former facilement des modèles de classification et de régression via une caractérisation implicite des données
- Calculer un ensemble complet de métriques d’évaluation, notamment les métriques de chaque instance

Pour plus d’informations, reportez-vous à [Utilisation de MMLSpark dans Azure Machine Learning](how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools pour IA
Visual Studio Code Tools pour IA est une extension dans Visual Studio Code permettant de générer, tester et déployer des solutions IA et d’apprentissage profond. Il propose de nombreux points d’intégration à Azure Machine Learning, dont :
- Un affichage de l’historique des exécutions qui montre les performances des exécutions d’apprentissage et des métriques enregistrées.
- Une vue Galerie permettant aux utilisateurs de parcourir et de démarrer de nouveaux projets avec Microsoft Cognitive Toolkit, TensorFlow et un grand nombre d’autres frameworks d’apprentissage profond. 
- Un mode explorateur pour sélectionner des cibles de calcul pour les scripts à exécuter.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Quelles sont les options d’apprentissage automatique de Microsoft ?
En dehors d’Azure Machine Learning, il existe une grande variété d’options dans Azure pour générer, déployer et gérer des modèles d’apprentissage automatique. 
* Microsoft Machine Learning Services dans SQL Server
* Microsoft Machine Learning Server
* Machine virtuelle DSVM
* Spark MLLib dans HDInsight
* Service Batch AI Training
* Microsoft Cognitive Toolkit
* Microsoft Cognitive Services


### <a name="microsoft-machine-learning-services-in-sql-server"></a>Microsoft Machine Learning Services dans SQL Server
[Microsoft Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) vous permet d’exécuter, de former et de déployer des modèles d’apprentissage automatique à l’aide de R ou Python. Vous pouvez utiliser les données situées en local et dans des bases de données SQL Server. 

Utilisez Microsoft Machine Learning Services quand vous devez former ou déployer des modèles en local ou dans des bases de données Microsoft SQL Server. Les modèles créés avec Machine Learning Services peuvent être déployés à l’aide du service Gestion des modèles Azure Machine Learning. 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) est un serveur d’entreprise pour l’hébergement et la gestion des charges de travail parallèles et distribuées des processus R et Python. Microsoft Machine Learning Server s’exécute sur Linux, Windows, Hadoop et Apache Spark. Il est également disponible sur [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Il fournit un moteur d’exécution pour les solutions créées à l’aide des [packages Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) et étend Python et R open source avec la prise en charge des scénarios suivants :

- Analytique hautes performances
- Analyse statistique
- Apprentissage automatique
- Jeux de données très volumineux

Des fonctionnalités à valeur ajoutée sont fournies par le biais des packages propriétaires qui sont installés avec le serveur. Pour le développement, vous pouvez utiliser comme IDE [Outils R pour Visual Studio](https://www.visualstudio.com/vs/rtvs/) et [Python Tools pour Visual Studio](https://www.visualstudio.com/vs/python/).

Utilisez Microsoft Machine Learning Server quand vous devez :

- Générer et déployer des modèles créés avec R et Python sur un serveur
- Distribuer une formation R et Python à l’échelle sur un cluster Hadoop ou Spark.

### <a name="data-science-virtual-machine"></a>Machine virtuelle DSVM
La machine virtuelle [DSVM (Data Science Virtual Machine)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-virtual-machine-overview) est une image de machine virtuelle personnalisée sur le cloud Microsoft Azure spécialement conçue pour la science des données. Elle inclut de nombreux outils de science des données populaires et d’autres outils sont préinstallés et préconfigurés afin d’accélérer la création d’applications intelligentes à des fins d’analytique avancée. Elle est disponible sur Windows Server et sur Linux. Nous vous proposons l’édition Windows de la DSVM sous Server 2016 et Server 2012. Nous vous proposons l’édition Linux de la DSVM sous Ubuntu 16.04 LTS et sous des versions Linux basées sur OpenLogic 7.2 CentOS. 

Utilisez l’image DSVM quand vous devez exécuter ou héberger vos tâches sur un nœud unique, ou si vous devez monter en puissance à distance le traitement sur un seul ordinateur. L’image DSVM est prise en charge en tant que cible pour les services Expérimentation Azure Machine Learning et Gestion des modèles Azure Machine Learning. 

### <a name="spark-mllib-in-hdinsight"></a>Spark MLLib dans HDInsight
[Spark MLLib dans HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) vous permet de créer des modèles dans le cadre de tâches Spark qui s’exécutent sur les données volumineuses. Spark vous permet de transformer et préparer facilement les données, puis de monter en charge la création du modèle dans une seule tâche. Les modèles créés par le biais de Spark MLLib peuvent être déployés, gérés et surveillés via le service Gestion des modèles Azure Machine Learning. Les exécutions d’apprentissage peuvent être réparties et gérées avec le service Expérimentation Azure Machine Learning. Spark peut également être utilisé pour la montée en charge des tâches de préparation de données créées dans Machine Learning Workbench. 

Utilisez Spark quand vous devez monter en charge le traitement des données et créer des modèles dans le cadre d’un pipeline de données. Vous pouvez créer des tâches Spark dans Scala, Java, Python ou R. 

### <a name="batch-ai-training"></a>Batch AI Training 
[Azure Batch AI Training](https://aka.ms/batchaitraining) vous aide à faire des essais en parallèle avec vos modèles IA à l’aide de tout framework, puis effectue leur apprentissage à l’échelle sur des GPU en cluster. Décrivez les spécifications de votre tâche et la configuration à exécuter, et nous nous occupons du reste. 

Batch AI Training vous permet de monter en charge des tâches d’apprentissage profond sur des GPU en cluster, en utilisant des frameworks comme :

- Cognitive Toolkit
- Caffe
- Chainer
- TensorFlow

Le service Gestion des modèles Azure Machine Learning peut servir à retirer des modèles de Batch AI Training pour les déployer, gérer et surveiller.  Batch AI Training sera intégré au service Expérimentation Azure Machine Learning à l’avenir. 

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit
[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) est un kit de ressources d’apprentissage profond unifié qui décrit les réseaux neuronaux comme des étapes de calcul dans un graphe orienté. Dans ce graphe orienté, les nœuds terminaux représentent des valeurs d’entrée ou des paramètres réseau, tandis que les autres nœuds représentent des opérations de matrice sur leurs entrées. Cognitive Toolkit vous permet de réaliser et combiner facilement des types de modèles populaires tels que des réseaux DNN de transfert du flux, des réseaux convolutionnels (CNN) et des réseaux récurrents (RNN/LSTM). Il implémente la formation SGD (Stochastic Gradient Descent), rétropropagation des erreurs, avec distinction automatique et parallélisation sur plusieurs GPU et serveurs.

Utilisez Cognitive Toolkit quand vous souhaitez générer un modèle avec l’apprentissage profond.  Cognitive Toolkit peut être utilisé dans tous les services précédents.

### <a name="microsoft-cognitive-services"></a>Microsoft Cognitive Services
Microsoft Cognitive Services est un ensemble de 30 API qui vous permettent de créer des applications qui utilisent des méthodes de communication naturelles. Ces API permettent à vos applications de voir, d’entendre, de parler, de comprendre et d’interpréter nos besoins avec juste quelques lignes de code. Ajoutez facilement des caractéristiques intelligentes à vos applications, par exemple : 

- Émotion et détection de sentiments
- Vision et reconnaissance vocale
- Compréhension de la langue
- Connaissances et recherche

Microsoft Cognitive Services peut être utilisé pour développer des applications sur des appareils et plateformes. Les API ne cessent de s’améliorer et sont faciles à configurer. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Étapes suivantes
* [Installer et créer Azure Machine Learning](quickstart-installation.md)

