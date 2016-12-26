---
title: "Utiliser la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio | Microsoft Docs"
description: "Découvrez comment utiliser la vue d’exécution du vertex pour examiner des travaux Data Lake Analytics."
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ac5a64b759376c06e058ae015b73f1b73b7d1e7b
ms.openlocfilehash: b2acf4fd95c1611ecd580b508339d1305010fdd8


---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Utilisation de la vue d’exécution du vertex dans Azure Data Lake Tools pour Visual Studio
Découvrez comment utiliser la vue d’exécution du vertex pour examiner des travaux Data Lake Analytics.

## <a name="prerequisites"></a>Composants requis
* Quelques connaissances élémentaires sur l’utilisation de Data Lake Tools pour Visual Studio afin de développer un script U-SQL.  Consultez [Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Affichage de la vue d’exécution du vertex
Pour un travail spécifique, vous pouvez cliquer sur le lien « Vue d’exécution du vertex » dans le coin inférieur gauche. Vous pouvez être invité à charger d’abord les profils et cette opération peut prendre un certain temps en fonction de la connectivité réseau.

![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Présentation de la vue d’exécution du vertex
Une fois affichée, la vue d’exécution du vertex se compose de trois parties :

![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

* Sélecteur de vertex : le sélecteur de vertex apparaît à gauche.  Vous pouvez sélectionner les vertex par fonctionnalités (par exemple les 10 premières données lues, ou par étape).
  
    Choisir les vertex sur le chemin critique est l’un des filtres les plus utilisés. Le chemin critique représente le plus long chemin d’un travail SQL-U. Il permet d’optimiser vos travaux en identifiant les vertex qui prennent le plus de temps.
* Le volet central supérieur :
  
    ![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)
  
    Cette vue affiche également l’état d’exécution de tous les vertex. Elle convertit le temps en fonction de votre ordinateur local et présente les divers états en différentes couleurs.
* Le volet central inférieur :
  
    ![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)
  
  * Nom du processus : le nom de l’instance de vertex. Il est composé de différentes parties de StageName|VertexName|VertexRunInstance. Par exemple, le vertex SV7_Split[62].v1 représente la seconde instance en cours d’exécution (.v1, index commençant à 0) du vertex numéro 62 de l’étape SV7_Split.
  * Total des données lues/écrites : données qui ont été lues/écrites par ce vertex.
  * État/Statut de sortie : état final du vertex terminé.
  * Code de sortie/type d’échec : erreur en cas d’échec du vertex.
  * Raison de la création : pourquoi le vertex a été créé.
  * Latence de la ressource/latence du processus/latence de la file d’attente PN : délai pendant lequel le vertex attend des ressources, délai de traitement des données et temps de présence dans la file d’attente.
  * GUID du processus/créateur : GUID du vertex en cours d’exécution ou de son créateur.
  * Version : la énième instance du vertex en cours d’exécution (le système peut planifier de nouvelles instances d’un vertex pour plusieurs raisons, par exemple en cas de basculement, de redondance du calcul, etc..)
  * Heure de création de la version.
  * Heure de début de création du processus/Heure de mise en file d’attente du processus/Heure de début du processus/Heure de fin du processus : début du processus de création du vertex ; début du processus de mise en file d’attente du vertex ; début du processus d’un certain vertex ; fin d’un certain vertex.

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir une vue d'ensemble de Data Lake Analytics, consultez [Vue d'ensemble de Data Lake Analytics Azure](data-lake-analytics-overview.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour consigner les informations de diagnostic, consultez [Accès aux journaux de diagnostic d’Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour afficher les détails d’un travail, consultez [Utilisation de l’Explorateur de travaux et de la Vue des travaux pour les travaux Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
* Pour découvrir les outils Data Lake pour Visual Studio Code, consultez la page [Use the Azure Data Lake Tools for Visual Studio Code (Utiliser les outils Azure Data Lake pour Visual Studio Code)](data-lake-analytics-data-lake-tools-for-vscode.md).



<!--HONumber=Nov16_HO4-->


