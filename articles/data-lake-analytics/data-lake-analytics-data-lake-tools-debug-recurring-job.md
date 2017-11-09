---
title: "Comment résoudre les problèmes liés à une tâche périodique inhabituelle | Microsoft Docs"
description: "Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio pour le débogage local d’une tâche périodique."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>Comment résoudre les problèmes liés à une tâche périodique inhabituelle

Dans ce document, nous allons expliquer comment utiliser [Azure Data Lake Tools pour Visual Studio](http://aka.ms/adltoolsvs) afin de résoudre les problèmes liés à une tâche périodique. Pour en savoir plus sur le pipeline et les tâches périodiques, cliquez [ici](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/).
Les tâches périodiques partagent généralement la même logique de requête et des données d’entrée similaires. Par exemple, une tâche périodique s’exécute chaque lundi matin à 8 h 00. Elle a pour but de compter les utilisateurs actifs la semaine dernière. Les scripts de ces tâches partagent un modèle de script contenant la logique de requête, et les entrées de ces tâches correspondent aux données d’utilisation de la semaine dernière. En partageant la même logique de requête et des entrées similaires, les performances de ces tâches sont généralement semblables et stables. Si l’une de vos tâches périodiques présente soudain des performances anormales, échoue ou ralentit beaucoup, vous voudrez peut-être :

1.  Afficher les rapports statistiques des exécutions de préversions de la tâche périodique pour voir ce qui est arrivé.
2.  Comparer la tâche anormale à une tâche normale pour déterminer ce qui a changé.

**La vue des tâches associées** dans Azure Data Lake Tools pour Visual Studio vous permet d’accélérer la résolution des problèmes dans les deux cas.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Étape 1 : Rechercher les tâches périodiques et ouvrir la vue des tâches associées

Pour utiliser la vue des tâches associées afin de résoudre un problème lié à une tâche périodique, vous devez tout d’abord rechercher la tâche périodique dans Visual Studio, puis ouvrir la vue des tâches associées.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Cas n°1 : Vous disposez de l’URL de la tâche périodique

Sous **Outils > Data Lake > Vue des travaux**, vous pouvez coller l’URL de la tâche pour ouvrir la vue des travaux dans Visual Studio, et utiliser l’option d’affichage des tâches associées pour ouvrir la vue des tâches associées.

![Afficher les tâches associées dans Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Cas n°2 : Vous disposez du pipeline pour la tâche périodique, mais pas de l’URL

Dans Visual Studio, vous pouvez ouvrir l’explorateur de pipelines via **Explorateur de serveurs > votre compte Data Lake Analytics > Pipelines** (si ce nœud n’apparaît pas dans l’Explorateur de serveurs, récupérez la dernière version de l’outil [ici](http://aka.ms/adltoolsvs)). Dans l’explorateur de pipelines, tous les pipelines du compte ADLA sont répertoriés à gauche. Vous pouvez développer les pipelines pour rechercher toutes les tâches périodiques, puis cliquer sur celle qui rencontre des problèmes pour que la vue des tâches associées s’ouvre à droite.

![Afficher les tâches associées dans Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Afficher les tâches associées dans Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>Étape 2 : Analyser le rapport statistique

Un résumé et un rapport statistique sont affichés en haut de la vue des tâches associées, par le biais de laquelle vous pouvez obtenir la cause potentielle de l’anomalie. 

1.  Vous devez d’abord rechercher la tâche anormale dans le rapport. L’axe X affiche l’heure de soumission des tâches, ce qui vous permet de repérer la tâche anormale.
2.  Procédez comme suit pour consulter les statistiques et obtenir des informations sur la tâche anormale et les solutions possibles.

![Afficher les tâches associées dans Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>Étape 3 : Comparer la tâche périodique anormale à une tâche normale

Vous trouverez toutes les tâches périodiques soumises via la liste de tâches en bas de la vue des tâches associées. En cliquant avec le bouton droit, vous pouvez comparer la tâche anormale avec une tâche précédente normale, afin d’obtenir plus d’informations et des solutions potentielles dans la vue de comparaison des tâches.

![Afficher les tâches associées dans Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Généralement, vous devez prêter attention aux grandes différences entre ces 2 tâches, car il s’agit probablement de la cause des problèmes de performances. Vous pouvez également procéder comme suit pour effectuer une vérification plus poussée.

![Afficher les tâches associées dans Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Étapes suivantes

* [Comment déboguer et résoudre des problèmes d’asymétrie des données](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Comment déboguer un échec de tâche U-SQL pour l’erreur de code définie par l’utilisateur](data-lake-analytics-debug-u-sql-jobs.md)