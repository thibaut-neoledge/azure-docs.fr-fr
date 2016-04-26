<properties
   pageTitle="Prise en main : connexion à SQL Data Warehouse Azure | Microsoft Azure"
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
   ms.author="mausher;barbkess;sonyama"/>

# Se connecter et lancer des requêtes avec SQLCM

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

Cette procédure pas à pas vous explique comment vous connecter à une base de données Azure SQL Data Warehouse et l’interroger en quelques minutes en utilisant l’utilitaire sqlcmd.exe. Lors de cette procédure pas à pas, vous allez :

+ Installer les logiciels requis
+ Connecter une base de données contenant l’exemple de base de données AdventureWorksDW.
+ Exécuter une requête sur l’exemple de base de données  

## Composants requis

+ [sqlcmd.exe](https://msdn.microsoft.com/library/azure/ms162773.aspx) : pour télécharger sqlcmd.exe, veuillez consulter les [ Utilitaires de ligne de commande Microsoft 11 pour SQL Server](http://go.microsoft.com/fwlink/?LinkId=321501).

## Obtenir le nom complet de votre serveur SQL Azure

Pour vous connecter à votre base de données, vous devez disposer du nom complet du serveur (****servername**.database.windows.net*) contenant la base de données à laquelle vous souhaitez vous connecter.

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez à la base de données à laquelle vous souhaitez vous connecter.
3. Recherchez le nom complet du serveur (nous allons l’utiliser dans les étapes à suivre) :

![][1]


## Se connecter à SQL Data Warehouse avec sqlcmd

Pour vous connecter à une instance spécifique de SQL Data Warehouse en utilisant sqlcmd, vous devez ouvrir la fenêtre d’invite de commandes et saisir la chaîne **sqlcmd**, suivie de la chaîne de connexion de votre base de données SQL Data Warehouse. La chaîne de connexion doit contenir les paramètres suivants :

+ **Serveur (-S) :** nom du serveur, sous la forme `<`Nom\_serveur`>`.database.windows.net.
+ **Base de données (-D) :** nom de la base de données.
+ **Utilisateur (-U) :** utilisateur du serveur sous la forme `<`utilisateur`>`
+ **Mot de passe (-P) :** mot de passe associé à l’utilisateur.
+ **Activer les identificateurs marqués (-I) :** les identificateurs marqués doivent être activés pour que la connexion à une instance SQL Data Warehouse soit possible.

Par conséquent, pour vous connecter à une instance SQL Data Warehouse, saisissez ce qui suit :

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
```

## Exécuter les exemples de requêtes

Une fois la connexion établie, vous pouvez envoyer des instructions Transact-SQL prises en charge à l’instance.

```sql
C:\>sqlcmd -S <Server Name>.database.windows.net -d <Database> -U <User> -P <Password> -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Pour en savoir plus sur sqlcmd, consultez la [documentation relative à sqlcmd](https://msdn.microsoft.com/library/azure/ms162773.aspx).


## Étapes suivantes

Comme vous pouvez à présent vous connecter et exécuter des requêtes, essayez de [vous connecter avec Power BI][].

[vous connecter avec Power BI]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png

<!---HONumber=AcomDC_0420_2016-->