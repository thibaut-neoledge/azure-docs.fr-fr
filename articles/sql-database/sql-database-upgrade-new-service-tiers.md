<properties 
	pageTitle="Mise à jour des bases de données SQL des éditions Web ou Business vers les nouveaux niveaux de service" 
	description="Mettez à niveau les bases de données SQL Azure Web ou Business vers les nouveaux niveaux de performance et niveaux de service de la base de données SQL Azure : De base, Standard et Premium." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/28/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Mise à jour des bases de données SQL des éditions Web ou Business vers les nouveaux niveaux de service

Les [bases de données Web et Business Azure SQL seront retirées](sql-database-web-business-sunset-faq.md) et il est donc temps d mettre à niveau les bases de données Web ou Business vers les [niveaux de service de base, Standard, Premium ou Elastic](sql-database-service-tiers.md).


> [AZURE.IMPORTANT]La mise à niveau des bases de données Web ou Business vers un nouveau niveau de service ne met pas la base de données hors connexion. La base de données reste en ligne et disponible pour l'ensemble de l'opération de mise à niveau.


Pour vous aider lors de la mise à niveau, le service Base de données SQL recommande un niveau de service et de performances approprié (niveau tarifaire) pour chaque base de données. En analysant l’historique d’utilisation de chaque base de données, le service recommande un niveau qui convient le mieux pour l’exécution des charges de travail de votre base de données existante.

Le niveau tarifaire recommandé pour chaque base de données est fourni au cours du processus de modification d'un niveau de service de bases de données Web ou Business, ou lors de la mise à niveau vers SQL Database V12.

En fonction de votre environnement spécifique, le service peut recommander la mise à niveau de tout ou partie de vos bases de données dans un [pool de base de données élastiques](sql-database-elastic-pool.md).

Pour afficher les niveaux de service recommandés pour vos bases de données qui seront retirées, vous pouvez utiliser le [portail Azure en version préliminaire](https://portal.azure.com) ou PowerShell. Pour des instructions détaillées, consultez :

- [Mise à niveau vers SQL Database V12 (portail Azure en version préliminaire)](sql-database-v12-upgrade.md)
- [Mise à niveau vers la Base de données SQL V12 (PowerShell)](sql-database-upgrade-server.md)


Il est important de noter que votre base de données SQL n’est pas verrouillée sur un niveau de performances ou de service spécifique. De cette façon, si les exigences de votre base de données changent, vous pouvez facilement passer à un autre niveau de service ou de performances disponible. En fait, les bases de données SQL De base, Standard et Premium sont facturées à l’heure et peuvent être augmentées ou réduites 4 fois par période de 24 heures. Cela signifie que vous pouvez ajuster le niveau de service et le niveau de performances pour optimiser les performances, l’ensemble de fonctionnalités et le coût de votre base de données, selon les besoins de votre application et les différentes charges de travail. Cela signifie également que l’évaluation et la modification du niveau de service et du niveau de performances de votre base de données (augmentation ou réduction) est un processus continu qui doit faire partie de votre routine de réglage des performances et de maintenance.
 
Pour plus d'informations sur les différences entre Web/Business et les nouveaux niveaux de service, et pour obtenir d’autres détails sur la migration, téléchargez le [Guide de la Migration des base de données Web et Business](http://download.microsoft.com/download/3/C/9/3C9FF3D8-E702-4F5D-A38C-5EFA1DBA73E6/Azure_SQL_Database_Migration_Cookbook.pdf).



## Vue d'ensemble

<p>Les bases de données SQL Azure Web et Business s’exécutent dans un environnement mutualisé partagé sans aucune capacité de ressources réservée pour la base de données. L’activité des autres bases de données dans cet environnement de ressources partagées peut affecter vos performances. La disponibilité des ressources à un moment donné dépend essentiellement des autres charges de travail simultanées exécutées dans le système. Cela peut entraîner des performances d’application de base de données très fluctuantes et imprévisibles. D’après les commentaires des clients, ces performances imprévisibles sont difficiles à gérer et ils préfèrent des performances plus prévisibles.

À partir de ces commentaires, le service Azure SQL Database a introduit de nouveaux niveaux de service de base de données [(De base, Standard et Premium)](sql-database-service-tiers.md), qui offrent des performances prévisibles et plusieurs nouvelles fonctionnalités pour la continuité de l’activité et la sécurité. Ces nouveaux niveaux de service sont conçus pour fournir un niveau de ressources spécifié pour une charge de travail de base de données, indépendamment des autres charges de travail client exécutées dans cet environnement. Ainsi, le comportement des performances devient très prévisible.

La question est maintenant d’évaluer les nouveaux niveaux de service et de choisir celui qui convient le mieux à vos bases de données Web et Business actuelles, et de comprendre le processus de mise à jour actuel.

Finalement, le meilleur choix est la combinaison du niveau de service et de performances qui fournit l’équilibre optimal entre les fonctionnalités, les performances et les coûts, tout en répondant entièrement aux besoins de votre entreprise et aux exigences de votre application.

Ce document propose une méthodologie interactive pour la mise à jour d’une base de données Web ou Business vers l’un des nouveaux niveaux de service/niveaux de performances.


## Mise à niveau des bases de données Web et Business

La mise à niveau des bases de données Web ou Business vers un nouveau niveau de performances/de service ne met pas la base de données hors connexion. La base de données continuera de fonctionner pendant l’opération de mise à jour. Au moment précis de la transition vers le nouveau niveau de performances, une perte temporaire des connexions à la base de données peut parfois se produire pendant quelques secondes. Si une application gère les problèmes temporaires d’interruption de connexion, il suffit d’établir une protection contre la perte de connexion à la fin de la mise à jour.

La mise à jour d’une base de données Web ou Business vers un nouveau niveau de service implique les étapes suivantes :


1. Déterminer le niveau de service en fonction de la capacité des fonctionnalités
2. Déterminer un niveau de performances acceptable en fonction de l’utilisation historique des ressources
3. Pourquoi les performances existantes de ma base de données Web ou Business correspondent-elles aux niveaux Premium supérieurs ?
4. Réglage de votre charge de travail pour s’ajuster à un niveau inférieur
5. *Effectuer la mise à jour vers le nouveau niveau de service/niveau de performances*
6. Contrôle de la mise à jour du nouveau niveau de service/niveau de performances
7. Contrôle de la base de données après la mise à niveau



## 1\. Déterminer le niveau de service en fonction de la capacité des fonctionnalités

Les niveaux de service De base, Standard et Premium proposent des fonctions différentes. La première étape de la sélection d’un niveau approprié consiste à déterminer le niveau de service qui fournit le niveau minimal de fonctionnalités requis pour votre entreprise et application.

Par exemple, vous devez prendre en compte la durée de conservation des longues sauvegardes, si des fonctions de [géo-réplication Standard ou Active](sql-database-business-continuity.md) sont requises, la taille globale maximale de la base de données nécessaire, etc. Ces exigences déterminent votre choix de niveau de service minimal.

Le niveau « De base » est principalement utilisé pour les bases de données très petites et avec une faible activité. Par conséquent, s’il s’agit d’une mise à jour, il convient généralement de choisir le niveau « Standard » ou « Premium », en fonction du niveau minimal de fonctionnalités requis.

Les nouvelles fonctionnalités de niveau de service et des niveaux de performance sont résumées et comparées dans le tableau suivant :

[AZURE.INCLUDE [Tableau de niveaux de service de base de données SQL](../../includes/sql-database-service-tiers-table.md)]

**Ressources supplémentaires pour la comparaison des niveaux de service et des niveaux de performances :**

| Article | Description |
|:--|:--|
|[Niveaux de service et niveaux de performances de la base de données SQL Azure](sql-database-service-tiers.md)| Vue d’ensemble, métriques et capacités de chaque niveau de service (et comment contrôler l’utilisation de la base de données dans le portail de gestion et à l’aide des vues de gestion dynamique). |
|[Continuité de l’activité des bases de données SQL Azure](sql-database-business-continuity.md)|Détails relatifs aux fonctions de continuité des activités et de récupération d’urgence (restauration jusqu’à une date et heure, géo-restauration, géo-réplication) disponibles pour chaque niveau de service.|
|[Tarification des bases de données SQL](http://azure.microsoft.com/pricing/details/sql-database/)|Informations détaillées relatives à la tarification des différents niveaux de service et niveaux de performances.|

<br>

Après avoir sélectionné un niveau de service approprié qui répond aux exigences de votre base de données, l’étape suivante consiste à sélectionner un niveau de performances acceptable pour les charges de travail de votre base de données.



## 2\. Déterminer un niveau de performances acceptable en fonction de l’utilisation historique des ressources

Le service de base de données SQL Azure présente des informations dans le portail de gestion et dans les vues système, afin de vous suggérer le nouveau niveau de service et le niveau de performances comparables à votre base de données Web ou Business existante.

Dans la mesure où les bases de données Web et Business ne sont associées à aucune limite de ressources/DTU garantie, nous normalisons les valeurs de pourcentage en termes de quantité de ressources disponibles pour une base de données au niveau de performances S2. Le pourcentage DTU moyen utilisé par une base de données dans un intervalle donné peut être calculé comme la valeur de pourcentage la plus élevée entre l’utilisation du processeur, les E/S et le journal dans cet intervalle.


Utilisez le portail Azure en version préliminaire pour obtenir une vue d’ensemble du pourcentage d’utilisation DTU, puis explorez les détails à l’aide de vues système.

Vous pouvez également utiliser le portail Azure en version préliminaire pour afficher le niveau de service recommandé pour votre base de données Web ou Business lorsque vous mettez à niveau un serveur vers la base de données SQL Azure V12.

### Comment afficher le niveau de service recommandé dans le portail Azure en version préliminaire
Le portail Azure recommande le niveau de service approprié pour votre base de données Web ou Business lors du processus de mise à niveau d’un serveur vers une base de données SQL V12. La recommandation est basée sur une analyse historique de la consommation des ressources de la base de données.

**Nouveau portail de gestion**

1. Connectez-vous au [portail Azure en version préliminaire](https://portal.azure.com) et accédez à un serveur contenant une base de données Web ou Business.
2. Cliquez sur la partie **Dernière mise à jour** du panneau serveur.
3. Cliquez sur **Mettre à niveau ce serveur**.

Le panneau **Mettre à niveau ce serveur** affiche maintenant la liste des bases de données Web ou Business du serveur avec le niveau de service recommandé.



### Affichage de la consommation DTU dans le portail de gestion
Le portail de gestion permet de connaître la consommation DTU d’une base de données Web ou Business. Les informations DTU sont disponibles dans le portail Azure actuel.


**Portail de gestion**

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com) et accédez à une base de données Web ou Business.
2. Cliquez sur l’onglet **Surveiller**.
3. Cliquez sur **Ajouter des métriques**.
4. Sélectionnez **Pourcentage DTU** et cliquez sur la coche en bas pour confirmer.

Après avoir confirmé, vous verrez les données relatives au **pourcentage DTU** dans la table. N’oubliez pas que c’est un pourcentage par rapport aux DTU dans une base de données de niveau Standard(S2), ce qui correspond à 50 DTU.

Il convient également de noter que ces données sont la moyenne d’échantillons enregistrés toutes les 5 minutes. Il peut donc y avoir des pics d’activité entre les échantillons qui n’apparaissent pas dans ces métriques.

![Données de pourcentage DTU][2]

Notez que les données dans l’exemple ci-dessus montrent une utilisation moyenne d’environ 10 DTU (19,23 % de 50) et un pourcentage DTU maximal de ~28 DTU (55,83 % x 50). En supposant que ces données représentent ma charge de travail typique, il conviendrait probablement de sélectionner le niveau Standard(S1) pour ma mise à niveau initiale. Le niveau Standard(S0) fournit 10 DTU, ce qui correspond à mon utilisation moyenne, mais cela signifie que ma base de données s’exécuterait en moyenne à 100 %, ce qui n’est pas une bonne idée. Si le niveau S1 est probablement un bon choix pour mon utilisation moyenne, qu’en est-il des moments où j’atteins le niveau maximal ? Je sais peut-être que les pics viennent d’un processus de maintenance nocturne, n’ayant aucun effet sur l’utilisation des clients, des performances réduites pendant ce laps de temps peuvent être acceptables. Toutefois, il est possible que je ne sache pas quand le maximum est atteint. Ma consommation de pourcentage DTU peut alors demander une analyse plus poussée.

Pour explorer les détails de la consommation des ressources par la base de données, vous pouvez utiliser les vues système fournies.


### Vues système


Les données relatives à la consommation de ressources par les bases de données Web et Business sont accessibles via la vue [sys.resource\_stats](http://msdn.microsoft.com/library/azure/dn269979.aspx) dans la base de données master du serveur logique sur lequel se trouve votre base de données actuelle. Il affiche les données de consommation des ressources en pourcentage de la limite du niveau de performances. Cette vue affiche les données des 14 derniers jours, par intervalles de 5 minutes.

> [AZURE.NOTE]Vous pouvez maintenant utiliser la vue [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) des bases de données Web et Business pour obtenir une vue de granularité plus élevée (toutes les 15 secondes) des données de consommation des ressources. Comme sys.dm\_db\_resource\_stats conserve uniquement les données historiques pour une heure, vous pouvez interroger cette vue de gestion dynamique toutes les heures et stocker les données pour une analyse supplémentaire.

Exécutez la requête suivante sur la base de données master pour récupérer la consommation DTU moyenne :

 
                   
     SELECT start_time, end_time
	 ,(SELECT Max(v)
         FROM (VALUES (avg_cpu_percent)
                    , (avg_physical_data_read_percent)
                    , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.resource_stats
    WHERE database_name = '<your db name>'
    ORDER BY end_time DESC;

Les données retournées par [sys.resource\_stats](https://msdn.microsoft.com/library/dn269979.aspx) et [sys.dm\_db\_resource\_stats](https://msdn.microsoft.com/library/dn800981.aspx) pour les niveaux Web et Business indiquent les pourcentages en fonction du niveau de performance Standard S2. Par exemple, lors de l’exécution sur une base de données Web ou Business, si les valeurs retournent 70 %, ce pourcentage correspond aux 70 % de la limite de niveau S2. En outre, pour les bases de données Web et Business, les pourcentages peuvent refléter les nombres supérieurs à 100 %, également à partir de la limite de niveau S2.

Les informations sur la consommation DTU en termes de niveau de base de données S2 vous permettent de normaliser la consommation actuelle de vos bases de données Web et Business en termes de bases de données du nouveau service et de déterminer le niveau qui leur convient le mieux. Par exemple, si le pourcentage moyen de consommation DTU indique une valeur de 80 %, cela signifie que la base de données consomme des DTU à 80 % de la limite d’une base de données au niveau de performances S2. Si des valeurs sont supérieures à 100 % dans la vue **sys.resource\_stats**, vous avez besoin d’un niveau de performances supérieur à S2. Par exemple, supposons que vous observez une valeur de pourcentage DTU maximale de 300 %. Cela vous indique que vous utilisez trois fois plus de ressources que la quantité disponible dans un niveau S2. Pour déterminer une taille de départ raisonnable, comparez les DTU disponibles dans un niveau S2 (50 DTU) à celles des niveaux de taille supérieure (S3P1 = 100 DTU ou 200 % de S2, P2 = 200 DTU ou 400 % de S2). Avec une valeur de 300 % de S2, il est souhaitable de commencer par un niveau P2 et de recommencer les tests.

En fonction du pourcentage d’utilisation DTU et de l’édition la plus grande correspondant à votre charge de travail, vous pouvez déterminer le niveau de service et le niveau de performances les mieux adaptés à la charge de travail de votre base de données (comme indiqué par le pourcentage DTU et la puissance DTU relative de divers [niveaux de performances)](sql-database-service-tiers.md). Le tableau suivant indique la correspondance entre le pourcentage de consommation des ressources Web/Business et les niveaux de performances des nouveaux niveaux de service :

![Consommation des ressources][4]

> **Remarque :** les nombres DTU relatif des différents niveaux de performances sont basés sur la charge de travail de [référence d’Azure SQL Database Benchmark](http://msdn.microsoft.com/library/azure/dn741327.aspx). Dans la mesure où la charge de travail de votre base de données est susceptible de ne pas être égale à celle de référence, vous devez utiliser les calculs ci-dessus pour trouver le nouveau niveau de service adapté à votre base de données Web/Business. Une fois que vous avez déplacé la base de données vers le nouveau niveau, utilisez la procédure décrite dans la section précédente pour valider/ajuster le niveau de service par rapport aux besoins de votre charge de travail.
> 
> bien que le nouveau niveau d’édition/niveau de performances suggéré tienne compte de l’activité de votre base de données pendant les 14 derniers jours, ces données sont basées sur des échantillons de données de consommation de ressources mesurés toutes les 5 minutes. Par conséquent, les pics d’activité à court terme inférieurs à 5 minutes peuvent ne pas apparaître. Ce guide sert donc de point de départ pour mettre à jour votre base de données. Une fois la base de données mise à jour vers le niveau suggéré, vous devez effectuer des contrôles, tests et validations supplémentaires pour déterminer si la base de données doit être élevée ou abaissée à un autre niveau de service/niveau de performances.

Voici une requête sur la base de données master qui effectue le calcul pour votre base de données de niveau Web/Business et suggère l’édition la plus susceptible de convenir à votre charge de travail pour chacun de ces intervalles de 5 minutes d’échantillonnage des données.

> **Remarque :** cette requête est utile uniquement pour les bases de données Web/Business et **ne fournira pas** de résultats corrects pour les bases de données des nouveaux services.

    WITH DTU_mapping AS
    ( SELECT *
        FROM ( VALUES (1, 10,'Basic'), (2, 20,'S0'), (3, 40,'S1'), (4, 100, 'S2')
                    , (5, 200, 'S3/P1'), (6, 1600,'Premium')
           ) AS t(id, percent_of_S2, target_edition)
    ), rc as
    ( SELECT start_time, end_time
           , (SELECT Max(v)
                FROM (VALUES (avg_cpu_percent)
                       		, (avg_physical_data_read_percent)
                       		, (avg_log_write_percent)
                   ) AS value(v)) as [avg_DTU_percent]
        FROM sys.resource_stats	
       WHERE database_name = 'WebDB'
    )
    SELECT rc.*
         , (SELECT TOP(1) t.target_edition
              FROM DTU_mapping AS t
             WHERE t.percent_of_S2 > CAST(1.2*rc.avg_DTU_percent as int)
             ORDER BY t.percent_of_S2) as target_edition
    FROM rc;

**Exemple de résultat :**

![Exemple de résultat](media/sql-database-upgrade-new-service-tiers/sample_result.png)

Sous forme de graphique : vous pouvez consulter la tendance du pourcentage moyen de consommation DTU au fil du temps. Voici un exemple de graphique pour une base de données située au niveau S2 la plupart du temps, avec des pics d’activité atteignant un niveau de base de données P1. La consommation DTU oscille entre les limites du niveau « De Base » et celles du niveau « P1 ». Pour que cette base de données s’adapte complètement au nouveau niveau, il vous faut une base de données de niveau de service Premium avec un niveau de performances « P1 ». Toutefois, une base de données de niveau S2 peut fonctionner si ces pics occasionnels vers le niveau P1 sont rares.

![Utilisation d’DTU](media/sql-database-upgrade-new-service-tiers/DTU_usage.png)

**Impact de la mémoire sur les performances :** même si la mémoire est l’une des dimensions de ressources qui contribue à la notation DTU, la base de données SQL est conçue pour utiliser toute la mémoire disponible pour les opérations de base de données. Pour cette mémoire raison, la consommation n’est pas incluse dans la consommation DTU moyenne de la requête ci-dessus. En revanche, si vous rétrogradez vers un niveau de performance inférieur, la mémoire disponible pour la base de données est réduite. Il peut en résulter une consommation d’E/S supérieure affectant les DTU consommées. Par conséquent, lors de la baisse vers un niveau de performance inférieur, assurez-vous que vous disposez d’une marge suffisante dans le pourcentage d’E/S. Utilisez la vue de gestion dynamique [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) mentionnée ci-dessus pour surveiller ce point.



## 3\. Pourquoi les performances existantes de ma base de données Web ou Business correspondent-elles aux niveaux Premium supérieurs ?

Les bases de données Web et Business n’ont aucune quantité spécifique de capacité de ressources réservée pour une base de données. En outre, il n’existe aucun mécanisme permettant aux clients d’augmenter ou de réduire les performances d’une base de données Web ou Business. De ce fait, les performances des bases de données Web et Business vont d’un niveau d’extrême lenteur à Premium. L’éventail variable des performances est *injustement* dépendant du niveau global de consommation de ressources à tout instant par d’autres bases de données au sein de l’environnement mutualisé partageant les ressources.

Bien évidemment, l’objectif des services de base de données SQL Azure est que toutes les bases de données Web et Business soient aussi proches du 100 % optimal que possible. Ils ont également effectué un travail remarquable pour que les niveaux de performances moyens des bases de données Web et Business continuent à s’approcher des niveaux Premium. Voilà pourquoi les performances de votre base de données existante peuvent correspondre aux niveaux Premium actuels. Malheureusement, cela a entraîné des attentes quelque peu irréalistes lors de la comparaison des bases de données Web et Business aux nouveaux niveaux de performances/de service, pour évaluer vers quel niveau de service effectuer la mise à niveau.

Afin de mieux comprendre les différences entre les niveaux de service Web/Business et De base, Standard et Premium, consultez l’image ci-dessous. Imaginons 9 bases de données SQL en cours d’exécution dans le modèle Web/Business à ressources partagées et 9 bases de données SQL en cours d’exécution dans le modèle de niveau de service De base, Standard et Premium. Dans le modèle Web/Business, vous voyez clairement les effets que les « voisins bruyants » ont sur les autres bases de données qui font partie de ce pool de ressources partagées. Lorsqu’une base de données exécute une charge de travail gourmande en ressources, toutes les autres bases de données du pool sont touchées et les ressources disponibles commencent à diminuer. Dans les niveaux de service De base, Standard et Premium, une quantité spécifique de ressources ***est*** allouée pour chaque base de données. De ce fait, les autres bases de données dans l’environnement partagé sont isolées du problème du « voisin bruyant » (noisy neighbor) et liées par le niveau de performances sélectionné pour la base de données.

![Performances prévisibles des nouveaux niveaux de service][3]

Si votre pourcentage DTU global est très élevé, il convient de vous pencher sur les métriques détaillées qu’incluent les DTU, en particulier l’utilisation des E/S du journal et de la mémoire de la base de données. Vous pourrez peut-être y trouver des domaines dans lesquels optimiser et réduire votre consommation d’DTU.


## 4\. Réglage de la charge de travail de votre base de données pour s’ajuster à un niveau inférieur
Si l’analyse de l’utilisation historique des ressources de votre base de données indique que vous devez mettre à niveau vers un niveau de performances plus coûteux que ce que vous envisagez, vous pouvez rechercher les domaines dans lesquels un réglage des performances plus fin peut être utile.

Compte tenu de votre connaissance des détails de votre application, si l’utilisation des ressources semble très élevée par rapport à ce que vous attendez de la charge de travail standard, vous disposez peut-être de possibilités de réglage des performances bénéfiques à votre utilisation.

En réalité, toutes les bases de données peuvent tirer profit d’un nouveau réglage des performances.

En plus des réglages de maintenance standard comme l’analyse des index, les plans d’exécution, etc., vous devez optimiser vos routines d’accès aux données en fonction de la base de données SQL Azure ciblée.

| Article | Description |
|:--|:--|
| [Guide des performances de base de données SQL Azure](http://msdn.microsoft.com/library/dn369873.aspx) | La section « Réglage de votre Application » fournit des recommandations détaillées et des techniques relatives au réglage des performances d’une base de données SQL Azure.|
|[Outils de surveillance et de réglage des performances](https://msdn.microsoft.com/library/ms179428.aspx)| Liens et descriptions des outils disponibles pour surveiller les événements de SQL Server et paramétrer la conception physique de la base de données.|
|[Surveillance et réglage des performances](https://msdn.microsoft.com/library/ms189081.aspx)|Section relative au réglage des performances de SQL Server 2014 sur MSDN.|
| [Résolution des problèmes de performances dans SQL Server 2008](https://msdn.microsoft.com/library/dd672789.aspx) | Un ancien livre blanc, mais toujours d’actualité, qui propose des conseils pour résoudre les problèmes de performances courants, y compris des informations très utiles sur la résolution des goulots d’étranglement au niveau du processeur et de la mémoire.|
|[Résolution de problèmes et optimisation des requêtes de base de données SQL Azure](http://social.technet.microsoft.com/wiki/contents/articles/1104.troubleshoot-and-optimize-queries-with-azure-sql-database.aspx)|Rubrique ancienne mais toujours pertinente sur les vues de gestion dynamique et leur utilisation pour résoudre des problèmes.|



## 5\. Effectuer la mise à jour vers le nouveau niveau de service/niveau de performances
Après avoir déterminé le niveau de service et le niveau de performances appropriés pour votre base de données Web ou Business, vous avez le choix entre plusieurs méthodes pour mettre à jour la base de données vers le nouveau service :

| Outil de gestion | Modification du niveau de performances et du niveau de service d’une base de données|
| :---| :---|
| [Portail de gestion Azure](https://manage.windowsazure.com) | Cliquez sur l’onglet **Mise à l’échelle** de la page du tableau de bord de votre base de données. |
| [Azure PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) | Utilisez l’applet de commande [Set-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546732.aspx). |
| [API REST de gestion de service](http://msdn.microsoft.com/library/azure/dn505719.aspx) | Utilisez la commande [Mettre à jour la base de données](http://msdn.microsoft.com/library/dn505718.aspx).|
| [Transact-SQL](http://msdn.microsoft.com/library/bb510741.aspx) | Utilisez l’instruction [ALTER DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/ms174269.aspx). |

Pour plus d’informations, consultez la page [Modification des niveaux de service et des niveaux de performance de base de données](sql-database-scale-up.md)


## 6\. Contrôle de la mise à jour du nouveau niveau de service/niveau de performances
La base de données SQL Azure fournit des informations sur la progression des opérations de gestion (telles que CREATE, ALTER, DROP) effectuées sur une base de données dans la vue de gestion dynamique sys.dm\_operation\_status de la base de données master du serveur logique où se trouve votre base de données active. Consultez la documentation relative à sys.dm \_operation \_status (http://msdn.microsoft.com/library/azure/dn270022.aspx). Utilisez la vue de gestion dynamique du statut de l’opération pour déterminer l’avancement de la mise à niveau d’une base de données. Cet exemple de requête affiche toutes les opérations de gestion effectuées sur une base de données :

    SELECT o.operation, o.state_desc, o.percent_complete
    , o.error_code, o.error_desc, o.error_severity, o.error_state
    , o.start_time, o.last_modify_time
    FROM sys.dm_operation_status AS o
    WHERE o.resource_type_desc = 'DATABASE'
    and o.major_resource_id = '<database_name>'
    ORDER BY o.last_modify_time DESC;

Si vous avez utilisé le portail de gestion pour la mise à jour, une notification est également disponible à partir du portail pour l’opération.

## 7\. Contrôle de la base de données après la mise à niveau
Après la mise à jour de la base de données Web/Business vers le nouveau niveau de service, nous vous recommandons de surveiller activement la base de données pour vous assurer que les applications s’exécutent au niveau de performances souhaité et optimiser l’utilisation en fonction des besoins. Les étapes supplémentaires suivantes sont recommandées pour la surveillance de la base de données.


**Données sur la consommation de ressources :** pour les bases de données de base, Standard et Premium, des données plus granulaires sur la consommation des ressources sont disponibles via une nouvelle vue de gestion dynamique appelée [sys.dm\_ db\_ resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) dans la base de données utilisateur. Cette vue offre presque en temps réel les informations de consommation des ressources avec un niveau de granularité de 15 secondes pour l’heure précédente de l’opération. La consommation de pourcentage DTU pour un intervalle est calculée comme la consommation de pourcentage maximal des dimensions UC, E/S et journal. Voici une requête pour calculer la moyenne de pourcentage DTU sur la dernière heure :

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

 
La [documentation](http://msdn.microsoft.com/library/dn800981.aspx) additionnelle contient les détails de l’utilisation de cette vue de gestion dynamique. [Conseils pour les performances de base de données SQL azure](http://msdn.microsoft.com/library/azure/dn369873.aspx) explique comment surveiller et optimiser votre application.


- **Alertes :** configurer les « alertes » dans le portail de gestion Azure pour vous avertir quand la consommation DTU d’une base de données mise à niveau approche d’un niveau élevé. Les alertes de la base de données peuvent être configurées dans le portail de gestion Azure à des fins de diverses mesures de performances comme DTU, UC, E/S et journal. 

	Par exemple, vous pouvez configurer une alerte par courrier électronique sur « Pourcentage DTU » si la valeur moyenne du pourcentage DTU est supérieure à 75 % pendant les 5 dernières minutes. Reportez-vous à [Réception de notifications d'alerte](insights-receive-alert-notifications.md) pour en savoir plus sur la configuration des notifications d'alerte.


- **Mise à niveau/rétrogradation planifiée au niveau des performances :** si votre application comporte des scénarios spécifiques qui ne nécessitent de meilleures performances qu’à certains moments de la journée/semaine, vous pouvez utiliser [Azure Automation](https://azure.microsoft.com/documentation/services/automation/) pour augmenter ou réduire la taille de votre base de données avec un niveau de performance supérieur/inférieur comme opération planifiée.

	Par exemple, mettez à jour la base de données vers un niveau de performances supérieur pour la durée d’une tâche de maintenance/un traitement par lots hebdomadaire et réduisez-la à la fin de la tâche. Ce type de planification est également utile pour les opérations gourmandes en ressources volumineuses, comme la reconstruction d’index, le chargement de données, etc. Notez que le modèle de facturation de la base de données SQL Azure est basé sur l’utilisation horaire d’un niveau de performance/niveau de service. Cette flexibilité vous permet de planifier des mises à jour à moindre coût.



## Résumé
Le service Azure SQL Database fournit des données et des outils de télémétrie permettant d’évaluer les charges de travail de vos bases de données Web/Business et de déterminer le niveau de service pour la mise à jour. Le processus de mise à jour est assez simple et peut être effectué sans déconnecter la base de données et sans perte de données. Les bases de données mises à jour bénéficient de performances prévisibles et de fonctionnalités supplémentaires fournies par les nouveaux niveaux de service.




<!--Image references-->
[2]: ./media/sql-database-upgrade-new-service-tiers/portal-dtus.JPG
[3]: ./media/sql-database-upgrade-new-service-tiers/web-business-noisy-neighbor.png
[4]: ./media/sql-database-upgrade-new-service-tiers/resource_consumption.png

 

<!---HONumber=Oct15_HO2-->