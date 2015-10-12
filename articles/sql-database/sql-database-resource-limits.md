<properties
	pageTitle="Limites de ressources de base de données SQL Azure"
	description="Cette page décrit certaines limites de ressources courantes pour une base de données SQL Azure."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="09/11/2015"
	ms.author="jroth" />


# Limites de ressources de base de données SQL Azure

## Vue d’ensemble

La base de données SQL Azure gère les ressources disponibles pour une base de données à l’aide de deux mécanismes différents : **Gouvernance des ressources** et **Application de limites**. Cette rubrique décrit ces deux domaines principaux de la gestion des ressources.

## Gouvernance des ressources
L’un des objectifs de conception des niveaux de service Basique, Standard et Premium est que la base de données SQL Azure se comporte comme si elle s’exécutait sur sa propre machine, complètement isolée des autres bases de données. La gouvernance des ressources émule ce comportement. Si l’utilisation des ressources agrégées atteint les ressources maximum disponibles de processeur, de mémoire, d’E/S du journal et d’E/S des données affectées à la base de données, la gouvernance des données mettra en file d’attente les requêtes en exécution et affectera des ressources aux requêtes en file d’attente à mesure qu’elles se libèrent.

Comme sur une machine dédiée, l’utilisation de toutes les ressources disponibles entraîne une exécution plus longue des requêtes en cours d’exécution, ce qui peut provoquer des délais d’expiration de commande sur le client. Les applications avec la logique de nouvelle tentative agressive et les applications qui exécutent des requêtes sur la base de données avec une fréquence élevée peuvent rencontrer des messages d’erreur lorsque vous tentez d’exécuter de nouvelles requêtes et que la limite de demandes simultanées a été atteinte.

### Recommandations :
Surveillez l’utilisation des ressources, ainsi que les temps de réponse moyens des requêtes lorsque vous approchez de l’utilisation maximale d’une base de données. Si vous rencontrez des latences de requête supérieures, généralement, trois options s’offrent à vous :

1.	Réduire la quantité de requêtes entrantes dans la base de données afin d’éviter l’expiration et l’accumulation de demandes.

2.	Affecter un niveau de performance supérieur à la base de données.

3.	Optimiser les requêtes pour réduire l’utilisation des ressources de chaque requête. Pour plus d’informations, consultez la section Analyse/Compréhension de requêtes de l’article Guide des performances de base de données SQL Azure.

## Application de limites
Les ressources autres que le processeur, la mémoire, les E/S de journal et les E/S de données sont appliquées en refusant les nouvelles demandes lorsque les limites sont atteintes. Les clients reçoivent un [message d’erreur](sql-database-develop-error-messages.md) en fonction de la limite qui a été atteinte.

Par exemple, le nombre de connexions à une base de données SQL ainsi que le nombre de demandes simultanées qui peuvent être traitées est limité. La base de données SQL permet un nombre de connexions à la base de données supérieur au nombre de demandes simultanées afin de prendre en charge le groupement de connexions. Bien que la quantité de connexions disponibles puisse facilement être contrôlée par l’application, la quantité de demandes parallèles est souvent bien plus difficile à estimer et à contrôler. Il est possible de rencontrer des erreurs notamment pendant les charges maximales, lorsque l’application envoie trop de demandes ou que la base de données atteint ses limites de ressources et commence à empiler les threads de travail en raison de l’exécution de requêtes plus longues.

## Niveaux de service et niveaux de performances

Pour une base de données unique, les limites d’une base de données sont définies par ses niveaux de service et de performances. Le tableau suivant décrit les caractéristiques des bases de données De base, Standard et Premium de bases de données à différents niveaux de performances.

[AZURE.INCLUDE [Tableau de niveaux de service de base de données SQL](../../includes/sql-database-service-tiers-table.md)]

Les [pools de base de données élastique](sql-database-elastic-pool.md) partagent des ressources entre les bases de données dans le pool. Le tableau suivant décrit les caractéristiques des pools de base de données élastique De base, Standard et Premium.

[AZURE.INCLUDE [Niveaux de service de base de données SQL pour les bases de données élastiques](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Pour obtenir une explication détaillée des niveaux de service, consultez [Niveaux de service et de performances dans Azure SQL Database](sql-database-service-tiers.md).

## Quota de DTU par serveur

La base de données SQL Azure a un quota de DTU par serveur logique d’actuellement 2 000 DTU. Ce quota représente les DTU qu’un serveur logique peut héberger, en fonction de la somme des DTU et du niveau de performance de chaque base de données sur le serveur. Par exemple, un serveur avec 5 bases de données Basique (5 x 5 DTU maximum), 2 bases de données Standard S1 (2 x 20 DTU maximum) et 3 bases de données Premium P1 (3 x 100 DTU maximum) a consommé 365 DTU sur son quota de 2 000 DTU.

>[AZURE.NOTE]Vous pouvez demander une augmentation de ce quota en [contactant le support technique](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

## Autres limites de SQL Database

| Domaine | Limite | Description |
|---|---|---|
| Bases de données utilisant l’exportation automatique par abonnement | 10 | L’exportation automatique vous permet de créer une planification personnalisée pour sauvegarder vos bases de données SQL. Pour plus d’informations, consultez [Bases de données SQL : prise en charge des exportations de base de données SQL automatisée](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|

## Ressources

[Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md)

[Niveaux de service et niveaux de performances de la base de données SQL Azure](sql-database-service-tiers.md)

[Messages d'erreur pour les programmes clients SQL Database](sql-database-develop-error-messages.md)

<!---HONumber=Oct15_HO1-->