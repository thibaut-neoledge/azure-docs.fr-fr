---
title: Utilisation des vues T-SQL dans Azure SQL Data Warehouse | Microsoft Docs
description: "Conseils relatifs à l’utilisation de vues Transact-SQL dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: b5208f32-8f4a-4056-8788-2adbb253d9fd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: d2a03be810bd7f792876607ec735eb578b65a3b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="views-in-sql-data-warehouse"></a>Vues proposées par SQL Data Warehouse
Les vues sont particulièrement utiles dans SQL Data Warehouse. Vous pouvez les utiliser de différentes façons, afin d’améliorer la qualité de votre solution.  Cet article met en évidence quelques exemples montrant comment enrichir votre solution avec des vues, ainsi que les limites à prendre en considération.

> [!NOTE]
> La syntaxe de `CREATE VIEW` n’est pas abordée dans cet article. Consultez l’article [CREATE VIEW][CREATE VIEW] sur MSDN pour obtenir ces informations de référence.
> 
> 

## <a name="architectural-abstraction"></a>Abstraction architecturale
Un des modèles d’application les plus courants consiste à recréer des tables au moyen de la commande CREATE TABLE AS SELECT (CTAS), suivie d’un modèle de changement de nom d’un objet, lors du chargement des données.

L’exemple ci-dessous permet d’ajouter de nouveaux enregistrements de date à une dimension de date. Notez comment un nouvel objet, DimDate_New, est d’abord créé et ensuite renommé pour remplacer la version d’origine de la table.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Toutefois, cette approche peut provoquer l’apparition et la disparition de table d’une vue utilisateur, ainsi que l’affichage de messages d’erreur de type « la table n’existe pas ». Vous pouvez utiliser des vues pour fournir aux utilisateurs une couche de présentation cohérente alors que les objets sous-jacents sont renommés. Grâce à la mise à disposition d’un accès aux données via une vue, les utilisateurs n’ont pas besoin d’afficher les tables sous-jacentes. Ainsi, l’expérience utilisateur est plus cohérente ; les concepteurs d’entrepôts de données peuvent faire évoluer le modèle de données tout en optimisant les performances, en exécutant la commande CTAS lors du processus de chargement des données.    

## <a name="performance-optimization"></a>Optimisation des performances
Une vue peut également être utilisée pour mettre en place des jointures plus performantes entre les tables. Ainsi, une vue peut inclure une clé de distribution redondante parmi ses critères de jointure, afin de réduire le nombre de déplacements de données.  Elle peut également permettre de forcer une indication de jointure ou de requête spécifique. Utiliser des vues de cette manière permet de garantir que les jointures sont toujours effectuées de façon optimale en évitant d’avoir à se souvenir de leur construction correcte.

## <a name="limitations"></a>Limitations
Dans SQL Data Warehouse, les vues concernent uniquement les métadonnées.  De ce fait, les options suivantes ne sont pas disponibles :

* Il n’existe aucune option de liaison de schéma.
* Les tables de base ne peuvent pas être mises à jour par le biais de la vue.
* Vous ne pouvez pas créer des vues sur des tables temporaires.
* Les indications EXPAND/NOEXPAND ne sont pas prises en charge.
* SQL Data Warehouse n’inclut aucune vue indexée.

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez l’article [Vue d’ensemble sur le développement SQL Data Warehouse][SQL Data Warehouse development overview].
Pour la syntaxe de `CREATE VIEW`, consultez [CREATE VIEW][CREATE VIEW].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CREATE VIEW]: https://msdn.microsoft.com/en-us/library/ms187956.aspx

<!--Other Web references-->
