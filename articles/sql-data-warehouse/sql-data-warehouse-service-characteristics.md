<properties
   pageTitle="Caractéristiques du service SQL Data Warehouse | Microsoft Azure"
   description="Valeurs maximales pour les connexions, les requêtes, les opérations DDL et DML Transact-SQL et les vues système pour SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/11/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Caractéristiques du service SQL Data Warehouse

Ces caractéristiques incluent les valeurs maximales qu’Azure SQL Data Warehouse a établies pour prendre en charge les charges de travail d’analyse les plus exigeantes tout en garantissant que chaque requête dispose des ressources nécessaires pour des performances optimales.

## Connexions

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Session | Sessions simultanées ouvertes | 1 024<br/><br/>Nous prenons en charge 1 024 connexions simultanées qui peuvent toutes envoyer des demandes simultanées à l’entrepôt de données. Notez qu’il existe des limites sur le nombre de requêtes pouvant s’exécuter simultanément. Quand une limite est dépassée, la demande est placée dans une file d’attente interne où elle attend d’être traitée.|
| Session | Mémoire maximale pour les instructions préparées | 20 Mo |
| Connexions | Connexions par serveur SQL. | 500 000 |


## Traitement des requêtes

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Interroger | Requêtes simultanées sur les tables utilisateur. | 32<br/><br/>Les requêtes supplémentaires sont placées dans une file d’attente interne où elles attendent d’être traitées. Quel que soit le nombre de requêtes s’exécutant simultanément, chaque requête est optimisée pour tirer pleinement parti de l’architecture de traitement massivement parallèle.|
| Interroger | Requêtes mises en file d’attente sur les tables utilisateur. | 1 000 |
| Interroger | Requêtes simultanées sur les vues système. | 100 |
| Interroger | Requêtes mises en file d’attente sur les vues système | 1 000 |
| Interroger | Nombre maximal de paramètres | 2 098 |
| Batch | Taille maximale | 65 536*4 096 |


## Langage de définition de données (DDL)

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Table | Tables par base de données | 2 milliards |
| Table | Colonnes par table | 1 024 colonnes |
| Table | Octets par colonne | 8 000 octets |
| Table | Octets par ligne, taille définie | 8 060 octets<br/><br/>Le nombre d’octets par ligne est calculé de la même manière que pour SQL Server avec la compression de page activée. Il ne peut pas être supérieur à 8 060 octets. Pour estimer la taille de ligne, reportez-vous aux calculs de taille de ligne dans [Estimer la taille d’un index cluster](https://msdn.microsoft.com/library/ms178085.aspx) sur MSDN.<br/><br/>Pour obtenir la liste des tailles de type de données SQL Data Warehouse, consultez [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx). |
| Table | Partitions par table | 15 000<br/><br/>Pour des performances élevées, nous vous recommandons de réduire au minimum le nombre de partitions nécessaires tout en prenant quand même en charge les besoins de votre entreprise. À mesure que le nombre de partitions augmente, la charge pour les opérations DDL et DML augmente et ralentit les performances.|
| Table | Caractères par valeur limite de partition.| 4000 |
| Index | Index non-cluster par table. | 999<br/><br/>Applicable uniquement aux tables de stockage de lignes.|
| Index | Index cluster par table. | 1<br><br/>Applicable à la fois aux tables de stockage de lignes et de stockage de colonnes.|
| Index | Lignes dans un groupe de lignes d’index de stockage de colonnes | 1 024<br/><br/>Chaque index de stockage de colonnes est implémenté sous la forme de plusieurs index de stockage de colonnes. Notez que si vous insérez 1 024 lignes dans un index de stockage de colonnes SQL Data Warehouse, les lignes ne sont pas toutes placées dans le même groupe de lignes.|
| Index | Générations simultanées d’index de stockage de colonnes cluster. | 32<br/><br/>Applicable quand les index de stockage de colonnes cluster reposent tous sur des tables différentes. Une seule génération d’index de stockage de colonnes cluster est autorisée par table. Les demandes supplémentaires attendent dans une file d’attente.|
| Index | Taille de la clé d’index. | 900 octets.<br/><br/>Applicable aux index de stockage de lignes uniquement.<br/><br/>Il est possible de créer des index sur des colonnes varchar d’une taille maximale de plus de 900 octets si les données existantes dans les colonnes n’excèdent pas 900 octets quand l’index est créé. Toutefois, les actions INSERT ou UPDATE ultérieures sur les colonnes, qui amènent la taille totale à dépasser 900 octets, échouent.|
| Index | Colonnes clés par index. | 16<br/><br/>Applicable aux index de stockage de lignes uniquement. Les index de stockage de colonnes cluster incluent toutes les colonnes.|
| Statistiques | Taille des valeurs de colonnes combinées. | 900 octets. |
| Statistiques | Colonnes par objet de statistiques. | 32 |
| Statistiques | Statistiques créées sur les colonnes par table. | 30 000 |
| Procédures stockées | Nombre maximal de niveaux d’imbrication. | 8 |
| Affichage | Colonnes par vue | 1 024 |


## Langage de manipulation de données (DML)

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Résultats SELECT | Colonnes par ligne | 4 096<br/><br/>Une ligne ne peut pas contenir plus de 4 096 colonnes dans le résultat SELECT. Le nombre de 4 096 colonnes n’est pas toujours garanti. Si le plan de requête exige une table temporaire, le maximum de 1 024 colonnes par table peut s’appliquer.|
| Résultats SELECT | Octets par ligne | > 8 060<br/><br/>Le nombre d’octets par ligne dans le résultat SELECT peut dépasser le maximum de 8 060 octets autorisé pour les lignes de table. Si le plan de requête de l’instruction SELECT a besoin d’une table temporaire, le maximum de 8 060 octets pour la table peut s’appliquer.|
| Résultats SELECT | Octets par colonne | > 8 000<br/><br/>Le nombre d’octets par colonne dans le résultat SELECT peut dépasser le maximum de 8 000 octets autorisé pour les colonnes de table. Si le plan de requête de l’instruction SELECT a besoin d’une table temporaire, le maximum de 8000 octets pour la table peut s’appliquer.|
| SELECT | Sous-requêtes imbriquées | 32<br/><br/>Un instruction SELECT ne peut pas contenir plus de 32 sous-requêtes imbriquées. Le nombre de 32 sous-requêtes n’est pas toujours garanti. Par exemple, une instruction JOIN peut introduire une sous-requête dans le plan de requête. Le nombre de sous-requêtes peut aussi être limité par la mémoire disponible.|
| SELECT | Colonnes par JOIN | 1 024 colonnes<br/><br/>L’instruction JOIN ne peut pas contenir plus de 1 024 colonnes. Le nombre de 1024 colonnes n’est pas toujours garanti. Si le plan JOIN exige une table temporaire avec davantage de colonnes que le résultat JOIN, la limite de 1 024 s’applique à la table temporaire. |
| SELECT | Octets par colonnes GROUP BY. | 8 060<br/><br/>Les colonnes incluses dans la clause GROUP BY peuvent comporter un maximum de 8 060 octets.|
| SELECT | Octets par colonnes ORDER BY | 8 060 octets.<br/><br/>Les colonnes incluses dans la clause ORDER BY peuvent comporter un maximum de 8 060 octets.|
| INSERT | Octets par colonne, largeur fixe et variable. | 8 000 octets. Toute tentative d’insertion de davantage d’octets que la valeur définie pour la colonne génère une erreur.|
| INSERT | Octets par ligne, colonnes de largeur variable. | > 8 060 octets. Les données au-delà des 8 060 octets sont placées dans la zone de stockage de dépassement de capacité.|
| UPDATE | Octets par colonne, largeur fixe et variable. | 8 000 octets. Toute tentative de mise à jour d’une colonne vers une valeur exigeant davantage d’octets que la valeur définie pour la colonne génère une erreur.|
| Identificateurs et constantes par instruction | Nombre d’identificateurs et constantes référencés. | 65 535<br/><br/>SQL Data Warehouse limite le nombre d’identificateurs et de constantes pouvant être contenus dans une seule expression d’une requête. Cette limite s’élève à 65 535. Le dépassement de ce nombre génère l’erreur SQL Server 8632. Pour plus d’informations, consultez [Erreur interne : une limite des services d’expression est dépassée](http://support.microsoft.com/kb/913050/).|

## Vues système

| Vue système | Nombre maximal de lignes |
| :--------------------------------- | :------------|
| Sys.dm\_pdw\_component\_health\_alerts | 10 000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | Nombre total de rôles de travail DMS pour les 1 000 dernières demandes SQL. |
| sys.dm\_pdw\_dms\_worker\_pairs | 10 000 |
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
[vue d’ensemble de référence de SQL Data Warehouse]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0218_2016-->