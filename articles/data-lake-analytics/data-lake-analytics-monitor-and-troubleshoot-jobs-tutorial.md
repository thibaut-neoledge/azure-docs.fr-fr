---
title: "Dépanner les travaux Azure Data Lake Analytics à l’aide du portail Azure | Microsoft Docs"
description: "Apprenez à utiliser le portail Azure afin de dépanner les travaux Data Lake Analytics. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 194b5d79505afbfd0208f63dd182a0e03227ba69
ms.openlocfilehash: 1915201480f57c5f132e13bb5ce4283fbea975fe


---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Dépanner les travaux Azure Data Lake Analytics à l’aide du portail Azure
Apprenez à utiliser le portail Azure afin de dépanner les travaux Data Lake Analytics.

Dans ce didacticiel, vous allez identifier un problème de fichier source manquant et utiliser le portail Azure pour résoudre le problème.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Connaissances de base du processus de travail Analytique Data Lake**. Consultez [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).
* **Un compte Analytique Data Lake**. Consultez [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account).
* **Copier les données d’exemple dans le compte Data Lake Store par défaut**.  Voir [Préparer les données source](data-lake-analytics-get-started-portal.md#prepare-source-data)

## <a name="submit-a-data-lake-analytics-job"></a>Envoyer le travail Analytique Data Lake
Maintenant, vous allez créer un travail U-SQL avec un nom de fichier erroné.  

**Pour soumettre le travail**

1. À partir du portail Azure, cliquez sur **Microsoft Azure** dans le coin supérieur gauche.
2. Cliquez sur la vignette indiquant le nom de votre compte Analytique Data Lake.  Elle a été épinglée ici lorsque le compte a été créé.
   Si le compte n’est pas épinglé ici, consultez [Ouvrir un compte Analytics à partir du portail](data-lake-analytics-manage-use-portal.md#access-adla-account).
3. Cliquez sur **Nouveau travail** dans le menu du haut.
4. Saisissez le nom du travail et le script U-SQL suivant :

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    Le fichier source défini dans le script est **/Samples/Data/SearchLog.tsv1**, alors qu’il devrait s’agir de **/Samples/Data/SearchLog.tsv**.
5. Cliquez sur **Soumettre le travail** en haut. Un nouveau volet Détails du travail s’ouvre. Dans la barre de titre, l’état du travail est affiché. L’achèvement prend quelques minutes. Vous pouvez cliquer sur **Actualiser** pour obtenir l’état le plus récent.
6. Attendez que l’état du travail passe à **Échec**.  Si le travail est **Réussi**, c’est parce que vous n’avez pas supprimé le dossier /Samples. Consultez la section **Configuration requise** au début du didacticiel.

Vous vous demandez peut-être - pourquoi il prend autant de temps pour un petit projet.  N’oubliez pas qu’Analytique Data Lake est conçu pour traiter des données volumineuses.  Il se distingue particulièrement en cas de traitement d’une grande quantité de données avec son système distribué.

Supposons que vous avez soumis le travail et fermé le portail.  Dans la section suivante, vous allez apprendre à résoudre les problèmes de la tâche.

## <a name="troubleshoot-the-job"></a>Résolution des problèmes
Dans la dernière section, vous avez envoyé un travail et ce dernier a échoué.  

**Pour voir tous les travaux**

1. À partir du portail Azure, cliquez sur **Microsoft Azure** dans le coin supérieur gauche.
2. Cliquez sur la vignette indiquant le nom de votre compte Analytique Data Lake.  Le résumé du travail est affiché sur la vignette **Gestion des tâches** .

    ![Gestion du travail Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    La gestion des tâches vous offre un aperçu de l’état du travail. Notez qu’il s’agit d’une tâche ayant échoué.
3. Cliquez sur la vignette **Gestion de la tâche** pour rechercher les travaux. Les travaux sont classés dans les catégories **En cours**, **En file d’attente** et **Terminé**. Vous devez voir le travail qui a échoué dans la section **Terminé** . Il doit être le premier sur la liste. Lorsque vous avez un grand nombre de travaux, vous pouvez cliquer sur **Filtrer** pour vous aider à localiser les travaux.

    ![Travail de filtre d’Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Cliquez sur le travail ayant échoué dans la liste pour ouvrir les détails du travail dans un nouveau panneau :

    ![Travail d’échec d’Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Remarquez le bouton **Renvoyer** . Après avoir résolu le problème, vous pouvez renvoyer le travail.
5. Cliquez sur la partie mise en évidence de la capture d’écran précédente pour ouvrir les détails de l’erreur.  Le résultat suivant doit s’afficher :

    ![Détails sur le travail d’échec d’Analytique Data Lake Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Elle vous indique que le dossier source est introuvable.
6. Cliquez sur **Dupliquer le Script**.
7. Mettez à jour le chemin d’accès **FROM** pour obtenir ce qui suit :

    « Samples/Data/SearchLog.tsv »
8. Cliquez sur **Envoyer le travail**.

## <a name="see-also"></a>Voir aussi
* [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md)
* [Prise en main d’Analytique Data Lake à l’aide d’Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Prise en main d’Analytique Data Lake Azure et U-SQL à l’aide de Visual Studio.](data-lake-analytics-u-sql-get-started.md)
* [Gestion d'Azure Data Lake Analytics à l'aide du portail Azure](data-lake-analytics-manage-use-portal.md)



<!--HONumber=Dec16_HO2-->


