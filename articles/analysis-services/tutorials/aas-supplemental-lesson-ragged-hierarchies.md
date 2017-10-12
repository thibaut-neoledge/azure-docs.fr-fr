---
title: "Leçon supplémentaire du didacticiel Azure Analysis Services : Hiérarchies déséquilibrées | Microsoft Docs"
description: "Décrit comment corriger les hiérarchies déséquilibrées dans le didacticiel Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.openlocfilehash: d34b2123153406640cf03bc9f57efa557af4cfaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="supplemental-lesson---ragged-hierarchies"></a>Leçon supplémentaire – Hiérarchies déséquilibrées

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Dans cette leçon supplémentaire, vous allez résoudre un problème courant se produisant lors de l’ajout d’un tableau croisé dynamique sur des hiérarchies qui contiennent des valeurs vides (membres) à différents niveaux. Par exemple, une organisation où un cadre a à la fois des responsables de département et des non-cadres comme collaborateurs. Ou bien des hiérarchies géographiques composées des éléments Pays-Région-Ville, où certaines villes n’ont pas d’état ou de région parent, par exemple Washington D.C. ou la Cité du Vatican. Quand une hiérarchie a des membres vides, elle descend souvent à des niveaux différents ou déséquilibrés.

![aas-lesson-detail-ragged-hierarchies-table](../tutorials/media/aas-lesson-detail-ragged-hierarchies-table.png)

Les modèles tabulaires de niveau de compatibilité 1400 disposent d’une propriété **Masquer les membres** supplémentaire pour les hiérarchies. Le paramètre **Par défaut** suppose qu’il n’y a aucun membre vide à aucun niveau. Le paramètre **Masquer les membres vides** exclut les membres vides de la hiérarchie quand ils sont ajoutés à un tableau croisé dynamique ou à un rapport.  
  
Durée estimée pour suivre cette leçon : **20 minutes**  
  
## <a name="prerequisites"></a>Prérequis  
Cette rubrique de leçon supplémentaire fait partie d’un didacticiel de modélisation tabulaire. Avant d’effectuer les tâches de cette leçon supplémentaire, vous devez avoir effectué toutes les leçons précédentes ou disposer d’un exemple de projet de modèle de ventes sur Internet Adventure Works. 

Si vous avez créé le projet de ventes sur Internet AW dans le cadre du didacticiel, votre modèle ne contient encore aucune donnée ou hiérarchie déséquilibrée. Pour suivre cette leçon supplémentaire, vous devez d’abord créer le problème en ajoutant des tables supplémentaires, puis créer des relations, des colonnes calculées, une mesure et une hiérarchie d’organisation. Cette partie prend environ 15 minutes. Ensuite, vous allez résoudre le problème en quelques minutes.  

## <a name="add-tables-and-objects"></a>Ajouter des tables et des objets
  
### <a name="to-add-new-tables-to-your-model"></a>Pour ajouter de nouvelles tables à votre modèle
  
1.  Dans l’Explorateur de modèles tabulaires, développez **Sources de données**, puis cliquez avec le bouton droit sur votre connexion > **Importer de nouvelles tables**.
  
2.  Dans le navigateur, sélectionnez **DimEmployee** et **FactResellerSales**, puis cliquez sur **OK**.

3.  Dans l’éditeur de requête, cliquez sur **Importer**.

4.  Créez les [relations](../tutorials/aas-lesson-4-create-relationships.md) suivantes :

    | Table 1           | Colonne       | Direction du filtre   | Table 2     | Colonne      | Actif |
    |-------------------|--------------|--------------------|-------------|-------------|--------|
    | FactResellerSales | OrderDateKey | Par défaut            | DimDate     | Date        | Oui    |
    | FactResellerSales | DueDate      | Par défaut            | DimDate     | Date        | Non     |
    | FactResellerSales | ShipDateKey  | Par défaut            | DimDate     | Date        | Non     |
    | FactResellerSales | ProductKey   | Par défaut            | DimProduct  | ProductKey  | Oui    |
    | FactResellerSales | EmployeeKey  | Vers les deux tables | DimEmployee | EmployeeKey | Oui    |

5. Dans la table **DimEmployee**, créez les [colonnes calculées](../tutorials/aas-lesson-5-create-calculated-columns.md) suivantes : 

    **Path** 
    ```
    =PATH([EmployeeKey],[ParentEmployeeKey])
    ```

    **FullName** 
    ```
    =[FirstName] & " " & [MiddleName] & " " & [LastName]
    ```

    **Level1** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,1)) 
    ```

    **Level2** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,2)) 
    ```

    **Level3** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,3)) 
    ```

    **Level4** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,4)) 
    ```

    **Level5** 
    ```
    =LOOKUPVALUE(DimEmployee[FullName],DimEmployee[EmployeeKey],PATHITEM([Path],1,5)) 
    ```

6.  Dans la table **DimEmployee**, créez une [hiérarchie](../tutorials/aas-lesson-9-create-hierarchies.md) nommée **Organization**. Ajoutez les colonnes suivantes, dans l’ordre : **Level1**, **Level2**, **Level3**, **Level4**, **Level5**.

7.  Dans la table **FactResellerSales**, créez la [mesure](../tutorials/aas-lesson-6-create-measures.md) suivante :

    ```
    ResellerTotalSales:=SUM([SalesAmount])
    ```

8.  Utilisez [Analyser dans Excel](../tutorials/aas-lesson-12-analyze-in-excel.md) pour ouvrir Excel et créer automatiquement un tableau croisé dynamique.

9.  Dans **Champs de tableau croisé dynamique**, ajoutez la hiérarchie **Organization** de la table **DimEmployee** à **Lignes** et la mesure **ResellerTotalSales** de la table **FactResellerSales** à **Valeurs**.

    ![aas-lesson-detail-ragged-hierarchies-pivottable](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable.png)

    Comme vous pouvez le voir dans le tableau croisé dynamique, la hiérarchie affiche les lignes qui sont déséquilibrées. Il y a de nombreuses lignes où des membres vides sont affichés.

## <a name="to-fix-the-ragged-hierarchy-by-setting-the-hide-members-property"></a>Pour corriger la hiérarchie déséquilibrée en définissant la propriété Masquer les membres

1.  Dans l’**Explorateur de modèles tabulaires**, développez **Tables** > **DimEmployee** > **Hiérarchies** > **Organization**.

2.  Dans **Propriétés** > **Masquer les membres**, sélectionnez **Masquer les membres vides**. 

    ![aas-lesson-detail-ragged-hierarchies-hidemembers](../tutorials/media/aas-lesson-detail-ragged-hierarchies-hidemembers.png)

3.  De retour dans Excel, actualisez le tableau croisé dynamique. 

    ![aas-lesson-detail-ragged-hierarchies-pivottable-refresh](../tutorials/media/aas-lesson-detail-ragged-hierarchies-pivottable-refresh.png)

    Voilà qui est beaucoup mieux !

## <a name="see-also"></a>Voir aussi   
[Leçon 9 : Créer des hiérarchies](../tutorials/aas-lesson-9-create-hierarchies.md)  
[Leçon supplémentaire – Sécurité dynamique](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[Leçon supplémentaire – Lignes de détails](../tutorials/aas-supplemental-lesson-detail-rows.md)  