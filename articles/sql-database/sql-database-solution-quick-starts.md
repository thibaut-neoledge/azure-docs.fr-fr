<properties
   pageTitle="Démarrages rapides de la solution de base de données SQL Azure | Microsoft Azure"
   description="Découvrez les solutions de base de données SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="06/01/2016"
   ms.author="carlrab"/>

# Explorer les démarrages rapides de la solution de base de données SQL Azure

Cet article présente une vue d’ensemble des démarrages rapides de la solution de base de données SQL Azure. Ces démarrages rapides montrent l’utilisation de la base de données SQL dans la solution complète à partir de scénarios réels. Pour accéder à des didacticiels pas à pas simples illustrant l’utilisation d’une fonctionnalité particulière de la base de données SQL Azure, consultez [Explorer les didacticiels de la base de données SQL Azure](sql-database-explore-tutorials.md).

## Collecter et surveiller les données d’utilisation des ressources dans plusieurs pools

Ce démarrage rapide apporte une solution pour la collecte et l’analyse de l’utilisation des ressources d’une base de données SQL Azure dans plusieurs pools d’un abonnement. Lorsque vous disposez d’un grand nombre de bases de données dans un abonnement, il est fastidieux d’analyser chaque pool élastique séparément. Pour éviter ce problème, vous pouvez associer les applets de commande PowerShell de la base de données SQL et les requêtes T-SQL pour collecter les données d’utilisation des ressources de plusieurs pools et de leurs bases de données pour la surveillance et l’analyse de l’utilisation des ressources.

[Gérer plusieurs pools élastiques dans une base de données SQL avec PowerShell et Power BI](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) dans le référentiel GitHub d’exemples SQL Server fournit un ensemble de scripts PowerShell et de requêtes T-SQL, ainsi que la documentation de son rôle et de son utilisation.

## Prendre en main les pools élastiques dans un scénario SaaS

Ce démarrage rapide apporte une solution pour un scénario software as a solution (SaaS) qui s’appuie sur des pools élastiques pour obtenir une base de données principale économique et évolutive d’une application SaaS. Dans cette solution, nous vous guiderons dans l’implémentation d’une application web qui vous permettra de visualiser la charge créée sur un pool élastique par un générateur de charge à l’aide d’un tableau de bord personnalisé en complément du portail Azure.

[Saas-scenario-with-elastic-pools](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) dans le référentiel GitHub d’exemples SQL Server fournit un générateur de charge et une application web de surveillance, ainsi que la documentation de son rôle et de son utilisation.

## Étapes suivantes

[Exploration des didacticiels relatifs à la base de données SQL Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0608_2016-->