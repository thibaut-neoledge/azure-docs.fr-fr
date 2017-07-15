---
title: "Niveaux de service et de performances de Microsoft Azure SQL Database | Microsoft Docs"
description: "Comparaison entre les niveaux de service et de performances de Microsoft Azure SQL Database pour des bases de données uniques et présentation des pools élastiques SQL"
keywords: "options de base de données,performances de base de données"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/30/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 68d55d2dd088ce6350bd65b79206f161f9d3d788
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# Options de performances disponibles pour Microsoft Azure SQL Database
<a id="what-performance-options-are-available-for-an-azure-sql-database" class="xliff"></a>

[Microsoft Azure SQL Database](sql-database-technical-overview.md) offre quatre niveaux de service pour des bases de données uniques ou [regroupées](sql-database-elastic-pool.md). Ces niveaux de service comprennent les niveaux **De base**, **Standard**, **Premium** et **Premium RS**. Chaque service dispose de plusieurs niveaux de performances ([DTU](sql-database-what-is-a-dtu.md)) et options de stockage permettant de gérer diverses charges de travail et tailles de données. Les niveaux de performances plus élevés offrent des ressources de stockage et de calcul supplémentaires, afin de générer un meilleur débit et une capacité supérieure. Vous pouvez modifier les niveaux de service et de performances et le stockage de manière dynamique, sans aucun temps d’arrêt. 
- Les niveaux de service **De base**, **Standard** et **Premium** proposent tous un contrat de niveau de service garantissant un temps d’activité de 99,99 %. Par ailleurs, ils offrent des options de continuité d’activité flexibles, des fonctionnalités de gestion de la sécurité et une facturation à l’heure. 
- Les niveaux de performances fournis par le niveau **Premium RS** sont comparables à ceux du niveau Premium, avec un contrat de niveau de service plus limité. Il s’exécute en effet avec un nombre de copies redondantes inférieur à celui des bases de données des autres niveaux de service. Par conséquent, en cas d’échec d’un service, vous devrez peut-être restaurer votre base de données à partir d’une sauvegarde avec un délai allant jusqu’à 5 minutes.

> [!IMPORTANT]
> Une base de données Azure SQL Database obtient toujours un ensemble de ressources garanti, et les caractéristiques de performances attendues de votre base de données ne sont affectées par aucune autre base de données présente dans Azure. 

## Choix d’un niveau de service
<a id="choosing-a-service-tier" class="xliff"></a>
Le tableau suivant fournit des exemples de niveaux adaptés à différentes charges de travail d'application.

| Niveau de service | Charges de travail cibles |
| :--- | --- |
| **De base** | Idéal pour une petite base de données prenant en général en charge une seule opération active à la fois. Exemple : bases de données utilisées pour le développement ou le test ou pour des applications à petite échelle rarement utilisées. |
| **Standard** |Option idéale pour les applications cloud avec des exigences minimes ou moyennes en matières de performances d’E/S, en prenant en charge plusieurs requêtes simultanées. Exemple : applications Web ou de groupe de travail. |
| **Premium** | Option conçue pour un volume transactionnel élevé avec de hautes exigences en matière de performances d’E/S, en prenant en charge un grand nombre d’utilisateurs simultanés. Exemple : bases de données prenant en charge des applications critiques. |
| **Premium RS** | Conçu pour les charges de travail intensives en E/S qui ne nécessitent pas de garanties de disponibilité maximale. Parmi les exemples, on peut citer les charges de travail hautes performances ou une charge de travail analytique où la base de données n’est pas le système d’enregistrement. |
|||

Vous pouvez créer des bases de données uniques avec des ressources dédiées au sein d’un niveau de service avec un [niveau de performances](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels) spécifique. Vous pouvez aussi créer des bases de données dans un [pool élastique SQL](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus). Dans un pool élastique SQL, les ressources de calcul et de stockage sont partagées entre plusieurs bases de données au sein d’un même serveur logique. 

Les ressources disponibles pour les bases de données uniques sont exprimées en termes d’unités de transaction de base de données (DTU) et celles des pools élastiques, en termes d’unités de transaction de base de données élastique (eDTU). Pour en savoir plus sur les DTU et les eDTU, voir [Explication des unités de transaction de base de données (DTU) et des unités de transaction de base de données élastique (eDTU)](sql-database-what-is-a-dtu.md).

Pour choisir un niveau de service, commencez par déterminer les fonctionnalités de base de données minimales dont vous avez besoin :

| **Fonctionnalités de niveau de service** | **De base** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| Taille maximale de la base de données unique | 2 Go | 250 Go | 4 To*  | 500 Go  |
| Taille maximale du pool élastique | 156 Go | 2,9 To | 4 To* | 750 Go |
| Taille maximale de la base de données dans un pool élastique | 2 Go | 250 Go | 500 Go | 500 Go |
| Nombre maximal de bases de données par pool | 500  | 500 | 100 | 100 |
| DTU max de la base de données unique | 5 | 100 | 4000 | 1 000 |
| DTU max de la base de données dans un pool élastique | 5 | 3000 | 4000 | 1 000 |
| Période de rétention de sauvegarde de bases de données | 7 jours | 35 jours | 35 jours | 35 jours |
||||||

> [!IMPORTANT]
> L’option de stockage jusqu’à 4 To est actuellement disponible dans les régions suivantes : Est des États-Unis 2, États-Unis de l’Ouest, Gouvernement des États-Unis - Virginie, Europe de l’Ouest, Centre de l’Allemagne, Asie du Sud-Est, Japon de l’Est, Est de l’Australie, Centre du Canada et Est du Canada. Consultez l’article [Limitations 4 To actuelles](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize).
>

Une fois que vous avez déterminé le niveau de service adéquat, vous êtes prêt à déterminer le niveau de performances (nombre de DTU) et la capacité de stockage requis pour la base de données. 

- Les niveaux de performances S2 et S3 du niveau **Standard** constituent souvent un bon point de départ. 
- Dans le cas des bases de données présentant des exigences élevées en termes de processeur ou d’E/S, les niveaux de performances du niveau **Premium** sont adaptés. 
- Le niveau **Premium** offre davantage de ressources processeur et 10 fois plus d’E/S au départ que le niveau de performances **Standard** le plus élevé.
- Le niveau **Premium RS** offre les mêmes performances que le niveau **Premium** à un coût plus abordable, mais avec un contrat de niveau de service limité.

> [!IMPORTANT]
> Consultez la section sur les [pools élastiques SQL](sql-database-elastic-pool.md) pour savoir comment regrouper des bases de données dans des pools élastiques SQL, afin de partager des ressources de calcul et de stockage. Le reste de la section s’intéresse aux niveaux de service et aux niveaux de performance des bases de données uniques.
>

## Niveau de service et niveau de performances d’une base de données unique
<a id="single-database-service-tiers-and-performance-levels" class="xliff"></a>
Il existe plusieurs niveaux de performances et capacités de stockage au sein de chaque niveau de service pour les bases de données uniques. 

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## Mise à l’échelle supérieure ou inférieure d’une base de données unique
<a id="scaling-up-or-scaling-down-a-single-database" class="xliff"></a>

Après avoir choisi un niveau de service et un niveau de performances initiaux, vous pouvez mettre à l’échelle supérieure ou inférieure une base de données unique de façon dynamique sur la base de votre expérience concrète.  

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

La modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Le délai de basculement peut varier, mais il est généralement de moins de 4 secondes, et ne dépassera pas les 30 secondes dans 99 % des cas. Ce délai peut se révéler supérieur, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.  

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service Standard ne demande pas plus de 6 heures. Le changement des niveaux de performances d’une base de données de la même taille dans le niveau de service Premium doit s’effectuer en moins de 3 heures.

* Si vous effectuez la mise à niveau vers un niveau de service ou de performance supérieur, la taille maximale de la base de données n’augmente pas, à moins que vous n’en fassiez la demande.
* Pour pouvoir passer à une version antérieure, la base de données doit présenter une taille inférieure à la taille maximale autorisée par le niveau de service voulu. 
* Lors de la mise à niveau d’une base de données pour laquelle la [géoréplication](sql-database-geo-replication-portal.md) est activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données primaire (conseil général).
* Avant de passer à une version antérieure à un niveau de service **Premium**, vous devez arrêter toutes les relations de géoréplication. Vous pouvez suivre la procédure décrite à la section relative à la [récupération suite à une indisponibilité de service](sql-database-disaster-recovery.md) pour arrêter le processus de réplication entre la base de données principale et les bases de données secondaires actives.
* Les offres de service de restauration sont différentes selon les niveaux de service. Si vous retournez au niveau de service **De base**, la rétention des fichiers de sauvegarde sera de courte durée (consultez la section relative aux [sauvegardes Azure SQL Database](sql-database-automated-backups.md)).
* Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.


## Limitations actuelles des bases de données P11 et P15 avec une taille maximale de 4 To
<a id="current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize" class="xliff"></a>

Dans certaines régions (précédemment mentionnées), la taille maximale prise en charge est de 4 To pour des bases de données P11 et P15. Les considérations et limitations suivantes s’appliquent aux bases de données P11 et P15 avec une taille maximale de 4 To :

- Si vous choisissez l’option de taille maximale de 4 To lors de la création d’une base de données (avec une valeur de 4 To ou de 4096 Go), la commande de création échoue avec une erreur si la base de données est configurée dans une région non prise en charge.
- Pour les bases de données P11/P15 existantes situées dans l’une des régions prises en charge, vous pouvez augmenter la taille maximale du stockage à 4 To. Vous pouvez vous en assurer avec l’option [SÉLECTIONNER DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) ou en examinant la taille de la base de données sur le Portail Azure. La mise à niveau d’une base de données P11 ou P15 existante ne peut être effectuée que par le biais d’une connexion du principal au niveau du serveur ou par les membres du rôle de base de données dbmanager. 
- En cas d’exécution d’une mise à niveau dans une région prise en charge, la configuration est mise à jour immédiatement. La base de données reste en ligne pendant le processus de mise à niveau. Toutefois, vous ne pourrez pas utiliser la totalité des 4 To de stockage tant que les fichiers de base de données réels n’ont pas été mis à niveau vers la nouvelle taille maximale. La durée requise dépend de la taille de la base de données mise à niveau.  
- Lors de la création ou de la mise à jour d’une base de données P11 ou P15, vous n’avez le choix qu’entre les tailles maximales de 1 To ou de 4 To. Les tailles de stockage intermédiaires ne sont actuellement pas prises en charge. Lorsque vous créez une base de données P11/P15, l’option de stockage par défaut de 1 To est présélectionnée. Pour les bases de données situées dans l’une des régions prises en charge, vous pouvez augmenter le stockage maximal d’une base de données unique (nouvelle ou existante) à 4 To. Pour toutes les autres régions, la taille maximale ne peut pas être supérieure à 1 To. Le prix ne change pas lorsque vous sélectionnez l’option de 4 To de stockage inclus.
- La taille maximale de base de données de 4 To ne peut pas être modifiée pour 1 To, même si le stockage réel utilisé est inférieur à 1 To. Par conséquent, vous ne pouvez pas rétrograder une base de données P11-4 To/P15-4 To vers un niveau de performance P11-1 To/P15-1 To ou inférieur (tel que P1 à P6) avant que nous ne mettions à disposition des options de stockage supplémentaire pour les autres niveaux de performance. Cette restriction s’applique également aux scénarios de restauration et de copie, notamment la limite de restauration dans le temps, la restauration géographique, la rétention de sauvegarde à long terme et la copie de bases de données. Une fois qu’une base de données est configurée avec l’option 4 To, toutes les opérations de restauration de cette base de données doivent s’effectuer vers une base de données P11/P15 avec une taille maximale de 4 To.
- Lors de la création ou de la mise à niveau d’une base de données P11/P15 dans une région non prise en charge, l’opération de création ou de mise à niveau échoue avec le message d’erreur suivant : **Les bases de données P11 et P15 avec jusqu’à 4 To de stockage sont disponibles dans les régions Est des États-Unis 2, États-Unis de l’Ouest, Gouvernement des États-Unis - Virginie, Europe de l’Ouest, Centre de l’Allemagne, Asie du Sud-Est, Japon de l’Est, Est de l’Australie, Centre du Canada et Est du Canada.**
- Pour les scénarios de géoréplication active :
   - Configuration d’une relation de géoréplication : si la base de données primaire est de niveau P11 ou P15, la ou les bases de données secondaires doivent également être de niveau P11 ou P15. Les niveaux de performance inférieurs sont rejetés pour les bases de données secondaires, car ils ne sont pas en mesure de prendre en charge 4 To.
   - Mise à niveau de la base de données primaire dans une relation de géoréplication : le fait de modifier la taille maximale de la base de données primaire pour 4 To déclenche la même modification sur la base de données secondaire. Les deux mises à niveau doivent aboutir pour que la modification sur la base de données principale prenne effet. Des limitations de région pour l’option de 4 To s’appliquent (voir ci-dessus). Si la base de données secondaire se situe dans une région qui ne prend pas en charge l’option de 4 To, la base de données primaire n’est pas mise à niveau.
- L’utilisation du service Import/Export pour le chargement des bases de données P11-4 To/P15-4 To n’est pas prise en charge. Utilisez SqlPackage.exe pour [importer](sql-database-import.md) et [exporter](sql-database-export.md) les données.

## Gérer les niveaux de service et les niveaux de performances d’une base de données unique via le portail Azure
<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-portal" class="xliff"></a>

Pour définir ou modifier le niveau de service, le niveau de performance ou la capacité de stockage d’une base de données Azure SQL Database (nouvelle ou existante) via le portail Azure, ouvrez la fenêtre **Configurer les performances** de la base de données en cliquant sur **Niveau tarifaire (DTU de mise à l’échelle)**, comme indiqué dans la capture d’écran suivante. 

- Définissez ou modifiez le niveau de service en sélectionnant le niveau de service de la charge de travail. 
- Définissez ou modifiez le niveau de performance (**DTU**) dans un niveau de service à l’aide du curseur **DTU**.
- Définissez ou modifiez la capacité de stockage pour le niveau de performances à l’aide du curseur **Stockage**. 

  ![Configurer le niveau de service et le niveau de performances](./media/sql-database-service-tiers/service-tier-performance-level.png)

> [!IMPORTANT]
> Parcourez la section [Limitations actuelles des bases de données P11 et P15 avec une taille maximale de 4 To](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize) lors de la sélection d’un niveau de service P11 ou P15.
>

## Gérer les niveaux de service et les niveaux de performances d’une base de données unique via PowerShell
<a id="manage-single-database-service-tiers-and-performance-levels-using-powershell" class="xliff"></a>

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage de bases de données Azure SQL Database via PowerShell, utilisez les applets de commande PowerShell suivantes. Si vous devez installer ou mettre à niveau PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps). 

| Applet de commande | Description |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Crée une base de données |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtient une ou plusieurs bases de données|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Définit les propriétés d’une base de données, ou déplace une base de données existante dans un pool élastique|


> [!TIP]
> Consultez [Surveillance et mise à l’échelle d’une instance SQL Database unique à l’aide de PowerShell](scripts/sql-database-monitor-and-scale-database-powershell.md) pour un exemple de script PowerShell qui surveille les mesures de performances d’une base de données, l’adapte à un niveau de performances supérieur et crée une règle d’alerte sur l’une des mesures de performances.

## Gérer des niveaux de service et des niveaux de performances d’une base de données unique via Azure CLI
<a id="manage-single-database-service-tiers-and-performance-levels-using-the-azure-cli" class="xliff"></a>

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage Azure SQL Database via Azure CLI, utilisez les commandes [Azure CLI SQL Database](/cli/azure/sql/db) suivantes. Utilisez [Cloud Shell](/azure/cloud-shell/overview) pour exécuter l’interface CLI dans votre navigateur ou [l’installer](/cli/azure/install-azure-cli) sur macOS, Linux ou Windows. Pour créer et gérer les pools élastiques SQL, voir [Pools élastiques](sql-database-elastic-pool.md).

| Applet de commande | Description |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#create) |Crée une base de données|
|[az sql db list](/cli/azure/sql/db#list)|Liste toutes les bases de données et les entrepôts de données d’un serveur, ou toutes les bases de données d’un pool élastique|
|[az sql db list-editions](/cli/azure/sql/db#list-editions)|Liste les objectifs de service disponibles et les limites de stockage|
|[az sql db list-usages](/cli/azure/sql/db#list-usages)|Renvoie les données d’utilisation de la base de données|
|[az sql db show](/cli/azure/sql/db#show)|Obtient une base de données ou un entrepôt de données|
|[az sql db update](/cli/azure/sql/db#update)|Met à jour une base de données|

> [!TIP]
> Voir [Surveiller et mettre à l’échelle une instance unique SQL Database à l’aide de l’interface Azure CLI](scripts/sql-database-monitor-and-scale-database-cli.md) pour un exemple de script CLI permettant la mise à l’échelle d’une instance unique de Microsoft Azure SQL Database vers un nouveau niveau de performance après l’analyse des données de taille de la base de données.
>

## Gérer es niveaux de service et les niveaux de performances d’une base de données unique via Transact-SQL
<a id="manage-single-database-service-tiers-and-performance-levels-using-transact-sql" class="xliff"></a>

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage Azure SQL Database via Transact-SQL, utilisez les commandes T-SQL suivantes. Vous pouvez entrer ces commandes à l’aide du portail Azure, de [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), de [Visual Studio Code](https://code.visualstudio.com/docs), ou de tout autre programme pouvant se connecter à un serveur Azure SQL Database et transmettre des commandes Transact-SQL. 

| Commande | Description |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Crée une nouvelle base de données. Vous devez être connecté à la base de données MASTER pour créer une base de données.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Modifie une base de données SQL Azure. |
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Renvoie l’édition (niveau de service), l’objectif de service (niveau tarifaire) et, s’il y en a un, le nom du pool élastique Azure SQL Database ou d’un entrepôt de données Azure SQL Data Warehouse. Si vous êtes connecté à la base de données MASTER d’un serveur Azure SQL Database, renvoie les informations au sujet de toutes les bases de données. Pour SQL Azure Data Warehouse, vous devez être connecté à la base de données MASTER.|
|[sys.database_usage (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-usage-azure-sql-database)|Liste le nombre, le type et la durée des bases de données sur un serveur de base de données SQL Azure.|

L’exemple suivant illustre la modification du paramètre de taille maximale (maxsize) à l’aide de la commande ALTER DATABASE :

 ```sql
ALTER DATABASE <myDatabaseName> 
   MODIFY (MAXSIZE = 4096 GB);
```

## Gérer les bases de données uniques via l’interface de programmation d’applications (API) REST
<a id="manage-single-databases-using-the-rest-api" class="xliff"></a>

Pour définir ou modifier les niveaux de service, les niveaux de performances et la capacité de stockage Azure SQL Database via l’API REST, consultez la section relative à [l’API REST pour Azure SQL Database](/rest/api/sql/).

## Étapes suivantes
<a id="next-steps" class="xliff"></a>

* En savoir plus sur les [DTU](sql-database-what-is-a-dtu.md).
* Pour savoir comment surveiller l’utilisation des DTU, voir [Surveiller et régler les performances](sql-database-troubleshoot-performance.md).


