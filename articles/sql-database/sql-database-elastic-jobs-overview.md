<properties 
	pageTitle="Vue d'ensemble des tâches de base de données élastiques" 
	description="Illustre le service de tâche de base de données élastique" 
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
	ms.date="06/25/2015" 
	ms.author="sidneyh" />

# Vue d’ensemble des tâches de base de données élastiques

L’option **Tâches de base de données élastique** (version préliminaire) vous permet d’exécuter des scripts T-SQL (tâches) sur toutes les bases de données d’un [pool élastique de bases de données (version préliminaire)](sql-database-elastic-pool.md). Par exemple, vous pouvez facilement mettre à jour le schéma dans chaque base de données afin d'inclure une nouvelle table. Normalement, vous devez vous connecter indépendamment à chaque base de données pour exécuter les instructions T-SQL ou effectuer d'autres tâches administratives. L’option **Tâche de base de données élastique** gère la tâche de connexion et garantit l’exécution du script, tout en consignant le statut d’exécution de chaque base de données. Pour obtenir des instructions sur l’installation de la version préliminaire, consultez la page [Installation des composants de tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md).

![Service de tâche de base de données élastique][1]

## Avantages

* Définir, gérer et conserver des scripts T-SQL à exécuter sur l'ensemble d'un pool élastique de base de données
* Garantir l'exécution des scripts T-SQL avec une nouvelle tentative automatique et à l'échelle
* Suivre l'état d'exécution d'une tâche

## Scénarios

* Tâche administrative de performances, par exemple le déploiement d'un nouveau schéma
* Mise à jour des données de référence, par exemple les informations de produit communes à toutes les bases de données
* Reconstruction des index pour améliorer les performances des requêtes

## Fonctionnement de la tâche

1.	Installez les services utilisés par les tâches de base de données élastiques. Voir [Installation de tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md). Si l'installation échoue, voir [comment désinstaller](sql-database-elastic-jobs-uninstall.md).
2.	Configurez le pool élastique de bases de données pour l'exécution de la tâche via l'[ajout d'un utilisateur à chaque base de données](sql-database-elastic-jobs-add-logins-to-dbs.md).
3.	Créez un script T-SQL idempotent qui peut être exécuté sur chacune des bases de données du pool.
4.	Procédez comme suit pour exécuter le script : [Création et gestion des tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md) 

## Composants et tarification 

Les composants suivants fonctionnent en synergie pour créer un service Cloud Azure permettant l'exécution ad hoc de tâches administratives. Les composants sont installés et configurés automatiquement pendant l'installation, dans le cadre de votre abonnement. Vous pouvez identifier les services car ils portent tous le même nom généré automatiquement. Le nom est unique et se compose d'un préfixe « edj » suivi de 21 caractères générés de façon aléatoire.

* **Service Cloud Azure** : les tâches de base de données élastiques (version préliminaire) sont fournies sous forme d'un service Cloud Azure hébergé par le client pour exécuter les tâches demandées. À partir du portail, le service est déployé et hébergé dans votre abonnement Microsoft Azure. Par défaut, le service déployé s'exécute avec un minimum de deux rôles de travail pour garantir une haute disponibilité. La taille par défaut de chaque rôle de travail (ElasticDatabaseJobWorker) s'exécute sur une instance A0. Pour la tarification, voir [Tarification des services cloud](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Base de données SQL Azure** : le service utilise une base de données SQL Azure appelée **base de données de contrôle** pour stocker toutes les métadonnées de la tâche. Le niveau de service par défaut est S0. Pour en savoir plus, voir [Tarification de la base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).
* **Azure Service Bus** : Azure Service Bus sert à coordonner le travail au sein du service Cloud Azure. Voir [Tarification de Service Bus](http://azure.microsoft.com/pricing/details/service-bus/).
* **Azure Storage** : un compte Azure Storage sert à stocker la journalisation des résultats de diagnostic au cas où un problème nécessite un débogage supplémentaire (une pratique courante pour les [diagnostics Azure](../cloud-services-dotnet-diagnostics.md)). Pour la tarification, voir[Tarification d'Azure Storage](http://azure.microsoft.com/pricing/details/storage/).

## Étapes suivantes
[Installez les composants](sql-database-elastic-jobs-service-installation.md) puis [créez et ajoutez une connexion à chaque base de données du pool](sql-database-elastic-jobs-add-logins-to-dbs.md). Pour mieux comprendre la création et la gestion de tâches, consultez la page [Création et gestion de tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=August15_HO6-->