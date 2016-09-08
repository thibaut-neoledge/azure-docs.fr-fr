<properties
   pageTitle="Interroger Azure SQL Data Warehouse (sqlcmd)| Microsoft Azure"
   description="Interrogation d’Azure SQL Data Warehouse à l’aide de l’utilitaire de ligne de commande sqlcmd."
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
   ms.date="08/30/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Interroger Azure SQL Data Warehouse (sqlcmd)

> [AZURE.SELECTOR]
- [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
- [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
- [Visual Studio](sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)

Cette procédure pas à pas utilise l’utilitaire de ligne de commande [sqlcmd][] pour interroger un entrepôt de données Azure SQL Data Warehouse.

## 1\. Connecter

Pour commencer à utiliser [sqlcmd][], ouvrez l’invite de commandes et entrez **sqlcmd** suivi de la chaîne de connexion de votre base de données SQL Data Warehouse. La chaîne de connexion requiert les paramètres suivants :

+ **Serveur (-S) :** nom du serveur, sous la forme `<`Nom\_serveur`>`.database.windows.net.
+ **Base de données (-d) :** nom de la base de données.
+ **Utilisateur (-U) :** utilisateur du serveur sous la forme `<`utilisateur`>`
+ **Mot de passe (-P) :** mot de passe associé à l’utilisateur.
+ **Activer les identificateurs marqués (-I) :** les identificateurs marqués doivent être activés pour que la connexion à une instance SQL Data Warehouse soit possible.

Par exemple, votre chaîne de connexion peut ressembler à ceci :

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

> [AZURE.NOTE] L’option -I, qui active les identificateurs entre guillemets, est actuellement requise pour la connexion à SQL Data Warehouse.

## 2\. Requête

Une fois la connexion établie, vous pouvez envoyer des instructions Transact-SQL prises en charge à l’instance. Dans cet exemple, les requêtes sont soumises en mode interactif.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Les exemples ci-après vous indiquent comment exécuter vos requêtes en mode batch à l’aide de l’option -Q ou en redirigeant votre SQL vers sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Étapes suivantes

Consultez la [documentation sqlcmd][sqlcmd] pour en savoir plus sur les options disponibles dans sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->

<!---HONumber=AcomDC_0831_2016-->