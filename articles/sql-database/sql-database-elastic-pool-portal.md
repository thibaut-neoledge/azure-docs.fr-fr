<properties
	pageTitle="Créer des pools de base de données élastique évolutive| Microsoft Azure"
	description="Comment ajouter un pool de base de données élastique évolutive à votre configuration de base de données SQL pour faciliter l’administration et le partage des ressources entre plusieurs bases de données."
	keywords="base de données évolutive, configuration de la base de données"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Créer un pool de base de données élastique évolutif pour les bases de données SQL dans le portail Azure

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Cet article vous montre comment créer un [pool de base de données élastique](sql-database-elastic-pool.md) évolutif en utilisant le portail Azure. La configuration de base de données SQL avec des pools de base de données élastique simplifie l’administration et le partage des ressources entre plusieurs bases de données.

> [AZURE.NOTE] Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs SQL Database V12. Si vous disposez d’un serveur SQL Database V11, vous pouvez [utiliser PowerShell pour effectuer une mise à niveau vers V12 et créer un pool](sql-database-upgrade-server-powershell.md) en une seule étape.


Avant de commencer, vous avez besoin d’une base de données sur un serveur SQL Database V12. Dans le cas contraire, consultez [Création de votre première base de données SQL Azure](sql-database-get-started.md) pour en créer une en moins de cinq minutes. Si vous disposez déjà d’un serveur SQL Database V11, vous pouvez [assurer la mise à niveau vers V12 dans le portail](sql-database-v12-upgrade.md), puis revenir et suivre les instructions ci-après pour créer un pool.


## Étape 1 : Création d’un nouveau pool

Créez un pool élastique de bases de données en ajoutant un nouveau pool à un serveur. Vous pouvez ajouter plusieurs pools à un serveur, mais il est impossible d’ajouter des bases de données de différents serveurs dans le même pool.


1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Serveurs SQL**.
2. Sélectionnez le serveur qui contient les bases de données que vous souhaitez ajouter au pool.
3. Cliquez sur **Nouveau pool** ou, si vous voyez un message indiquant un pool recommandé, cliquez dessus pour l’examiner et créer facilement un pool optimisé pour les bases de données de votre serveur. Vous trouverez d’autres informations sur les recommandations ci-dessous.


    ![Ajouter un pool à un serveur](./media/sql-database-elastic-pool-portal/new-pool.png)


4. Sur le panneau **Pool de bases de données élastiques**, vous pouvez laisser le nom par défaut ou taper un nom pour le nouveau pool.

    ![Configurer un pool élastique](./media/sql-database-elastic-pool-portal/configure-elastic-pool.png)


### Pools de base de données élastiques recommandés

Lorsque vous accédez à un serveur SQL Database, il se peut qu’un message indiquant que des pools de base de données élastiques recommandés sont disponibles pour le serveur (V12 uniquement) s’affiche.

### Pourquoi ai-je reçu une recommandation ?

Le service SQL Database évalue l’historique d’utilisation et recommande un ou plusieurs pools élastiques de bases de données lorsqu’il est plus économique que l’utilisation de bases de données uniques.

![pool recommandé](./media/sql-database-elastic-pool-portal/recommended-pool.png)

Chaque recommandation est configurée avec un sous-ensemble unique de bases de données du serveur qui correspond le mieux au pool.

Chaque recommandation de pool contient ce qui suit :

- Le niveau de tarification du pool (De base, Standard ou Premium).
- Quantité appropriée d’eDTU du pool.
- Les paramètres eDTU min./max. de base de données élastiques.
- La liste des bases de données recommandées.

Le service prend en compte les 30 derniers jours de télémétrie lors de la recommandation de pools élastiques de bases de données. Pour qu’une base de données soit considérée comme candidate à un pool élastique de données, elle doit exister depuis au moins 7 jours. Les bases de données qui se trouvent déjà dans un pool élastique de bases de données ne sont pas considérées comme candidates pour les recommandations de pool élastique de bases de données.

Le service évalue les besoins en ressources et la rentabilité du déplacement des bases de données uniques dans chaque niveau de service vers des pools élastiques de bases de données du même niveau. Par exemple, toutes les bases de données Standard d’un serveur sont évaluées pour leur compatibilité avec un pool élastique Standard. Cela signifie que le service n'effectue aucune recommandation multiniveau telle que le déplacement d'une base de données Standard dans un pool Premium.


### Création d’un pool recommandé

1. Cliquez sur le message pour afficher la liste des pools recommandés.
1. Cliquez sur un pool pour consulter le détail des recommandations.
2. Passez en revue tous les paramètres de la recommandation et acceptez-la en l’état ou modifiez la recommandation pour l’adapter à vos besoins spécifiques.


## Étape 2 : sélectionner un niveau de tarification

Le niveau de tarification du pool détermine les fonctionnalités disponibles pour les bases de données élastiques dans le pool, ainsi que le nombre maximal d'eDTU (eDTU MAX) et le stockage (Go) disponibles pour chaque base de données. Pour plus d’informations, voir [Niveaux de service](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE] Actuellement, dans la version préliminaire, vous ne pouvez pas modifier le niveau de tarification d'un pool élastique de bases de données après sa création. Pour modifier le niveau de tarification d’un pool élastique existant, créez un nouveau pool élastique dans le niveau de tarification souhaité et migrez les bases de données élastiques vers ce nouveau pool.

   ![Sélectionnez un niveau tarifaire](./media/sql-database-elastic-pool-portal/pricing-tier.png)

### Configurer le pool

Après avoir défini le niveau de tarification, cliquez sur Configurer le pool à l’endroit où vous ajoutez des bases de données, définissez la quantité d’eDTU et de stockage (Go de pool), et définissez les nombres minimum et maximum d’eDTU des bases de données élastiques dans le pool.

## Étape 3 : ajouter des bases de données au pool

Vous pouvez ajouter des bases de données à un pool ou en supprimer une à tout moment.

1. Lors de la création du pool, cliquez sur **Ajouter des bases de données** sur le panneau **Configurer un pool**.
2. Sélectionnez les bases de données que vous souhaitez ajouter au pool :

    ![Ajouter des bases de données](./media/sql-database-elastic-pool-portal/add-databases.png)

    Vous devez respecter les conditions suivantes lorsque vous sélectionnez une base de données à ajouter à un pool :

    - Le pool doit disposer d'espace pour la base de données. Il ne peut pas déjà contenir le nombre maximal de bases de données. Plus spécifiquement, le pool doit avoir suffisamment d'eDTU disponibles pour couvrir le nombre d'eDTU garanti par base de données (par exemple, si le nombre d'eDTU garanti est 400 pour le groupe et 10 pour chaque base de données, le nombre maximal de bases de données autorisé dans le pool est 40 (400 eDTU/10 eDTU garanties par base de données = 40 bases de données max).
    - Les fonctionnalités actuelles utilisées par la base de données doivent être disponibles dans le pool.

### Recommandations dynamiques

Après avoir ajouté des bases de données au pool, des recommandations seront générées de façon dynamique en fonction de l’historique d’utilisation des bases de données que vous avez sélectionnées. Ces recommandations seront affichées dans le graphique d’utilisation d’eDTU et de Go ainsi que dans la bannière de recommandation en haut du panneau **Configurer le pool**. Ces recommandations sont destinées à vous aider à créer un pool optimisé pour vos bases de données spécifiques.


 ![recommandations dynamiques](./media/sql-database-elastic-pool-portal/dynamic-recommendation.png)


## Étape 4: définition des caractéristiques de performances du pool

Vous configurez les performances du pool en définissant les paramètres de performance pour le pool et les bases de données élastiques dans le pool. N’oubliez pas que les **paramètres de base de données élastique** s’appliquent à toutes les bases de données du pool.

 ![Configurer un pool élastique](./media/sql-database-elastic-pool-portal/configure-performance.png)

Il existe trois paramètres configurables qui définissent les performances pour le regroupement : le nombre d'eDTU garanti pour le pool, eDTU MIN et eDTU MAX pour les bases de données élastiques dans le pool. La table suivante décrit chaque paramètre et fournit des conseils pour leur définition. Pour découvrir les paramètres spécifiques des valeurs disponibles, voir [Référence du pool de base de données élastique](sql-database-elastic-pool-reference.md).

| Paramètre de performance | Description |
| :--- | :--- |
| **POOL eDTU** : nombre d’eDTU garanti pour le pool | Il s'agit du nombre d'eDTU garanti disponibles et partagées par toutes les bases de données dans le pool. <br> Le nombre d’eDTU garanti spécifique pour un groupe doit être configuré en tenant compte de l’utilisation historique des eDTU du groupe. Cette taille peut également être définie par le nombre d'eDTU garanti souhaité par base de données et l'utilisation simultanée des bases de données actives. Le nombre d'eDTU garanti pour le pool dépend également de la quantité de stockage disponible pour le pool. Pour chaque eDTU que vous allouez au pool, vous obtenez une quantité de stockage de base de données fixe. <br> **Quelle valeur définir pour le nombre d’eDTU garanti pour le pool ?** <br>Au minimum, vous devez définir le nombre d’eDTU garanti pour le pool sur ([nombre de bases de données] x [utilisation moyenne des eDTU par base de données]). |
| **eDTU MIN** : nombre d’eDTU garanti pour chaque base de données | Le nombre d'eDTU garanti (eDTU guarantee) par base de données est le nombre d'eDTU garanti pour une base de données unique. <br> **Quelle valeur définir pour le nombre d'eDTU garanti par base de données ?** <br> En règle générale, le nombre d’eDTU garanti par base de données (eDTU MIN) est défini sur n’importe quelle valeur comprise entre 0 et l’([utilisation moyenne par base de données]). Le nombre d'eDTU garanti par base de données est un paramètre global qui définit le nombre d'eDTU garanti pour toutes les bases de données dans le pool. |
| **eDTU MAX** : nombre d’eDTU maximal par base de données | L’eDTU MAX par base de données est le nombre maximum d'eDTU qu'une base de données unique peut utiliser. Définissez un nombre d'eDTU maximal par base de données suffisamment élevé pour gérer les rafales ou les pics que vos bases de données peuvent rencontrer. Vous pouvez définir ce seuil jusqu'à la limite du système, qui dépend du niveau de tarification du pool (1 000 eDTU en mode Premium) Le niveau spécifique de ce seuil doit tenir compte de modèles de pic d'utilisation des bases de données au sein du groupe. Une certaine allocation excessive du groupe est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d'utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d'utilisation.<br> **Quelle valeur définir pour le nombre d'eDTU maximal par base de données ?** <br> Définissez eDTU MAX (nombre maximal d’eDTU par base de données) sur le ([pic d’utilisation de la base de données]). Par exemple, supposons que le pic d'utilisation par base de données est 50 UDBD et que 20 % des 100 bases de données du groupe connaissent simultanément un pic d'utilisation. Si le nombre d'eDTU maximal par base de données est défini sur 50 eDTU, vous pouvez envisager une allocation 5 fois plus élevée du pool et définir le nombre d'eDTU garanti pour le groupe (POOL eDTU) sur 1 000 eDTU. Il est également à noter que le nombre d'eDTU maximal n'est pas une garantie de ressource pour une base de données. Il s'agit d'un plafond du nombre d'eDTU qui peut être atteint s'il est défini. |


## Ajout et suppression de bases de données d’un pool

Une fois le pool créé, vous pouvez ajouter ou supprimer des bases de données existantes dans le pool, en ajoutant ou en supprimant des bases de données sur la page **Bases de données élastiques** (accédez à votre pool, puis cliquez sur le lien **Bases de données élastiques** dans **Essentials**).

- Cliquez sur **Ajouter des bases de données** pour ouvrir la liste des bases de données que vous pouvez ajouter au pool.

    - ou -

- Sélectionnez les bases de données que vous souhaitez supprimer du pool, puis cliquez sur **Supprimer les bases de données**.

![pool recommandé](./media/sql-database-elastic-pool-portal/add-remove-databases.png)

## Créer une nouvelle base de données dans un pool

Créez une base de données dans un pool en accédant au pool souhaité et en cliquant sur **Créer une base de données**.

La base de données SQL est déjà configurée pour le serveur approprié et le pool : entrez donc un nom et sélectionnez vos options de base de données, puis cliquez sur **OK** pour créer la base de données :


   ![créer des bases de données élastiques](./media/sql-database-elastic-pool-portal/create-database.png)



## Surveiller et gérer un pool élastique de bases de données

Après avoir créé un pool élastique de bases de données, vous pouvez surveiller et gérer le pool dans le portail en accédant à la liste de pools existants et en sélectionnant le pool souhaité.

Après avoir créé un pool, vous pouvez :

- Sélectionner **Configurer le pool** pour modifier les paramètres d’eDTU par pool et par base de données.
- Sélectionner **Créer une tâche** et gérer les bases de données dans le pool en créant des tâches élastiques. Les tâches élastiques vous permettent d'exécuter des scripts Transact-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).
- Sélectionner **Gérer les tâches** pour gérer les travaux élastiques existants.
- Utilisez l’instruction **Créer une base de données** pour créer une base de données dans le pool.
- Cliquez sur le lien de configuration du pool pour régler le nombre d’eDTU et de Go par pool
- Cliquez sur le lien de bases de données pour ajouter des bases de données ou les supprimer du pool.
- Cliquez sur le lien de configuration de base de données pour ajuster les nombres maximum et minimum d’eDTU pour les bases de données élastiques. 

![Surveiller un pool élastique](./media/sql-database-elastic-pool-portal/pool-tasks.png)


Lorsque vous sélectionnez un pool existant, vous pouvez voir l'utilisation des ressources du pool. Cliquez sur le graphique **Utilisation des ressources** pour ouvrir le panneau **Mesure** dans lequel vous pouvez personnaliser le graphique et les alertes de configuration.

![Surveiller un pool élastique][4] 
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
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=AcomDC_0204_2016-->