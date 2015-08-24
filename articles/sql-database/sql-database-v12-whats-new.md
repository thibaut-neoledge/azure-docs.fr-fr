<properties 
	pageTitle="Nouveautés de SQL Database V12 | Microsoft Azure" 
	description="Explique pourquoi les systèmes d’entreprise qui utilisent Azure SQL Database dans le cloud profitent de la mise à niveau vers la version 12 (V12)." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/10/2015" 
	ms.author="genemi"/>


# Nouveautés de SQL Database V12


Cette rubrique décrit les nombreux avantages de la nouvelle version 12 (V12) d’Azure SQL Database par rapport à la version 11.


Nous continuons d’ajouter des fonctionnalités à la version 12 (V12). Par conséquent, nous vous encourageons à consulter notre page web sur les mises à jour des services pour Azure et à utiliser ses filtres :


- Filtrez sur [Service SQL Database](http://azure.microsoft.com/updates/?service=sql-database).
- Filtrez sur [annonces](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de disponibilité générale pour les fonctionnalités SQL Database.


Les dernières informations sur les limites de ressources pour une base de données SQL se trouvent sur la page :<br/>[Limites de ressources d’une base de données SQL Azure](sql-database-resource-limits.md).


## Compatibilité améliorée des applications avec SQL Server


Un objectif important de la version 12 (V12) de SQL Database était d’améliorer la compatibilité avec Microsoft SQL Server 2014. Entre autres, la version 12 (V12) est désormais équivalente à SQL Server dans le domaine de la programmabilité. Exemple :


- [Assemblys Common Language Runtime (CLR)](http://msdn.microsoft.com/library/ms189524.aspx)
- [Fonctions Windows](https://msdn.microsoft.com/library/bb934097.aspx), avec [OVER](http://msdn.microsoft.com/library/ms189461.aspx) 
- [Index XML](https://msdn.microsoft.com/library/bb934097.aspx) et [index XML sélectifs](http://msdn.microsoft.com/library/jj670104.aspx)
- [Suivi des modifications](http://msdn.microsoft.com/library/bb933875.aspx)
- [SELECT...INTO](http://msdn.microsoft.com/library/ms188029.aspx)
- [Recherche en texte intégral](http://msdn.microsoft.com/library/ms142571.aspx)


Consultez [cette page](http://msdn.microsoft.com/library/azure/ee336281.aspx) pour découvrir les quelques fonctionnalités non prises en charge par Base de données SQL.


## Plus de performances pour le niveau Premium, nouveaux niveaux de performances


Dans la version 12 (V12), nous avons augmenté les unités de base de données (UDBD) affectées à tous les niveaux de performances Premium de 25 %, sans coût supplémentaire. Les gains de performances sont possibles grâce aux nouvelles fonctionnalités, comme :


- La prise en charge des [index columnstore](http://msdn.microsoft.com/library/gg492153.aspx) en mémoire.
- [Le partitionnement de table par lignes](http://msdn.microsoft.com/library/ms187802.aspx) grâce aux améliorations associées à [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx).
- La disponibilité des vues de gestion dynamique [(DMV)](http://msdn.microsoft.com/library/ms188754.aspx) et les événements étendus [(XEvents)](https://msdn.microsoft.com/library/bb630282.aspx) pour aider à surveiller et affiner les performances.


## Une meilleure prise en charge des fournisseurs SaaS cloud


Uniquement dans la version 12 (V12), nous avons publié le nouveau niveau de performances Standard S3 et la version préliminaire publique des [pools de base de données élastiques](sql-database-elastic-pool.md). Il s’agit d’une solution spécialement conçue pour les fournisseurs SaaS cloud. Avec les pools de bases de données élastiques, vous pouvez :


- Partager les unités de base de données (UDBD) entre les bases de données pour réduire les coûts pour un grand nombre de bases de données.
- Exécuter des [tâches de base de données élastique](sql-database-elastic-jobs-overview.md) pour gérer les bases de données à grande échelle.


## Améliorations de la sécurité


La sécurité est une préoccupation essentielle pour quiconque mène ses activités dans le cloud. Les dernières fonctionnalités de sécurité publiées dans la version 12 (V12) comprennent :


- [Sécurité au niveau de la ligne](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Dynamic Data Masking (masquage des données dynamiques)](sql-database-dynamic-data-masking-get-started.md)
- [Bases de données à relation contenant-contenu](http://msdn.microsoft.com/library/azure/ff394108.aspx)
- [Rôles d’application](http://msdn.microsoft.com/library/ms190998.aspx) gérés avec GRANT, DENY et REVOKE
- [Chiffrement transparent des données](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)


## Continuité d’activité améliorée lors de la récupération


La version 12 (V12) offre des valeurs sensiblement améliorées pour les objectifs de point de récupération (RPO) et les temps de récupération estimés (ERT) :


| Fonctionnalité de continuité des activités | Version antérieure | Version 12 (V12) |
| :-- | :-- | :-- |
| Géo-restauration | • RPO < 24 heures.<br/>• ERT < 12 heures. | • RPO < 1 heure.<br/>• ERT < 12 heures. |
| Géo-réplication standard | • RPO < 30 minutes.<br/>• ERT < 2 heures. | • RPO < 5 secondes.<br/>• ERT < 30 secondes. |
| Géo-réplication active | • RPO < 5 minutes.<br/>• ERT < 1 heure. | • RPO < 5 secondes.<br/>• ERT < 30 secondes. |


Pour plus d’informations, consultez la rubrique [Continuité de l’activité Base de données SQL Azure](https://msdn.microsoft.com/library/azure/hh852669.aspx).


## Autres raisons pour effectuer la mise à niveau maintenant


Il y a de nombreuses bonnes raisons pour lesquelles les clients doivent passer de la version 11 à la version 12 (V12) d’Azure SQL Database :


- SQL Database V12 présente une longue liste de fonctionnalités, bien plus longue que celle de la version 11.
- Nous continuons à ajouter de nouvelles fonctionnalités à la version 12 (V12), mais aucune nouvelle fonctionnalité ne sera ajoutée à la 11.
- La plupart des nouvelles fonctionnalités sont publiées dans la version 12 (V12) de Base de données SQL avant qu’elles ne soient intégrées à Microsoft SQL Server.


## Vous utilisez déjà la version 12 (V12) ?


Un bon moyen de voir si vous avez une base de données ou un serveur logique en cours qui s’exécute sur une version antérieure du service SQL Database est d’effectuer les opérations suivantes :


1. Ouvrez le [portail Azure en version préliminaire](http://portal.azure.com/).
2. Cliquez sur **Parcourir**.
3. Cliquez sur **Serveurs SQL**.
4. L’icône en regard du serveur ou de la base de données vous dit tout :
 - ![Icône d’un serveur version 12](./media/sql-database-v12-whats-new/v12_icon.png) **Serveur logique V12**
 - ![Icône d’un serveur de version antérieure](./media/sql-database-v12-whats-new/earlier_icon.png) **Serveur logique d’une version antérieure**


Une autre technique pour déterminer la version consiste à exécuter l’instruction `SELECT @@version;` dans votre base de données et à regarder les résultats comme ceux-ci :


- **12**.0.2000.10 &nbsp; *(version V12)*
- **11**.0.9228.18 &nbsp; *(version 11)*


Les bases de données version 12 (V12) peuvent uniquement être hébergées sur un serveur logique version 12. Et un serveur version 12 (V12) peut uniquement héberger des bases de données de la version 12 (V12).


Si vous n’utilisez pas encore la version 12 (V12), vous pouvez mettre à niveau votre serveur logique en suivant les étapes de la section [Mise à niveau vers la version 12 (V12) de Base de données SQL](sql-database-v12-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Régions en disponibilité générale


- Le 31 juillet 2015, toutes les régions avaient été promues en disponibilité générale.
- La version 12 (V12) a été publiée en décembre 2014, mais uniquement à l’état de version préliminaire.

Voir les [Conditions d’utilisation supplémentaires des versions préliminaires de Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!---HONumber=August15_HO7-->