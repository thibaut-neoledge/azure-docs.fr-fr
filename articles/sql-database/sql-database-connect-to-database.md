<properties
	pageTitle="Connexion à une base de données SQL Azure à l'aide de SSMS | Microsoft Azure"
	description="Découvrez comment vous connecter à une base de données SQL Azure à l'aide de SSMS"
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# Se connecter avec SQL Server Management Studio

Procédez comme suit pour installer SQL Server Management Studio (SSMS) et l’utiliser pour vous connecter à votre base de données SQL et l’interroger.

## Composants requis
* Un exemple de base de données SQL AdventureWorks, comme décrit dans [Prise en main de Microsoft Azure SQL Database](sql-database-get-started.md).

## Installer et démarrer SQL Server Management Studio (SSMS)
1. Accédez à la page de téléchargement de [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299), cliquez sur **Télécharger**, puis choisissez la version 32 bits (x86) ou 64 bits (x64) du téléchargement MgmtStudio.

	![MgtmtStudio32BIT ou MgmtStudio64BIT][1]
2.	Suivez les invites lorsque vous installez SSMS en utilisant les paramètres par défaut.
3.	Une fois le téléchargement terminé, recherchez SQL Server 2014 Management Studio sur votre ordinateur, puis démarrez SSMS.


## Se connecter à la base de données SQL
1. Ouvrez SSMS.
2. Dans la boîte de dialogue **Connexion au serveur**, dans la zone **Nom du serveur**, entrez le nom du serveur au format *&lt;nomserveur>*.**database.windows.net**.
3. Dans la liste **Authentification**, sélectionnez **Authentification SQL Server**.
4. Entrez les identifiants de **connexion** et le **mot de passe** que vous avez spécifiés lors de la création de votre serveur de base de données SQL.

	![Boîte de dialogue Se connecter au serveur][2]
5. Cliquez sur le bouton **Options**.
6. Dans la zone **Se connecter à la base de données**, entrez **AdventureWorks** et cliquez sur **Se connecter**.

	![Se connecter à la base de données.][3]

### Si la connexion échoue
Assurez-vous que le pare-feu du serveur logique que vous avez créé autorise les connexions à partir de votre ordinateur local. Pour plus d’informations, consultez la rubrique [Configuration des paramètres du pare-feu (Base de données SQL Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Exécuter les exemples de requêtes

1. Dans l’**Explorateur d’objets**, accédez à la base de données **AdventureWorks**.
2. Cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**.

	![Nouvelle requête][4]

3. Dans la fenêtre de requête, copiez et collez le code suivant :

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Cliquez sur le bouton **Exécuter**. La capture d'écran suivante illustre une requête réussie.

	![Succès][5]

## Étapes suivantes
Vous pouvez utiliser les instructions Transact-SQL pour créer ou gérer les bases de données. Pour plus d’informations, consultez la rubrique [CREATE DATABASE (Base de données SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) et [Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio](sql-database-manage-azure-ssms.md). Vous pouvez également enregistrer les événements dans le stockage Azure. Pour plus d’informations, consultez la rubrique [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png

<!---HONumber=August15_HO8-->