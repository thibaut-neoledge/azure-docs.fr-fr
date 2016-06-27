<properties
	pageTitle="Connexion à une base de données SQL avec PHP sous Windows | Microsoft Azure"
	description="Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d'un client Windows et fournit des liens vers les composants logiciels nécessaires requis par le client."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2016"
	ms.author="meetb"/>


# Connexion à SQL Database à l'aide de PHP sur Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique montre comment vous connecter à Azure SQL Database à partir d'une application cliente écrite en PHP qui s'exécute sur Windows.

## Étape 1 : configurer l’environnement de développement

[Configurer l’environnement de développement pour le développement PHP](https://msdn.microsoft.com/library/mt720663.aspx)

## Étape 2 : créer une base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## Étape 3 : obtenir les informations de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Étape 4 : Exécuter l’exemple de code

* [Preuve de concept sur la connexion à SQL avec PHP](https://msdn.microsoft.com/library/mt720665.aspx)
* [Se connecter de façon robuste à SQL avec PHP](https://msdn.microsoft.com/library/mt720667.aspx)


## Étapes suivantes


Pour plus d'informations sur l'installation et l'utilisation de PHP, consultez [Accès aux bases de données du serveur SQL avec PHP](http://social.technet.microsoft.com/wiki/contents/articles/1258.accessing-sql-server-databases-from-php.aspx).

## Ressources supplémentaires

Pour plus d’informations sur les modèles d’architecture de données des applications de base de données de logiciels en tant que service (SaaS) mutualisés, consultez [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

<!---HONumber=AcomDC_0615_2016-->