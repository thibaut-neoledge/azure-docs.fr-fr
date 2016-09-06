<properties
   pageTitle="Se connecter à Azure SQL Data Warehouse | Microsoft Azure"
   description="Vue d’ensemble de la connexion à Azure SQL Data Warehouse"
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
   ms.date="08/27/2016"
   ms.author="sonyama;barbkess"/>

# Se connecter à Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-connect-overview.md)
- [Authentification](sql-data-warehouse-authentication.md)
- [Pilotes](sql-data-warehouse-connection-strings.md)

Vue d’ensemble de la connexion à Azure SQL Data Warehouse.

## Découvrir la chaîne de connexion à partir du portail

Votre SQL Data Warehouse est associé à un serveur SQL Azure. Pour vous connecter, vous devez disposer du nom complet du serveur. Par exemple, **myserver**.database.windows.net.

Pour rechercher le nom complet du serveur :

1. Accédez au [portail Azure][].
2. Cliquez sur **Bases de données SQL**, puis sur la base de données à laquelle vous souhaitez vous connecter. Cet exemple utilise l’exemple de base de données AdventureWorksDW.
3. Recherchez le nom complet du serveur.

    ![Nom complet du serveur][1]

## Paramètres de connexion

SQL Data Warehouse standardise quelques paramètres pendant la connexion et la création d’objets. Ces paramètres ne peuvent pas être remplacés.

| Paramètre de base de données | Valeur |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ACTIVÉ |
| [QUOTED\_IDENTIFIERS][] | ACTIVÉ |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## Analyse des connexions et des requêtes

Une fois qu’une connexion et qu’une session ont été établies, vous êtes prêt à écrire et à soumettre des requêtes à SQL Data Warehouse. Pour savoir comment surveiller les sessions et les requêtes, consultez [Surveiller votre charge de travail à l’aide de vues de gestion dynamique][].

## Étapes suivantes

Pour commencer à interroger votre entrepôt de données avec Visual Studio et d’autres applications, consultez [Soumettre des requêtes avec Visual Studio][].

<!--Articles-->
[Soumettre des requêtes avec Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Surveiller votre charge de travail à l’aide de vues de gestion dynamique]: ./sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[portail Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0831_2016-->