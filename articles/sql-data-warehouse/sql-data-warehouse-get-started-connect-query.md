<properties
   pageTitle="Prise en main : connexion à SQL Data Warehouse Azure | Microsoft Azure"
   description="Familiarisez-vous avec la procédure de connexion à SQL Data Warehouse et découvrez comme exécuter certaines requêtes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="twounder;barbkess"/>

# Se connecter et lancer des requêtes avec Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Cette procédure pas à pas vous explique comment vous connecter à une base de données Azure SQL Data Warehouse et l’interroger en quelques minutes en utilisant Visual Studio. Lors de cette procédure pas à pas, vous allez :

+ Installer les logiciels requis
+ Connecter une base de données contenant l’exemple de base de données AdventureWorksDW.
+ Exécuter une requête sur l’exemple de base de données  

## Composants requis

+ Visual Studio 2013/2015 : pour télécharger et installer Visual Studio 2015 et/ou SSDT, consultez [Installer Visual Studio et SSDT](sql-data-warehouse-install-visual-studio.md)

## Obtenir le nom complet de votre serveur SQL Azure

Pour vous connecter à votre base de données, vous devez disposer du nom complet du serveur (****servername**.database.windows.net*) contenant la base de données à laquelle vous souhaitez vous connecter.

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez à la base de données à laquelle vous souhaitez vous connecter.
3. Recherchez le nom complet du serveur (nous allons l’utiliser dans les étapes à suivre) :

![][1]

## Se connecter à la base de données SQL

1. Ouvrez Visual Studio.
2. Depuis le menu Affichage, ouvrez **SQL Server Object Explorer**.
 
![][2]

3. Cliquez sur le bouton **Ajouter SQL Server**

![][3]

4. Saisissez le *nom du serveur* que nous avons capturé ci-dessus
5. Dans la liste **Authentification**, sélectionnez **Authentification SQL Server**.
6. Saisissez les identifiants de **Connexion** et le **Mot de passe** que vous avez spécifiés lors de la création de votre serveur SQL Database, puis cliquez sur **Connexion**.

## Exécuter les exemples de requêtes

Maintenant que nous avons enregistré notre serveur, passons à l’écriture des requêtes.

1. Cliquez sur la base de données utilisateur dans l’outil SSDT.

2. Cliquez sur le bouton **Nouvelle requête** Une nouvelle fenêtre s’ouvre.

![][4]

3. Saisissez le code suivant dans la fenêtre de la requête :

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Exécutez la requête.

	Pour exécuter la requête, cliquez sur la flèche verte ci-dessous, ou utilisez le raccourci `CTRL`+`SHIFT`+`E` :

## Étapes suivantes

Comme vous pouvez à présent vous connecter et exécuter des requêtes, essayez de [vous connecter avec Power BI][].

[vous connecter avec Power BI]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect-query/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect-query/connection-dialog.png
[4]: ./media/sql-data-warehouse-get-started-connect-query/new-query.png

<!---HONumber=AcomDC_0107_2016-->