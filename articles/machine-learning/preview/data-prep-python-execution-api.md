---
title: "Guide d’utilisation détaillé de l’API d’exécution de la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit des détails sur l’exécution des sources de données conçues au préalable et des packages de préparation de données."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: ceecc718f8198e9a88cae4acbe97e7f26d95f984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="executing-data-sources-and-data-preparation-packages-from-python"></a>Exécution de sources de données et de packages de préparation des données à partir de Python

Pour utiliser une source de données ou un package de préparation des données dans Python, procédez comme suit :
- Accédez à l’onglet Données de votre projet.
- Cliquez avec le bouton droit de la souris sur la source appropriée.
- Cliquez sur l’option permettant de générer le fichier de code d’accès aux données.

Cette action crée un petit script Python qui exécute le package et renvoie un tableau de données.

## <a name="data-sources"></a>Sources de données

Le module `azureml.dataprep.datasource` contient une seule fonction pour exécuter une source de données et renvoyer un tableau de données : `load_datasource(path, secrets=None, spark=None)`.
- `path` est le chemin d’accès à la source de données (fichier .dsource).
- `secrets` est un objet dictionnaire facultatif mettant en correspondances les clés avec les secrets.
- `spark` est une valeur booléenne facultative spécifiant s’il faut renvoyer un tableau de données Spark ou Pandas. Par défaut, Azure ML Workbench détermine le type de tableau de données à renvoyer lors de l’exécution selon le contexte.

## <a name="data-preparation-packages"></a>Packages de préparation des données

Le module `azureml.dataprep.package` contient trois fonctions qui exécutent un flux de données à partir d’un package de préparation des données.

### <a name="execution-functions"></a>Fonctions d’exécution

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` : envoie le flux de données spécifié pour exécution, mais ne renvoie pas de tableau de données.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` : exécute le flux de données spécifié et renvoie les résultats sous la forme d’un tableau de données.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` : exécute le flux de données spécifié en fonction d’une source de données en mémoire et renvoie les résultats sous la forme d’un tableau de données. L’argument `user_config` est un objet dictionnaire qui mappe le chemin d’accès absolu d’une source de données (fichier .dsource) à une source de données en mémoire représentée sous la forme d’une liste de listes.

### <a name="common-arguments"></a>Arguments communs

- `package_path` est le chemin d’accès au package de préparation des données (fichier .dprep).
- `dataflow_idx` est l’index à partir de zéro du flux de données dans le package à exécuter. Si le flux de données spécifié fait référence à d’autres flux de données ou sources de données, ils sont également exécutés.
- `secrets` est un objet dictionnaire facultatif mettant en correspondances les clés avec les secrets.
- `spark` est une valeur booléenne facultative spécifiant s’il faut renvoyer un tableau de données Spark ou Pandas. Par défaut, Azure ML Workbench détermine le type de tableau de données à renvoyer lors de l’exécution selon le contexte.
