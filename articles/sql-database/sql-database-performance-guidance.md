---
title: "Microsoft Azure SQL Database et performances pour les bases de données uniques | Microsoft Docs"
description: "Cet article vous aide à déterminer le niveau de service adapté à votre application. Il vous présente également des moyens de paramétrer votre application pour tirer le meilleur parti de votre instance Azure SQL Database."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/04/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: ec13e72de7ccebefbaa88309f8248f29b569ef2f
ms.openlocfilehash: e14b037f962afb803a2271b221e6309c7e8220cd


---
# <a name="azure-sql-database-and-performance-for-single-databases"></a>Microsoft Azure SQL Database et performances pour les bases de données uniques
Azure SQL Database propose trois [niveaux de service](sql-database-service-tiers.md): De base, Standard et Premium. Chaque niveau de service isole strictement les ressources pouvant être utilisées par SQL Database, et garantit des performances prévisibles pour ce niveau de service. Dans cet article, nous vous offrons des recommandations qui vous aideront à choisir le niveau de service adapté à votre application. Nous abordons également des modes de paramétrage de votre application destinés à tirer le meilleur de Microsoft Azure SQL Database.

> [!NOTE]
> Cet article se concentre sur les recommandations de performances pour les bases de données uniques dans Microsoft Azure SQL Database. Pour un guide des performances relatives aux pools élastiques, consultez [Considérations sur les prix et performances pour les pools élastiques](sql-database-elastic-pool-guidance.md). Notez, cependant, que vous pouvez appliquer un grand nombre des recommandations de cet article sur les bases de données d’un pool élastique, afin d’obtenir des avantages similaires en matière de performances.
> 
> 

Vous pouvez choisir entre trois niveaux de service Azure SQL Database (les performances sont mesurées en unités de débit de base de données, ou [DTU](sql-database-what-is-a-dtu.md)) :

* **De base**. Le niveau de service De base est conçu pour assurer une bonne prévisibilité des performances pour chaque base de données, heure après heure. Dans une base de données De base, des ressources suffisantes prennent en charge de bonnes performances au sein d’une petite instance qui ne présente pas plusieurs requêtes simultanées.
* **Standard**. Le niveau de service Standard offre une meilleure prévisibilité des performances et place la barre plus haut pour les bases de données avec plusieurs requêtes simultanées, par exemple les applications web ou de groupe de travail. Lorsque vous choisissez une base de données de niveau de service Standard, vous pouvez dimensionner votre application de base de données en fonction de performances prévisibles, minute après minute.
* **Premium**. Le niveau de service Premium offre des performances prévisibles seconde après seconde pour chaque base de données Premium. Lorsque vous choisissez le niveau de service Premium, vous pouvez dimensionner votre application de base de données en fonction de la charge maximale de votre instance. Le plan supprime les scénarios dans lesquels l’écart de performances peut allonger le traitement des petites requêtes dans les opérations sensibles à la latence. Ce modèle simplifie considérablement les cycles de validation de développement et de produit nécessaires pour les applications qui doivent appliquer des instructions fortes concernant les besoins maximum en ressources, l’écart de performances ou la latence des requêtes.

À chaque niveau de service, vous définissez le niveau de performances et disposer ainsi de la possibilité de payer uniquement la capacité nécessaire. Vous pouvez [ajuster la capacité](sql-database-scale-up.md), en l’augmentant ou en la diminuant, en fonction de l’évolution de la charge de travail. Par exemple, si la charge de travail de votre base de données est élevée au cours de la période des achats de rentrée scolaire, vous pouvez augmenter le niveau de performances pour une durée définie, par exemple entre juillet et septembre. Il est ensuite possible de le réduire à la fin de la période chargée. Vous pouvez réduire vos coûts en optimisant votre environnement cloud conformément aux caractéristiques saisonnières de votre entreprise. Ce modèle fonctionne également bien pour les cycles de version de logiciels. Une équipe de test peut allouer de la capacité pendant des séries de test, et libérer cette capacité une fois les tests terminés. Dans un modèle de requête de capacité, vous payez uniquement la capacité nécessaire, en évitant de financer des ressources dédiées que vous n’utiliseriez que rarement.

## <a name="why-service-tiers"></a>Pourquoi des niveaux de service ?
Bien que la charge de travail de chaque base de données puisse différer, les niveaux de service visent à assurer la prévisibilité des performances dans un large éventail de niveaux de performances. Les clients présentant des exigences d’envergure en matière de ressources de bases de données peuvent opérer dans un environnement informatique davantage dédié.

### <a name="common-service-tier-use-cases"></a>Cas d’utilisation courants des niveaux de service
#### <a name="basic"></a>De base
* **Vous débutez juste avec Azure SQL Database**. Bien souvent, les applications en cours de développement ne requièrent pas de hauts niveaux de performances. Les bases de données de base constituent un environnement idéal pour le développement à moindre coût des bases de données.
* **Vous disposez d’une base de données avec un utilisateur unique**. Généralement, les applications qui associent un seul utilisateur à une base de données n’ont pas des exigences élevées en matière d’accès concurrentiel et de performances. Les applications de ce type font des candidates idéales pour le niveau de service De base.

#### <a name="standard"></a>Standard
* **Votre base de données présente de multiples requêtes simultanées**. Les applications utilisées simultanément par plusieurs utilisateurs requièrent généralement des niveaux de performances plus élevés. Par exemple, les sites web avec un trafic modéré ou des applications de service qui requièrent une quantité supérieure de ressources font d’excellents candidats pour le niveau de service Standard.

#### <a name="premium"></a>Premium
La plupart des cas d’utilisation du niveau de service Premium présentent une ou plusieurs de ces caractéristiques :

* **Pic de charge élevés**. Une application qui nécessite un volume élevé d’UC, de mémoire ou d’entrée/sortie (E/S) pour exécuter ses opérations requiert un niveau de performances élevé, dédié. Par exemple, si une opération de base de données est connue pour utiliser plusieurs cœurs d’UC pendant une période prolongée, l’utilisation du niveau de service Premium est appropriée.
* **Plusieurs requêtes simultanées**. Certaines applications de base de données gèrent de nombreuses demandes simultanées, par exemple un site web avec un volume de trafic élevé. Les niveaux de service De base et Standard présentent des limites au nombre de demandes simultanées par base de données. Les applications qui requièrent plus de connexions doivent choisir une taille de réservation appropriée pour traiter le nombre maximum de demandes nécessaires.
* **Latence faible**. Certaines applications doivent garantir une réponse de la base de données dans un délai minimum. Si une procédure stockée donnée est appelée dans le cadre d’une opération client plus large, il existe peut-être une exigence de renvoi depuis l’appel en moins de 20 millisecondes 99 % du temps. Ce type d’application bénéficie du niveau de service Premium afin de garantir la disponibilité de la puissance de calcul.

Le niveau exact dont vous avez besoin pour votre base de données SQL dépend des exigences de charge maximale pour chaque dimension de ressource. Certaines applications utilisent une quantité insignifiante pour une ressource mais ont des exigences considérables pour d’autres.

## <a name="service-tier-capabilities-and-limits"></a>Capacités et limites des niveaux de service
Chaque niveau de service et de performance est associé à différentes limites et caractéristiques de performances. Le tableau suivant décrit ces caractéristiques pour une base de données unique.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="maximum-in-memory-oltp-storage"></a>Stockage OLTP In-Memory maximum
Vous pouvez utiliser la vue **sys.dm_db_resource_stats** pour surveiller votre utilisation du stockage Microsoft Azure en mémoire. Pour plus d’informations sur la surveillance, consultez [Surveiller le stockage OLTP In-Memory](sql-database-in-memory-oltp-monitoring.md).

### <a name="maximum-concurrent-requests"></a>Nombre maximal de requêtes simultanées
Pour afficher le nombre de requêtes simultanées, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Pour analyser la charge de travail d’une base de données SQL Server locale, modifiez cette requête pour la filtrer selon la base de données spécifique que vous analysez. Par exemple, si vous utilisez une base de données locale nommée MyDatabase, cette requête Transact-SQL renvoie le nombre de requêtes simultanées dans cette base de données :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Il s’agit simplement d’un instantané à un point unique dans le temps. Pour obtenir une meilleure compréhension de votre charge de travail et des exigences liées aux demandes simultanées, il vous faut collecter plusieurs échantillons au fil du temps.

### <a name="maximum-concurrent-logins"></a>Nombre maximal de connexions simultanées
Vous pouvez analyser vos modèles d'utilisateur et d'application pour avoir une idée de la fréquence des connexions. Vous pouvez également exécuter des charges réelles dans un environnement de test pour vous assurer que vous n’atteignez pas cette limite ou d’autres limites décrites dans cet article. Il n’existe aucune requête ou vue de gestion dynamique qui peut vous indiquer le nombre de connexions simultanées ou un historique dédié.

Si plusieurs clients utilisent la même chaîne de connexion, le service authentifie chaque connexion. Si 10 utilisateurs se connectent simultanément à une base de données avec les mêmes nom d’utilisateur et mot de passe, dix connexions simultanées seront établies. Cette limite s’applique uniquement à la durée de la connexion et de l’authentification. Si ces mêmes 10 utilisateurs se connectent séquentiellement à la base de données, le nombre de connexions simultanées ne sera jamais supérieur à 1.

> [!NOTE]
> Actuellement, cette limite ne s’applique pas aux bases de données de pools élastiques.
> 
> 

### <a name="maximum-sessions"></a>Nombre maximal de sessions
Pour afficher le nombre de sessions simultanément actives, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Si vous analysez une charge de travail SQL Server locale, modifiez la requête pour vous concentrer sur une base de données spécifique. Cette requête vous aide à déterminer les éventuels besoins de votre session pour la base de données si vous envisagez de la déplacer vers Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Là encore, ces requêtes renvoient un nombre à un point dans le temps. Si vous collectez plusieurs échantillons au fil du temps, vous bénéficiez d’une meilleure compréhension de l’utilisation de votre session.

Pour l’analyse de SQL Database, vous pouvez obtenir des statistiques d’historique sur les sessions. Interrogez **sys.resource_stats**, puis utilisez la colonne **active_session_count**. Pour plus d’informations sur l’utilisation de cet affichage, consultez la section suivante.

## <a name="monitor-resource-use"></a>Surveiller l’utilisation des ressources
Il existe deux vues qui vous permettent de surveiller l’utilisation des ressources d’une base de données SQL par rapport à son niveau de service :

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Vous pouvez utiliser la vue [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans chaque SQL Database. La vue **sys.dm_db_resource_stats** représente les données d’utilisation récente des ressources par rapport au niveau de service. Les pourcentages moyens d’UC, d’E/S des données, d’écritures du journal et de mémoire sont enregistrés toutes les 15 secondes et conservés pendant une heure.

Étant donné que cette vue fournit un aperçu plus granulaire de l’utilisation des ressources, utilisez d’abord **sys.dm_db_resource_stats** pour n’importe quelle analyse d’état actuel ou pour la résolution des problèmes. Par exemple, cette requête affiche l’utilisation moyenne et maximale des ressources pour la base de données actuelle sur la dernière heure :

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Pour les autres requêtes, consultez les exemples dans [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

### <a name="sysresourcestats"></a>sys.resource_stats
La vue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) de la base de données **master** fournit des informations supplémentaires vous permettant d’analyser les performances de votre SQL Database à ses niveaux de service et de performances spécifiques. Les données, qui sont collectées toutes les 5 minutes, sont conservées pendant environ 35 jours. Cette vue est utile pour une analyse historique de plus long terme sur l’utilisation des ressources par votre base de données SQL.

Le graphique suivant illustre l’utilisation des ressources d’UC pour une base de données Premium avec le niveau de performances P2 pour chaque heure de la semaine. Ce graphique spécifique commence un lundi, affiche 5 journées de travail, puis un week-end où l’application connaît une activité réduite.

![Utilisation des ressources de base de données SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

D’après les données, cette base de données présente actuellement une charge d’UC maximum juste supérieure à 50 % de l’utilisation de l’UC par rapport au niveau de performances P2 (à la mi-journée le mardi). Si l’UC est le facteur dominant dans le profil de ressource de l’application, vous pourriez décider que P2 est le niveau de performances approprié pour garantir que la charge de travail est toujours adaptée. Si vous prévoyez une croissance de l’application au fil du temps, vous avez tout intérêt à avoir une mémoire-tampon de ressources supplémentaires, afin que l’application n’atteigne jamais le plafond de performances. Si vous augmentez le niveau de performances, vous pouvez éviter les erreurs consultables par les clients susceptibles de se produire lorsqu’une application ne dispose pas de la puissance nécessaire au traitement efficace des requêtes, plus particulièrement au sein des environnements sensibles à la latence. Comme exemple, considérons une base de données qui prend en charge une application remplissant les pages web en fonction des résultats des appels de la base de données.

Notez que d’autres types d’applications peuvent interpréter différemment le même graphique. Par exemple, si une application essaie de traiter les données de paie chaque jour et obtient le même graphique, ce genre de modèle de « traitement par lot » peut convenir dans un niveau de performances P1. Le niveau de performances P1 possède 100 DTU, contre 200 DTU pour le niveau de performances P2. Le niveau de performances P1 fournit donc la moitié des performances du niveau de performances P2. Par conséquent, 50 % d’utilisation de l’UC au niveau P2 correspond à 100 % d’utilisation de l’UC au niveau de performance P1. Si l’application n’a pas de délai d’expiration, le fait qu’une tâche volumineuse s’exécute en 2 h ou 2 h 30 peut ne pas avoir d’importance à condition qu’elle soit effectuée le jour même. Une application de cette catégorie peut probablement utiliser un niveau de performances P1. Vous pouvez tirer parti du fait qu’il y a des périodes pendant la journée où l’utilisation des ressources est moindre, ce qui signifie que toute période de pointe peut déborder sur l’un des creux plus tard dans la journée. Le niveau de performances P1 peut convenir pour une application de ce type (et permettre de réaliser des économies) tant que les tâches peuvent se terminer à temps chaque jour.

La SQL Database Azure expose les informations sur les ressources utilisées pour chaque base de données active dans la vue **sys.resource_stats** de la base de données **master** dans chaque serveur. Les données de la table sont agrégées par intervalle de 5 minutes. Avec les niveaux de service De base, Standard et Premium, les données peuvent prendre plus de 5 minutes pour apparaître dans la table, ce qui signifie qu’elles conviennent mieux aux analyses historiques qu’aux analyses en temps réel. L’interrogation de la vue **sys.resource_stats** affiche l’historique récent d’une base de données et confirme si la réservation sélectionnée a fourni la performance souhaitée lorsque c’était nécessaire.

> [!NOTE]
> Vous devez être connecté à la base de données **master** de votre serveur SQL Database logique pour interroger **sys.resource_stats** dans les exemples suivants.
> 
> 

Cet exemple illustre l’exposition des données dans cette vue :

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La vue de catalogue sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L’exemple suivant vous montre différentes manières d’utiliser la vue du catalogue **sys.resource_stats** pour obtenir des informations sur l’utilisation des ressources par votre SQL Database :

1. Pour consulter l’utilisation des ressources pour la base de données userdb1 au cours de la semaine passée, exécutez cette requête :
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Afin d’évaluer l’adéquation entre votre charge de travail et le niveau de performances, vous devez étudier les différents aspects des mesures de ressources : UC, lectures, écritures, nombre de Workers et nombre de sessions. Voici une requête modifiée à l’aide de **sys.resource_stats** afin d’indiquer les valeurs moyennes et maximales de ces mesures de ressources :
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Avec les informations ci-dessus relatives aux valeurs moyennes et maximales de chaque mesure de ressources, vous pouvez évaluer l’adéquation entre votre charge de travail et le niveau de performances que vous avez choisi. En général, les valeurs moyennes de **sys.resource_stats** vous offrent une bonne référence à utiliser par rapport à la taille cible. Elles doivent constituer votre principale jauge de mesure. Par exemple, il se peut que vous utilisiez le niveau de service Standard avec le niveau de performance S2. Les pourcentages d’utilisation moyens pour les lectures et écritures d’UC et d’E/S se situent en deçà de 40 %, le nombre moyen de Workers est inférieur à 50 et le nombre moyens de sessions est inférieur à 200. Votre charge de travail peut être prise en charge par le niveau de performance S1. Il est facile de voir si votre base de données s’intègre dans les limites de Workers et de sessions. Pour voir si une base de données s’adapte à un niveau de performances inférieur en ce qui concerne l’UC, les lectures et les écritures, divisez le nombre de DTU du niveau de performances inférieur par le nombre de DTU de votre niveau de performances actuel et multipliez le résultat par 100 :
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Le résultat est la différence de performances relative entre les deux niveaux de performances en pourcentage. Si votre utilisation des ressources ne dépasse pas cette quantité, votre charge de travail peut être traitée par ce niveau de performances inférieur. Toutefois, vous devez également examiner toutes les plages de valeurs d’utilisation des ressources et déterminer, en vous appuyant sur le pourcentage, la fréquence selon laquelle la charge de travail de votre base de données s’adapterait au niveau de performances inférieur. La requête suivante génère le pourcentage d’adéquation par dimension de ressource, selon le seuil de 40 % calculé dans cet exemple :
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    En fonction de votre objectif de niveau de service de la base de données, vous pouvez décider si votre charge de travail s’adapte au niveau de performances inférieur. Si votre objectif de niveau de service de charge de travail de la base de données est de 99,9 % et que la requête précédente retourne des valeurs supérieures à 99,9 pour les trois dimensions de ressources, il est probable que votre charge de travail s’adapte au niveau de performances inférieur.
   
    En examinant le pourcentage d’adéquation, vous savez par ailleurs si vous devez passer au niveau de performances supérieur pour atteindre votre objectif de niveau de service. Par exemple, l’utilisation d’UC suivante est indiquée pour la base de données userdb1 pour la semaine passée :
   
   | Pourcentage moyen d’UC | Pourcentage maximum d’UC |
   | --- | --- |
   | 24,5 |100,00 |
   
    Le pourcentage moyen d’UC représente environ un quart de la limite du niveau de performances, ce qui conviendrait bien au niveau de performances de la base de données. Toutefois, la valeur maximum montre que la base de données atteint la limite du niveau de performances. Devez-vous passer au niveau de performances supérieur ? Vous devez examiner le nombre de fois où votre charge de travail atteint 100 % et comparer ce chiffre à votre objectif de niveau de service de charge de travail de la base de données.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Si cette requête retourne une valeur inférieure à 99,9 % pour l’une des trois dimensions de ressources, vous devez envisager de passer au niveau de performances supérieur, ou employer des techniques de paramétrage de l’application afin de réduire la charge sur la base de données SQL.
4. Cet exercice tient également compte de l’augmentation prévue de votre charge de travail à l’avenir.

## <a name="tune-your-application"></a>Paramétrer votre application
Dans un SQL Server local traditionnel, le processus de planification de la capacité initiale est souvent séparé du processus d’exécution d’une application en production. Le matériel et les licences de produit sont achetés dans un premier temps, tandis que le paramétrage des performances est exécuté ultérieurement. Lorsque vous utilisez Microsoft Azure SQL Database, il est judicieux d’imbriquer le processus d’exécution d’une application, puis de le paramétrer. Avec le modèle de paiement de la capacité à la demande, vous pouvez paramétrer votre application pour utiliser les ressources minimales requises immédiatement, au lieu de surprovisionner massivement du matériel en vous appuyant sur des hypothèses de plans de croissance à venir pour une application (qui sont souvent incorrectes). Certains clients peuvent décider de ne pas paramétrer une application et choisir à la place de surprovisionner des ressources matérielles. Cette approche peut s’avérer utile si vous ne souhaitez pas modifier une application clé pendant une période d’activité. Cependant, le paramétrage d’une application peut réduire vos besoins en ressources et diminuer vos factures mensuelles lorsque vous utilisez les niveaux de service dans Azure SQL Database.

### <a name="application-characteristics"></a>Caractéristiques des applications
Bien que les niveaux de service Azure SQL Database soient conçus pour améliorer la stabilité et la prévisibilité des performances d’une application, il existe quelques meilleures pratiques à mettre à profit pour le paramétrage de votre application afin de mieux tirer parti des ressources d’un niveau de performances. Bien que de nombreuses applications gagnent considérablement en performances en passant simplement à un niveau de performances et/ou de service supérieur, certaines d’entre elles nécessitant un paramétrage supplémentaire pour bénéficier de ces avantages. Pour améliorer les performances, envisagez de procéder à un paramétrage supplémentaire sur les applications présentant ces caractéristiques :

* **Les applications dont les performances sont lentes en raison d’un comportement bavard**. Les applications qui se caractérisent par un comportement bavard effectuent trop d’opérations d’accès aux données qui sont sensibles à la latence du réseau. Il vous faudra éventuellement modifier ces types d’applications pour réduire le nombre d’opérations d’accès aux données de la base de données SQL. Par exemple, vous pouvez améliorer les performances applicatives à l’aide de techniques comme le traitement par lot des requêtes ou le déplacement de requêtes vers les procédures stockées. Pour plus d’informations, consultez la section [Traitement par lot des requêtes](#batch-queries).
* **Bases de données présentant une charge de travail intensive ne pouvant pas être prise en charge par un seul ordinateur**. Les bases de données qui dépassent le quota de ressources du niveau supérieur de performance Premium peuvent profiter de la montée en charge de votre charge de travail. Pour plus d’informations, consultez les sections [Partitionnement entre plusieurs bases de données](#cross-database-sharding) et [Partitionnement fonctionnel](#functional-partitioning).
* **Applications présentant des requêtes non optimales**. Les applications, en particulier celles de la couche d’accès aux données, qui présentant des requêtes mal paramétrées peuvent ne pas tirer parti d’un niveau de performance supérieur. Cela inclut les requêtes dépourvues d’une clause WHERE et les requêtes présentant des index manquants ou des statistiques obsolètes. Ces applications bénéficient des techniques de paramétrage des performances de requête standard. For more information, consultez les sections [Index manquants](#missing-indexes) et [Paramétrage/Compréhension de requêtes](#query-tuning-and-hinting).
* **Les applications dotées d’un accès aux données non optimal**. Les applications qui rencontrent des problèmes inhérents de concurrence d’accès aux données, par exemple d’interblocage, peuvent ne pas tirer parti d’un niveau de performances supérieur. Envisagez de réduire les boucles sur l’instance Microsoft Azure SQL Database en mettant en cache des données côté client à l’aide du service Azure Caching ou d’autres technologies de mise en cache. Consultez la section [Mise en cache de la couche Application](#application-tier-caching).

## <a name="tuning-techniques"></a>Techniques de paramétrage
Cette section explique certaines techniques que vous pouvez utiliser pour paramétrer Azure SQL Database afin d’obtenir les meilleures performances de votre application et d’être en mesure d’exécuter le plus petit niveau de performances possible. Plusieurs de ces techniques correspondent aux meilleures pratiques de paramétrage SQL Server traditionnelles, mais certaines sont spécifiques à Azure SQL Database. Dans certains cas, vous pouvez examiner les ressources utilisées par une base de données afin de déterminer des zones à paramétrer davantage et d’étendre les techniques traditionnelles SQL Server afin qu’elles fonctionnent dans Azure SQL Database.

### <a name="azure-portal-tools"></a>Outils du Portail Azure
Dans le portail Azure, vous trouverez deux outils qui peuvent vous aider à analyser et à corriger les problèmes de performances de votre base de données SQL :

* [Query Performance Insight](sql-database-query-performance.md)
* [SQL Database Advisor](sql-database-advisor.md)

Le portail Azure comporte davantage d’informations sur ces deux outils et sur les méthodes d’utilisation correspondantes. Pour diagnostiquer et corriger efficacement les problèmes, nous vous recommandons d’essayer dans un premier temps les outils dans le portail Azure. Nous vous recommandons d’utiliser les approches de paramétrage manuel évoquées plus bas, relatives aux index manquants et au paramétrage des requêtes, dans des cas particuliers.

### <a name="missing-indexes"></a>Index manquants
Un problème courant dans les performances de base de données OLTP est la conception physique de la base de données. Souvent, les schémas de base de données sont conçus et livrés sans test de mise à l’échelle (en charge ou en volume de données). Malheureusement, les performances d’un plan de requête peuvent être acceptables à petite échelle mais se dégrader notablement avec des volumes de données au niveau de la production. La cause la plus courante de ce problème est l’absence d’index appropriés pour satisfaire des filtres ou d’autres restrictions dans une requête. Souvent, cela se manifeste comme une analyse de table là où une recherche d’index pourrait suffire.

Dans cet exemple, le plan de requête sélectionné utilise une analyse là où une recherche suffirait :

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

![Un plan de requête avec index manquants](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database peut vous aider à rechercher et à corriger les conditions communes d’index manquants. Les vues de gestion dynamique intégrées à Microsoft Azure SQL Database analysent les compilations de requêtes dans lesquelles un index réduirait considérablement le coût estimé d’exécution d’une requête. Pendant l’exécution de requêtes, SQL Database suit la fréquence selon laquelle chaque plan de requête est exécuté, ainsi que l’écart estimé entre le plan de requête en cours d’exécution et le plan imaginé dans lequel cet index existait. Ces vues de gestion dynamique vous permettent d’identifier rapidement les modifications de conception physique de la base de données susceptibles d’améliorer le coût total de la charge de travail pour une base de données et sa charge de travail réelle.

Vous pouvez utiliser cette requête pour évaluer les index manquants potentiels :

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

Dans cet exemple, la requête a entraîné cette suggestion :

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Après sa création, cette même instruction SELECT sélectionne un plan différent, qui utilise une recherche en lieu et place d’une analyse, puis exécute plus efficacement le plan :

![Un plan de requête avec index corrigés](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

L’information clé est que la capacité d’E/S d’un système partagé est plus limitée que celle d’un serveur dédié. Il est primordial de réduire les E/S inutiles pour tirer le meilleur parti du système dans la DTU de chaque niveau de performances des niveaux de service dans Azure SQL Database. Des choix appropriés de conception physique de base de données peuvent considérablement améliorer la latence des requêtes individuelles, le débit des requêtes simultanées que vous pouvez traiter par unité d’échelle, et réduire les coûts nécessaires pour satisfaire la requête. Pour plus d’informations sur les DMV d’index manquants , consultez [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Paramétrage/Compréhension de requêtes
L’optimiseur de requête de Microsoft Azure SQL Database est similaire à l’optimiseur de requête SQL Server traditionnel. La plupart des meilleures pratiques pour le paramétrage des requêtes et la compréhension du raisonnement relatif aux limitations du modèle pour l’optimiseur de requête s’appliquent également à Microsoft Azure SQL Database. Si vous paramétrez des requêtes dans Microsoft Azure SQL Database, vous pouvez profiter de l’avantage supplémentaire de réduction des exigences de ressources globales. Votre application peut s’exécuter à un coût inférieur à celui d’une instance équivalent non paramétrée, car elle peut fonctionner à un niveau de performance moindre.

Un scénario courant dans SQL Server, qui s’applique également à Microsoft Azure SQL Database, est la détection des paramètres par l’optimiseur de requête. Pendant la compilation, l’optimiseur de requête évalue la valeur actuelle d’un paramètre pour déterminer s’il est possible de générer un plan de requête plus optimal. Bien que cette stratégie mène souvent à un plan de requête qui est beaucoup plus rapide qu’un plan compilé sans valeurs de paramètre connues, il fonctionne actuellement de manière imparfaite dans SQL Server et dans Microsoft Azure SQL Database. Parfois le paramètre n’est pas surveillé, et d’autres fois le paramètre est surveillé mais le plan généré n’est pas optimal pour l’ensemble complet de valeurs de paramètres d’une charge de travail. Microsoft inclut des indicateurs de requête (directives) pour vous permettre de spécifier vos intentions plus délibérément et de remplacer le comportement par défaut pour la détection des paramètres. Souvent, l’utilisation d’indicateurs peut résoudre les cas où le comportement de SQL Server/de Microsoft Azure SQL Database par défaut est imparfait pour une charge de travail client donnée.

L’exemple suivant illustre la manière dont le processeur de requêtes peut générer un plan non optimal en matière de performances et de ressources requises. Cet exemple vous montre que si vous utilisez un indicateur de requête, vous pouvez réduire la durée d’exécution et le nombre de ressources requises pour votre base de données SQL :

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

Le code d’installation crée une table qui contient une distribution décalée des données. Le plan de requête optimal varie en fonction du paramètre sélectionné. Malheureusement, le comportement de mise en cache du plan ne recompile pas toujours la requête en fonction de la valeur de paramètre la plus courante. Par conséquent, il est possible de mettre en cache un plan non optimal et de l’utiliser pour plusieurs valeurs, même si un autre plan pourrait constituer un meilleur choix en moyenne. Ensuite, le plan de requête génère deux procédures stockées identiques, sauf que celle-ci présente un indicateur spécifique de requête.

**Exemple, partie 1**

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

**Exemple, partie 2**

(Nous vous recommandons d’attendre au moins 10 minutes avant de commencer la partie 2 de l’exemple, de manière à ce que les résultats soient différents dans les données de télémétrie obtenues.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Chaque partie de cet exemple essaie d’exécuter une instruction insert paramétrable 1 000 fois (pour générer une charge suffisante pour être intéressante sur un jeu de données de test). Lors de l’exécution des procédures stockées, le processeur de requêtes examine la valeur de paramètre transmise à la procédure lors de sa première compilation (appelée détection de paramètres). Le processeur met en cache le plan résultant et l’utilise pour les appels ultérieurs, même si la valeur de paramètre est différente. Le plan optimal peut ne pas être utilisé dans tous les cas. Parfois, les clients doivent guider l’optimiseur vers le choix d’un plan adapté aux situations courantes, plutôt qu’aux situations spécifiques lorsque la requête a été compilée en premier lieu. Dans cet exemple, le plan initial génère un plan de type analyse qui lit toutes les lignes pour trouver chaque valeur qui correspond au paramètre :

![Optimisation des requêtes à l’aide d’un plan d’analyse](./media/sql-database-performance-guidance/query_tuning_1.png)

Étant donné que nous avons exécuté la procédure avec la valeur 1, le plan obtenu était optimal pour 1, mais non optimal pour toutes les autres valeurs dans la table. Le résultat n’est probablement pas ce que vous attendriez en sélectionnant chaque plan de manière aléatoire, car le plan s’exécute plus lentement et utilise davantage de ressources.

Si vous exécutez le test avec `SET STATISTICS IO` défini sur `ON`, la tâche d’analyse logique de cet exemple est effectuée en arrière-plan. Vous pouvez voir que 1 148 lectures sont effectuées par le plan (ce qui est inefficace, si le cas moyen renvoie juste une ligne) :

![Optimisation des requêtes à l’aide d’une analyse logique](./media/sql-database-performance-guidance/query_tuning_2.png)

La deuxième partie de l’exemple utilise un indicateur de requête pour spécifier à l’optimiseur d’utiliser une valeur spécifique pendant le processus de compilation. Dans ce cas, il force le processeur de requêtes à ignorer la valeur transmise en tant que paramètre, et à prendre en compte `UNKNOWN`. Cela fait référence à une valeur présentant la fréquence moyenne de la table (en ignorant le décalage). Le plan obtenu est un plan basé sur la recherche, qui est plus rapide et utilise moins de ressources en moyenne que le plan de la partie 1 de cet exemple :

![Optimisation des requêtes à l’aide d’un indicateur de requête](./media/sql-database-performance-guidance/query_tuning_3.png)

Vous pouvez voir l’effet dans la table **sys.resource_stats** (un délai est respecté entre l’exécution du test et l’ajout des données dans la table). Dans cet exemple, la partie 1 s’est exécutée pendant la fenêtre temporelle 22:25:00, et la partie 2 pendant la fenêtre temporelle 22:35:00. Notez que la première fenêtre temporelle a utilisé plus de ressources que la dernière (en raison d’améliorations de l’efficacité du plan).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Résultats des exemples de paramétrage des requêtes](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Bien que le volume de cet exemple soit intentionnellement petit, l’effet des paramètres non optimaux peut s’avérer conséquent, plus particulièrement sur les bases de données plus grandes. La différence, dans les cas extrêmes, se chiffre en secondes pour les scénarios rapides et en heures pour les scénarios lents.
> 
> 

Vous pouvez examiner **sys.resource_stats** pour déterminer si la ressource d’un test donnée utilise plus ou moins de ressource qu’un autre test. Lorsque vous comparez les données, espacez les tests dans le temps pour qu’ils ne soient pas groupés dans la même fenêtre temporelle de 5 minutes dans la vue **sys.resource_stats**. Le but de cet exercice est de réduire les ressources totales utilisées, et non pas de réduire les ressources. En règle générale, l’optimisation d’une partie de code pour la latence permet également de réduire l’utilisation des ressources. Assurez-vous que les modifications apportées à une application sont nécessaires, et que les modifications n’affectent pas négativement les utilisateurs susceptibles d’utiliser des indicateurs de requêtes dans l’application.

Si une charge de travail présente un ensemble de requêtes répétitives, il est souvent judicieux de capturer et de valider l’optimalité de ces choix de plan, dans la mesure où ils proposeront l’unité de taille de ressource minimum requise pour héberger la base de données. Après la validation, réexaminez régulièrement les plans afin de vous assurer de leur non-dégradation. Pour plus d’informations, consultez la page [Indicateurs de requête (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Partitionnement entre plusieurs bases de données
Microsoft Azure SQL Database s’exécutant sur du matériel, les limites de capacité pour une base de données unique sont inférieures par rapport à une installation SQL Server locale traditionnelle. Certains clients utilisent des techniques de partitionnement pour diffuser les opérations de base de données sur plusieurs bases de données lorsqu’elles ne s’adaptent pas aux limites d’une base de données unique dans Microsoft Azure SQL Database. La plupart des clients utilisant des techniques de partitionnement dans Microsoft Azure SQL Database fractionnent leurs données dans une seule dimension sur plusieurs bases de données. Pour cette approche, vous devez comprendre que les applications OLTP exécutent souvent des transactions s’appliquant uniquement à une ligne ou à un petit groupe de lignes dans le schéma.

> [!NOTE]
> La base de données SQL fournit désormais une bibliothèque pour faciliter le partitionnement. Pour en savoir plus, consultez [Vue d'ensemble de la bibliothèque cliente de bases de données élastiques](sql-database-elastic-database-client-library.md).
> 
> 

Par exemple, si une base de données comporte le nom d’un client, une commande et des détails relatifs à la commande (comme vu dans l’exemple de base de données Northwind classique fourni dans SQL Server), vous pouvez fractionner ces données en plusieurs bases de données, en regroupant un client avec la commande associée et les détails relatifs à cette commande. Vous pouvez garantir que les données du client sont conservées dans une base de données unique. L’application fractionnerait différents clients sur les bases de données, répartissant ainsi efficacement la charge sur celles-ci. Le partitionnement permet non seulement aux clients d’éviter la limite de taille maximum de la base de données, mais également à Microsoft Azure SQL Database de traiter les charges de travail qui sont beaucoup plus volumineuses que les limites des différents niveaux de performances à condition que chaque base de données individuelle s’adapte à sa DTU.

Bien que le partitionnement de base de données ne réduise pas la capacité des ressources globales pour une solution, cette technique est très efficace pour prendre en charge des solutions volumineuses réparties sur plusieurs bases de données. Chaque base de données peut s’exécuter à un niveau de performance différent afin de prendre en charge des bases de données très grandes, efficaces, dont les besoins en ressources sont importants.

### <a name="functional-partitioning"></a>Partitionnement fonctionnel
Les utilisateurs SQL Server combinent bien souvent de nombreuses fonctions dans une seule base de données. Par exemple, si une application contient une logique pour gérer le stock d’un magasin, cette base de données peut contenir la logique associée au stock, le suivi des bons de commande, les procédures stockées et les vues indexées/matérialisées pour la gestion de rapports de fin de mois et d’autres fonctions. Cette technique facilite l’administration de la base de données pour des opérations comme la sauvegarde, mais elle nécessite également que vous redimensionniez le matériel pour gérer la charge maximale sur toutes les fonctions d’une application.

Si vous utilisez une architecture de montée en charge dans Microsoft Azure SQL Database, il peut être judicieux de répartir différentes fonctions d’une application entre plusieurs bases de données. En appliquant cette technique, chaque application est mise à l’échelle indépendamment. Lorsqu’une application devient plus occupée (et reçoit plus de charge sur la base de données), l’administrateur peut choisir des niveaux de performances indépendamment pour chaque fonction dans une application. À la limite, cette architecture permet à une application de devenir plus grande que ce qu’un seul ordinateur peut gérer en diffusant la charge sur plusieurs ordinateurs.

### <a name="batch-queries"></a>Traitement par lot des requêtes
Pour les applications qui accèdent aux données sous la forme de requêtes ad hoc, fréquentes et à volume élevé, une grande partie du temps de réponse est passée sur la communication réseau entre la couche Application et la couche Microsoft Azure SQL Database. Même lorsque l’application et Microsoft Azure SQL Database résident dans le même centre de données, la latence du réseau entre les deux peut être accrue par un nombre élevé d’opérations d’accès aux données. Pour réduire les boucles réseau requises par les opérations d’accès aux données, envisagez d’utiliser l’option pour traiter par lot les requêtes ad hoc, ou pour les compiler en tant que procédures stockées. Si vous traitez par lot les requêtes ad hoc, vous pouvez envoyer plusieurs requêtes sous forme de lot important communiqué dans une boucle unique à destination de l’instance Microsoft Azure SQL Database. Si vous compilez les requêtes ad hoc dans une procédure stockée, vous pouvez obtenir des résultats similaires à ceux du traitement par lot. L’utilisation d’une procédure stockée vous permet également d’augmenter les possibilités de mise en cache des plans de requête dans Azure SQL Database pour les exécutions suivantes de la même procédure stockée.

Certaines applications sont gourmandes en écriture. Parfois, il est possible de réduire la charge d’E/S totale sur une base de données en étudiant comment effectuer des écritures par lot. Souvent, cela est aussi simple que d’utiliser des transactions explicites au lieu des transactions de validation automatique dans les procédures stockées et les lots ad hoc. Vous trouverez une évaluation des différentes techniques utilisables à la page [Comment utiliser le traitement par lots pour améliorer les performances des applications de base de données SQL](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Faites des essais avec votre propre charge de travail afin d’identifier le modèle approprié pour le traitement par lot. Assurez-vous de tenir compte du fait que les modèles peuvent présenter des garanties de cohérence transactionnelle légèrement différentes. L’identification de la charge de travail adaptée pour la réduction de l’utilisation des ressources nécessite de trouver le bon équilibre entre cohérence et performances.

### <a name="application-tier-caching"></a>Mise en cache de la couche Application
Certaines applications de base de données contiennent des charges de travail à lecture intensive. Les couches de mise en cache peuvent contribuer à réduire la charge sur la base de données et éventuellement le niveau de performance requis pour la prise en charge d’une base de données à l’aide d’Azure SQL Database. Le [Cache Redis Azure](https://azure.microsoft.com/services/cache/)permet à un client possédant une charge de travail à lecture intensive de lire les données une seule fois (ou peut-être une seule fois par ordinateur de la couche Application, selon la façon dont il est configuré) et de stocker ces données en dehors de la base de données SQL Azure. Cela permet de réduire la charge de la base de données (UC et E/S de lecture), mais il existe un impact sur la cohérence transactionnelle, étant donné que les données lues à partir du cache peuvent être désynchronisées avec les données de la base de données. Si un certain niveau d’incohérence est acceptable dans de nombreuses applications, cela ne se vérifie pas pour l’ensemble des charges de travail. Assurez-vous de comprendre pleinement les exigences d’une application avant d’utiliser une stratégie de mise en cache de couche Application.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les niveaux de service, consultez la section [Options et performances de la base de données SQL](sql-database-service-tiers.md)
* Pour de plus amples informations sur les pools élastiques, consultez la page [Qu’est-ce qu’un pool élastique Azure ?](sql-database-elastic-pool.md)
* Pour plus d’informations sur les performances et les pools élastiques, consultez la page [Quand envisager d’utiliser un pool élastique ?](sql-database-elastic-pool-guidance.md)




<!--HONumber=Jan17_HO2-->


