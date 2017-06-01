---
title: "Présentation du catalogue U-SQL Azure Data Lake Analytics | Microsoft Docs"
description: "Présentation du catalogue U-SQL Azure Data Lake Analytics"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 55fef96f-e941-4d09-af5e-dd7c88c502b2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: ef766161f53b92003c266f26af10a37790d8fad8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="use-azure-data-lake-analytics-u-sql-catalog"></a>Utilisation du catalogue Azure Data Lake Analytics (U-SQL)
Le catalogue U-SQL est utilisé pour structurer les données et le code afin que les scripts U-SQL puissent les partager. Le catalogue permet les meilleures performances possibles avec les données comprises dans Azure Data Lake.

Chaque compte Azure Data Lake Analytics a exactement un catalogue U-SQL associé. Vous ne pouvez pas supprimer le catalogue U-SQL. Actuellement, les catalogues U-SQL ne peuvent pas se partager entre les comptes Data Lake Store.

Chaque catalogue U-SQL contient une base de données appelée **Master**. Vous ne pouvez pas supprimer la base de données Master.  Chaque catalogue U-SQL peut contenir plusieurs bases de données supplémentaires.

La base de données U-SQL contient les éléments suivants :

* Assemblys : pour le partage du code .NET entre les scripts U-SQL.
* Fonctions tables : pour le partage du code U-SQL entre les scripts U-SQL.
* Tables : pour le partage des données entre les scripts U-SQL.
* Schémas : pour le partage des schémas de table entre les scripts U-SQL.

## <a name="manage-catalogs"></a>Gérer les catalogues
Chaque compte Azure Data Lake Analytics dispose d’un compte Azure Data Lake Store par défaut qui lui est associé. Ce compte Data Lake Store est désigné en tant que compte Data Lake Store par défaut. Le catalogue U-SQL est stocké dans le compte Data Lake Store par défaut sous le dossier /catalog. Ne supprimez aucun fichier dans le dossier /catalog.

### <a name="use-azure-portal"></a>Utiliser le portail Azure
Consultez [Gérer Data Lake Analytics à l’aide du portail](data-lake-analytics-manage-use-portal.md#manage-data-lake-analytics-accounts).

### <a name="use-data-lake-tools-for-visual-studio"></a>Utilisez les outils Data Lake pour Visual Studio.
Vous pouvez utiliser les outils Data Lake pour Visual Studio pour gérer le catalogue.  Pour plus d’informations sur les outils, consultez [Utilisation des outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

**Pour gérer le catalogue**

1. Ouvrez Visual Studio et connectez-vous à Azure. Pour obtenir les instructions, consultez [Connexion à Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure).
2. Ouvrez **l’Explorateur de serveurs** en appuyant sur **Ctrl+Alt+S**.
3. À partir de **l’Explorateur de serveurs**, développez successivement **Azure**, **Data Lake Analytics**, votre compte Data Lake Analytics, **Bases de données**, puis développez **master**.

    - Pour ajouter une nouvelle base de données, cliquez avec le bouton droit sur **Base de données**, puis cliquez sur **Créer une base de données**.
    - Pour ajouter un nouvel assembly, cliquez avec le bouton droit sur **Assemblys**, puis cliquez sur **Inscrire l’assembly**.
    - Pour ajouter un nouveau schéma, cliquez avec le bouton droit sur **Schémas**, puis cliquez sur **Créer un schéma**.
    - Pour ajouter une nouvelle table, cliquez avec le bouton droit sur **Tables**, puis cliquez sur **Créer une table**.
    - Pour ajouter une nouvelle fonction table, consultez [Développer des opérateurs définis par l’utilisateur U-SQL pour des tâches Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md).


![Parcourir les catalogues U-SQL Visual Studio](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)

## <a name="see-also"></a>Voir aussi
* Prise en main

  * [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
  * [Prise en main de Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [Prise en main de Data Lake Analytics à l'aide du Kit de développement logiciel (SDK) .NET Azure](data-lake-analytics-get-started-net-sdk.md)
  * [Développer des scripts U-SQL avec les outils Data Lake pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
  * [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
* U-SQL et développement

  * [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
  * [Utiliser les fonctions U-SQL dans les travaux d’analyse Azure Data Lake](data-lake-analytics-use-window-functions.md)
  * [Développer des opérateurs définis par l’utilisateur U-SQL pour des travaux Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* Gestion

  * [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
  * [Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
  * [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
* Didacticiel de bout en bout

  * [Utiliser les didacticiels interactifs Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
  * [Analyser les journaux des sites web à l’aide d’Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)

