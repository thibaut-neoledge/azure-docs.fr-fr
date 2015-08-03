<properties 
	pageTitle="Créer et gérer un pool élastique de bases de données SQL (aperçu)" 
	description="Créer un seul pool de ressources à partager dans un groupe de bases de données SQL Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Créer et gérer un pool élastique de bases de données SQL (version préliminaire)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Cet article vous montre comment créer un pool élastique avec le [Portail Azure](https://portal.azure.com).

Les pools élastiques simplifient le processus de création, de maintenance et de gestion des performances et des coûts pour un grand nombre de bases de données.
 

> [AZURE.NOTE]Les pools élastiques sont actuellement en version préliminaire et uniquement disponibles avec des serveurs de base de données SQL V12.

 


## Conditions préalables

Pour créer un pool élastique, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur la VERSION D'ÉVALUATION GRATUITE en haut de cette page, puis continuez la lecture de cet article.
- Un serveur de base de données SQL Azure version 12. Si vous n’avez pas de serveur V12, créez-en un en suivant les étapes figurant dans [Créer votre première base de données Azure SQL](sql-database-get-started.md).



## Créer un pool élastique

Créez un pool élastique en ajoutant un nouveau pool à un serveur. Vous pouvez ajouter plusieurs pools à un serveur, mais un seul serveur peut être associé à chaque pool. En outre, tout ou partie des bases de données figurant sur un serveur peuvent être ajoutées à un pool.


1.	Sélectionnez un serveur de base de données SQL V12 qui contient les bases de données que vous souhaitez ajouter au pool.
2.	Créez le pool en sélectionnant **Ajouter un pool** en haut du panneau **SQL Server**.

   ![Créer un pool élastique][1]

## Configurer un pool élastique

Configurer le pool en définissant le niveau de tarification, en ajoutant des bases de données et en configurant les caractéristiques de performances du pool.

*Lorsque vous sélectionnez la commande **Ajouter un pool**, vous devez accepter les termes du contrat de l'aperçu en sélectionnant **TERMES DE LA VERSION PRÉLIMINAIRE** et en remplissant le panneau **Termes de la version préliminaire**. Il vous suffit d'accepter les termes du contrat une fois pour chaque abonnement.*

   ![Configurer un pool élastique][2]


### Niveau de tarification

Le niveau de tarification d'un pool élastique est quelque peu analogue au niveau de service d'une base de données SQL. Le niveau de tarification détermine les fonctionnalités disponibles pour les bases de données élastiques dans le pool, ainsi que le nombre maximal d'UDBD (DTU MAX) et le stockage (Go) disponibles pour chaque base de données.

> [AZURE.NOTE]La version préliminaire est actuellement limitée au niveau de tarification **Standard**.

| Niveau de tarification | DTU MAX par base de données |
| :--- | :--- |
| Standard | 100 DTU MAX par base de données |

### Ajouter des bases de données

Vous pouvez sélectionner à tout moment les bases de données spécifiques à inclure dans le pool. Lorsque vous créez un nouveau pool, Azure recommande les bases de données adaptées pour un pool en les marquant pour l'inclusion. Vous pouvez ajouter toutes les bases de données disponibles sur le serveur, ou vous pouvez sélectionner des bases de données dans la liste initiale ou en effacer selon vos besoins.

   ![Ajouter des bases de données][5]

Vous devez respecter les conditions suivantes lorsque vous sélectionnez une base de données à ajouter à un pool :

- Le pool doit disposer d'espace pour la base de données. Il ne peut pas déjà contenir le nombre maximal de bases de données. Plus spécifiquement, le pool doit avoir suffisamment d'UDBD disponibles pour couvrir le nombre d'UDBD garanti par base de données (par exemple, si le nombre d'UDBD garanti est 400 pour le groupe et 10 pour chaque base de données, le nombre maximal de bases de données autorisé dans le pool est 40 (400 UDBD/10 UDBD garanties par base de données = 40 bases de données max).
- Les fonctionnalités actuelles utilisées par la base de données doivent être disponibles dans le pool. 


### Configurer les performances

Vous configurez les performances du pool en définissant les paramètres de performance pour le pool et les bases de données élastiques dans le pool. N'oubliez pas que les **paramètres de base de données élastique** s'appliquent à toutes les bases de données dans le pool.

   ![Configurer un pool élastique][3]

Il existe trois paramètres qui définissent les performances pour le regroupement. Le nombre d'UDBD garanti pour le pool, DTU MIN et DTU MAX pour les bases de données élastiques dans le pool. La table suivante décrit chaque paramètre et fournit des conseils pour leur définition.

| Paramètre de performance | Description |
| :--- | :--- |
| **POOL DTU/GB** - nombre d'UDBD garanti pour le pool | Il s'agit du nombre d'UDBD garanti disponibles et partagées par toutes les bases de données dans le pool. <br> Actuellement, vous pouvez définir le paramètre sur 100, 200, 400, 800 ou 1200. <br> Le nombre d'UDBD garanti spécifique pour un groupe doit être configuré en tenant compte de l'utilisation historique des UDBD du groupe. Cette taille peut également être définie par le nombre d'UDBD garanti souhaité par base de données et l'utilisation simultanée des bases de données actives. Le nombre d'UDBD garanti pour le pool dépend également de la quantité de stockage disponible pour le pool. Pour chaque UDBD que vous allouez au pool, vous obtenez 1 Go de stockage de base de données (1 UDBD = 1 Go de stockage). <br> **Quelle valeur définir pour le nombre d'UDBD garanti pour le pool ?** <br>Au minimum, vous devez définir le nombre de DTU garanti pour le pool sur ([nombre de bases de données] x [utilisation moyenne des DTU par base de données]) |
| **DTU MIN** - nombre d'UDBD garanti pour chaque base de données | Le nombre d'UDBD garanti (DTU guarantee) par base de données est le nombre d'UDBD garanti pour une base de données unique. Actuellement, vous pouvez définir ce nombre garanti sur 0, 10, 20 ou 50 UDBD, ou vous pouvez choisir de ne pas fournir une garantie aux bases de données dans le groupe (DTU MIN=0). <br> **Quelle valeur définir pour le nombre d'UDBD garanti par base de données ?** <br> Au minimum, vous devez définir le nombre de DTU garantis par base de données (DTU MIN) sur l’([utilisation moyenne par base de données]). Le nombre d'UDBD garanti par base de données est un paramètre global qui définit le nombre d'UDBD garanti pour toutes les bases de données dans le pool. |
| **DTU MAX** - nombre d'UDBD maximal par base de données | Le DTU MAX par base de données est le nombre maximum d'UDBD qu'une base de données unique peut utiliser. Définissez un nombre d'UDBD maximal par base de données suffisamment élevé pour gérer les rafales ou les pics que vos bases de données peuvent rencontrer. Vous pouvez définir ce seuil jusqu'à la limite du système, qui dépend du niveau de tarification du pool (100 UDBD en mode Standard). Le niveau spécifique de ce seuil doit tenir compte de modèles de pic d'utilisation des bases de données au sein du groupe. Une certaine allocation excessive du groupe est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d'utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d'utilisation.<br> **Quelle valeur définir pour le nombre d'UDBD maximal par base de données ?** <br> Définissez DTU MAX (nombre maximal de DTU par base de données) sur le ([pic d’utilisation de la base de données]). Par exemple, supposons que le pic d'utilisation par base de données est 50 UDBD et que 20 % des 100 bases de données du groupe connaissent simultanément un pic d'utilisation. Si le nombre d'UDBD maximal par base de données est défini sur 50 UDBD, vous pouvez envisager une allocation 5 fois plus élevée du groupe et définir le nombre d'UDBD garanti pour le groupe sur 1 000 UDBD. Il est également à noter que le nombre d'UDBD maximal n'est pas une garantie de ressource pour une base de données. Il s'agit d'un plafond du nombre d'UDBD qui peut être atteint s'il est défini. |


## Ajout de bases de données dans un pool élastique

Une fois le pool créé, vous pouvez ajouter des bases de données au pool ou en supprimer


## Surveiller et gérer un pool élastique

Après avoir créé un pool élastique, vous pouvez surveiller et gérer le pool dans le portail en accédant à la liste de pools existants et en sélectionnant le pool souhaité.

Après avoir créé un pool, vous pouvez :

- Sélectionner **Configurer le pool** pour modifier les paramètres d'UDBD par pool et par base de données.
- Sélectionner **Créer une tâche** et gérer les bases de données dans le pool en créant des tâches élastiques. Les tâches élastiques facilitent l'exécution de scripts T-SQL, quel que soit le nombre de bases de données dans le pool. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).
- Sélectionner **Gérer les tâches** pour gérer les travaux élastiques existants.



![Surveiller un pool élastique][8]




![Surveiller un pool élastique][4]

Lorsque vous sélectionnez un pool existant, vous pouvez voir l'utilisation des ressources du pool. Cliquez sur le graphique **Utilisation des ressources** pour ouvrir le panneau **Mesure** dans lequel vous pouvez personnaliser le graphique et les alertes de configuration.


![utilisation des ressources][6]

Cliquez sur **Modifier le graphique** pour ajouter des paramètres afin de consulter facilement les données de télémétrie pour le pool.


![modifier le graphique][7]







## Étapes suivantes
Après avoir créé un pool élastique, vous pouvez gérer les bases de données du pool en créant des tâches élastiques. Les tâches élastiques facilitent l'exécution de scripts T-SQL, quel que soit le nombre de bases de données dans le pool.

Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).



## Rubriques connexes

- [Pools élastiques de bases de données SQL](sql-database-elastic-pool.md)
- [Créer un pool de base de données SQL avec PowerShell](sql-database-elastic-pool-powershell.md)
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

<!---HONumber=July15_HO4-->