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
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh" />

# Vue d’ensemble des tâches de base de données élastiques

La fonctionnalité **Tâches de bases de données élastiques** (version préliminaire) vous permet d'exécuter un script Transact-SQL (T-SQL) ou d’appliquer un DACPAC dans un groupe de bases de données, notamment une collection de bases de données personnalisées, toutes les bases de données dans un [pool de bases de données élastique (version préliminaire)](sql-database-elastic-pool.md) ou un ensemble de partitions (créé à l’aide d’une [bibliothèque cliente d’une base de données élastique](sql-database-elastic-database-client-library.md)). Dans la version préliminaire, la fonctionnalité **Tâches de bases de données élastiques** est actuellement un service cloud Azure hébergé par le client qui permet l'exécution des tâches administratives ad hoc et planifiées, appelées tâches. L’utilisation de cette fonctionnalité vous permet de gérer facilement, et de manière fiable, un grand nombre de bases de données SQL Azure à grande échelle en exécutant des scripts Transact-SQL pour réaliser des opérations administratives telles que les modifications de schéma, la gestion des informations d’identification, les mises à jour de données de référence, la collecte des données de performances ou la collecte télémétrique du locataire (client). Normalement, vous devez vous connecter indépendamment à chaque base de données pour exécuter les instructions Transact-SQL ou effectuer d'autres tâches administratives. La fonctionnalité **Tâches de bases de données élastiques** gère la tâche de connexion et garantit l'exécution du script, tout en consignant le statut d'exécution de chaque base de données. Pour obtenir des instructions sur l’installation, consultez la page [Installation des composants de tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md).

![Service de tâche de base de données élastique][1]

## Avantages
* Définition des groupes personnalisés de bases de données SQL Azure
* Définir, gérer et conserver des scripts Transact-SQL à exécuter dans un groupe de bases de données SQL Azure 
* Déployer une application de couche Données (DACPAC)
* Exécuter des scripts Transact-SQL ou l'application de DACPAC fiables avec de nouvelles tentatives automatiques et à grande échelle
* Suivre l'état d'exécution d'une tâche
* Définir des calendriers d'exécution
* Effectuer la collecte de données dans une collection de bases de données SQL Azure en enregistrant les résultats dans une table de destination unique

> [AZURE.NOTE]La fonctionnalité **Tâches de bases de données élastique** dans le portail Azure expose un ensemble de fonctionnalités limité et également limité aux pools élastiques SQL Azure. Utiliser les APIs PowerShell pour accéder à l'ensemble des fonctionnalités actuelles.

## Scénarios

* Tâche administrative de performances, par exemple le déploiement d'un nouveau schéma
* Mettre à jour les données de référence, par exemple les informations de produit communes à toutes les bases de données, même à l’aide des planifications pour automatiser les mises à jour chaque jour ouvrable après plusieurs heures.
* Recréer des indexes pour améliorer les performances d’une requête. La reconstruction peut être configurée pour s'exécuter dans une collection de bases de données de façon récurrente, par exemple pendant les heures creuses.
* Collecter les résultats de la requête à partir d'un ensemble de bases de données dans une table centrale sur une base continue. Les requêtes de performances peuvent être exécutées en permanence et configurées pour déclencher des tâches supplémentaires à exécuter.
* Exécuter des requêtes de traitement de données avec un temps d’exécution plus long sur un grand ensemble de bases de données, par exemple, la collection de télémétrie de client. Les résultats sont rassemblés dans une table de destination unique pour une analyse ultérieure.

## Révision simple de bout en bout des tâches de bases de données élastiques
1.	Installez les composants de **Tâches de bases de données élastiques**. Pour en savoir plus, consultez [Installation des tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md). Si l'installation échoue, voir [comment désinstaller](sql-database-elastic-jobs-uninstall.md).
2.	Utilisez les APIs PowerShell pour accéder à davantage de fonctionnalités, par exemple la création de collections de bases de données personnalisées, l’ajout de planifications et/ou la collecte des ensembles de résultats. Utilisez le portail pour une installation simple et la création/la surveillance des tâches limitées à l'exécution par rapport à un **pool de bases de données élastiques**. 
3.	Créez des informations d'identification chiffrées pour l'exécution d’une tâche et [ajoutez l'utilisateur (ou le rôle) à chaque base de données du groupe](sql-database-elastic-jobs-add-logins-to-dbs.md).
4.	Créez un script T-SQL idempotent qui peut être exécuté sur chacune des bases de données du pool.
5.	Procédez comme suit pour exécuter le script : [Création et gestion des tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md) 

## Composants et tarification 
Les composants suivants fonctionnent en synergie pour créer un service Cloud Azure permettant l'exécution ad hoc de tâches administratives. Les composants sont installés et configurés automatiquement pendant l'installation, dans le cadre de votre abonnement. Vous pouvez identifier les services car ils portent tous le même nom généré automatiquement. Le nom est unique et se compose d'un préfixe « edj » suivi de 21 caractères générés de façon aléatoire.

* **Service Cloud Azure** : les tâches de base de données élastiques (version préliminaire) sont fournies sous forme d'un service Cloud Azure hébergé par le client pour exécuter les tâches demandées. À partir du portail, le service est déployé et hébergé dans votre abonnement Microsoft Azure. Par défaut, le service déployé s'exécute avec un minimum de deux rôles de travail pour garantir une haute disponibilité. La taille par défaut de chaque rôle de travail (ElasticDatabaseJobWorker) s'exécute sur une instance A0. Pour la tarification, voir [Tarification des services cloud](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Base de données SQL Azure** : le service utilise une base de données SQL Azure appelée **base de données de contrôle** pour stocker toutes les métadonnées de la tâche. Le niveau de service par défaut est S0. Pour en savoir plus, voir [Tarification de la base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).
* **Azure Service Bus** : Azure Service Bus sert à coordonner le travail au sein du service Cloud Azure. Voir [Tarification de Service Bus](http://azure.microsoft.com/pricing/details/service-bus/).
* **Azure Storage** : un compte Azure Storage sert à stocker la journalisation des résultats de diagnostic au cas où un problème nécessite un débogage supplémentaire (une pratique courante pour les [diagnostics Azure](../cloud-services-dotnet-diagnostics.md)). Pour la tarification, voir[Tarification d'Azure Storage](http://azure.microsoft.com/pricing/details/storage/).

## Fonctionnement des tâches de bases de données élastiques
1.	Une base de données SQL Azure est conçue comme une base de données de contrôle qui stocke toutes les métadonnées et les données d’état.
2.	Pour accéder à la base de données de contrôle, accédez à la fonctionnalité **Tâches de bases de données élastiques** pour lancer et effectuer le suivi des tâches à exécuter.
3.	Deux rôles communiquent avec la base de données de contrôle : 
	* Contrôleur : détermine les tâches qui nécessitent d’autres tâches pour effectuer la tâche demandée, ainsi que les tâches des nouvelles tentatives ayant échoué en créant de nouvelles tâches.
	* Exécution de tâches de travail : exécute les tâches de travail.

### Types de tâches de travail
Il existe plusieurs types de tâches qui effectuent l'exécution des tâches :

* ShardMapRefresh : interroge la carte de partitions pour déterminer toutes les bases de données utilisées en tant que partitions
* ScriptSplit : répartit le script sur l’instruction « GO » dans des lots
* ExpandJob : crée des tâches enfants pour chaque base de données à partir d'une tâche qui cible un groupe de bases de données
* ScriptExecution : exécute un script sur une base de données spécifique à l'aide des informations d'identification définies
* Dacpac : applique un DACPAC à une base de données à spécifique à l'aide des informations d'identification spécifiques

## Flux de travail de l'exécution de tâche de bout en bout
1.	À l'aide du portail ou de l'API PowerShell, une tâche est insérée dans la **base de données de contrôle**. La tâche demande l'exécution d'un script Transact-SQL sur un groupe de bases de données à l'aide des informations d'identification spécifiques.
2.	Le contrôleur identifie la nouvelle tâche. Les tâches sont créées et exécutées pour fractionner le script et pour actualiser les bases de données du groupe. Enfin, une nouvelle tâche est créée et exécutée pour développer la tâche et créer des tâches enfants où chacune de celles-ci est configurée pour exécuter le script Transact-SQL sur une base de données individuelle du groupe.
3.	Le contrôleur identifie la nouvelle tâche enfant. Pour chaque tâche, le contrôleur crée et déclenche une tâche pour exécuter le script sur une base de données. 
4.	Une fois toutes les tâches terminées, le contrôleur met à jour et définit les tâches sur l’état terminé. À tout moment pendant l'exécution de la tâche, l'API PowerShell peut être utilisée pour afficher l'état actuel de l'exécution de la tâche. Toutes les heures retournées par les APIs PowerShell sont représentées au format UTC. Si vous le souhaitez, une demande d'annulation peut être lancée pour arrêter une tâche. 

## Étapes suivantes
[Installez les composants](sql-database-elastic-jobs-service-installation.md) puis [créez et ajoutez un journal dans chaque base de données du groupe](sql-database-elastic-jobs-add-logins-to-dbs.md). Pour mieux comprendre la création et la gestion de tâches, consultez la page [Création et gestion de tâches de bases de données élastiques](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=August15_HO7-->