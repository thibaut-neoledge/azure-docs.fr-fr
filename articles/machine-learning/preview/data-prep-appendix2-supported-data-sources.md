---
title: "Sources de données prises en charge disponibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des sources de données prises en charge disponibles pour la préparation des données Azure Machine Learning."
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
ms.openlocfilehash: 1ef4c5c33d98cfeb566e8fe23bda9e0d3f041781
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2017
---
# <a name="supported-data-sources-for-this-release"></a>Sources de données prises en charge pour cette version 
Le document suivant présente la liste des sources de données actuellement prises en charge dans la préparation des données Azure Machine Learning.

Les sources de données prises en charge pour cette version sont répertoriées ci-dessous.

## <a name="types"></a>Types 
### <a name="directory-versus-file"></a>Répertoire et fichier
*Fichiers/répertoires* : choisissez un fichier unique et lisez-le dans la préparation des données. Le type de fichier est analysé afin de déterminer les paramètres par défaut pour la connexion de fichiers affichée dans l’écran suivant. Choisissez un répertoire ou un ensemble de fichiers contenus dans un répertoire (le sélecteur de fichiers permet d’effectuer plusieurs sélections). Les deux approches entraînent la lecture des fichiers en tant que flux de données unique, et les fichiers sont ajoutés les uns aux autres (les en-têtes sont supprimés si nécessaire).

Les types de fichiers sont les suivants :
- Délimité (.csv, .tsv, .txt, etc.) 
- Largeur fixe
- Texte brut
- Fichier JSON

### <a name="csv-file"></a>Fichier CSV
Lit un fichier CSV à partir du stockage.

#### <a name="options"></a>Options
- Séparateur
- Commentaire
- headers
- Symbole décimal
- Encodage de fichier
- Lignes à ignorer

### <a name="tsv-file"></a>Fichier TSV
Lit un fichier de valeurs TSV à partir du stockage.

#### <a name="options"></a>Options
- Commentaire
- headers
- Encodage de fichier
- Lignes à ignorer

### <a name="excel-xlsxlsx"></a>Excel (.xls/.xlsx)
Lit un fichier Excel, une feuille à la fois, en spécifiant le nom ou le numéro de la feuille.

#### <a name="options"></a>Options
- Nom/numéro de la feuille
- headers
- Lignes à ignorer

### <a name="json-file"></a>Fichier JSON
Lire un fichier JSON à partir du stockage. Notez que le fichier est « aplati » lors de la lecture.

#### <a name="options"></a>Options
Aucun

### <a name="parquet"></a>Parquet
Lire un jeu de données Parquet, fichier ou dossier unique.

Le format Parquet peut prendre différentes formes dans le stockage. Pour les jeux de données plus petits, un fichier .parquet unique est parfois utilisé. Différentes bibliothèques Python prennent en charge la lecture ou l’écriture sur un fichier .parquet unique. Pour le moment, Azure Machine Learning Workbench s’appuie sur la bibliothèque Python PyArrow pour la lecture de données Parquet lors de l’utilisation interactive locale. Il prend en charge les fichiers .parquet uniques (à condition qu’ils soient écrits comme tels et non dans le cadre d’un jeu de données plus grand). Il prend également en charge les jeux de données Parquet. 

Un jeu de données Parquet est une collection de plusieurs fichiers .parquet représentant chacun une partition plus petite d’un plus grand jeu de données. Les jeux de données sont généralement contenus dans un dossier. Ils constituent le format de sortie Parquet par défaut pour les plateformes courantes telles que Spark et Hive.

>[!NOTE]
>Lors de la lecture de données Parquet qui se trouvent dans un dossier contenant plusieurs fichiers .parquet, il est plus sûr de sélectionner le répertoire pour la lecture et de cocher l’option **Parquet Dataset** (Jeu de données Parquet). Ainsi, PyArrow lira la totalité du dossier plutôt que chaque fichier. Cela garantit la prise en charge de la lecture de méthodes de stockage Parquet sur disque plus compliquées (par exemple le partitionnement de dossier).

L’exécution avec montée en puissance repose sur les capacités de lecture Parquet de Spark et prend en charge les fichiers individuels et les dossiers.

#### <a name="options"></a>Options
*Parquet dataset* (Jeu de données Parquet) : cette option détermine si Azure Machine Learning Workbench utilise le mode décoché ou le mode coché. Le mode décoché développe un répertoire donné et tente de lire chaque fichier qu’il contient. Le mode coché traite le répertoire comme jeu de données complet et laisse PyArrow déterminer la meilleure façon d’interpréter les fichiers.


## <a name="locations"></a>Emplacements
### <a name="local"></a>Local
Emplacement de stockage réseau mappé ou disque dur local

### <a name="azure-blob-storage"></a>Stockage d'objets blob Azure
Nécessite un abonnement Azure.

