---
title: "Destinations/sorties de données prises en charge disponibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des destinations/sorties prises en charge disponibles pour la préparation des données Azure ML."
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
ms.date: 09/07/2017
ms.openlocfilehash: 415ceba02eb3c8da3de5ab3aa6980fbe5bae2db9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-exports-for-this-preview"></a>Exportations de données prises en charge pour cette version d’évaluation 
Il est possible d’exporter vers différents formats. Ces formats peuvent être utilisés pour conserver les résultats intermédiaires de la préparation des données avant leur intégration au reste du flux de travail Machine Learning.

## <a name="types"></a>Types 
### <a name="csv-file"></a>Fichier CSV 
Écrivez un fichier de valeurs séparées par des virgules (CSV) dans le stockage.

#### <a name="options"></a>Options
- Fins de ligne
- Remplacer les valeurs NULL avec
- Remplacer les erreurs avec 
- Séparateur


### <a name="parquet"></a>Parquet ###
Écrivez un jeu de données dans le stockage au format Parquet.

Le format Parquet peut prendre différentes formes dans le stockage. Pour les jeux de données de petite taille, un seul fichier « .parquet » est parfois utilisé. Différentes bibliothèques Python prennent en charge la lecture/écriture dans des fichiers « .parquet » uniques. Pour le moment, AMLWB s’appuie sur la bibliothèque Python PyArrow pour l’écriture de Parquet lors de l’utilisation « interactive » locale. Cela signifie que le fichier .parquet unique est actuellement le seul format de sortie Parquet pris en charge lors d’une utilisation interactive locale.

Au cours des exécutions évolutives (sur Spark), AMLWB s’appuie sur les fonctionnalités de lecture/écriture Parquet de Spark. Le format de sortie par défaut de Spark pour Parquet (actuellement le seul pris en charge) relève d’une structure semblable à un jeu de données HIVE. C’est-à-dire, un dossier contenant plusieurs fichiers « .parquet » étant chacun une partition d’un plus grand jeu de données. 

#### <a name="caveats"></a>Mises en garde ####
Le format Parquet est relativement récent et son implémentation comporte certaines incohérences entre les différentes bibliothèques. Par exemple, Spark impose des restrictions quant à la validité des caractères à utiliser dans les noms de colonne lors de l’écriture dans Parquet, ce qui n’est pas le cas de PyArrow. Tous les caractères du jeu « ,;{}()\\n\\t= » ne peuvent pas apparaître dans un nom de colonne.

>[!NOTE]
>Pour garantir la compatibilité avec Spark, à chaque fois que des données sont écrites dans Parquet, les occurrences de ces caractères dans les noms de colonne sont remplacées par « _ » (trait de soulignement).**

>[!NOTE]
>Pour assurer la cohérence sur site et après toute mise à l’échelle, les noms de colonnes des données écrites dans Parquet via l’application, Python ou Spark, sont expurgés pour garantir la compatibilité avec Spark. Pour garantir la validité des noms de colonnes attendus lors de l’écriture dans Parquet, les caractères du jeu de caractères non valide Spark doivent être supprimés des colonnes avant l’écriture.



## <a name="locations"></a>Emplacements 
### <a name="local"></a>Local 
Emplacement de stockage réseau mappé ou disque dur local

### <a name="azure-blob"></a>Objet blob Azure 
Stockage Azure (BLOB), nécessite un abonnement Azure

