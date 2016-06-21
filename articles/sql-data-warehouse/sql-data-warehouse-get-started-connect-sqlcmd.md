<properties
   pageTitle="Exécuter des requêtes avec SQLCMD | Microsoft Azure"
   description="Découvrez comment exécuter des requêtes auprès de votre Data Warehouse avec SQLCMD."
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
   ms.date="06/09/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Exécuter des requêtes avec SQLCMD

> [AZURE.SELECTOR]
- [Power BI][]
- [Azure Machine Learning][]
- [SQLCMD][]

Cette procédure détaillée vous indique comment exécuter des requêtes auprès d’Azure SQL Data Warehouse à l’aide de l’utilitaire sqlcmd.exe.

## Configuration requise

+ Pour télécharger [sqlcmd.exe][], voir [Utilitaires en ligne de commande 11 de Microsoft pour SQL Server][].

## Connecter

Pour commencer à utiliser sqlcmd, ouvrez l’invite de commandes et entrez **sqlcmd** suivi de la chaîne de connexion de votre base de données SQL Data Warehouse. La chaîne de connexion doit contenir les paramètres suivants :

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

## Interroger

Une fois la connexion établie, vous pouvez envoyer des instructions Transact-SQL prises en charge à l’instance. Dans cet exemple, les requêtes sont soumises en mode interactif.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Les exemples ci-après vous indiquent comment exécuter vos requêtes en mode batch à l’aide de l’option -Q ou en redirigeant votre SQL vers sqlcmd.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
C:\>"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## Étapes suivantes

Pour plus d’informations sur l’ensemble des options de l’utilitaire sqlcmd, voir [Utilitaire sqlcmd][sqlcmd.exe].

<!--Articles-->
[connecting with PowerBI]: ./sql-data-warehouse-integrate-power-bi.md
[Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Azure Machine Learning]: ./sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md
[SQLCMD]: ./sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other-->
[sqlcmd.exe]: https://msdn.microsoft.com/fr-FR/library/ms162773.aspx
[Utilitaires en ligne de commande 11 de Microsoft pour SQL Server]: http://go.microsoft.com/fwlink/?LinkId=321501
[Azure portal]: https://portal.azure.com

<!--Image references-->

<!---HONumber=AcomDC_0615_2016-->