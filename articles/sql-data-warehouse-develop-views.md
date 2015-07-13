<properties
   pageTitle="Vues dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’utilisation de vues Transact-SQL dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

 
# Vues proposées par SQL Data Warehouse

Les vues sont particulièrement utiles dans SQL Data Warehouse. Vous pouvez les utiliser de différentes façons, afin d’améliorer la qualité de votre solution.

Cet article met en évidence quelques exemples indiquant comment enrichir votre solution via l’implémentation de vues. Vous devez également tenir compte de certaines limitations.

## Abstraction architecturale
Un des modèles d’application les plus courants consiste à recréer des tables au moyen de la commande CREATE TABLE AS SELECT (CTAS), suivie d’un modèle de changement de nom d’un objet, lors du chargement des données.

L’exemple ci-dessous permet d’ajouter de nouveaux enregistrements de date à une dimension de date. Vous pouvez voir que l’objet « DimDate_New » est créé pour la première fois, puis renommé, de façon à remplacer la version d’origine de cet objet. ``` CREATE TABLE dbo.DimDate_New WITH (DISTRIBUTION = REPLICATE , CLUSTERED INDEX (DateKey ASC) ) AS SELECT * FROM dbo.DimDate AS prod UNION ALL SELECT * FROM dbo.DimDate_stg AS stg ;

RENAME OBJECT DimDate TO DimDate_Old; RENAME OBJECT DimDate_New TO DimDate;

``` Toutefois, cela peut entraîner l’apparition ou la disparition d’objets de la vue d’un utilisateur dans l’Explorateur d’objets SQL Server de SSDT. Vous pouvez utiliser des vues pour fournir aux consommateurs d’entrepôts de données une couche de présentation cohérente alors que les objets sous-jacents sont renommés. Grâce à la mise à disposition d’un accès aux données via une vue, les utilisateurs n’ont pas besoin d’afficher les tables sous-jacentes. Ainsi, l’expérience utilisateur est plus cohérente ; les concepteurs d’entrepôts de données peuvent faire évoluer le modèle de données tout en optimisant les performances, en exécutant la commande CTAS lors du processus de chargement des données.

## Optimisation des performances
Une vue est un moyen intelligent de mettre en place des jointures plus performantes entre les tables. Ainsi, une vue peut inclure une clé de distribution redondante parmi ses critères de jointure, afin de réduire le nombre de déplacements de données. Elle peut également permettre de forcer une indication de jointure ou de requête spécifique. Cela garantit que la jointure est toujours effectuée de façon optimale et ne dépend pas du fait que l’utilisateur pense à construire correctement la jointure.

## Limitations
Dans SQL Data Warehouse, les vues concernent uniquement les métadonnées.

De ce fait, les options suivantes ne sont pas disponibles : - Il n’existe aucune option de liaison de schéma. - Les tables de base ne peuvent pas être mises à jour via la vue. - Il n’est pas possible de créer de vues sur des tables temporaires. - Les indications EXPAND/NOEXPAND ne sont pas prises en charge. - SQL Data Warehouse n’inclut aucune vue indexée.


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez la rubrique [Vue d’ensemble sur le développement SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Vue d’ensemble sur le développement SQL Data Warehouse]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO1-->