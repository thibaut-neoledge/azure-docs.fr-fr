---
title: "Leçon 4 du didacticiel Azure Analysis Services : Créer des relations | Microsoft Docs"
description: "Explique comment créer des relations dans le projet du didacticiel Azure Analysis Services."
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
ms.openlocfilehash: 2d1cf3761a59350fde9bbb0ac99b6eb96261a322
ms.contentlocale: fr-fr
ms.lasthandoff: 05/05/2017

---
# <a name="lesson-4-create-relationships"></a>Leçon 4 : Créer des relations

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

Dans cette leçon, vous allez vérifier les relations qui ont été créées automatiquement lorsque vous avez importé les données, et vous allez ajouter des relations entre différentes tables. Une relation est une connexion entre deux tables qui définit la manière dont les données de ces tables doivent être mises en corrélation. Par exemple, les tables DimProduct et DimProductSubcategory ont une relation basée sur le fait que chaque produit appartient à une sous-catégorie. Pour plus d’informations, consultez [Relations](https://docs.microsoft.com/sql/analysis-services/tabular-models/relationships-ssas-tabular).
  
Durée estimée pour suivre cette leçon : **10 minutes**  
  
## <a name="prerequisites"></a>Prérequis  
Cette rubrique fait partie d’un didacticiel de modélisation tabulaire, qui doit être suivi dans l’ordre prévu. Avant d’effectuer les tâches de cette leçon, vous devez avoir terminé la leçon précédente : [Leçon 3 : Marquer en tant que Table de dates](../tutorials/aas-lesson-3-mark-as-date-table.md). 
  
## <a name="review-existing-relationships-and-add-new-relationships"></a>Vérifier les relations existantes et ajouter de nouvelles relations  
Lorsque vous avez importé des données à l’aide de la fonctionnalité Obtenir des données, vous avez obtenu sept tables de la base de données AdventureWorksDW2014. En règle générale, lorsque vous importez des données à partir d’une source relationnelle, les relations existantes sont importées automatiquement avec les données. Toutefois, avant de poursuivre avec la création de votre modèle, vous devez vérifier que les relations entre les tables ont été créées correctement. Pour ce didacticiel, vous allez également ajouter trois nouvelles relations.  
  
#### <a name="to-review-existing-relationships"></a>Pour vérifier les relations existantes  
  
1.  Cliquez sur le menu **Modèle** > **Vue du modèle** > **Vue de diagramme**.  

    Le Générateur de modèles apparaît maintenant dans la vue de diagramme, sous la forme d’un graphique montrant toutes les tables que vous avez importées, reliées entre elles par des lignes. Ces lignes indiquent les relations qui ont été créées automatiquement lorsque vous avez importé les données.
    
    ![aas-lesson4-diagram](../tutorials/media/aas-lesson4-diagram.png)
  
    Utilisez les contrôles de la minicarte situés dans le coin inférieur droit du Générateur de modèles pour ajuster la vue de manière à inclure autant de tables que possible. Vous pouvez également cliquer et faire glisser les tables à différents emplacements, de manière à les rapprocher ou à les placer dans un ordre particulier. Le déplacement des tables n’affecte pas les relations qui existent déjà entre elles. Pour afficher toutes les colonnes dans une table particulière, cliquez et faites glisser le bord de la table pour développer ou réduire sa taille.  
  
2.  Cliquez sur la ligne pleine située entre la table **DimCustomer** et la table **DimGeography**. La ligne pleine entre ces deux tables montre que cette relation est active, autrement dit, qu’elle est utilisée par défaut lors du calcul des formules DAX.  
  
    Notez que la colonne **GeographyKey** de la table **DimCustomer** et la colonne **GeographyKey** de la table **DimGeography** s’affichent désormais dans un cadre. Cela indique que ce sont les colonnes utilisées dans la relation. Les propriétés de la relation apparaissent maintenant aussi dans la fenêtre **Propriétés**.  
  
    > [!TIP]  
    > Outre l’utilisation du Générateur de modèles dans la vue de diagramme, vous pouvez également utiliser la boîte de dialogue Gérer les relations pour afficher les relations entre toutes les tables dans un format tabulaire. Dans l’Explorateur de modèles tabulaires, cliquez sur **Relations** > **Gérer les relations**.
  
3.  Utilisez le Concepteur de modèles avec la vue de diagramme ou la boîte de dialogue Gérer les relations pour vérifier que les relations suivantes ont été créées lorsque les tables ont été importées à partir de la base de données AdventureWorksDW :  
  
    |Actif|Table|Table de recherche associée|  
    |----------|---------|------------------------|  
    |Oui|**DimCustomer [GeographyKey]**|**DimGeography [GeographyKey]**|  
    |Oui|**DimProduct [ProductSubcategoryKey]**|**DimProductSubcategory [ProductSubcategoryKey]**|  
    |Oui|**DimProductSubcategory [ProductCategoryKey]**|**DimProductCategory [ProductCategoryKey]**|  
    |Oui|**FactInternetSales [CustomerKey]**|**DimCustomer [CustomerKey]**|  
    |Oui|**FactInternetSales [ProductKey]**|**DimProduct [ProductKey]**|  
  
    Si l’une des relations de la table ci-dessus est manquante, vérifiez que votre modèle comprend les tables suivantes : DimCustomer, DimDate, DimGeography, DimProduct, DimProductCategory, DimProductSubcategory et FactInternetSales. Si vous importez des tables provenant de la même connexion de source de données à des moments différents, les relations entre ces tables ne sont pas créées et doivent être créées manuellement.  

### <a name="take-a-closer-look"></a>Examen approfondi
Dans la vue de diagramme, vous remarquerez une flèche, un astérisque et un nombre sur les lignes qui indiquent la relation entre les tables.

![aas-lesson4-line](../tutorials/media/aas-lesson4-line.png)

La flèche indique la direction du filtre, l’astérisque indique que cette table est le côté « plusieurs » de la cardinalité de la relation, et le 1 indique que cette table est le côté « un » de la relation. Si vous devez modifier une relation (par exemple, modifier la direction du filtre de la relation ou sa cardinalité), double-cliquez sur la ligne de relation pour ouvrir la boîte de dialogue Modifier la relation.

![aas-lesson4-edit](../tutorials/media/aas-lesson4-edit.png)

Il est très probable que vous n’ayez jamais à modifier une relation. Ces fonctionnalités sont destinées à la modélisation de données avancée et sortent du cadre de ce didacticiel. Pour plus d’informations, consultez [Filtres croisés bidirectionnels pour les modèles tabulaires dans Analysis Services](https://docs.microsoft.com/sql/analysis-services/tabular-models/bi-directional-cross-filters-tabular-models-analysis-services).

Dans certains cas, vous devrez peut-être créer des relations supplémentaires entre les tables de votre modèle pour répondre aux besoins d’une certaine logique métier. Pour ce didacticiel, vous devez créer trois relations supplémentaires entre la table FactInternetSales et la table DimDate.  
  
#### <a name="to-add-new-relationships-between-tables"></a>Pour ajouter de nouvelles relations entre les tables  
  
1.  Dans le Concepteur de modèles, dans la table **FactInternetSales**, cliquez sur la colonne **OrderDate** et maintenez le bouton enfoncé, faites glisser le curseur vers la colonne **Date** de la table **DimDate**, puis relâchez le bouton.  

    Une ligne pleine s’affiche, indiquant que vous avez créé une relation active entre la colonne **OrderDate** de la table **Ventes sur Internet** et la colonne **Date** de la table **Date**. 
  
      ![aas-lesson4-new](../tutorials/media/aas-lesson4-new.png) 
  
    > [!NOTE]  
    > Lors de la création de relations, la cardinalité et la direction du filtre entre la table primaire et la table de recherche associée sont automatiquement sélectionnées.  
  
2.  Dans la table **FactInternetSales**, cliquez sur la colonne **DueDate** et maintenez le bouton enfoncé, faites glisser le curseur vers la colonne **Date** de la table **DimDate**, puis relâchez le bouton.  
  
    Une ligne en pointillé s’affiche, indiquant que vous avez créé une relation inactive entre la colonne **DueDate** de la table **FactInternetSales** et la colonne **Date** de la table **DimDate**. Vous pouvez avoir plusieurs relations entre les tables, mais une seule relation peut être active à la fois. Les relations inactives peuvent devenir actives si vous devez effectuer des agrégations spéciales dans des expressions DAX personnalisées.  
  
3.  Enfin, créez une dernière relation. Dans la table **FactInternetSales**, cliquez sur la colonne **ShipDate** et maintenez le bouton enfoncé, faites glisser le curseur vers la colonne **Date** de la table **DimDate**, puis relâchez le bouton.  
    
     ![aas-lesson4-newinactive](../tutorials/media/aas-lesson4-newinactive.png)
  
## <a name="whats-next"></a>Et ensuite ?
[Leçon 5 : Créer des colonnes calculées](../tutorials/aas-lesson-5-create-calculated-columns.md).
  
  
  

