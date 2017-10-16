---
title: "Guide pratique pour utiliser la fonctionnalité de collecte des données de gestion de modèle dans Azure Machine Learning Workbench | Microsoft Docs"
description: "Ce document décrit comment utiliser la fonctionnalité de collecte des données de gestion de modèle dans Azure Machine Learning Workbench"
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e751e2f0ec812de43a03749e04ff165fa62ec649
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-collect-model-data-using-data-collection"></a>Guide pratique pour collecter les données de modèle à l’aide d’une collecte de données

La fonctionnalité de collecte de données de modèle vous permet d’archiver des entrées et des prédictions de modèle à partir d’un service web d’apprentissage automatique. Ce document aborde les aspects suivants de la collection de données de modèle :

- Guide pratique pour installer le package de collecte de données
- Guide pratique pour utiliser la collecte de données
- Guide pratique pour afficher et utiliser les données collectées

## <a name="how-to-install-the-data-collection-package"></a>Guide pratique pour installer le package de collecte de données
La bibliothèque de collection de données peut être installée en mode natif sous Linux et Windows.

### <a name="windows"></a>Windows
Sous Windows, le module du collecteur de données peut être installé avec la commande suivante :

    pip install azureml.datacollector

### <a name="linux"></a>Linux
Sous Linux, la bibliothèque libxml++ doit être installée en premier. Exécutez la commande suivante sous sudo :

    sudo apt-get install libxml++2.6-2v5

Puis exécutez la commande suivante :

    pip install azureml.datacollector
## <a name="how-to-use-data-collection"></a>Guide pratique pour utiliser la collecte de données

Pour pouvoir utiliser la collecte de données de modèle, vous devez apporter les modifications suivantes à votre fichier de notation :

1. Ajoutez le code suivant au début du fichier
   
    ```python
    from azureml.datacollector import ModelDataCollector
    ```

2. Ajoutez les lignes de code suivantes à la fonction `init()`,
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

3. Ajoutez les lignes de code suivantes à la fonction `run(input_df)`,
    
    ```python
    global inputs_dc, prediction_dc
    inputs_dc.collect(input_df)
    prediction_dc.collect(pred)
    ```

    Assurez-vous que les variables `input_df` et `pred` (valeur de prédiction provenant de `model.predict()`) sont initialisées avant d’exécuter la fonction `collect()` sur ces variables.

4. Utilisez la commande `az ml service create realtime` avec le commutateur `--collect-model-data true` pour créer un service web en temps réel. Cela permet de s’assurer que les données de modèle sont collectées quand le service est en cours d’exécution.

     ```batch
    c:\temp\myIris> az ml service create realtime -f iris_score.py --model-file model.pkl -s service_schema.json -n irisapp -r python --collect-model-data true 
    ```
    
5. Pour tester la collecte de données, exécutez la commande `az ml service run realtime`,

    ```
    C:\Temp\myIris> az ml service run realtime -i irisapp -d "ADD YOUR INPUT DATA HERE!!" 
    ``` 
    
## <a name="how-to-view-and-consume-the-collected-data"></a>Guide pratique pour afficher et utiliser les données collectées
Pour afficher les données collectées dans le stockage d’objets blob :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Plus de services**.
3. Dans la zone de recherche, tapez `Storage accounts` et appuyez sur **Entrée**
4. Dans le panneau de recherche **Comptes de stockage**, sélectionnez la ressource **Compte de stockage**. Pour déterminer votre compte de stockage, utilisez les étapes suivantes,

    a. Accédez à Azure ML Workbench, sélectionnez le projet sur lequel vous travaillez, puis ouvrez une invite de ligne de commande à partir du menu **Fichier**
    
    b. Tapez `az ml env show -v` et vérifiez la valeur *storage_account*. Il s’agit du nom de votre compte de stockage

5. Cliquez sur **Conteneurs** dans le menu du panneau de ressources, puis sur le conteneur appelé **modeldata**. Vous devrez peut-être patienter jusqu'à 10 minutes après l’envoi de la première requête de service web avant que les données commencent à s’afficher dans le compte de stockage. Les données sont transmises dans les objets blob via le chemin d’accès au conteneur suivant :

    `/modeldata/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<day>/data.csv`

Les données peuvent être utilisées à partir d’objets blob Azure de différentes façons grâce aux logiciels Microsoft et aux outils open source. Voici quelques exemples :
 - Azure ML Workbench - Ouvrir le fichier csv dans Azure ML Workbench en ajoutant un fichier csv comme source de données
 - Excel - ouvrir les fichiers csv quotidiens sous forme qu’une feuille de calcul
 - [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/) : créer des graphiques avec les données extraites des données csv d’objets blob
 - [Spark](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-overview) : créer un tableau de données avec une grande partie des données csv
    ```python
    var df = spark.read.format("com.databricks.spark.csv").option("inferSchema","true").option("header","true").load("wasb://modeldata@<storageaccount>.blob.core.windows.net/<subscription_id>/<resource_group_name>/<model_management_account_name>/<webservice_name>/<model_id>-<model_name>-<model_version>/<identifier>/<year>/<month>/<date>/*")
    ```
- [Hive](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-tutorial-get-started) : charger les données csv dans une table Hive et effectuer des requêtes SQL directement sur un objet blob

