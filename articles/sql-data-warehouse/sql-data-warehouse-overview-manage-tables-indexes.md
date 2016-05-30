<properties
   pageTitle="Gestion des tables et index dans Azure SQL Data Warehouse | Microsoft Azure"
   description="Vue d’ensemble des considérations, meilleures pratiques et tâches de gestion des tables et index dans Azure SQL Data Warehouse"
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
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestion des tables et index dans Azure SQL Data Warehouse

Vue d’ensemble des considérations, meilleures pratiques et tâches de gestion des tables et index dans SQL Data Warehouse.



| Catégorie | Tâche ou considération | Description |
| :-----------------------| :---------------------------------------------- | :----------- |
| Index Columnstore | Reconstruire les index après le chargement ou l’insertion de données | Par défaut, SQL Data Warehouse stocke chaque table sous la forme d’un index columnstore en cluster. Ceci permet d’améliorer les performances et la compression des données, en particulier pour les tables volumineuses. Selon la façon dont vous chargez des données dans l’index columnstore, toutes les données risquent de ne pas être stockées avec la compression columnstore. Dans ce cas, il se peut que vous n’obteniez pas les performances que vous pourriez attendre des index columnstore. <br/><br/>Pour vérifier l’intégrité de vos index columnstore, consultez [Gérer les index columnstore][]. |
| Tables de hachage distribuées | Vérifier que les données sont réparties uniformément entre les différents nœuds | Les tables de hachage distribuées offrent dans presque tous les cas le moyen idéal d’optimiser les jointures et les agrégations sur des tables volumineuses. SQL Data Warehouse distribue les tables en fonction d’une colonne de distribution spécifiée. Certaines colonnes représentent des clés de distribution adéquates, d’autres non. Il est important que les lignes soient distribuées de façon uniforme. Vous pouvez tolérer un certain manque d’uniformité ou un décalage des données ; si le décalage des données est trop important, vous perdrez toutefois les avantages du processus massivement parallèle (MPP) qu’offre SQL Data Warehouse.<br/><br/>Pour éviter un décalage des données trop important sir vos tables de hachage distribuées, consultez [Gérer le décalage des données pour les tables distribuées dans Azure SQL Data Warehouse][]. |





## Étapes suivantes

Pour plus de conseils sur la gestion, consultez la présentation de la [gestion][].

<!--Image references-->

<!--Article references-->
[Gérer les index columnstore]: sql-data-warehouse-manage-columnstore-indexes.md
[Gérer le décalage des données pour les tables distribuées dans Azure SQL Data Warehouse]: sql-data-warehouse-manage-distributed-data-skew.md
[gestion]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->