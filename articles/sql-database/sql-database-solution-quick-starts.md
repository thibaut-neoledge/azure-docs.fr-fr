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
   ms.date="06/22/2016"
   ms.author="carlrab"/>

# Explorer les démarrages rapides de la solution de base de données SQL Azure

Cet article présente une vue d’ensemble des démarrages rapides de la solution de base de données SQL Azure. Ces démarrages rapides montrent l’utilisation de la base de données SQL dans la solution complète à partir de scénarios réels. Pour accéder à des didacticiels pas à pas simples illustrant l’utilisation d’une fonctionnalité particulière de la base de données SQL Azure, consultez [Explorer les didacticiels de la base de données SQL Azure](sql-database-explore-tutorials.md).

## Démonstration et travaux pratiques WingTipTickets

[Démonstration et les travaux pratiques WingTipTickets Azure SQL Database](https://github.com/microsoft/wingtiptickets) Ces fichiers comprennent des travaux pratiques qui illustrent l’utilisation d’une base de données Azure SQL Database et d’un exemple d’application Azure Search utilisés pour la vente de tickets de concerts.

## Collecter et surveiller les données d’utilisation des ressources dans plusieurs pools

Ce démarrage rapide apporte une solution pour la collecte et l’analyse de l’utilisation des ressources d’une base de données SQL Azure dans plusieurs pools d’un abonnement. Lorsque vous disposez d’un grand nombre de bases de données dans un abonnement, il est fastidieux d’analyser chaque pool élastique séparément. Pour éviter ce problème, vous pouvez associer les applets de commande PowerShell de la base de données SQL et les requêtes T-SQL pour collecter les données d’utilisation des ressources de plusieurs pools et de leurs bases de données pour la surveillance et l’analyse de l’utilisation des ressources.

[Manage Mulitiple Elastic Pools in SQL Database Using PowerShell and Power BI (Gérer plusieurs pools élastiques dans SQL Database avec PowerShell et Power BI)](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) dans le dépôt GitHub d’exemples SQL Server fournit un ensemble de scripts PowerShell et de requêtes T-SQL, ainsi que la documentation de son rôle et de son utilisation.

## Prendre en main les pools élastiques dans un scénario SaaS

Ce démarrage rapide apporte une solution pour un scénario software as a solution (SaaS) qui s’appuie sur des pools élastiques pour obtenir une base de données principale économique et évolutive d’une application SaaS. Dans cette solution, nous vous guiderons dans l’implémentation d’une application web qui vous permettra de visualiser la charge créée sur un pool élastique par un générateur de charge à l’aide d’un tableau de bord personnalisé en complément du portail Azure.

Le [tableau de bord personnalisé du pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) dans le dépôt GitHub d’exemples SQL Server fournit un générateur de charge et une application web de surveillance, ainsi que la documentation de son rôle et de son utilisation.

## Création d’une base de données SQL Azure à l’aide d’Entity Framework et le développement Code First

Cette vidéo et cet exemple fournissent une introduction au développement Code First ciblant une nouvelle base de données. Ce scénario inclut une base de données qui n’existe pas et que Code First ou une base de données vide à laquelle Code First ajoute de nouvelles tables. Code First vous permet de définir votre modèle à l’aide de classes C# ou VB.Net. Une configuration supplémentaire peut éventuellement être effectuée à l’aide d’attributs dans les classes et les propriétés ou à l’aide d’une API Fluent. Consultez la page [Code First pour une nouvelle base de données](https://msdn.microsoft.com/data/jj193542.aspx).

## Intégration des outils de base de données élastique dans une application Entity Framework

Cet exemple présente les modifications d’une application Entity Framework nécessaires pour l’intégration dans des [outils des bases de données élastiques](sql-database-elastic-scale-get-started.md). L’objectif est de composer une [gestion de carte de partitions](sql-database-elastic-scale-shard-map-management.md) et un [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) avec l’approche Entity Framework Code First. L[’exemple Code First de nouvelle base de données pour EF](http://msdn.microsoft.com/data/jj193542.aspx) sert d’exemple tout au long de ce document. L’exemple de code qui accompagne ce document fait partie de l’ensemble d’échantillons des outils de base de données élastique figurant parmi les exemples de code Visual Studio. Consultez [Bibliothèque cliente de la base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md).

## Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes

Cet exemple présente les modifications apportées à une application Entity Framework nécessaires pour l’intégration dans des [outils de bases de données élastiques](sql-database-elastic-scale-get-started.md) avec une [sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131). Cet exemple explique comment utiliser ces technologies conjointement, afin de créer une application proposant une couche Données hautement scalables, capable de prendre en charge des partitions multi-locataires, en utilisant SqlClient ADO.NET et/ou Entity Framework. Cet exemple étend la [bibliothèque cliente de bases de données élastiques à Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) en ajoutant la prise en charge des bases de données de partition multi-locataires. Il permet de créer une application console simple afin de créer des blogs et des publications, avec quatre locataires et deux bases de données de partition multi-locataires. Consultez [Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md).

## Application Tailspin Surveys

Cet exemple est une application web multi-locataires, appelée Surveys, qui permet aux utilisateurs de créer des enquêtes en ligne. L’exemple illustre certains points clés pour la gestion des identités utilisateur dans une application multi-locataires, notamment l’inscription, l’authentification, l’autorisation et les rôles d’application. Pour exécuter cet exemple, consultez la rubrique concernant [l’exécution de l’exemple d’application Tailspin Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

## Application de démonstration Contoso Clinic

Cet exemple présente les fonctionnalités de sécurité d’Azure SQL DB (V12). Pour exécuter cet exemple, consultez [Application de démonstration Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample).

## Étapes suivantes

[Exploration des didacticiels relatifs à la base de données SQL Azure](sql-database-explore-tutorials.md)

<!---HONumber=AcomDC_0720_2016-->