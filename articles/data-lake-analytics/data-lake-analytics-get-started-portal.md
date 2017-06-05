---
title: "Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment utiliser le portail Azure pour créer un compte Data Lake Analytics, créer un travail Data Lake Analytics avec U-SQL et envoyer le travail. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: eb85d8ef6b29605d7e26b0d2139a4a95c35141fb
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-portal"></a>Didacticiel : Prise en main du service Azure Data Lake Analytics à l’aide du portail Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser le portail Azure pour créer des comptes Azure Data Lake Analytics, définir des travaux dans [U-SQL](data-lake-analytics-u-sql-get-started.md) et envoyer des travaux au service Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Composants requis

Avant de commencer ce didacticiel, vous devez disposer d’un **abonnement Azure**. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake

À présent, vous allez créer un compte Data Lake Analytics et un compte Data Lake Store simultanément.  Cette étape est simple et ne prend qu’environ 60 pour se terminer.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Nouveau** >  **Intelligence + analyse** > **Data Lake Analytics**.
3. Sélectionnez des valeurs pour les éléments suivants :
   * **Nom** : donnez un nom à votre compte Data Lake Analytics (utilisez uniquement des lettres minuscules et des chiffres).
   * **Abonnement**: choisissez l’abonnement Azure utilisé pour le compte Analytics.
   * **Groupe de ressources**. Sélectionnez un groupe de ressources Azure existant ou créez-en un.
   * **Emplacement**. Sélectionnez un centre de données Azure pour le compte Data Lake Analytics.
   * **Data Lake Store** : suivez les instructions pour créer un compte Data Lake Store ou sélectionnez un compte existant. 
4. Si vous le souhaitez, sélectionnez un niveau tarifaire pour votre compte Data Lake Analytics.
5. Cliquez sur **Create**. 

## <a name="create-and-submit-data-lake-analytics-jobs"></a>Création et envoi de travaux Data Lake Analytics
Une fois que vous avez préparé la source de données, vous pouvez démarrer le développement d'un script U-SQL.  

**Pour soumettre un travail**

1. À partir du compte Data Lake Analytics, cliquez sur **Nouveau travail**.
2. Entrez le **Nom du travail**et le script U-SQL suivant :

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
```


Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv à l’aide d’**Outputters.Csv()**.

1. Cliquez sur **Envoyer le travail**.   
2. Attendez que l'état du travail passe à **Réussi**.
3. Si le travail échoue, consultez [Dépanner les travaux Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).
4. Cliquez sur l’onglet **Sortie**, puis sur `SearchLog-from-Data-Lake.csv`. 

## <a name="see-also"></a>Voir aussi

* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).

