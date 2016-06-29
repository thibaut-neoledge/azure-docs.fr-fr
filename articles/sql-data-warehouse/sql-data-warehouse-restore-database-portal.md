<properties
   pageTitle="Restauration d’un Azure SQL Data Warehouse (Portail) | Microsoft Azure"
   description="Tâches du portail Azure permettant de restaurer un Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/11/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restauration d’un Azure SQL Data Warehouse (Portail)

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Portail][]
- [PowerShell][]
- [REST][]

Dans cet article, vous allez apprendre à restaurer un Azure SQL Data Warehouse à l’aide du portail Azure.

## Avant de commencer

**Vérifiez votre capacité de DTU.** Chaque SQL Data Warehouse est hébergé par un serveur SQL logique. Ce serveur logique a une limite de capacité mesurée en DTU. Avant de pouvoir restaurer un SQL Data Warehouse, il est important de s’assurer que le serveur SQL logique qui héberge votre base de données a une capacité de DTU suffisante pour la base de données en cours de restauration. Consultez ce blog pour plus d’informations sur [l’affichage et l’augmentation du quota de DTU][].


## Restauration d’une base de données active ou en pause

Pour restaurer une base de données :

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Bases de données SQL**
3. Accédez à votre base de données et sélectionnez-la.
4. En haut du panneau de la base de données, cliquez sur **Restaurer**.
5. Spécifiez un nouveau **nom de base de données**, sélectionnez un **point de restauration**, puis cliquez sur **Créer**.
6. Le processus de restauration de la base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.

## restauration d’une base de données supprimée.

Pour restaurer une base de données supprimée :

1. Connectez-vous au [portail Azure][].
2. Sur le côté gauche de l’écran, sélectionnez l’option **PARCOURIR**, puis choisissez **Serveur SQL Server**.
3. Accédez à votre serveur et sélectionnez-le.
4. Dans le panneau du serveur, faites défiler jusqu’à Opérations, puis cliquez sur la vignette **Bases de données supprimées**.
5. Sélectionnez la base de données supprimée que vous souhaitez restaurer.
5. Spécifiez un nouveau **nom de base de données**, puis cliquez sur **Créer**.
6. Le processus de restauration de la base de données commence. Vous pouvez le surveiller à l’aide du volet **NOTIFICATIONS**.


## Étapes suivantes
Pour plus d’informations sur les fonctionnalités de continuité d’activité des éditions de Base de données SQL Azure, voir la [vue d’ensemble de la continuité des activités de la base de données SQL Azure][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble de la continuité des activités de la base de données SQL Azure]: ./sql-database-business-continuity.md
[Vue d'ensemble]: ./sql-data-warehouse-restore-database-overview.md
[Portail]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->

<!--Blog references-->
[l’affichage et l’augmentation du quota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[portail Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0615_2016-->