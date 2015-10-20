<properties
	pageTitle="Connexion à une base de données SQL Azure à l'aide de SSMS | Microsoft Azure"
	description="Découvrez comment vous connecter à une base de données SQL Azure à l'aide de SSMS"
	metaCanonical=""
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg" 
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="sstein" />

# Se connecter avec SQL Server Management Studio

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Cet article vous explique comment installer SQL Server Management Studio (SSMS), se connecter à un serveur de base de données dans Azure et effectuer une requête simple à l’aide d’instructions Transact-SQL.

Vous avez d’abord besoin d’une base de données SQL dans Azure. Vous pouvez en créer une rapidement en suivant les instructions de [Prise en main de Microsoft Azure SQL Database](sql-database-get-started.md). Ces exemples sont basés sur la base de données AdventureWorks que vous créez dans cet article, mais les mêmes étapes s’appliquent à toute base de données SQL jusqu’à l’exécution de la requête.

## Installer et démarrer SQL Server Management Studio (SSMS)

Quand vous travaillez avec SQL Database, vous devez utiliser la version la plus récente de SSMS. Pour l’obtenir, accédez à [Télécharger SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Avec la version la plus récente, SSMS vous avertit automatiquement quand la mise à jour plus récente est disponible.

## Démarrez SSMS et connectez-vous à votre serveur de base de données SQL.

1. Tapez « Microsoft SQL Server Management Studio » dans la zone de recherche de Windows, puis cliquez sur l’application pour ordinateur pour démarrer SSMS.
2. Dans la boîte de dialogue **Connexion au serveur**, dans la zone **Nom du serveur**, saisissez le nom du serveur qui héberge votre base de données SQL selon le format *&lt;nomserveur>*.**database.windows.net**.
3. Choisissez **Authentification SQL Server** dans la liste **Authentification**.
4. Saisissez la **connexion** et le **mot de passe** que vous avez configurés au moment de la création du serveur, puis cliquez sur **Connexion**.

	![Connexion de SSMS au serveur Azure SQL Database](./media/sql-database-connect-query-ssms/1-connect.png)

### Si la connexion échoue

Les échecs de connexion les plus courants sont dus à des erreurs dans le nom du serveur, dans le nom d’utilisateur ou dans le mot de passe, ou quand le serveur n’autorise pas les connexions pour des raisons de sécurité. Assurez-vous que les paramètres de pare-feu du serveur autorisent les connexions à partir de l’adresse IP de votre ordinateur local et de l’adresse IP utilisée par le client SSMS. Elle sont parfois différentes.

Si la connexion échoue en raison d’une règle de pare-feu, l’adresse IP est signalée dans le message d’erreur. Ajoutez cette adresse IP à la règle de pare-feu du serveur. Pour plus d’informations, consultez la rubrique [Configuration des paramètres du pare-feu (Base de données SQL Azure)](sql-database-configure-firewall-settings.md).

## Exécuter les exemples de requêtes

Après vous être connecté, vous pouvez exécuter un exemple de requête. Si vous n’avez pas créé la base de données en utilisant l’exemple AdventureWorks dans [Prise en main de Microsoft Azure SQL Database](sql-database-get-started.md), cette requête ne fonctionnera pas. Passez directement aux étapes suivantes pour en savoir plus.

1. Dans l’**Explorateur d’objets**, accédez à la base de données **AdventureWorks**.
2. Cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**.

	![Nouvelle requête](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Dans la fenêtre de requête, copiez et collez le code suivant :

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Cliquez sur le bouton **Exécuter**. La capture d'écran suivante illustre une requête réussie.

	![Succès](./media/sql-database-connect-query-ssms/5-success.png)

## Étapes suivantes

Vous pouvez utiliser des instructions Transact-SQL pour créer et gérer des bases de données dans Azure de la même façon que vous pouvez le faire avec SQL Server. Si vous êtes familiarisé avec l’utilisation de Transact-SQL avec SQL Server, consultez [Informations sur Transact-SQL avec Azure SQL Database](sql-database-transact-sql-information.md) pour obtenir un récapitulatif des différences.

Si vous débutez avec Transact-SQL, consultez [Didacticiel : Écriture d’instructions Transact-SQL](https://msdn.microsoft.com/library/ms365303.aspx) et [Informations de référence sur Transact-SQL (moteur de base de données)](https://msdn.microsoft.com/library/bb510741.aspx).

<!---HONumber=Oct15_HO3-->