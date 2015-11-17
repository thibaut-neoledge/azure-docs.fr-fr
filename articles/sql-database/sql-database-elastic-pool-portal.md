<properties
	pageTitle="Créer un pool élastique de bases de données SQL Azure avec le portail Azure en version préliminaire | Microsoft Azure"
	description="Créez un pool élastique de base de données pour partager des ressources sur plusieurs bases de données de Azure SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="11/06/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Créer un pool élastique de bases de données avec le portail Azure en version préliminaire.

> [AZURE.SELECTOR]
- [Azure preview portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Cet article vous montre comment créer un [pool élastique de bases de données](sql-database-elastic-pool.md) en utilisant le portail Azure en version préliminaire.

> [AZURE.NOTE]Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs de base de données SQL V12. Si vous disposez d’un serveur de base de données SQL V11, vous pouvez [utiliser PowerShell pour effectuer une mise à niveau vers V12 et créer un pool](sql-database-upgrade-server.md) en une seule étape.


Avant de commencer, vous avez besoin d’une base de données sur un serveur SQL Database V12. Dans le cas contraire, consultez [Création de votre première base de données SQL Azure](sql-database-get-started.md) pour en créer une en moins de cinq minutes. Ou si vous disposez déjà d’un serveur de base de données SQL V11, vous pouvez [assurer la mise à niveau vers V12 dans le portail](sql-database-v12-upgrade.md), puis revenir et suivre les instructions ci-après pour créer un pool.


## Étape 1 : ajouter un pool à un serveur

Créez un pool élastique de bases de données en ajoutant un nouveau pool à un serveur. Vous pouvez ajouter plusieurs pools à un serveur, mais un seul (1) serveur peut être associé à chaque pool. En outre, tout ou partie des bases de données figurant sur un serveur peuvent être ajoutées à un pool.


Dans le [portail Azure en version préliminaire](https://ms.portal.azure.com/), cliquez sur **serveurs SQL**, cliquez sur le serveur qui héberge les bases de données que vous souhaitez ajouter au pool, puis cliquez sur **Ajouter un pool**.

![Ajouter un pool à un serveur](./media/sql-database-elastic-pool-portal/elastic-pool-add-pool.png)

- ou -

Si vous voyez un message indiquant un pool recommandé pour un serveur, cliquez dessus pour l'examiner et créer facilement un pool qui est optimisé pour les bases de données de votre serveur. Pour plus d'informations, consultez [Pools de base de données élastiques recommandés](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).
   
  
![Créer un pool élastique][1]


Le volet **Pool de bases de données élastiques** fournit des options pour choisir le niveau de tarification, ajouter des bases de données et configurer les caractéristiques de performances du pool.

> [AZURE.NOTE]Lorsque vous sélectionnez la commande **Ajouter un pool** pour la première fois, vous devez accepter les termes du contrat de la version préliminaire en sélectionnant **TERMES DE LA VERSION PRÉLIMINAIRE** et en remplissant le panneau **Termes de la version préliminaire**. Il vous suffit de faire cela une fois pour chaque abonnement.

   ![Configurer un pool élastique][2]

## Étape 2 : sélectionner un niveau de tarification

Le niveau de tarification du pool détermine les fonctionnalités disponibles pour les bases de données élastiques dans le pool, ainsi que le nombre maximal d'eDTU (eDTU MAX) et le stockage (Go) disponibles pour chaque base de données. Pour plus d'informations, consultez [Niveaux de service](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE]Actuellement, dans la version préliminaire, vous ne pouvez pas modifier le niveau de tarification d'un pool élastique de bases de données après sa création. Pour modifier le niveau de tarification d’un pool élastique existant, créez un nouveau pool élastique dans le niveau de tarification souhaité et migrez les bases de données élastiques vers ce nouveau pool.

   ![Niveau de tarification][9]


### Recommandations sur les niveaux de tarification

Le service de base de données SQL évalue l'historique d'utilisation et recommande un ou plusieurs pools élastiques de bases de données lorsqu'il est plus économique que l'utilisation de bases de données uniques.

Les niveaux de tarification indiqués par une étoile (![étoile][10]) sont recommandés en fonction des charges de travail de vos bases de données.

Lorsque plusieurs niveaux de tarification sont recommandés, cela indique que plusieurs pools élastiques de bases de données doivent être créés. Chaque recommandation est configurée avec un sous-ensemble unique de bases de données du serveur qui correspond le mieux au pool.

En plus de suggérer simplement un niveau de tarification de pool élastique de bases de données, chaque recommandation de pool contient les éléments suivants :

- Le niveau de tarification du pool (De base, Standard ou Premium).
- Quantité appropriée d’eDTU du pool.
- Les paramètres eDTU min./max. de base de données élastiques.  
- La liste des bases de données recommandées.

Le service prend en compte les 30 derniers jours de télémétrie lors de la recommandation de pools élastiques de bases de données. Pour qu’une base de données soit considérée comme candidate à un pool élastique de données, elle doit exister depuis au moins 7 jours. Les bases de données qui se trouvent déjà dans un pool élastique de bases de données ne sont pas considérées comme candidates pour les recommandations de pool élastique de bases de données.

Le service évalue les besoins en ressources et la rentabilité du déplacement des bases de données uniques dans chaque niveau de service vers des pools élastiques de bases de données du même niveau. Par exemple, toutes les bases de données Standard d’un serveur sont évaluées pour leur compatibilité avec un pool élastique Standard. Cela signifie que le service n'effectue aucune recommandation multiniveau telle que le déplacement d'une base de données Standard dans un pool Premium.

>[AZURE.NOTE]Les bases de données Web et Business sont mappées vers l'un des nouveaux niveaux De base, Standard ou Premium en fonction de leur historique d'utilisation et de la taille de leurs bases de données. Le mappage vers de nouveaux niveaux recommande des bases de données Web et Business dans le pool qui leur est approprié.


## Étape 3 : ajouter des bases de données au pool

Vous pouvez sélectionner à tout moment les bases de données spécifiques à inclure dans le pool. Lorsque vous créez un nouveau pool, Azure recommande les bases de données adaptées pour un pool en les marquant pour l'inclusion. Vous pouvez ajouter toutes les bases de données disponibles sur le serveur, ou vous pouvez sélectionner des bases de données dans la liste initiale ou en effacer selon vos besoins.

   ![Ajouter des bases de données][5]

Vous devez respecter les conditions suivantes lorsque vous sélectionnez une base de données à ajouter à un pool :

- Le pool doit disposer d'espace pour la base de données. Il ne peut pas déjà contenir le nombre maximal de bases de données. Plus spécifiquement, le pool doit avoir suffisamment d'eDTU disponibles pour couvrir le nombre d'eDTU garanti par base de données (par exemple, si le nombre d'eDTU garanti est 400 pour le groupe et 10 pour chaque base de données, le nombre maximal de bases de données autorisé dans le pool est 40 (400 eDTU/10 eDTU garanties par base de données = 40 bases de données max).
- Les fonctionnalités actuelles utilisées par la base de données doivent être disponibles dans le pool.


## Étape 4 : ajuster les caractéristiques de performances

Vous configurez les performances du pool en définissant les paramètres de performance pour le pool et les bases de données élastiques dans le pool. N'oubliez pas que les **paramètres de base de données élastique** s'appliquent à toutes les bases de données dans le pool.

   ![Configurer un pool élastique][3]

Il existe trois paramètres configurables qui définissent les performances pour le regroupement : le nombre d'eDTU garanti pour le pool, eDTU MIN et eDTU MAX pour les bases de données élastiques dans le pool. La table suivante décrit chaque paramètre et fournit des conseils pour leur définition. Pour découvrir les paramètres spécifiques des valeurs disponibles, consultez la [Référence du pool de base de données élastique](sql-database-elastic-pool-reference.md).

| Paramètre de performance | Description |
| :--- | :--- |
| **POOL eDTU** : nombre d'eDTU garanti pour le pool | Il s'agit du nombre d'eDTU garanti disponibles et partagées par toutes les bases de données dans le pool. <br> Le nombre d'eDTU garanti spécifique pour un groupe doit être configuré en tenant compte de l'utilisation historique des eDTU du groupe. Cette taille peut également être définie par le nombre d'eDTU garanti souhaité par base de données et l'utilisation simultanée des bases de données actives. Le nombre d'eDTU garanti pour le pool dépend également de la quantité de stockage disponible pour le pool. Pour chaque eDTU que vous allouez au pool, vous obtenez une quantité de stockage de base de données fixe. <br> **Quelle valeur définir pour le nombre d'eDTU garanti pour le pool ?** <br>Au minimum, vous devez définir le nombre d’eDTU garanti pour le pool sur ([nombre de bases de données] x [utilisation moyenne des eDTU par base de données]) |
| **eDTU MIN** : nombre d'eDTU garanti pour chaque base de données | Le nombre d'eDTU garanti (eDTU guarantee) par base de données est le nombre d'eDTU garanti pour une base de données unique. Dans les pools Standard, vous pouvez, par exemple, définir ce nombre garanti sur 0, 10, 20, 50 ou 100 eDTU, ou vous pouvez choisir de ne pas fournir une garantie aux bases de données dans le groupe (eDTU MIN=0). <br> **Quelle valeur définir pour le nombre d'eDTU garanti par base de données ?** <br> En règle générale, le nombre d'eDTU garanti par base de données (eDTU MIN) est défini sur n'importe quelle valeur comprise entre 0 et l'([utilisation moyenne par base de données]). Le nombre d'eDTU garanti par base de données est un paramètre global qui définit le nombre d'eDTU garanti pour toutes les bases de données dans le pool. |
| **eDTU MAX** : nombre d'eDTU maximal par base de données | L’eDTU MAX par base de données est le nombre maximum d'eDTU qu'une base de données unique peut utiliser. Définissez un nombre d'eDTU maximal par base de données suffisamment élevé pour gérer les rafales ou les pics que vos bases de données peuvent rencontrer. Vous pouvez définir ce seuil jusqu'à la limite du système, qui dépend du niveau de tarification du pool (1 000 eDTU en mode Premium) Le niveau spécifique de ce seuil doit tenir compte de modèles de pic d'utilisation des bases de données au sein du groupe. Une certaine allocation excessive du groupe est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d'utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d'utilisation.<br> **Quelle valeur définir pour le nombre d'eDTU maximal par base de données ?** <br> Définissez eDTU MAX (nombre maximal d'eDTU par base de données) sur le ([pic d'utilisation de la base de données]). Par exemple, supposons que le pic d'utilisation par base de données est 50 UDBD et que 20 % des 100 bases de données du groupe connaissent simultanément un pic d'utilisation. Si le nombre d'eDTU maximal par base de données est défini sur 50 eDTU, vous pouvez envisager une allocation 5 fois plus élevée du pool et définir le nombre d'eDTU garanti pour le groupe (POOL eDTU) sur 1 000 eDTU. Il est également à noter que le nombre d'eDTU maximal n'est pas une garantie de ressource pour une base de données. Il s'agit d'un plafond du nombre d'eDTU qui peut être atteint s'il est défini. |

## Pools de base de données élastiques recommandés

Lorsque vous accédez à un serveur SQL Database V12, il est possible qu’un message s’affiche, indiquant que des pools de base de données élastiques recommandés sont disponibles pour le serveur.

Comme pour les recommandations en matière de niveau tarifaire de pool de base de données élastique, les pools recommandées sont préconfigurés avec les éléments suivants déjà définis :

- Le niveau de tarification du pool.
- Quantité appropriée d’eDTU du pool.
- Les paramètres eDTU min./max. de base de données.  
- La liste des bases de données recommandées.

### Création d’un pool recommandé

1. Cliquez sur le message pour afficher la liste des pools recommandés :
 
     ![pools recommandés][12]
  
1. Cliquez sur un pool pour consulter les paramètres des recommandation détaillés.
2. Modifiez simplement le nom du pool et cliquez sur **OK** pour créer le pool. Vous ne pouvez pas modifier les pools recommandés après leur création.

    ![pool recommandé][11]


## Ajout et suppression de bases de données dans le pool

Une fois le pool créé, vous pouvez ajouter des bases de données au pool ou en supprimer en sélectionnant ou en supprimant des bases de données sur la page **Ajouter des bases de données**.

Après la création d'un pool, vous pouvez également utiliser Transact-SQL pour créer de nouvelles bases de données élastiques dans le pool et déplacer des bases de données dans ou hors d'un pool. Pour plus d'informations, consultez [Pool de bases de données élastiques de référence - Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*


## Surveiller et gérer un pool élastique de bases de données

Après avoir créé un pool élastique de bases de données, vous pouvez surveiller et gérer le pool dans le portail en accédant à la liste de pools existants et en sélectionnant le pool souhaité.

Après avoir créé un pool, vous pouvez :

- Sélectionner **Configurer le pool** pour modifier les paramètres d’eDTU par pool et par base de données.
- Sélectionner **Créer une tâche** et gérer les bases de données dans le pool en créant des tâches élastiques. Les tâches élastiques vous permettent d'exécuter des scripts Transact-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).
- Sélectionner **Gérer les tâches** pour gérer les travaux élastiques existants.



![Surveiller un pool élastique][8]




![Surveiller un pool élastique][4]

Lorsque vous sélectionnez un pool existant, vous pouvez voir l'utilisation des ressources du pool. Cliquez sur le graphique **Utilisation des ressources** pour ouvrir le panneau **Mesure** dans lequel vous pouvez personnaliser le graphique et les alertes de configuration.


![utilisation des ressources][6]

Cliquez sur **Modifier le graphique** pour ajouter des paramètres afin de consulter facilement les données de télémétrie pour le pool.


![modifier le graphique][7]




## Étapes suivantes
Après avoir créé un pool élastique de bases de données, vous pouvez gérer des bases de données du pool en créant des tâches élastiques. Les tâches élastiques facilitent l'exécution de scripts Transact-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).



## Ressources supplémentaires

- [Pools élastiques de bases de données SQL](sql-database-elastic-pool.md)
- [Créer un pool de bases de données SQL avec PowerShell](sql-database-elastic-pool-powershell.md)
- [Créer et gérer la base de données SQL avec C#](sql-database-client-library.md)
- [Référence de base de données élastique](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-portal/new-elastic-pool.png
[2]: ./media/sql-database-elastic-pool-portal/configure-elastic-pool.png
[3]: ./media/sql-database-elastic-pool-portal/configure-performance.png
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[5]: ./media/sql-database-elastic-pool-portal/add-databases.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[8]: ./media/sql-database-elastic-pool-portal/configure-pool.png
[9]: ./media/sql-database-elastic-pool-portal/pricing-tier.png
[10]: ./media/sql-database-elastic-pool-portal/star.png
[11]: ./media/sql-database-elastic-pool-portal/recommended-pool.png
[12]: ./media/sql-database-elastic-pool-portal/pools-message.png

<!---HONumber=Nov15_HO3-->