<properties
	pageTitle="Surveiller le stockage en mémoire XTP | Microsoft Azure"
	description="Estimer et surveiller la capacité et l’utilisation du stockage en mémoire XTP ; résoudre l’erreur de capacité 41805"
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Surveiller le stockage en mémoire XTP

Lorsque vous utilisez [In-Memory](sql-database-in-memory.md), les données des tables à mémoire optimisée et les variables de table résident dans un stockage en mémoire XTP. Chaque niveau de service Premium est doté d’une taille de stockage en mémoire maximale, qui est décrite dans l’article de [niveaux de Service de base de données SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Une fois que cette limite est dépassée, des opérations d’insertion et de mise à jour peuvent commencer à échouer (en générant l’erreur 41805). À ce stade, vous devez soit supprimer des données pour libérer de la mémoire, soit mettre à niveau le niveau de performances de votre base de données.

## Déterminer si la taille des données est adaptée à la capacité de stockage en mémoire

Déterminer la capacité de stockage : consultez l’article de [niveaux de Service de base de données SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) pour connaître les capacités de stockage des différents niveaux de service Premium.

L’estimation de la mémoire requise pour une table à mémoire optimisée s’effectue de la même façon pour SQL Server que dans Base de données SQL Azure. Prenez quelques minutes pour consulter cette rubrique sur [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Notez que la table et les lignes de variable de table, ainsi que les index, sont pris en compte pour le calcul de la taille maximale des données utilisateur. En outre, l’instruction ALTER TABLE a besoin de suffisamment d’espace pour créer une version de la table entière et de ses index.

## Surveillance et alerte

Vous pouvez surveiller l’utilisation du stockage en mémoire sous forme de pourcentage de la [capacité de stockage de votre niveau de performances](sql-database-service-tiers.md#service-tiers-for-single-databases) dans le [portail](http://portal.azure.com/) Azure :

- Sur le panneau Base de données, recherchez la zone Utilisation de ressources, puis cliquez sur Modifier.
- Sélectionnez ensuite la métrique du pourcentage de stockage en mémoire XTP.
- Pour ajouter une alerte, cliquez sur la zone Taux d’utilisation de la ressource pour ouvrir le panneau Métriques, puis cliquez sur Ajouter une alerte.

Vous pouvez également utiliser la requête suivante pour afficher l’utilisation du stockage en mémoire :

    select xtp_storage_percent from sys.dm_db_resource_stats


## Résoudre les situations de mémoire insuffisante - erreur 41805

Lorsque la mémoire devient insuffisante, les opérations INSERT, UPDATE et CREATE échouent en générant le message d’erreur 41805.

Le message d’erreur 41805 indique que les tables à mémoire optimisée et les variables de table ont dépassé la taille maximale.

Pour résoudre cette erreur, deux possibilités s’offrent à vous :


- supprimer des données des tables à mémoire optimisée, en déchargeant potentiellement les données vers des tables traditionnelles sur disque ;
- adapter le niveau de service afin de disposer d’un stockage en mémoire suffisant pour les données que vous devez conserver dans des tables à mémoire optimisée.

## Étapes suivantes
En savoir plus sur l’[Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO3-->