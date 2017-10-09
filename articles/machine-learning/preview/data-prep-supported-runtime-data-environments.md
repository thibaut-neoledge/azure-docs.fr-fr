---
title: "Combinaisons d’environnements d’exécution et de données prises en charge pour la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit une liste complète des combinaisons prises en charge pour les différents runtimes et sources de données pour la préparation des données Azure ML."
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
ms.date: 09/15/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 02e0ca96bff7781c242b4c5e965b229065e71725
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="supported-matrix-for-this-release"></a>Matrice de prise en charge pour cette version 
Quand votre code charge des données à l’aide de Sources de données ou obtient un cadre de données Pandas ou Spark avec la Préparation des données, les combinaisons suivantes d’emplacements de données et d’environnements de calcul d’expérimentation sont prises en charge :

|     |Fichiers locaux  |Stockage Blob Azure  |Base de données SQL Server***  |
|---------|---------|---------|---------|---------|
|Python local    |     Pris en charge    |Non pris en charge         | Non pris en charge        |         |
|Python Docker (machine virtuelle Linux)     |Pris en charge dans les fichiers projet uniquement*         | Non pris en charge        |        Non pris en charge |         |
|PySpark Docker (machine virtuelle Linux)     |Pris en charge dans les fichiers projet uniquement*     |Pris en charge         | Pris en charge**        |         |
|Azure Data Science Virtual Machine Python     |Pris en charge dans les fichiers projet uniquement*         |Non pris en charge         |Non pris en charge         |         |
|Azure Data Science Virtual Machine PySPark     | Pris en charge dans les fichiers projet uniquement*        |Non pris en charge         |Non pris en charge         |         |
|Azure HDInsight PySpark     | Non pris en charge        |Pris en charge         |Pris en charge**         |         |
|Azure HDInsight Python     | Non pris en charge        | Non pris en charge        | Non pris en charge        |         |

À l’heure actuelle, Azure Data Lake Store n’est pris en charge pour aucune cible de calcul.

*Quand des chemins de fichiers locaux sont utilisés, les fichiers dans le projet sont copiés dans l’environnement de calcul puis lus à cet endroit. Les fichiers en dehors du projet ne sont pas copiés et les chemins ne seront plus résolus dans l’environnement de calcul. Utilisez la Substitution de source de données pour que votre code puisse utiliser un fichier local en cas d’exécution locale, puis basculer vers un objet blob de stockage Azure pour une configuration d’exécution différente. Vous pouvez également utiliser la prise en charge de l’échantillonnage sur les Sources de données pour gérer des exécutions sur des données volumineuses uniquement dans certaines configurations d’exécution.

**Utilise le pilote SQL Server JDBC Maven 6.2.1. Vous devez vérifier que ce package (ou un package compatible) est inclus dans votre fichier spark_dependencies.yml pour l’environnement de calcul.

**Prend en charge Azure SQL Database, Azure SQL Data Warehouse ou Microsoft SQL Server à condition que la base de données soit accessible à partir de l’environnement de calcul. 

