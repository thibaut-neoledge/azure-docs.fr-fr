<properties
	pageTitle="Se connecter à Base de données SQL - SQL Server Management Studio | Microsoft Azure"
	description="Découvrez comment vous connecter à Base de données SQL sur Azure avec SQL Server Management Studio (SSMS). Ensuite, exécutez un exemple de requête à l’aide de Transact-SQL (T-SQL)."
	metaCanonical=""
	keywords="connexion à une base de données sql, sql server management studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="sstein;carlrab" />

# Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Cet article vous explique comment vous connecter à une base de données SQL Azure à l’aide de la dernière version de SQL Server Management Studio (SSMS) et effectuer une requête simple à l’aide d’instructions T-SQL (Transact-SQL).

[AZURE.INCLUDE [Connexion](../../includes/azure-getting-started-portal-login.md)]

[AZURE.INCLUDE [Installation de SSMS](../../includes/sql-server-management-studio-install.md)]

[AZURE.INCLUDE [Connecter SSMS](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]

Pour plus d’informations sur les règles de pare-feu, consultez [Procédure : configuration des paramètres de pare-feu (Azure SQL Database)](sql-database-configure-firewall-settings.md).

## Exécuter les exemples de requêtes

Après vous être connecté à votre serveur logique, vous pouvez vous connecter à une base de données et exécuter un exemple de requête.

1. Dans l'**Explorateur d'objets**, sélectionnez une base de données sur le serveur auquel vous avez accès, par exemple la base de données **AdventureWorks**.
2. Cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**.

	![Nouvelle requête. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Dans la fenêtre de requête, copiez et collez le code suivant :

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Cliquez sur le bouton **Exécuter**. La capture d'écran suivante illustre une requête réussie.

	![Succès. Se connecter à un serveur de base de données SQL : SQL Server Management Studio](./media/sql-database-connect-query-ssms/5-success.png)

## Étapes suivantes

Vous pouvez utiliser des instructions T-SQL pour créer et gérer des bases de données dans Azure de la même façon que vous pouvez le faire avec SQL Server. Si vous êtes familiarisé avec l’utilisation de T-SQL avec SQL Server, consultez [Informations sur Transact-SQL avec Azure SQL Database](sql-database-transact-sql-information.md) pour obtenir un récapitulatif des différences.

Si vous débutez avec T-SQL, consultez [Didacticiel : Écriture d’instructions Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) et [Informations de référence sur Transact-SQL (moteur de base de données)](https://msdn.microsoft.com/library/bb510741.aspx).

Pour commencer à créer des utilisateurs de base de données et des administrateurs d'utilisateurs de base de données, consultez la rubrique [Premiers pas avec la sécurité de la base de données SQL Azure](sql-database-get-started-security.md)

<!---HONumber=AcomDC_0420_2016-->