<properties
	pageTitle="Guide des performances de base de données SQL Azure pour les bases de données uniques"
	description="Cette rubrique fournit des conseils pour vous aider à déterminer le niveau de service adapté à votre application et fournit des recommandations pour le paramétrage de votre application afin de tirer le meilleur parti de votre base de données SQL Azure."
	services="sql-database"
	documentationCenter="na"
	authors="CarlRabeler"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="09/13/2016"
	ms.author="carlrab" />

# Guide des performances de base de données SQL Azure pour les bases de données uniques

## Vue d'ensemble

La base de données SQL Microsoft Azure compte trois [niveaux de service](sql-database-service-tiers.md) : De base, Standard et Premium. Tous ces niveaux isolent strictement les ressources fournies à votre base de données SQL Azure et garantissent des performances prévisibles. Les performances sont mesurées en DTU. Pour plus d’informations sur les DTU, consultez [Qu’est-ce qu’une DTU](sql-database-what-is-a-dtu.md). Cet article fournit des conseils pour vous aider à choisir le niveau de service adapté à votre application et fournit des recommandations pour le paramétrage de votre application afin de tirer le meilleur parti de votre base de données Azure SQL Database.

>[AZURE.NOTE] Cet article se concentre sur le guide des performances pour les bases de données uniques dans la base de données SQL. Pour un guide des performances relatives aux pools élastiques, consultez [Quand utiliser un pool de base de données élastique ?](sql-database-elastic-pool-guidance.md). Toutefois, notez que de nombreuses recommandations de paramétrage pour une base de données unique dans cet article peuvent être appliquées aux bases de données dans un pool élastique avec des avantages similaires en matière de performances.

- **De base** Le niveau de service De base est conçu pour assurer une bonne prévisibilité des performances pour chaque base de données, heure après heure. La DTU d’une base de données De base est conçue pour fournir les ressources suffisantes pour les petites bases de données, dépourvues de demandes simultanées multiples, pour de bonnes performances.
- **Standard** Le niveau de service Standard offre une meilleure prévisibilité des performances et place la barre plus haut pour les bases de données avec plusieurs demandes simultanées, par exemple les applications web ou de groupe de travail. L’utilisation d’une base de données de niveau de service Standard vous permet de dimensionner votre application de base de données en fonction de performances prévisibles, minute après minute.
- **Premium** Le niveau de service Premium offre des performances prévisibles seconde après seconde pour chaque base de données Premium. L’utilisation du niveau de service Premium permet de dimensionner votre application de base de données en fonction de la charge maximale pour cette base de données, et supprime les cas où l’écart de performances peut rallonger des petites requêtes dans les opérations sensibles à la latence. Ce modèle simplifie considérablement les cycles de validation de développement et de produit nécessaires pour les applications qui doivent appliquer des instructions fortes concernant les besoins maximum en ressources, l’écart de performances ou la latence des requêtes.

Les paramètres de niveau de performances de chaque niveau de service vous permettent de payer uniquement la capacité dont vous avez besoin et [d’activer ou de désactiver](sql-database-scale-up.md) de la capacité lorsque la charge de travail évolue. Par exemple, si la charge de travail de votre base de données est occupée au cours de la période des achats de rentrée scolaire, vous pouvez augmenter le niveau de performances de la base de données pendant cette période, puis le réduire après le pic. Vous pouvez réduire vos coûts en optimisant votre environnement cloud conformément aux caractéristiques saisonnières de votre entreprise. Ce modèle fonctionne également bien pour les cycles de version de logiciels. Une équipe de test peut allouer de la capacité pendant des séries de tests, et libérer cette capacité une fois les tests terminés. Cette demande de capacité est bien adaptée au modèle que vous payeriez pour la capacité à la demande et évite les dépenses en ressources dédiées rarement utilisées.

## Motifs d’utilisation des niveaux de service

Bien que chaque charge de travail puisse différer, les niveaux de service visent à assurer la prévisibilité des performances dans un large éventail de niveaux de performances. Les niveaux de service permettent aux clients dont les besoins en ressources sont très variables pour leurs bases de données de travailler dans un environnement informatique plus dédié.

### Cas d’utilisation du niveau de service De base :

- **Prise en main de la base de données SQL Azure** : souvent, les applications en cours de développement ne nécessitent pas des niveaux de performances élevés. Les bases de données De base fournissent un environnement idéal de développement de base de données à moindre coût.
- **Base de données avec un seul utilisateur** : généralement, les applications qui associent un seul utilisateur avec une base de données n'ont pas des exigences élevées en matière d'accès concurrentiel et de performances. Les applications de ce type font des candidates idéales pour le niveau de service De base.

### Cas d’utilisation du niveau de service Standard :

- **Base de données avec plusieurs demandes simultanées** : applications qui gèrent plusieurs utilisateurs simultanément. Par exemple, les sites web avec un trafic modéré ou des applications de service qui requièrent une quantité supérieure de ressources font d’excellents candidats pour le niveau de service Standard.

### Cas d’utilisation du niveau de service Premium :

- **Charge maximale élevée**: une application qui nécessite un volume élevé d'UC, de mémoire ou d'E/S pour exécuter ses opérations. Par exemple, si une opération de base de données est connue pour utiliser plusieurs cœurs d’UC pendant une période prolongée, l’utilisation du niveau de service Premium est appropriée.
- **Nombreuses demandes simultanées** : certaines applications de base de données gèrent de nombreuses demandes simultanées, par exemple un site web avec un volume de trafic élevé. Les niveaux de service De base et Standard présentent des limites au nombre de demandes simultanées. Les applications qui requièrent plus de connexions doivent choisir une taille de réservation appropriée pour traiter le nombre maximum de demandes nécessaires.
- **Faible latence** : certaines applications doivent garantir une réponse de la base de données dans un délai minimum. Si une procédure stockée donnée est appelée dans le cadre d’une opération client plus large, il existe peut-être une exigence de renvoi depuis l’appel en moins de 20 millisecondes 99 % du temps. Ce type d’application bénéficie du niveau de service Premium afin de garantir la disponibilité de la puissance de calcul.

Le niveau exact dont vous avez besoin dépend des exigences de charge maximale pour chaque dimension de ressource. Certaines applications peuvent utiliser une quantité insignifiante pour une ressource mais avoir des exigences considérables pour d’autres.

## Informations sur la tarification et la facturation

Les pools élastiques sont facturés en fonction des caractéristiques suivantes :

- Un pool élastique est facturé lors de sa création, même s’il n’inclut aucune base de données.
- Il est facturé toutes les heures. Cette fréquence de mesure est identique à celle des niveaux de performances associés aux bases de données uniques.
- Si un pool élastique est redimensionné en fonction d’un nouveau nombre d’eDTU, ce pool n’est pas facturé selon ce nouveau nombre, tant que l’opération de redimensionnement n’est pas terminée. Le modèle de facturation est le même que lors de la modification du niveau de performances des bases de données autonomes.


- Le prix d’un pool élastique est basé sur le nombre d’eDTU de ce pool. Le prix d’un pool élastique est indépendant de l’utilisation des bases de données élastiques qu’il contient.
- Le prix est calculé comme suit : le nombre d’eDTUs d’un pool x le prix unitaire par eDTU.

Le prix unitaire des eDTU d’un pool élastique est supérieur au prix unitaire des DTU d’une base de données autonome au sein du même niveau de service. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## Capacités et limites des niveaux de service
Chaque niveau de service et de performance est associé à différentes limites et caractéristiques de performances. Le tableau suivant décrit ces caractéristiques pour une base de données unique.

[AZURE.INCLUDE [Tableau de niveaux de service de base de données SQL](../../includes/sql-database-service-tiers-table.md)]

Les sections suivantes fournissent plus d’informations sur l’affichage de l’utilisation vis-à-vis de ces limites.

### Stockage In-Memory OLTP maximal

Vous pouvez utiliser la vue **sys.dm\_db\_resource\_stats** pour surveiller votre utilisation du stockage en mémoire. Pour plus d'informations sur la surveillance, consultez [Surveiller le stockage In-Memory OLTP](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] La version préliminaire d'In-Memory OLTP est actuellement prise en charge uniquement par les bases de données uniques et non par les bases de données de pools de bases de données élastiques.

### Nombre maximal de requêtes simultanées

Pour afficher le nombre de requêtes simultanées, exécutez la requête Transact-SQL suivante sur votre base de données SQL :

	SELECT COUNT(*) AS [Concurrent_Requests]
	FROM sys.dm_exec_requests R

Si vous analysez la charge de travail d'une base de données SQL Server locale, vous devez modifier cette requête pour la filtrer selon la base de données spécifique que vous analysez. Par exemple, si vous utilisez une base de données locale nommée MyDatabase, la requête Transact-SQL suivante renvoie le nombre de requêtes simultanées dans cette base de données.

	SELECT COUNT(*) AS [Concurrent_Requests]
	FROM sys.dm_exec_requests R
	INNER JOIN sys.databases D ON D.database_id = R.database_id
	AND D.name = 'MyDatabase'

Il s’agit simplement d’un instantané à un point unique dans le temps. Pour obtenir une meilleure compréhension de votre charge de travail, vous devez collecter plusieurs échantillons au fil du temps afin de mieux évaluer vos exigences en termes de requêtes simultanées.

### Nombre maximal de connexions simultanées

Il n'existe aucune requête ou DMV capable d'afficher le nombre ou l'historique des connexions simultanées. Vous pouvez analyser vos modèles d'utilisateur et d'application pour avoir une idée de la fréquence des connexions. Vous pouvez également exécuter des charges réelles dans un environnement de test pour vous assurer que vous n’atteignez pas cette limite ou d’autres limites décrites dans cette rubrique.

Si plusieurs clients utilisent la même chaîne de connexion, le service authentifie chaque connexion. Si 10 utilisateurs se connectent simultanément à une base de données avec les mêmes nom d’utilisateur et mot de passe, dix connexions simultanées seront établies. Cette limite s'applique uniquement à la durée de la connexion et de l'authentification. Par conséquent, si ces mêmes 10 utilisateurs se connectent séquentiellement à la base de données, le nombre de connexions simultanées ne sera jamais supérieur à 1.

>[AZURE.NOTE] Cette limite ne s'applique pas aux bases de données de pools de bases de données élastiques.

### Nombre maximal de sessions

Pour afficher le nombre actuel de vos sessions actives, exécutez la requête Transact-SQL suivante sur votre base de données SQL :

	SELECT COUNT(*) AS [Sessions]
	FROM sys.dm_exec_connections

Si vous analysez une charge de travail SQL Server locale, modifiez la requête pour vous concentrer sur une base de données spécifique. Cette requête vous aide à déterminer les éventuels besoins de votre session pour cette base de données si vous la déplacez vers Azure SQL Database.

	SELECT COUNT(*)  AS [Sessions]
	FROM sys.dm_exec_connections C
	INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
	INNER JOIN sys.databases D ON (D.database_id = S.database_id)
	WHERE D.name = 'MyDatabase'

Là encore, ces requêtes retournent un nombre à un point donné dans le temps, et recueillir plusieurs échantillons au fil du temps vous permet donc de mieux comprendre l'utilisation de votre session.

Pour analyser une base de données SQL, vous pouvez également interroger **sys.resource\_stats** afin d'obtenir des statistiques d'historique sur les sessions grâce à la colonne **active\_session\_count**. La section suivante consacrée à la surveillance fournit de plus amples informations sur l'utilisation de cette vue.

## Surveillance de l'utilisation des ressources
Il existe deux vues qui vous permettent de surveiller l'utilisation des ressources d'une base de données SQL par rapport à son niveau de service :

- [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### Avec sys.dm\_db\_resource\_stats
La vue [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) existe dans chaque base de données SQL et fournit les dernières données sur l'utilisation des ressources par rapport au niveau de service. Les pourcentages moyens de CPU, d'E/S des données, d'écritures du journal et de mémoire sont enregistrés toutes les 15 secondes et conservés pendant une heure.

Étant donné que cette vue fournit un aperçu plus granulaire de l’utilisation des ressources, vous devez d’abord utiliser **sys.dm\_db\_resource\_stats** pour n’importe quelle analyse d’état actuel ou pour la résolution des problèmes. Par exemple, la requête suivante affiche l’utilisation moyenne et maximale des ressources pour la base de données actuelle sur la dernière heure :

	SELECT  
	    AVG(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
	    MAX(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
	    AVG(avg_data_io_percent) AS 'Average Data IO In Percent',
	    MAX(avg_data_io_percent) AS 'Maximum Data IO In Percent',
	    AVG(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
	    MAX(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
	    AVG(avg_memory_usage_percent) AS 'Average Memory Usage In Percent',
	    MAX(avg_memory_usage_percent) AS 'Maximum Memory Usage In Percent'
	FROM sys.dm_db_resource_stats;  

Pour les autres requêtes, consultez les exemples dans [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### Avec sys.resource\_stats

La vue [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) de la base de données **master** fournit des informations supplémentaires pour analyser l'utilisation des performances de votre base de données SQL au sein de son niveau de service et de performance spécifique. Les données sont collectées toutes les cinq minutes et conservées pendant environ 35 jours. Cette vue est plus utile pour l'analyse historique à long terme de votre utilisation des ressources de base de données SQL.

Le graphique suivant illustre l’utilisation des ressources d’UC pour une base de données Premium avec le niveau de performances P2 pour chaque heure de la semaine. Ce graphique spécifique commence un lundi, affiche cinq journées de travail, puis un week-end où l’application connaît une activité réduite.

![Utilisation des ressources de base de données SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

D’après les données, cette base de données présente actuellement une charge d’UC maximum juste supérieure à 50 % de l’utilisation de l’UC par rapport au niveau de performances P2 (à la mi-journée le mardi). Si l’UC était le facteur dominant dans le profil de ressource de l’application, vous pourriez décider que P2 est le niveau de performances approprié pour garantir que la charge de travail est toujours adaptée. Si une application est prévue pour croître dans le temps, il est judicieux de permettre une mémoire tampon de ressources supplémentaire afin que l’application n’atteigne jamais le plafond. L’augmentation du niveau de performances permet d’éviter des erreurs visibles par le client, provoquées par une insuffisance de puissance de traitement efficace des demandes d’une base de données, en particulier dans les environnements sensibles à la latence (comme une base de données prenant en charge une application qui peint les pages web en fonction des résultats des appels de base de données).

Il est important de noter que d’autres types d’applications peuvent interpréter le même graphique différemment. Par exemple, si une application a essayé de traiter les données de paie chaque jour et a obtenu le même graphique, ce genre de modèle de « traitement par lot » peut convenir dans un niveau de performances P1. Le niveau de performances P1 possède 100 DTU, contre 200 DTU pour le niveau de performances P2. Le niveau de performances P1 fournit donc la moitié des performances du niveau de performances P2. Par conséquent, 50 % d’utilisation d’UC dans P2 équivalent à 100 % d’utilisation d’UC dans P1. Tant que l’application n’a pas de délai d’expiration, le fait qu’une tâche s’exécute en 2 h ou en 2 h 30 peut ne pas avoir d’importance à condition qu’elle soit effectuée le jour même. Une application de cette catégorie peut probablement utiliser un niveau de performances P1. Vous pouvez tirer parti du fait qu’il y a des périodes pendant la journée où l’utilisation des ressources est moindre, ce qui signifie que toute période de pointe peut déborder sur l’un des creux plus tard dans la journée. Le niveau de performances P1 peut convenir pour une application de ce type (et permettre de réaliser des économies) tant que les tâches peuvent se terminer à temps chaque jour.

La base de données SQL Azure expose les informations sur les ressources utilisées pour chaque base de données active dans la vue **sys.resource\_stats** de la base de données **master** dans chaque serveur. Les données de la table sont agrégées par intervalle de 5 minutes. Avec les niveaux de service De base, Standard et Premium, les données peuvent prendre plus de 5 minutes pour apparaître dans la table, ce qui signifie qu’elles conviennent mieux aux analyses historiques qu’aux analyses en temps réel. L'interrogation de la vue **sys.resource\_stats** affiche l'historique récent d'une base de données à valider si la réservation sélectionnée a fourni la performance souhaitée lorsque c'était nécessaire.

>[AZURE.NOTE] Vous devez être connecté à la base de données **master** de votre serveur de base de données SQL logique pour interroger **sys.resource\_stats** dans les exemples suivants.

L’exemple suivant montre comment les données de cette vue sont exposées :

	SELECT TOP 10 *
	FROM sys.resource_stats
	WHERE database_name = 'resource1'
	ORDER BY start_time DESC

![sys resource stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L’exemple suivant montre différentes approches permettant de comprendre l’utilisation des ressources de votre base de données SQL à l’aide de la vue de catalogue **sys.resource\_stats**.

1. Par exemple, pour examiner l’utilisation des ressources de la semaine précédente pour la base de données userdb1, vous pouvez exécuter la requête suivante.

		SELECT *
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND
		      start_time > DATEADD(day, -7, GETDATE())
		ORDER BY start_time DESC;

2. Afin d’évaluer l’adéquation entre votre charge de travail et le niveau de performances, vous devez étudier les différents aspects des métriques de ressources : UC, lectures, écritures, nombre de Workers et nombre de sessions. Voici une requête modifiée à l’aide de sys.resource\_stats afin d’indiquer les valeurs moyennes et maximales de ces métriques de ressources.

		SELECT
		    avg(avg_cpu_percent) AS 'Average CPU Utilization In Percent',
		    max(avg_cpu_percent) AS 'Maximum CPU Utilization In Percent',
		    avg(avg_data_io_percent) AS 'Average Physical Data IO Utilization In Percent',
		    max(avg_data_io_percent) AS 'Maximum Physical Data IO Utilization In Percent',
		    avg(avg_log_write_percent) AS 'Average Log Write Utilization In Percent',
		    max(avg_log_write_percent) AS 'Maximum Log Write Utilization In Percent',
		    avg(max_session_percent) AS 'Average % of Sessions',
		    max(max_session_percent) AS 'Maximum % of Sessions',
		    avg(max_worker_percent) AS 'Average % of Workers',
		    max(max_worker_percent) AS 'Maximum % of Workers'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Avec les informations ci-dessus relatives aux valeurs moyennes et maximales de chaque métrique de ressources, vous pouvez évaluer l’adéquation entre votre charge de travail et le niveau de performances que vous avez choisi. En général, les valeurs moyennes de sys.resource\_stats vous offrent une bonne référence à utiliser par rapport à la taille cible. Elles doivent constituer votre principale jauge de mesure. Par exemple, si vous utilisez le niveau de service Standard avec un niveau de performances S2, si les pourcentages d’utilisation moyens de l’UC et des lectures et écritures d’E/S sont inférieurs à 40 %, le nombre moyen de Workers inférieur à 50 et le nombre moyen de sessions inférieur à 200, votre charge de travail peut s’intégrer au niveau de performances S1. Il est facile de voir si votre base de données s’intègre dans les limites de Workers et de sessions. Pour voir si une base de données s’adapte à un niveau de performances inférieur en ce qui concerne l’UC, les lectures et les écritures, divisez le nombre de DTU du niveau de performances inférieur par le nombre de DTU de votre niveau de performances actuel et multipliez le résultat par 100 :

	**DTU S1 / DTU S2 * 100 = 20 / 50 * 100 = 40**

	Le résultat est la différence de performances relative entre les deux niveaux de performances en pourcentage. Si votre utilisation ne dépasse pas ce pourcentage, votre charge de travail peut s’adapter au niveau de performances inférieur. Toutefois, vous devez également examiner toutes les plages de valeurs d’utilisation des ressources et déterminer, en vous appuyant sur le pourcentage, la fréquence selon laquelle la charge de travail de votre base de données s’adapterait au niveau de performances inférieur. La requête suivante génère le pourcentage d’adéquation par dimension de ressource, selon le seuil de 40 % calculé précédemment.

		SELECT
		    (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
		    ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

	En fonction de votre objectif de niveau de service de la base de données, vous pouvez décider si votre charge de travail s’adapte au niveau de performances inférieur. Si votre objectif de niveau de service de charge de travail de la base de données est de 99,9 % et que la requête ci-dessus retourne des valeurs supérieures à 99,9 pour les trois dimensions de ressources, il est probable que votre charge de travail s’adapte au niveau de performances inférieur.

	Examiner le pourcentage d’adéquation vous indique également si vous devez passer au niveau de performances supérieur pour atteindre votre objectif de niveau de service. Par exemple, la base de données userdb1 indique l’utilisation suivante pour la semaine précédente.

	| Pourcentage moyen d’UC | Pourcentage maximum d’UC |
	|---|---|
	| 24,5 | 100,00 |

	Le pourcentage moyen d’UC représente environ un quart de la limite du niveau de performances, ce qui conviendrait bien au niveau de performances de la base de données. Toutefois, la valeur maximum montre que la base de données atteint la limite du niveau de performances. Devez-vous passer au niveau de performances supérieur ? Là encore, vous devez examiner le nombre de fois où votre charge de travail atteint 100 % et comparer ce chiffre à votre objectif de niveau de service de charge de travail de la base de données.

		SELECT
		(COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
		,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent’
		,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
		FROM sys.resource_stats
		WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

	Si cette requête retourne une valeur inférieure à 99,9 pour l’une des trois dimensions de ressources, vous devez envisager de passer au niveau de performances supérieur, ou employer des techniques de paramétrage de l’application afin de réduire la charge sur la base de données Azure SQL Database.

4. L’exercice précédent prend également en compte l’augmentation prévue de votre charge de travail à l’avenir.

## Paramétrage de votre application

Dans un SQL Server local traditionnel, le processus de planification de la capacité initiale est souvent séparé du processus d’exécution d’une application en production. En d’autres termes, l’achat de matériel et de licences associées pour exécuter SQL Server est effectué à l’avance, tandis que le paramétrage des performances est effectué après. Lorsque vous utilisez Azure SQL Database, il est recommandé (et, étant donné que vous êtes facturé chaque mois, probablement souhaitable) d’entrelacer le processus d’exécution et de paramétrage d’une application. Le modèle de paiement de la capacité à la demande vous permet de paramétrer votre application pour utiliser les ressources minimum nécessaires immédiatement, au lieu de surprovisionner massivement du matériel en vous appuyant sur des hypothèses de plans de croissance à venir pour une application (qui sont souvent incorrectes pour des prévisions trop lointaines). Certains clients peuvent décider de ne pas paramétrer une application et choisir à la place de surprovisionner des ressources matérielles. Cette approche peut se révéler judicieuse lorsque vous ne souhaitez pas changer une application clé pendant une période de forte activité de celle-ci. Le paramétrage d’une application peut réduire vos besoins en ressources et diminuer vos factures mensuelles lorsque vous utilisez les niveaux de service dans la base de données SQL Azure.

### Caractéristiques des applications

Alors que les niveaux de service sont conçus pour améliorer la stabilité et la prévisibilité des performances d’une application, il existe quelques meilleures pratiques, recommandées pour le paramétrage de votre application afin de mieux tirer des ressources d’un niveau de performances. De nombreuses applications gagnent considérablement en performances en passant simplement à un niveau de performances et/ou à un niveau de service supérieur. Toutefois, toutes les applications n’en tirent pas parti sans paramétrage supplémentaire. Les applications dotées des caractéristiques suivantes doivent également envisager un paramétrage supplémentaire afin d’optimiser les performances lors de l’utilisation de la base de données SQL Azure.

- **Les applications dont les performances sont lentes en raison d’un comportement bavard** : les applications qui se caractérisent par un comportement bavard effectuent trop d’opérations d’accès aux données qui sont sensibles à la latence du réseau. De telles applications peuvent nécessiter une modification pour réduire le nombre d’opérations d’accès aux données sur la base de données SQL Azure. Par exemple, les performances de l’application peuvent être améliorées à l’aide de techniques comme le traitement par lot des requêtes ad hoc ou le déplacement des requêtes dans des procédures stockées. Pour plus d’informations, consultez la section Traitement par lot des requêtes ci-après.
- **Les bases de données avec une charge de travail intensive qui ne peuvent pas être prises en charge par un seul ordinateur** : les bases de données qui dépassent les ressources du niveau de performances Premium le plus élevé ne sont pas de bonnes candidates. Ces bases de données peuvent bénéficier de la montée en charge de la charge de travail. Pour plus d’informations, consultez les sections Partitionnement entre plusieurs bases de données et Partitionnement fonctionnel ci-après.
- **Les applications qui contiennent des requêtes non optimales** : les applications, en particulier dans la couche d'accès aux données, qui ont des requêtes mal paramétrées peuvent ne pas être en mesure de choisir un niveau de performances supérieur comme prévu. Cela inclut les requêtes dépourvues d’une clause WHERE et les requêtes présentant des index manquants ou des statistiques obsolètes. Ces applications bénéficient des techniques de paramétrage des performances de requête standard. Pour plus d’informations, consultez les sections Index manquants et Paramétrage/Compréhension de requêtes ci-après.
- **Les applications dotées d'un accès aux données non optimal** : les applications qui rencontrent des problèmes inhérents de concurrence d'accès aux données, par exemple d'interblocage, peuvent ne pas être en mesure de choisir un niveau de performances supérieur. Les développeurs d’applications doivent envisager de réduire les boucles sur la base de données SQL Azure en mettant en cache des données côté client à l’aide du service Azure Caching ou d’autres technologies de mise en cache. Consultez la section relative à la mise en cache de la couche Application ci-après.

## Techniques de paramétrage
Cette section explique certaines techniques que vous pouvez utiliser pour paramétrer la base de données SQL Azure afin d’obtenir les meilleures performances de votre application et d’être en mesure d’exécuter le plus petit niveau de performances possible. Plusieurs de ces techniques correspondent aux meilleures pratiques de paramétrage SQL Server traditionnelles, mais certaines sont spécifiques à Azure SQL Database. Dans certains cas, les techniques SQL Server traditionnelles peuvent être étendues pour également fonctionner sur la base de données SQL Azure en examinant les ressources utilisées par une base de données afin de déterminer des zones à paramétrer davantage.

### Query Performance Insight et SQL Database Advisor
SQL Database fournit deux outils dans le Portail Azure pour l’analyse et la résolution des problèmes de performances qui affectent votre base de données :

- [Query Performance Insight](sql-database-query-performance.md)
- [SQL Database Advisor](sql-database-advisor.md)

Consultez les liens précédents pour plus d'informations sur chaque outil et leur utilisation. Les deux sections suivantes sur les index manquants et le réglage des requêtes fournissent d'autres façons de rechercher et de corriger manuellement des problèmes de performances similaires. Nous vous recommandons de tester d'abord les outils du portail pour diagnostiquer et corriger plus efficacement les problèmes. Utilisez l'approche du réglage manuel pour les cas spéciaux.

### Index manquants
Un problème courant dans les performances de base de données OLTP est la conception physique de la base de données. Souvent, les schémas de base de données sont conçus et livrés sans test de mise à l’échelle (en charge ou en volume de données). Malheureusement, les performances d’un plan de requête peuvent être acceptables à petite échelle mais se dégrader notablement avec des volumes de données au niveau de la production. La cause la plus courante de ce problème est l’absence d’index appropriés pour satisfaire des filtres ou d’autres restrictions dans une requête. Souvent, cela se manifeste comme une analyse de table là où une recherche d’index pourrait suffire.

Dans l’exemple suivant, le plan de requête sélectionné contient une analyse la où une recherche suffirait.

	DROP TABLE dbo.missingindex;
	CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO dbo.missingindex(col2) VALUES (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION;
	GO
	SELECT m1.col1
	FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
	WHERE m1.col2 = 4;

![plan de requête avec index manquants](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

La base de données SQL Azure contient des fonctionnalités pour indiquer aux administrateurs de base de données comment rechercher et résoudre les situations courantes d’index manquants. Les vues de gestion dynamique (DMV) intégrées à la base de données SQL Azure examinent la compilation de requêtes là où un index réduirait de manière significative le coût estimé d’exécution d’une requête. Pendant l’exécution de requêtes, SQL Database suit la fréquence selon laquelle chaque plan de requête est exécuté, ainsi que l’écart estimé entre le plan de requête en cours d’exécution et le plan imaginé dans lequel cet index existait. Ces DMV permettent à un administrateur de base de données de supposer rapidement quelles modifications de conception physique de la base de données amélioreraient le coût total de la charge de travail pour une base de données spécifiée et sa charge de travail réelle.

La requête suivante peut être utilisée pour évaluer les index manquants éventuels.

	SELECT CONVERT (varchar, getdate(), 126) AS runtime,
	    mig.index_group_handle, mid.index_handle,
	    CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
	            (migs.user_seeks + migs.user_scans)) AS improvement_measure,
	    'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
	              CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
	              (' + ISNULL (mid.equality_columns,'')
	              + CASE WHEN mid.equality_columns IS NOT NULL
	                          AND mid.inequality_columns IS NOT NULL
	                     THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
	              + ')'
	              + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
	    migs.*,
	    mid.database_id,
	    mid.[object_id]
	FROM sys.dm_db_missing_index_groups AS mig
	INNER JOIN sys.dm_db_missing_index_group_stats AS migs
	    ON migs.group_handle = mig.index_group_handle
	INNER JOIN sys.dm_db_missing_index_details AS mid
	    ON mig.index_handle = mid.index_handle
	ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Dans cet exemple, l’index suivant est suggéré.

	CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Une fois la création effectuée, cette même instruction SELECT sélectionne un autre plan qui utilise une recherche au lieu d’une analyse, s’exécutant plus efficacement comme indiqué dans le plan de requête suivant.

![plan de requête avec index corrigés](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

L’information clé est que la capacité d’E/S d’un système partagé est plus limitée que celle d’un serveur dédié. Il est primordial de réduire les E/S inutiles pour tirer le meilleur parti du système dans la DTU de chaque niveau de performances des niveaux de service dans Azure SQL Database. Des choix appropriés de conception physique de base de données peuvent considérablement améliorer la latence des requêtes individuelles, le débit des demandes simultanées que vous pouvez traiter par unité d’échelle, et réduire les coûts nécessaires pour satisfaire la requête. Pour plus d'informations sur les DMV d'index manquants , consultez [sys.dm\_db\_missing\_index\_details](https://msdn.microsoft.com/library/ms345434.aspx).

### Paramétrage/Compréhension de requêtes
L’optimiseur de requête d’Azure SQL Database est similaire à l’optimiseur de requête SQL Server traditionnel. La plupart des meilleures pratiques pour le paramétrage des requêtes et la compréhension du raisonnement relatif aux limitations du modèle pour l’optimiseur de requête s’appliquent également à Azure SQL Database. Le paramétrage des requêtes dans Azure SQL Database peut présenter l’avantage supplémentaire de réduire les exigences de ressources globales et permettre à une application de s’exécuter à un coût inférieur qu’en l’absence de paramétrage, car elle s’exécute à un niveau de performances inférieur.

Un exemple courant vu dans SQL Server qui s’applique également à la base de données SQL Azure est la façon dont les paramètres sont détectés lors de la compilation pour essayer de créer un plan plus optimal. La détection des paramètres est un processus par le biais duquel l’optimiseur de requête considère la valeur actuelle d’un paramètre lors de la compilation d’une requête dans l’espoir de générer un plan de requête plus optimal. Bien que cette stratégie mène souvent à un plan de requête beaucoup plus rapide qu’un plan compilé sans connaissance des valeurs de paramètre, le comportement actuel de SQL Server/de la base de données SQL Azure est imparfait : il existe des cas où les paramètres ne sont pas détectés, et des cas où les paramètres sont détectés mais où le plan généré est non optimal pour l’ensemble des valeurs de paramètre dans une charge de travail. Microsoft inclut des indicateurs de requête (directives) pour vous permettre de spécifier vos intentions plus délibérément et de remplacer le comportement par défaut pour la détection des paramètres. Souvent, l’utilisation d’indicateurs peut résoudre les cas où le comportement de SQL Server/de la base de données SQL Azure par défaut est imparfait pour une charge de travail client donnée.

L’exemple suivant montre comment le processeur de requêtes peut générer un plan non optimal pour les besoins en performances et en ressources et comment l’utilisation d’un indicateur de requête peut réduire l’exécution des requêtes et les besoins en ressources sur Azure SQL Database.

Vous trouverez ci-dessous un exemple de configuration.

	DROP TABLE psptest1;
	CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

	DECLARE @a int = 0;
	SET NOCOUNT ON;
	BEGIN TRANSACTION
	WHILE @a < 20000
	BEGIN
	    INSERT INTO psptest1(col2) values (1);
	    INSERT INTO psptest1(col2) values (@a);
	    SET @a += 1;
	END
	COMMIT TRANSACTION
	CREATE INDEX i1 on psptest1(col2);
	GO

	CREATE PROCEDURE psp1 (@param1 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1
	    WHERE col2 = @param1
	    ORDER BY col2;
	END
	GO

	CREATE PROCEDURE psp2 (@param2 int)
	AS
	BEGIN
	    INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
	    ORDER BY col2
	    OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
	END
	GO

	CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
	GO

Le code d’installation crée une table qui contient une distribution décalée des données. Le plan de requête optimal varie en fonction du paramètre sélectionné. Malheureusement, le comportement de mise en cache du plan ne recompile pas toujours la requête en fonction de la valeur de paramètre la plus courante, ce qui signifie qu’il est possible qu’un plan non optimal soit mis en cache et utilisé pour de nombreuses valeurs, même si un autre plan constituerait un meilleur choix. Deux procédures stockées sont alors créées à l’identique, sauf que l’une d’elles contient un indicateur de requête spécial.

**Exemple (partie 1) :**

	-- Prime Procedure Cache with scan plan
	EXEC psp1 @param1=1;
	TRUNCATE TABLE t1;

	-- Iterate multiple times to show the performance difference
	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp1 @param1=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

**Exemple (partie 2 ; patientez 10 minutes avant d’essayer cette partie afin qu’elle soit manifestement différente dans les données de télémétrie obtenues) :**

	EXEC psp2 @param2=1;
	TRUNCATE TABLE t1;

	DECLARE @i int = 0;
	WHILE @i < 1000
	BEGIN
	    EXEC psp2 @param2=2;
	    TRUNCATE TABLE t1;
	    SET @i += 1;
	END

Chaque partie de cet exemple essaie d’exécuter une instruction insert paramétrable 1 000 fois (pour générer une charge suffisante pour être intéressante sur un jeu de données de test). Lors de l’exécution des procédures stockées, le processeur de requêtes examine la valeur de paramètre transmise à la procédure lors de sa première compilation (appelée détection de paramètres). Le plan qui en résulte est mis en cache et utilisé pour les appels ultérieurs, même si la valeur de paramètre est différente. Par conséquent, le plan optimal peut ne pas être utilisé dans tous les cas. Parfois, les clients doivent guider l’optimiseur vers le choix d’un plan adapté aux situations courantes, plutôt qu’aux situations spécifiques lorsque la requête a été compilée en premier lieu. Dans cet exemple, le plan initial génère un plan de type analyse qui lit toutes les lignes pour trouver chaque valeur qui correspond au paramètre.

![Paramétrage de requêtes](./media/sql-database-performance-guidance/query_tuning_1.png)

Étant donné que nous avons exécuté la procédure avec la valeur 1, le plan obtenu était optimal pour 1, mais non optimal pour toutes les autres valeurs dans la table. Le comportement qui en résulte n’est probablement pas le comportement souhaité si vous sélectionnez chaque plan de façon aléatoire, car le plan s’exécute plus lentement et avec davantage de ressources.

L’exécution du test avec le paramètre « SET STATISTICS IO ON » montre le travail d’analyse logique effectué dans cet exemple en arrière-plan : vous pouvez voir que le plan effectue 1 148 lectures (ce qui est inefficace si la situation moyenne consiste à ne retourner qu’une seule ligne).

![Paramétrage de requêtes](./media/sql-database-performance-guidance/query_tuning_2.png)

La deuxième partie de l’exemple utilise un indicateur de requête pour spécifier à l’optimiseur d’utiliser une valeur spécifique pendant le processus de compilation. Dans ce cas, il force le processeur de requête à ignorer la valeur transmise comme paramètre et, à la place, à supposer une valeur « UNKNOWN », c’est-à-dire une valeur qui a la fréquence moyenne de la table (en ignorant le décalage). Le plan obtenu est un plan basé sur la recherche, qui est plus rapide et utilise moins de ressources en moyenne que le plan de la partie 1 de cet exemple.

![Paramétrage de requêtes](./media/sql-database-performance-guidance/query_tuning_3.png)

Les répercussions peuvent être observées en examinant la table **sys.resource\_stats** (il y a un délai entre le moment où vous exécutez le test et le moment où les données sont renseignées dans la table). Dans cet exemple, la partie 1 s’est exécutée pendant la fenêtre temporelle 22:25:00, et la partie 2 pendant la fenêtre temporelle 22:35:00. Notez que la première fenêtre temporelle a utilisé plus de ressources que la dernière (en raison d’améliorations de l’efficacité du plan).

	SELECT TOP 1000 *
	FROM sys.resource_stats
	WHERE database_name = 'resource1'
	ORDER BY start_time DESC

![Paramétrage de requêtes](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Bien que l’exemple utilisé ici soit volontairement petit, l’impact des paramètres non optimaux peut être considérable, notamment sur les bases de données volumineuses. Dans des cas extrêmes, la différence peut varier de quelques secondes à plusieurs heures pour les cas rapides et lents.

Vous pouvez examiner **sys.resource\_stats** pour déterminer si la ressource d'un test donné utilise plus ou moins de ressources qu'un autre test. Lors de la comparaison des données, espacez suffisamment les tests dans le temps pour qu’ils ne soient pas groupés dans la même fenêtre temporelle de 5 minutes dans la vue **sys.resource\_stats**. Le but de cet exercice est de réduire les ressources totales utilisées, et non pas de réduire les ressources. En règle générale, l’optimisation d’une partie de code pour la latence permet également de réduire l’utilisation des ressources. Assurez-vous que les modifications envisagées dans toute application sont nécessaires et n’affectent pas négativement l’expérience client pour quiconque utilisant une application lors de l’utilisation des indicateurs de requête.

Si une charge de travail contient un ensemble de requêtes répétitives, il est souvent judicieux de capturer et de valider l’optimalité de ces choix de plan dans la mesure où ils proposeront l’unité de taille de ressource minimum requise pour héberger la base de données. Une fois la validation effectuée, un réexamen occasionnel de ces plans peut vous assurer qu’ils ne sont pas dégradés. Pour plus d'informations sur les indicateurs de requête, consultez [Indicateurs de requête (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### Partitionnement entre plusieurs bases de données
Azure SQL Database s’exécutant sur du matériel, les limites de capacité pour une base de données unique sont inférieures par rapport à une installation SQL Server locale traditionnelle. Par conséquent, certains clients utilisent des techniques de partitionnement pour diffuser les opérations de base de données sur plusieurs bases de données lorsqu’elles ne s’adaptent pas aux limites d’une base de données unique dans Azure SQL Database. La plupart des clients utilisant des techniques de partitionnement aujourd’hui sur la base de données SQL Azure fractionnent leurs données dans une seule dimension sur plusieurs bases de données. L’approche implique de comprendre que, souvent, les applications OLTP exécutent des transactions qui s’appliquent uniquement à une ligne ou à un petit groupe de lignes dans le schéma.

>[AZURE.NOTE] La base de données SQL fournit désormais une bibliothèque pour faciliter le partitionnement. Pour en savoir plus, consultez [Vue d'ensemble de la bibliothèque cliente de bases de données élastiques](sql-database-elastic-database-client-library.md).

Par exemple, si une base de données contient un client, une commande et des détails relatifs à une commande (comme vu dans l’exemple de base de données Northwind classique fourni dans SQL Server), ces données pourraient être fractionnées en plusieurs bases de données, en regroupant un client avec la commande associée et les informations de détails relatifs à une commande et en garantissant qu’elles restent au sein d’une base de données unique. L’application fractionnerait différents clients sur les bases de données, répartissant ainsi efficacement la charge sur celles-ci. Le partitionnement permet non seulement aux clients d’éviter la limite de taille maximum de la base de données, mais également à Azure SQL Database de traiter les charges de travail qui sont beaucoup plus volumineuses que les limites des différents niveaux de performances à condition que chaque base de données individuelle s’adapte à sa DTU.

Bien que le partitionnement de base de données ne réduise pas la capacité des ressources globales pour une solution, cette technique est très efficace pour prendre en charge des solutions volumineuses réparties sur plusieurs bases de données ; elle permet à chaque base de données de s’exécuter dans un niveau de performances différent afin de prendre en charge de très grandes bases de données « efficaces » dont les besoins en ressources sont importants.

### Partitionnement fonctionnel
Les utilisateurs de SQL Server associent souvent plusieurs fonctions dans une base de données unique. Par exemple, si une application contient une logique pour gérer le stock d’un magasin, cette base de données peut contenir la logique associée au stock, le suivi des bons de commande, les procédures stockées et les vues indexées/matérialisées pour la gestion de rapports de fin de mois et d’autres fonctions. Cette technique offre l’avantage d’être capable de gérer facilement la base de données pour les opérations telles que la sauvegarde, mais elle nécessite également que vous dimensionniez le matériel pour gérer la charge maximale sur toutes les fonctions d’une application.

Dans une architecture de montée en charge utilisée dans Azure SQL Database, il est bénéfique de fractionner différentes fonctions d’une application dans différentes bases de données. Ainsi, chacune d’elles peut être mise à l’échelle indépendamment. Lorsqu’une application devient plus occupée (et reçoit plus de charge sur la base de données), l’administrateur peut choisir des niveaux de performances indépendamment pour chaque fonction dans une application. Dans la limite, cette architecture permet à une application de devenir plus grande que ce qu’un seul ordinateur peut gérer en diffusant la charge sur plusieurs ordinateurs.

### Traitement par lot des requêtes
Pour les applications qui accèdent aux données sous la forme de requêtes ad hoc, fréquentes et élevées, une grande partie du temps de réponse est passée sur la communication réseau entre la couche Application et la couche Azure SQL Database. Même lorsque l’application et Azure SQL Database résident dans le même centre de données, la latence du réseau entre les deux peut être accrue par un nombre élevé d’opérations d’accès aux données. Pour réduire les boucles sur le réseau pour ces opérations d’accès aux données, le développeur d’applications doit envisager les options de traitement par lot des requêtes ad hoc ou leur compilation dans les procédures stockées. Le traitement par lot des requêtes ad hoc permet d’envoyer plusieurs requêtes en un grand lot en un seul trajet vers Azure SQL Database. La compilation des requêtes ad hoc dans une procédure stockée peut obtenir le même résultat que le traitement par lot. L’utilisation d’une procédure stockée vous permet également d’augmenter les possibilités de mise en cache des plans de requête dans la base de données SQL Azure pour les exécutions suivantes de la même procédure stockée.

Certaines applications sont gourmandes en écriture. Parfois, il est possible de réduire la charge d’E/S totale sur une base de données en étudiant comment effectuer des écritures par lot. Souvent, cela est aussi simple que d’utiliser des transactions explicites au lieu des transactions de validation automatique dans les procédures stockées et les lots ad hoc. Vous trouverez une évaluation des différentes techniques utilisables à la page [Techniques de traitement par lot pour les applications de la base de données SQL dans Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Faites des essais avec votre propre charge de travail pour trouver le modèle approprié pour le traitement par lot, en prenant soin de comprendre qu’un modèle donné peut présenter des garanties de cohérence transactionnelle légèrement différentes. Déterminer la charge de travail qui réduit l’utilisation des ressources nécessite de trouver le bon équilibre entre cohérence et performances.

### Mise en cache de la couche Application
Certaines applications de base de données contiennent des charges de travail à lecture intensive. Il est possible d’utiliser les couches de mise en cache pour réduire la charge sur la base de données et, éventuellement, réduire le niveau de performances requis pour prendre en charge une base de données à l’aide de la base de données SQL Azure. Le [Cache Redis Azure](https://azure.microsoft.com/services/cache/) permet à un client possédant une charge de travail à lecture intensive de lire les données une seule fois (ou peut-être une seule fois par ordinateur de la couche Application, selon la façon dont il est configuré) et de stocker ces données en dehors de la base de données SQL Azure. Cela permet de réduire la charge de la base de données (UC et E/S de lecture), mais il existe un impact sur la cohérence transactionnelle, étant donné que les données lues à partir du cache peuvent être obsolètes vis-à-vis des données dans la base de données. Bien qu’il existe de nombreuses applications, où une certaine quantité d’incohérences est acceptable, cela n’est pas vrai pour toutes les charges de travail. Assurez-vous de comprendre pleinement les exigences d’une application avant d’utiliser une stratégie de mise en cache de couche Application.

## Étapes suivantes

- Pour plus d’informations sur les niveaux de service, consultez [Niveaux de service](sql-database-service-tiers.md).
- Pour plus d’informations sur les pools élastiques, consultez [Pools élastiques](sql-database-elastic-pool.md).
- Pour plus d’informations sur les performances et les pools élastiques, consultez [Quand utiliser un pool de base de données élastique ?](sql-database-elastic-pool-guidance.md).

<!---HONumber=AcomDC_0914_2016-->