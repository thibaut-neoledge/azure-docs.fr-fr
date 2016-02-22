<properties
   pageTitle="Spécifications de la capacité de SQL Data Warehouse | Microsoft Azure"
   description="Spécifications de la capacité minimale et maximale de SQL Data Warehouse."
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
   ms.date="02/10/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Spécifications de la capacité de SQL Data Warehouse

SQL Data Warehouse comporte des limites pour le traitement des requêtes et les vues système qui sont très similaires à celles de SQL Server. Certaines limites sont différentes en raison de l’architecture parallèle et évolutive sous-jacente. Voici un résumé des limites de SQL Data Warehouse.

## Connexions

| Catégorie | Description | Maximale |
| :---------------- | :------------------------------------------- | :----------------- |
| Session | Sessions simultanées ouvertes | 1 024<br/><br/>Nous prenons en charge 1 024 connexions simultanées qui peuvent toutes envoyer des demandes simultanées à l’entrepôt de données. Notez qu’il existe des limites sur le nombre de requêtes pouvant s’exécuter simultanément. Quand une limite est dépassée, la demande est placée dans une file d’attente interne où elle attend d’être traitée.|
| Session | Mémoire maximale pour les instructions préparées | 20 Mo |
| Connexions | Connexions par serveur. | 500 000 |


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
| Table | Octets par ligne, taille du tampon interne pour le déplacement de données | 32 768<br/><br/>SQL Data Warehouse utilise un tampon interne pour déplacer des lignes au sein du système SQL Data Warehouse distribué. Le service qui déplace les lignes est appelé service de déplacement des données (DMS) et stocke les lignes dans un format qui diffère de SQL Server.<br/><br/>Si une ligne ne tient pas dans le tampon interne, vous obtenez une erreur de compilation de requête ou une erreur de déplacement des données interne. Pour éviter ce problème, consultez [Informations détaillées sur la taille du tampon du service de déplacement des données (DMS)](#details-about-the-dms-buffer-size).|
| Table | Partitions par table | 15 000<br/><br/>Pour des performances élevées, nous vous recommandons de minimiser le nombre de partitions nécessaires tout en prenant quand même en charge les besoins de votre entreprise. À mesure que le nombre de partitions augmente, la charge pour les opérations DDL et DML augmente et ralentit les performances.|
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


## Informations détaillées sur la taille du tampon du service de déplacement des données (DMS)

SQL Data Warehouse utilise un tampon interne pour déplacer les lignes entre les nœuds de calcul principaux. Le service qui déplace les lignes est appelé service de déplacement des données (DMS) ; il utilise un format de stockage différent de celui de SQL Server.

Pour améliorer les performances des requêtes parallèles, DMS remplit toutes les données de longueur variable jusqu’à la taille maximale définie de la base de données SQL. Par exemple, la valeur « hello » pour un `nvarchar(2000) NOT NULL` utilise en fait 4 002 octets dans le tampon DMS. Elle utilise 2 octets pour chacun des 2 000 caractères plus 2 octets pour le terminateur NULL.

> [AZURE.NOTE] Une erreur interne se produit quand DMS essaie de déplacer une ligne qui dépasse la taille du tampon DMS de 32 768 octets. Si la taille de votre ligne dépasse celle du tampon DMS, vous devez revoir la définition de la table pour adapter la ligne au tampon DMS.

### Comment déterminer la taille de ligne pour le tampon DMS
Cet exemple décrit les tailles des données de longueur variable définies par DMS, puis montre comment calculer la taille du tampon DMS pour une ligne.
 
Dans le tampon DMS, la taille des données de longueur variable est la somme des éléments suivants :

- Taille définie pour les caractères.
- Les types NULL utilisent 8 octets pour l’indicateur NULL.
- Les types ASCII utilisent 1 caractère pour le terminateur NULL.
- Les types Unicode utilisent 2 caractères pour le terminateur NULL.
             
| Type de données | Taille du tampon DMS |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1 009 octets = 1 000 + 8 + 1 |
| char(1000) NOT NULL | 1 001 octets = 1 000 + 1 |
| nchar(1000) NULL | 2 010 octets = 2 000 + 8 + 2 |
| varchar(1000) NULL | 1 009 octets = 1 000 + 8 + 1 |
| varchar(1000) NOT NULL | 1 009 octets = 1000 + 8 + 1 |
| nvarchar(1000) NULL | 2 010 octets = 2 000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2 010 octets = 2 000 + 8 + 2 |
                
Dans le tampon DMS, les colonnes de largeur fixe utilisent la taille native de SQL Server. Si le type est nullable, DMS exige 8 octets supplémentaires. Pour la taille SQL Server, consultez le champ max\_length dans sys.types.

Par exemple :

| Type de données de largeur fixe | Taille du tampon DMS |
| :-------------------- | :----------------- |
| int NULL | 12 octets = 4 + 8 |
| int NOT NULL | 4 octets = 4 + 0 | 
                
En additionnant le tout, la taille définie du tampon DMS pour la ligne suivante s’élève à 31 134 octets, ce qui tient dans le tampon DMS.

| des colonnes | Type de données | Taille de colonne |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2 (7) NULL | 20 octets = 8 + 8 + 4 |
| col2 | float (4) NULL | 12 octets = 4 + 8 |
| col3 | nchar (6) NULL | 22 octets = 12 + 8 + 2 |
| col4 | char (7000) NULL | 7 009 octets = 7 000 + 8 + 1 |
| col5 | nvarchar (4000) | 8 002 octets = 8 000 + 2 |
| col6 | varchar (8000) NULL | 8 009 octets = 8 000 + 8 + 1 |
| col7 | varbinary (8000) | 8 000 octets |
| col8 | binary (60) | 60 octets |
                
              
### Exemple de dépassement de la taille du tampon DMS

Cet exemple illustre une insertion correcte d’une ligne dans SQL Data Warehouse, qui génère ensuite une erreur de dépassement de capacité DMS quand DMS doit déplacer la ligne correspondant à une jointure incompatible de distribution. Cet exemple démontre qu’il est préférable de créer des lignes plus petites qui tiennent dans le tampon DMS.

Dans l’exemple suivant, nous créons la table T1. La taille maximale possible de la ligne quand toutes les variables nvarchar comportent 4 000 caractères Unicode s’élève à plus de 40 000 octets, ce qui dépasse la taille maximale du tampon DMS.

Étant donné que la taille réelle définie de nvarchar utilise 26 octets, la définition de ligne est inférieure à 8 060 octets et peut tenir dans une page SQL Server. Par conséquent, l’instruction CREATE TABLE aboutit, même si DMS échoue lors de sa tentative de chargement de cette ligne dans le tampon DMS.

````
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
````
Cette étape montre que nous pouvons utiliser correctement INSERT pour insérer des données dans la table. Cette instruction charge les données directement dans SQL Server sans utiliser DMS, permettant ainsi d’éviter un échec de dépassement de capacité du tampon DMS. Integration Services réussit également à charger cette ligne.</para>

````
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````

Pour préparer la démonstration de ce déplacement de données, cet exemple crée une deuxième table avec la colonne de distribution CustomerKey.

````
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````
Étant donné que les deux tables ne sont pas distribuées sur CustomerKey, une jointure entre T1 et T2 sur CustomerKey est incompatible avec la distribution. DMS a besoin de charger au moins une ligne et la copier dans une autre distribution.

````
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
````

Comme prévu, DMS ne parvient pas à effectuer la jointure, car la ligne, quand toutes les colonnes nvarchar sont complétées, dépasse la taille du tampon DMS de 32 768 octets. Le message d’erreur suivant se produit.

````
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
````

## Étapes suivantes
Pour plus d’informations, consultez la [vue d’ensemble de référence de SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de référence de SQL Data Warehouse]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0211_2016-->