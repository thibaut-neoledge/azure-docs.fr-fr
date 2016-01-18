<properties
   pageTitle="Se connecter à SQL Data Warehouse | Microsoft Azure"
   description="Conseils en matière de connexion à SQL Data Warehouse pour le développement de solutions."
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
   ms.date="01/04/2016"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Se connecter à SQL Data Warehouse 
Pour vous connecter à SQL Data Warehouse, vous devez transmettre des informations d’identification de sécurité à des fins d’authentification. Lors de l’établissement d’une connexion, vous découvrirez également que certains paramètres de connexion sont configurés dans le cadre de l’établissement de votre session de requête.

Cet article détaille les aspects suivants de la connexion à SQL Data Warehouse :

- Authentification
- Paramètres de connexion
- Sessions et identificateurs de demande


## Authentification
Pour vous connecter à SQL Data Warehouse, vous devrez fournir les informations suivantes :

- Nom complet du serveur 
- Authentification SQL
- Nom d’utilisateur 
- Mot de passe
- Base de données par défaut (facultatif)

Il est important de noter que les utilisateurs doivent s’authentifier à l’aide de l’authentification SQL. L’authentification approuvée n’est pas prise en charge pour l’instant.

Par défaut, votre connexion se connecte à la base de données MASTER et non à votre base de données utilisateur. Pour vous connecter à votre base de données utilisateur, vous pouvez choisir d’effectuer l’une des deux opérations suivantes :

1. Spécifiez la base de données par défaut lors de l’inscription de votre serveur auprès de l’Explorateur d’objets SQL Server dans SQL Server Data Tools (SSDT) ou dans votre chaîne de connexion d’application. Par exemple, insérez le paramètre InitialCatalog pour une connexion ODBC.
2. Commencez par sélectionner la base de données utilisateur avant de créer une session dans SSDT.

> [AZURE.NOTE]Pour obtenir des conseils en matière de connexion à SQL Data Warehouse avec SSDT, consultez l’article de prise en main des procédures pour [se connecter et envoyer des requêtes][].

Il est important de noter que l’instruction Transact-SQL **USE <your DB>** n’est pas prise en charge pour la modification de la base de données pour une connexion.

## Protocoles de connexion d’application
Vous pouvez vous connecter à SQL Data Warehouse à l’aide de l’un des protocoles suivants :

- ADO.NET
- ODBC
- PHP
- JDBC

### Exemple de chaîne de connexion ADO.NET

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Exemple de chaîne de connexion ODBC

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Exemple de chaîne de connexion PHP

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Exemple de chaîne de connexion JDBC

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Paramètres de connexion
SQL Data Warehouse standardise quelques paramètres pendant la connexion et la création d’objets. Ces paramètres ne peuvent pas être remplacés.

| Paramètre de base de données | Valeur |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ACTIVÉ |
| QUOTED\_IDENTIFIERS | ACTIVÉ |
| NO\_COUNT | DÉSACTIVÉ |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| Classement de base de données | SQL\_Latin1\_General\_CP1\_CI\_AS |

## Sessions et demandes
Une fois qu’une connexion et qu’une session ont été établies, vous êtes prêt à écrire et à soumettre des requêtes à SQL Data Warehouse.

Chaque requête sera représentée par un ou plusieurs identificateurs de demande. Toutes les requêtes soumises sur cette connexion font partie d’une même session et seront donc représentées par un ID de session unique.

Toutefois, étant donné que SQL Data Warehouse est un système MPP distribué, les identificateurs de session et de demande sont tous deux exposés un peu différemment que dans SQL Server.

Les sessions et les demandes sont représentées de manière logique par leurs identificateurs respectifs.
	
| Identificateur | Exemple de valeur |
| :--------- | :------------ |
| ID de la session | SID123456 |
| ID de la demande | QID123456 |

Notez que l’ID de la session présente le préfixe SID (forme courte de « Session ID ») et que les demandes comportent le préfixe QID (forme courte de « Query ID »).

Ces informations vous aideront à identifier vos requêtes lorsque vous surveillerez les performances de ces dernières. Vous pouvez surveiller les performances de vos requêtes en utilisant le [portail Azure] et les vues de gestion dynamique.

Pour identifier la session actuelle, utilisez la fonction suivante :

```
SELECT SESSION_ID()
;
```

Pour visualiser toutes les requêtes qui sont en cours d’exécution ou qui ont été récemment exécutées dans votre entrepôt de données, vous pouvez utiliser une requête comme celle ci-dessous :

```
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

Notez que cette requête a été encapsulée dans une vue, ce qui vous permet de l’incorporer dans votre solution. Toutefois, pour visualiser les résultats, vous devrez créer la vue et l’exécuter.

## Étapes suivantes
Une fois connecté, vous pouvez commencer à concevoir vos tables. Pour plus d’informations, voir l’article décrivant la [conception de tables].

<!--Image references-->

<!--Azure.com references-->
[se connecter et envoyer des requêtes]: ./sql-data-warehouse-get-started-connect.md
[conception de tables]: ./sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0107_2016-->