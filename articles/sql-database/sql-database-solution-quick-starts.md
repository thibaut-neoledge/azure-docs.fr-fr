<properties
   pageTitle="Démarrages rapides de la solution de base de données SQL Azure | Microsoft Azure"
   description="Découvrez les solutions de base de données SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# Explorer les démarrages rapides de la solution de base de données SQL Azure

Cet article présente une vue d’ensemble des démarrages rapides de la solution de base de données SQL Azure. Ces démarrages rapides se trouvent dans le référentiel d’exemples GitHub SQL Server et montrent l’utilisation de la base de données SQL dans une solution complète basée sur des scénarios concrets. Pour accéder à des didacticiels pas à pas simples illustrant l’utilisation d’une fonctionnalité particulière de la base de données SQL, consultez [Explorer les didacticiels de la base de données SQL Azure](sql-database-explore-tutorials.md).

## Tester la démonstration et les travaux pratiques WingTipTickets

La démonstration et les travaux pratiques [Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets) illustrent l’utilisation d’une base de données Azure SQL Database et d’un exemple d’application Azure Search utilisés pour la vente de tickets de concerts.


## Collecter et surveiller les données d’utilisation des ressources dans plusieurs pools

[Solution Démarrage rapide : Télémétrie de pool élastique à l’aide de PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) apporte une solution pour la collecte et l’analyse de l’utilisation des ressources d’une base de données SQL Azure dans plusieurs pools d’un abonnement. Lorsque vous disposez d’un grand nombre de bases de données dans un abonnement, il est fastidieux d’analyser chaque pool élastique séparément.

Pour résoudre ce problème, vous pouvez associer les applets de commande PowerShell de la base de données SQL et les requêtes T-SQL pour collecter les données d’utilisation des ressources de plusieurs pools et de leurs bases de données. Cela vous permet de surveiller et d’analyser plus efficacement l’utilisation des ressources.

Ce démarrage rapide fournit un ensemble de scripts PowerShell et de requêtes T-SQL, ainsi que la documentation expliquant les actions de la solution et comment l’implémenter.

## Prise en main d’une base de données élastique dans un scénario SaaS

 [Solution Démarrage rapide : Tableau de bord personnalisé du pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) apporte une solution pour un scénario Software-as-a-Solution (SaaS) s’appuyant sur la fonctionnalité Base de données élastique de Base de données SQL afin d’obtenir une base de données principale économique et évolutive pour une application SaaS.

Cette solution vous guidera dans le processus d’implémentation d’une application web. Cette application web vous permet de visualiser la charge créée sur une base de données élastique par un générateur de charge qui utilise un tableau de bord personnalisé en plus du portail Azure.

Ce démarrage rapide fournit un générateur de charge et une application web de surveillance, ainsi que la documentation expliquant les actions de l’app et comment l’utiliser.

## Créer une base de données SQL Azure à l’aide du développement Code First et d’Entity Framework

Cette vidéo et l’exemple de la section [Code First pour une nouvelle base de données](https://msdn.microsoft.com/data/jj193542.aspx) fournissent une introduction au développement Code First ciblant une nouvelle base de données. Ce scénario permet de cibler une base de données qui n’existe pas mais qui sera créée par Code First. Le scénario crée également une base de données vide à laquelle Code First ajoute de nouvelles tables.

Code First vous permet de définir votre modèle à l’aide de classes C# ou Visual Basic .NET. Vous pouvez effectuer une configuration supplémentaire à l’aide d’attributs dans vos classes et propriétés ou à l’aide d’une API Fluent.

## Intégration des outils de base de données élastique dans une application Entity Framework

La [Bibliothèque cliente de la base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) montre les modifications que vous devez apporter à une application Entity Framework pour l’intégrer aux [outils de base de données élastique](sql-database-elastic-scale-get-started.md). L'objectif est de composer une [gestion de carte de partitions](sql-database-elastic-scale-shard-map-management.md) et un [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) avec l'approche Entity Framework Code First.

L[’exemple Code First de nouvelle base de données pour EF](http://msdn.microsoft.com/data/jj193542.aspx) sert d'exemple tout au long de ce document. L’exemple de code qui accompagne ce document fait partie de l’ensemble d’échantillons des outils de base de données élastique figurant parmi les exemples de code Visual Studio.

## Intégrer des outils de base de données élastique à la sécurité au niveau des lignes

La section [Applications mutualisées avec outils de base de données élastique et sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md) affiche les modifications que vous devez apporter à une application Entity Framework pour intégrer des [outils de base de données élastique](sql-database-elastic-scale-get-started.md) à une [sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131). Cet exemple explique comment utiliser ces technologies conjointement, afin de créer une application proposant une couche Données hautement scalables, capable de prendre en charge des partitions multi-locataires.

Vous pouvez le faire à l’aide d’ADO.NET SqlClient ou d’Entity Framework. Cet exemple étend la [bibliothèque cliente de bases de données élastiques à Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) en ajoutant la prise en charge des bases de données de partition multi-locataires. Il permet de créer une application console simple afin de créer des blogs et des publications, avec quatre locataires et deux bases de données de partition multi-locataires.

## Créer des enquêtes en ligne avec l’application Tailspin Surveys

Cet [exemple d’application Tailspin Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) est une application web multi-locataires qui permet aux utilisateurs de créer des enquêtes en ligne. L’exemple aborde certains points clés concernant la gestion des identités utilisateur dans une application mutualisée, notamment l’inscription, l’authentification, l’autorisation et les rôles d’application.

## En savoir plus sur les dernières fonctionnalités de sécurité de la base de données SQL avec l’application de démonstration Contoso Clinic

Cette [application de démonstration Contoso Clinique](https://github.com/Microsoft/azure-sql-security-sample) présente les dernières fonctionnalités de sécurité de la base de données SQL.

## Étapes suivantes

[Exploration des didacticiels relatifs à la base de données SQL Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0907_2016-->