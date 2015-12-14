<properties
   pageTitle="Connexion à une base de données SQL Azure à l'aide de SSMS | Microsoft Azure"
   description="Découvrez comment vous connecter à une base de données SQL Azure à l'aide de SSMS"
   services="sql-database"
   documentationCenter=""
   authors="sidneyh"
   manager="jeffreyg"
   editor=""
   tags=""/>
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2015"
   ms.author="sidneyh"/>

# Se connecter avec SQL Server Management Studio (SSMS)

Procédez comme suit pour vous connecter à votre base de données SQL et l’interroger à l’aide de SQL Server Management Studio (SSMS).

## Composants requis

* SQL Server Management Studio (SSMS) : pour télécharger la dernière version de SSMS, consultez [Télécharger SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
* Exemple de base de données AdventureWorks, comme décrit dans [Prise en main de Microsoft Azure SQL Database](sql-database-get-started.md).


## Obtenir le nom complet de votre serveur SQL Azure

Pour vous connecter à votre base de données, vous devez disposer du nom complet du serveur (****nom\_serveur**.database.windows.net*) contenant la base de données à laquelle vous souhaitez vous connecter.

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez à la base de données à laquelle vous souhaitez vous connecter.
3. Recherchez le nom complet du serveur :

    ![nom complet du serveur][6]

    Utilisez le nom complet du serveur à l’étape 3 ci-dessous.



## Se connecter à votre base de données SQL

1. Ouvrez SSMS.
2. Cliquez sur **Connexion** > **Moteur de base de données...**

    ![Connexion > Moteur de base de données][7]

2. Dans la boîte de dialogue **Connexion au serveur**, dans la zone **Nom du serveur**, tapez le nom du serveur au format *&lt;nom\_serveur>*.**database.windows.net**.
3. Dans la liste **Authentification**, sélectionnez **Authentification SQL Server**.
4. Entrez les identifiants de **Connexion** et le **Mot de passe** que vous avez spécifiés lors de la création de votre serveur SQL Database, puis cliquez sur **Connexion**.

	![Boîte de dialogue Se connecter au serveur][2]



### Si la connexion échoue
Assurez-vous que le pare-feu du serveur logique que vous avez créé autorise les connexions à partir de votre ordinateur local. Pour plus d’informations, consultez [Procédure : configuration des paramètres du pare-feu sur SQL Database](sql-database-configure-firewall-settings.md).

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
Vous pouvez utiliser les instructions Transact-SQL pour créer ou gérer les bases de données. Pour plus d’informations, consultez [CRÉER UNE BASE DE DONNÉES (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) et [Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio](sql-database-manage-azure-ssms.md). Vous pouvez également enregistrer les événements dans le stockage Azure. Pour plus d’informations, consultez [Prise en main de l’audit de base de données SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=AcomDC_1203_2015-->