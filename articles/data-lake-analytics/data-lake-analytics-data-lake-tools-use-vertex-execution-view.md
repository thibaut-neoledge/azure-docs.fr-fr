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
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: b788e7bc8ded86ebd49cc0be73e5b4e1bcbeaba3
ms.contentlocale: fr-fr
ms.lasthandoff: 06/20/2017


---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Utilisation de la vue d’exécution du vertex dans Azure Data Lake Tools pour Visual Studio
Découvrez comment utiliser la vue d’exécution du vertex pour examiner des travaux Data Lake Analytics.

## <a name="prerequisites"></a>Composants requis

Vous devez posséder des connaissances élémentaires sur l’utilisation de Data Lake Tools pour Visual Studio afin de développer un script U-SQL.  Consultez [Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Affichage de la vue d’exécution du vertex
Ouvrez une tâche U-SQL dans Data Lake Tools pour Visual Studio. Dans l’angle inférieur gauche, cliquez sur **Vue de l’exécution du vertex**. Vous pouvez être invité à charger d’abord les profils, et cette opération peut prendre un certain temps en fonction de la connectivité réseau.

![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Présentation de la vue d’exécution du vertex
La vue d’exécution du vertex comprend trois parties :

![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Le **Sélecteur de vertex**, situé sur la gauche, permet de sélectionner les vertex par fonctionnalités (par exemple, les 10 premières données lues, ou par étape). L’un des filtres les plus utilisés est celui qui permet d’afficher les **vertex situés sur un chemin critique**. Le **chemin critique** est la plus longue chaîne de vertex d’une tâche U-SQL. Le fait de comprendre le chemin critique vous permet d’optimiser vos tâches, car vous pouvez identifier les vertex qui prennent le plus de temps.
  
![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Le volet central supérieur affiche **l’état d’exécution de tous les vertex**.
  
![Vue d’exécution du vertex Data Lake Analytics Tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

Le volet central inférieur affiche des informations sur chaque vertex :
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
* Pour consigner les informations de diagnostic, consultez [Accès aux journaux de diagnostic d’Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour afficher les détails d’un travail, consultez [Utilisation de l’Explorateur de travaux et de la Vue des travaux pour les travaux Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)

