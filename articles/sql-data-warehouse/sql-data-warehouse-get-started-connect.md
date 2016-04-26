<properties
   pageTitle="Se connecter à SQL Data Warehouse avec Visual Studio | Microsoft Azure"
   description="Familiarisez-vous avec la procédure de connexion à SQL Data Warehouse et découvrez comme exécuter certaines requêtes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="sonyama;barbkess"/>

# Se connecter à SQL Data Warehouse avec Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Cette procédure pas à pas vous explique comment vous connecter à une base de données Azure SQL Data Warehouse en quelques minutes en utilisant SQL Server Data Tools (SSDT) dans Visual Studio. Une fois que vous serez connecté, vous exécuterez une requête simple.

## Configuration requise

+ Exemple de base de données AdventureWorksDW dans SQL Data Warehouse. Pour créer cet élément, consultez la page [Créer une base de données SQL Data Warehouse](sql-data-warehouse-get-started-provision.md).
+ SQL Server Data Tools pour Visual Studio Pour obtenir des instructions et options d’installation, consultez [Installation de Visual Studio et/ou SSDT](sql-data-warehouse-install-visual-studio.md).

## Étape 1 : Obtenir le nom complet de votre serveur SQL Azure

Votre base de données est associée à un serveur SQL Azure. Pour vous connecter à votre base de données, vous devez disposer du nom complet du serveur (**servername**.database.windows.net*).

Pour obtenir le nom complet du serveur

1. Accédez au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Bases de données SQL**, puis sur la base de données à laquelle vous souhaitez vous connecter. Cet exemple utilise l’exemple de base de données AdventureWorksDW.
3. Recherchez le nom complet du serveur.

    ![Nom complet du serveur][1]

## Étape 2 : Se connecter à la base de données SQL
Pour optimiser les performances, utilisez Visual Studio 2015 avec la [dernière mise à jour de SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).

1. Ouvrir Visual Studio 2013 ou 2015
2. Ouvrez l’Explorateur d’objets SQL Server. Pour ce faire, sélectionnez **Affichage** > **Explorateur d’objets SQL Server**.

    ![Explorateur d’objets SQL Server][2]

3. Cliquez sur l’icône **Ajouter SQL Server**.

    ![Ajouter SQL Server][3]

1. Renseignez les champs dans la fenêtre Se connecter au serveur.

    ![Se connecter au serveur][4]

    - **Nom du serveur**. Saisissez le *nom du serveur* précédemment identifié.
    - **Authentification**. Sélectionnez Authentification SQL Server.
    - **Nom d’utilisateur** et **Mot de passe**. Entrez le nom d’utilisateur et le mot de passe du serveur SQL Azure.
    - **Nom de la base de données**. Entrez le nom de la base de données pour la base de données SQL DW.
    - Cliquez sur **Connecter**.

1. Pour voir plus d’informations, développez votre serveur SQL Azure. Vous pouvez afficher les bases de données associées au serveur. Développez AdventureWorksDW pour voir les tables de votre exemple de base de données.

    ![Explorer AdventureWorksDW][5]


## Étape 3 : Exécuter un exemple de requête

Maintenant qu’une connexion à votre base de données a été établie, passons à l’écriture d’une requête.

1. Cliquez avec le bouton droit sur votre base de données dans l’Explorateur d’objets SQL Server.

2. Sélectionnez **Nouvelle requête**. Une nouvelle fenêtre de requête s’ouvre.

    ![Nouvelle requête][6]

3. Copiez la requête TSQL suivante dans la fenêtre de requête :

    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```

4. Exécutez la requête. Pour ce faire, cliquez sur la flèche verte ou utilisez le raccourci `CTRL`+`SHIFT`+`E`.

    ![Exécuter une requête][7]

1. Passez en revue les résultats de la requête. Dans cet exemple, la table FactInternetSales a 60 398 lignes.

    ![Résultats de la requête][8]

## Étapes suivantes

Comme vous pouvez à présent vous connecter et exécuter des requêtes, essayez de [visualiser les données avec Power BI][].

[visualiser les données avec Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0420_2016-->