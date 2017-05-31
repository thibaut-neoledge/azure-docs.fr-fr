---
title: "Leçon supplémentaire du didacticiel Azure Analysis Services : Lignes de détails | Microsoft Docs"
description: "Explique comment créer une expression de lignes de détails dans le didacticiel Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 2058e74c1c2e719e879a3808d24790bfe86a9e1b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/05/2017

---
# <a name="supplemental-lesson---detail-rows"></a>Leçon supplémentaire – Lignes de détails

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Dans cette leçon supplémentaire, vous allez utiliser l’éditeur DAX pour définir une expression de lignes de détail personnalisée. Une expression de lignes de détail est une propriété sur une mesure qui fournit aux utilisateurs finaux des informations supplémentaires sur les résultats agrégées d’une mesure. 
  
Durée estimée pour suivre cette leçon : **10 minutes**  
  
## <a name="prerequisites"></a>Prérequis  
Cette rubrique de leçon supplémentaire fait partie d’un didacticiel de modélisation tabulaire. Avant d’effectuer les tâches de cette leçon supplémentaire, vous devez avoir effectué toutes les leçons précédentes ou disposer d’un exemple de projet de modèle de ventes sur Internet Adventure Works.  
  
## <a name="what-do-we-need-to-solve"></a>Quels problèmes devons-nous résoudre ?
Examinons les détails de notre mesure InternetTotalSales avant l’ajout d’expression de lignes de détail.

1.  Dans SSDT, cliquez sur le menu **Modèle** > **Analyser dans Excel** pour ouvrir Excel et créer un tableau croisé dynamique vide.
  
2.  Dans **Champs de tableau croisé dynamique**, ajoutez la mesure **InternetTotalSales** de la table FactInternetSales à **Valeurs**, **CalendarYear** de la table DimDate à **Colonnes** et **EnglishCountryRegionName** à **Lignes**. Notre tableau croisé dynamique nous indique maintenant les résultats agrégés de la mesure InternetTotalSales par région et par année. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. Dans le tableau croisé dynamique, double-cliquez sur une valeur agrégée pour une année et un nom de région. Ici, nous avons double-cliqué sur la valeur correspondant à l’Australie et à l’année 2014. Une nouvelle feuille contenant des données nombreuses mais pas vraiment utiles s’ouvre.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
Nous aimerions voir ici une table contenant des colonnes et des lignes de données qui contribuent au résultat agrégé de notre mesure InternetTotalSales. Pour ce faire, nous pouvons ajouter une expression de lignes de détail comme propriété de la mesure.

## <a name="add-a-detail-rows-expression"></a>Ajouter une expression de lignes de détail

#### <a name="to-create-a-detail-rows-expression"></a>Pour créer une expression de lignes de détail 
  
1. Dans SSDT, dans la grille de mesure de la table FactInternetSales, cliquez sur la mesure **InternetTotalSales**. 

2. Dans **Propriétés** > **Expression de lignes de détail**, cliquez sur le bouton de l’éditeur pour ouvrir l’éditeur DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. Dans l’éditeur DAX, entrez l’expression suivante :

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Cette expression indique que des noms, des colonnes et des résultats de mesure provenant de la table FactInternetSales et de tables associées sont retournés quand un utilisateur double-clique sur un résultat agrégé dans un tableau croisé dynamique ou un rapport.

4. De retour dans Excel, supprimez la feuille créée à l’étape 3, puis double-cliquez sur une valeur agrégée. Cette fois, avec une propriété d’expression de lignes de détail définie pour la mesure, une nouvelle feuille contenant des données beaucoup plus utiles s’ouvre.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Redéployez votre modèle.

  
## <a name="see-also"></a>Voir aussi  
[SELECTCOLUMNS, fonction (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Leçon supplémentaire – Sécurité dynamique](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Leçon supplémentaire – Hiérarchies déséquilibrées](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  

