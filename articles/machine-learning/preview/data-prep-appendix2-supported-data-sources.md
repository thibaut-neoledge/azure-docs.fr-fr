---
title: "Sources de données prises en charge disponibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des sources de données prises en charge disponibles pour la préparation des données Azure ML."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0a77b31e07f118155094fab4d3393ffdb37c9c6f
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="supported-data-sources-for-this-release"></a>Sources de données prises en charge pour cette version 
Le document suivant présente la liste des sources de données actuellement prises en charge dans la préparation des données.

Les sources de données prises en charge pour cette version sont listées ci-dessous.

## <a name="types"></a>Types 
### <a name="directory-vs-file"></a>Répertoire et fichier
Fichiers/répertoires : choisissez un seul fichier et lisez-le dans la Préparation des données. Le type de fichier est analysé afin de déterminer les paramètres par défaut pour la connexion de fichiers affichée dans l’écran suivant. Choisissez un répertoire ou un ensemble de fichiers contenus dans un répertoire (le sélecteur de fichiers permet d’effectuer plusieurs sélections). Les deux approches entraînent la lecture des fichiers en tant que flux de données unique, et les fichiers sont ajoutés les uns aux autres (les en-têtes sont supprimés si nécessaire).

Les types de fichiers pris en charge sont les suivants :
- Délimité (csv, tsv, txt, etc.) 
- Largeur fixe
- Texte brut
- Fichier JSON

### <a name="csv-file"></a>Fichier CSV
Lire un fichier de valeurs séparées par des virgules à partir du stockage

#### <a name="options"></a>Options
- Séparateur
- Commentaire
- En-têtes
- Symbole décimal
- Encodage de fichier
- Lignes à ignorer

### <a name="tsv-file"></a>Fichier TSV
Lire un fichier de valeurs séparées par des tabulations à partir du stockage

#### <a name="options"></a>Options
- Commentaire
- En-têtes
- Encodage de fichier
- Lignes à ignorer

### <a name="excel-xlsxlsx"></a>Excel (.xls/xlsx)
Lire un fichier Excel, une feuille à la fois, en spécifiant le nom ou le numéro de la feuille

#### <a name="options"></a>Options
- Nom/numéro de la feuille
- En-têtes
- Lignes à ignorer

### <a name="json-file"></a>Fichier JSON
Lire un fichier JSON à partir du stockage (notez que le fichier est « aplati » lors de la lecture)

#### <a name="options"></a>Options
- Aucune

### <a name="parquet"></a>Parquet
Lire un jeu de données Parquet, fichier ou dossier unique.

Le format Parquet peut prendre différentes formes dans le stockage. Pour les petits jeux de données, un seul fichier « .parquet » est parfois utilisé. Différentes bibliothèques Python prennent en charge la lecture/écriture dans des fichiers « .parquet » uniques. Pour le moment, AMLWB s’appuie sur la bibliothèque Python PyArrow pour la lecture de Parquet lors de l’utilisation « interactive » locale. Il prend en charge les fichiers « .parquet » uniques (à condition qu’ils soient écrits comme tels, et non dans le cadre d’un jeu de données plus grand), ainsi que les jeux de données Parquet. Un jeu de données Parquet est une collection de plusieurs fichiers « .parquet » représentant chacun une partition plus petite d’un plus grand jeu de données. Les jeux de données sont généralement contenus dans un dossier, et constituent le format de sortie Parquet par défaut pour les plateformes courantes telles que Spark et Hive.

>[!NOTE]
>Lors de la lecture de données Parquet qui se trouvent dans un dossier contenant plusieurs fichiers « .parquet », il est plus sûr de sélectionner le répertoire pour la lecture et de cocher l’option « Parquet Dataset » (Jeu de données Parquet). Ainsi, PyArrow lira la totalité du dossier plutôt que chacun des fichiers, ce qui garantira la prise en charge de la lecture de méthodes de stockage Parquet sur disque plus compliquées (par exemple le partitionnement de dossier).**

L’exécution avec montée en puissance repose sur les capacités de lecture Parquet de Spark, et prend en charge les fichiers individuels et les dossiers, comme en mode interactif local.

#### <a name="options"></a>Options
- Jeu de données Parquet
  - Cette option détermine si AMLWB étend un répertoire donné et tente de lire chaque fichier individuellement (mode décoché), ou s’il traite le répertoire comme jeu de données complet et laisse PyArrow déterminer la meilleure façon d’interpréter les fichiers (mode coché).


## <a name="locations"></a>Emplacements
### <a name="local"></a>Local
Disque dur local ou emplacement de stockage réseau mappé

### <a name="azure-blob"></a>Objet blob Azure
Azure Storage (BLOB) nécessite un abonnement Azure


