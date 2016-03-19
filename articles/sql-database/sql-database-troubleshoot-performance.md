<properties
	pageTitle="Dépannage du fonctionnement d’une base de données dans la base de données SQL Azure."
	description="Étapes rapides pour la résolution des problèmes de base de données."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="v-shysun"/>

# Dépannage des performances de base de données à l’aide de la base de données SQL Azure
Vous pouvez modifier le [niveau de service](sql-database-service-tiers.md) d’une seule base de données ou augmenter le nombre d’eDTU d’un pool de base de données élastique à tout moment pour améliorer les performances, mais il se peut que vous vouliez identifier des opportunités pour améliorer et optimiser d’abord des performances de requêtes. Des index manquants et des requêtes incorrectement optimisées sont souvent à l’origine de performances de base de données limitées.

## Étapes servant à évaluer et à adapter les performances de base de données
1.	Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez la base de données, puis utilisez le graphique d’analyse pour rechercher des ressources proches de leur maximum. Consommation de DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier l’intervalle de temps et les valeurs affichées.
2.	Utilisez [Analyse des performances des requêtes](sql-database-query-performance.md) pour évaluer les requêtes à l’aide de DTU, puis utilisez [Conseiller d’indexation](sql-database-index-advisor.md) pour recommander et créer des index.
3.	Vous pouvez utiliser des vues de gestion dynamique (DMV), des événements étendus (Xevents) et le magasin de requêtes dans SSMS pour obtenir des paramètres de performances en temps réel. Consultez la [rubrique de conseils de performance](sql-database-performance-guidance.md) pour découvrir l’analyse détaillée et des conseils de réglage.

## Opérations servant à améliorer les performances de base de données avec davantage de ressources
1.	Pour les bases de données uniques, vous pouvez [Modifier les niveaux de service](sql-database-scale-up.md) à la demande pour améliorer les performances de la base de données.
2.	S’il existe plusieurs bases de données, envisagez d’utiliser des [pools de base de données élastiques](sql-database-elastic-pool-guidance.md) pour une mise à l’échelle automatique des ressources.

En cas de persistance des problèmes de performance, contactez l’assistance pour ouvrir un numéro d’assistance.

<!---HONumber=AcomDC_1217_2015-->