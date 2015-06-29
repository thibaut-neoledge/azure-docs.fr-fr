<properties 
	title="Elastic database query – previewing May 2015" 
	pageTitle="Requête de base de données élastique : version préliminaire prévue pour mai 2015" 
	description="Annonce relative à la fonctionnalité de requête élastique" 
	metaKeywords="azure sql database elastic global queries" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh" />

# Requête de base de données élastique : version préliminaire prévue pour juin 2015 

Les requêtes de base de données élastique sont une nouvelle fonctionnalité de la base de données SQL Microsoft Azure. Une version préliminaire de cette nouvelle fonction est prévue à la fin du mois de juin 2015. Elle introduit la possibilité d’exécuter une requête T-SQL étendue sur plusieurs bases de données, à l’aide d’une connexion unique à une base de données SQL Microsoft Azure. Ainsi, un jeu de données partitionné défini à l’aide d’un mappage de partition de base de données élastique s’affiche sous la forme d’un magasin de données intégré unique. Le traitement des requêtes distribuées utilisées à des fins d’intégration des données et de création de rapports est intégralement effectué en arrière-plan. Les connexions sont prises en charge via n’importe quel pilote capable de communiquer avec la base de données SQL, notamment ADO.Net, ODBC, JDBC, Node.js, PHP, etc.

## Requêtes de base de données élastique : scénarios

L’objectif d’une requête de base de données élastique est de simplifier les scénarios de création de rapports sur une couche Données partitionnée dans laquelle plusieurs bases de données ou partitions fournissent des lignes, regroupées au sein du résultat global unique d’une requête T-SQL. Cette requête T-SQL peut être composée de manière directe par l’utilisateur ou l’application, ou de manière indirecte, via des outils connectés à la base de données élastique globale associée aux requêtes. Cette option est particulièrement utile pour les outils décisionnels commerciaux ou les outils de création de rapports et d’intégration des données, ainsi que les packages de logiciels qui ne peuvent pas être étendus directement à l’aide des bibliothèques de l’infrastructure élastique. Elle permet également d’accéder facilement à une collection entière de données partitionnées, via des requêtes émises par SQL Server Management Studio ou Visual Studio, et vous assure un accès transparent aux données de plusieurs partitions, à partir d’Entity Framework ou d’autres environnements ORM. La figure 1 illustre ce scénario, dans lequel une requête de base de données élastique de requêtes globales propose une autre méthode pour soumettre des requêtes à la couche Données partitionnée en plus de l’application cloud existante (qui peut utiliser des bibliothèques de l’infrastructure élastique).

![Requêtes de base de données élastique][1]

Les scénarios peuvent être caractérisés par les topologies suivantes :

-	Couche Données partitionnée (topologie 1) : la couche Données a été conçue pour le partitionnement. Ce partitionnement est effectué et géré à l’aide de (1) la bibliothèque cliente de base de données élastique ou (2) la fonction d’auto-partitionnement. Le processus de création de rapports repose sur l’élaboration de rapports entre plusieurs partitions présentant des exigences plus élevées que ce que propose une requête sur plusieurs partitions dans la bibliothèque cliente de base de données élastique, en termes de fonctions ou de connectivité. 

-	Conception de bases de données multiples (topologie 2) : dans ce cas, la couche Données a été partitionnée verticalement, de sorte que différents types de données résident sur différentes bases de données. Le processus de création de rapports repose sur l’élaboration de rapports sur les données distribuées sur plusieurs bases de données, mais qui doivent être intégrées dans un seul résultat global pour les besoins du rapport.

La version préliminaire de la requête de base de données élastique portera sur ces deux processus. Les clients présentant la topologie 1 peuvent se fier à leur mappage de partition existant, s’ils utilisent la bibliothèque cliente de base de données élastique pour gérer les partitions. Les adeptes de l’auto-partitionnement devront créer un mappage de partition en utilisant les outils de base de données élastique associés à leurs couches Données, afin de pouvoir utiliser cette fonction de version préliminaire. Quant aux clients présentant la topologie 2, ils doivent créer un mappage de partition différent pour chacune de leurs bases de données. Ces mappages de partition doivent pointer vers une seule partition, qui peut alors exposer ses tables pour les requêtes entre partitions.

## Étapes suivantes
La fonction de requête de base de données élastique doit faire l’objet d’une version préliminaire à la fin du mois de juin 2015. Pour en savoir plus et obtenir une procédure pas à pas sur l’utilisation de cette fonction, le moment venu, consultez à nouveau cette page.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
<!--anchors-->

<!---HONumber=58_postMigration-->