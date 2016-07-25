<properties
   pageTitle="Limites de la capacité de SQL Data Warehouse | Microsoft Azure"
   description="Valeurs maximales pour les connexions, les bases de données, les tables et les requêtes pour SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/11/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Limites de la capacité de SQL Data Warehouse

Les tableaux ci-après présentent les valeurs maximales autorisées pour les différents composants d’Azure SQL Data Warehouse.


## Gestion des charges de travail

| Catégorie | Description | Maximale |
| :------------------ | :------------------------------------------- | :----------------- |
| [Data Warehouse Units (DWU)][]| Ressources de calcul, de mémoire et d’E/S | 6000 |
| Connexion de base de données | Sessions simultanées ouvertes | 1 024<br/><br/>Nous prenons en charge un maximum de 1 024 connexions actives, chacune pouvant envoyer simultanément des requêtes à une base de données SQL Data Warehouse. Notez que le nombre de requêtes pouvant réellement s’exécuter simultanément est limité. En cas de dépassement d’une limite de concurrence, la demande est placée dans une file d’attente interne où elle attend d’être traitée.|
| Connexion de base de données | Mémoire maximale pour les instructions préparées | 20 Mo |
| [Gestion des charges de travail][] | Nombre maximal de requêtes concurrentes | 32<br/><br/> Par défaut, SQL Data Warehouse exécute un maximum de 32 requêtes simultanées et place en file d’attente les requêtes restantes.<br/><br/>Le niveau de concurrence peut diminuer lorsque les utilisateurs sont affectés à une classe de ressource supérieure. Certaines requêtes, comme les requêtes DMV, sont toujours autorisées à s’exécuter.|


## Objets de base de données

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Base de données | Taille maximale | 240 To compressés sur disque<br/><br/>Cet espace est indépendant de tempdb ou de l’espace de journalisation. Par conséquent, cet espace est dédié aux tables permanentes. La compression cluster columnstore est estimée à cinq fois, ce qui signifie que la taille décompressée de la base de données peut augmenter jusqu’à environ 1 Po quand toutes les tables sont columnstore cluster (type de table par défaut).|
| Table | Taille maximale | 60 To compressés sur disque |
| Table | Tables par base de données | 2 milliards |
| Table | Colonnes par table | 1 024 colonnes |
| Table | Octets par colonne | 8 000 octets |
| Table | Octets par ligne, taille définie | 8 060 octets<br/><br/>Le nombre d’octets par ligne est calculé de la même manière que pour SQL Server avec la compression de page activée. Comme SQL Server, SQL Data Warehouse prend en charge le stockage de dépassement de ligne qui permet d’envoyer les colonnes de longueur variable hors ligne. Seule une racine de 24 octets est stockée dans l’enregistrement principal pour les colonnes de longueur variable envoyées hors ligne. Pour plus d’informations, consultez la rubrique [Données de dépassement de ligne de plus de 8 Ko][] dans la documentation en ligne de SQL Server.<br/><br/>Pour obtenir la liste des tailles des types de données SQL Data Warehouse, consultez [CREATE TABLE (Azure SQL Data Warehouse)][]. |
| Table | Partitions par table | 15 000<br/><br/>Pour des performances élevées, nous vous recommandons de minimiser le nombre de partitions nécessaires tout en prenant quand même en charge les besoins de votre entreprise. À mesure que le nombre de partitions augmente, la charge pour les opérations Langage de définition de données (DDL) et Langage de manipulation de données (DML) augmente et ralentit les performances.|
| Table | Caractères par valeur limite de partition.| 4000 |
| Index | Index non-cluster par table. | 999<br/><br/>Applicable uniquement aux tables de stockage de lignes.|
| Index | Index cluster par table. | 1<br><br/>Applicable à la fois aux tables de stockage de lignes et aux tables columnstore.|
| Index | Lignes dans un groupe de lignes d’index de stockage de colonnes | 1 024<br/><br/>Chaque index columnstore est implémenté sous la forme de plusieurs index columnstore. Notez que si vous insérez 1 024 lignes dans un index de stockage de colonnes SQL Data Warehouse, les lignes ne sont pas toutes placées dans le même groupe de lignes.|
| Index | Générations simultanées d’index de stockage de colonnes cluster. | 32<br/><br/>Applicable quand les index cluster columnstore reposent tous sur des tables différentes. Une seule génération d’index de stockage de colonnes cluster est autorisée par table. Les demandes supplémentaires attendent dans une file d’attente.|
| Index | Taille de la clé d’index. | 900 octets.<br/><br/>Applicable aux index de stockage de lignes uniquement.<br/><br/>Il est possible de créer des index sur des colonnes varchar d’une taille maximale de plus de 900 octets si les données existantes dans les colonnes n’excèdent pas 900 octets quand l’index est créé. Toutefois, les actions INSERT ou UPDATE ultérieures sur les colonnes, qui amènent la taille totale à dépasser 900 octets, échouent.|
| Index | Colonnes clés par index. | 16<br/><br/>Applicable aux index de stockage de lignes uniquement. Les index de stockage de colonnes cluster incluent toutes les colonnes.|
| Statistiques | Taille des valeurs de colonnes combinées. | 900 octets. |
| Statistiques | Colonnes par objet de statistiques. | 32 |
| Statistiques | Statistiques créées sur les colonnes par table. | 30 000 |
| Procédures stockées | Nombre maximal de niveaux d’imbrication. | 8 |
| Affichage | Colonnes par vue | 1 024 |


## Charges

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Charges Polybase | Octets par ligne | 32 768<br/><br/>Les charges Polybase sont limitées au chargement de lignes de moins de 32 Ko et qui ne peuvent pas être chargées vers VARCHR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX). Bien que cette limite demeure applicable actuellement, elle sera supprimée assez rapidement.<br/><br/>


## Requêtes

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Interroger | Requêtes mises en file d’attente sur les tables utilisateur. | 1 000 |
| Interroger | Requêtes simultanées sur les vues système. | 100 |
| Interroger | Requêtes mises en file d’attente sur les vues système | 1 000 |
| Interroger | Nombre maximal de paramètres | 2 098 |
| Batch | Taille maximale | 65 536*4 096 |
| Résultats SELECT | Colonnes par ligne | 4 096<br/><br/>Une ligne ne peut pas contenir plus de 4 096 colonnes dans le résultat SELECT. Le nombre de 4 096 colonnes n’est pas toujours garanti. Si le plan de requête exige une table temporaire, le maximum de 1 024 colonnes par table peut s’appliquer.|
| SELECT | Sous-requêtes imbriquées | 32<br/><br/>Une instruction SELECT ne peut pas contenir plus de 32 sous-requêtes imbriquées. Le nombre de 32 sous-requêtes n’est pas toujours garanti. Par exemple, une instruction JOIN peut introduire une sous-requête dans le plan de requête. Le nombre de sous-requêtes peut aussi être limité par la mémoire disponible.|
| SELECT | Colonnes par JOIN | 1 024 colonnes<br/><br/>L’instruction JOIN ne peut pas contenir plus de 1 024 colonnes. Le nombre de 1024 colonnes n’est pas toujours garanti. Si le plan JOIN exige une table temporaire avec davantage de colonnes que le résultat JOIN, la limite de 1 024 s’applique à la table temporaire. |
| SELECT | Octets par colonnes GROUP BY. | 8 060<br/><br/>Les colonnes incluses dans la clause GROUP BY peuvent comporter un maximum de 8 060 octets.|
| SELECT | Octets par colonnes ORDER BY | 8 060 octets.<br/><br/>Les colonnes incluses dans la clause ORDER BY peuvent comporter un maximum de 8 060 octets.|
| Identificateurs et constantes par instruction | Nombre d’identificateurs et constantes référencés. | 65 535<br/><br/>SQL Data Warehouse limite le nombre d’identificateurs et de constantes pouvant être contenus dans une seule expression d’une requête. Cette limite s’élève à 65 535. Le dépassement de ce nombre génère l’erreur SQL Server 8632. Pour plus d’informations, consultez [Erreur interne : une limite des services d’expression a été atteinte][].|


## Metadata

| Vue système | Nombre maximal de lignes |
| :--------------------------------- | :------------|
| Sys.dm\_pdw\_component\_health\_alerts | 10 000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Nombre total de rôles de travail DMS pour les 1 000 dernières demandes SQL. |
| sys.dm\_pdw\_errors | 10 000 |
| sys.dm\_pdw\_exec\_requests | 10 000 |
| sys.dm\_pdw\_exec\_sessions | 10 000 |
| sys.dm\_pdw\_request\_steps | Nombre total d’étapes pour les 1 000 dernières demandes SQL stockées dans sys.dm\_pdw\_exec\_requests. |
| sys.dm\_pdw\_os\_event\_logs | 10 000 |
| sys.dm\_pdw\_sql\_requests | Les 1 000 dernières demandes SQL stockées dans sys.dm\_pdw\_exec\_requests. |


## Étapes suivantes
Pour plus d’informations, consultez la [vue d’ensemble de référence de SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Data Warehouse Units (DWU)]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[vue d’ensemble de référence de SQL Data Warehouse]: ./sql-data-warehouse-overview-reference.md
[Gestion des charges de travail]: ./sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->
[Données de dépassement de ligne de plus de 8 Ko]: https://msdn.microsoft.com/library/ms186981.aspx
[CREATE TABLE (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Erreur interne : une limite des services d’expression a été atteinte]: https://support.microsoft.com/kb/913050

<!---HONumber=AcomDC_0713_2016-->