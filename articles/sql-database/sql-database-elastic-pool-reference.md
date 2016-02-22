<properties
	pageTitle="Référence du pool de base de données élastique pour SQL Database | Microsoft Azure" 
	description="Ce document de référence fournit des liens et des informations sur les articles relatifs aux pools de bases de données élastiques, ainsi que sur la programmabilité."
	keywords="eDTU"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/08/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Référence du pool de bases de données élastiques SQL

Ce document de référence fournit des liens et des informations sur les articles relatifs aux pools de bases de données élastiques, ainsi que sur la programmabilité. Pour les développeurs SaaS chargés d’administrer des dizaines, des centaines, voire des milliers de bases de données, un pool de bases de données élastiques simplifie la création, la gestion et la maintenance des coûts autant que des performances, sur l’ensemble du groupe de bases de données.

Un pool de bases de données élastique est un ensemble d’unités de débit de base de données élastiques (eDTU) et d’unités de stockage (Go) qui sont partagées par plusieurs bases de données. Vous pouvez à tout moment ajouter des éléments dans les bases de données élastiques, et supprimer des bases de données dans le pool. Les bases de données élastiques du pool utilisent uniquement les ressources qu’il leur faut dans le pool, libérant les ressources disponibles à l’attention des bases de données qui les requièrent, exclusivement. Pour obtenir de l’aide pour déterminer si vos bases de données tireraient parti d’un pool de base de données élastique, consultez [Considérations sur les prix et performances pour un pool de base de données élastique](sql-database-elastic-pool-guidance.md).



## Conditions requises pour créer et gérer des pools de bases de données élastiques


- Les pools de bases de données élastiques sont uniquement disponibles sur les serveurs de base de données SQL Azure version 12. Pour effectuer la mise à niveau vers la version 12 et migrer vos bases de données directement dans un pool, consultez [Mettre à niveau vers Azure SQL Database V12](sql-database-upgrade-server-powershell.md).
- La création et la gestion des pools de bases de données élastiques sont prises en charge à l’aide du [portail Azure](https://portal.azure.com), de [PowerShell](sql-database-elastic-pool-powershell.md) et de la bibliothèque cliente .NET (Azure Resource Manager uniquement) ; le [portail Classic](https://manage.windowsazure.com/) et les commandes de gestion de service ne sont pas pris en charge.
- En outre, la création de nouvelles bases de données élastiques et le déplacement de bases de données existantes dans et hors des pools de bases de données élastiques sont prises en charge à l’aide de [Transact-SQL](#transact-sql).


## Liste d’articles

Les articles suivants vous aideront à vous familiariser avec l’utilisation des bases de données et tâches élastiques :

| Article | Description |
| :-- | :-- |
| [Pools de bases de données SQL élastiques](sql-database-elastic-pool.md) | Vue d’ensemble des pools élastiques de bases de données |
| [Considérations sur les prix et performances](sql-database-elastic-pool-guidance.md) | Déterminer si l’utilisation d’un pool élastique de bases de données est économique. |
| [Créer et gérer un pool élastique de bases de données SQL à l’aide du portail Azure](sql-database-elastic-pool-portal.md) | Créer et gérer un pool élastique de bases de données avec le portail Azure |
| [Créer et gérer un pool de base de données SQL élastique à l’aide de PowerShell](sql-database-elastic-pool-powershell.md) | Créer et gérer un pool élastique de bases de données avec des applets de commande PowerShell |
| [Créer et gérer une base de données SQL avec la bibliothèque de base de données SQL Azure pour .NET](sql-database-elastic-pool-powershell.md) | Créer et gérer un pool élastique de bases de données avec C# |
| [Vue d’ensemble des tâches de base de données élastique](sql-database-elastic-jobs-overview.md) | Vue d’ensemble du service de tâches élastiques, qui permet l’exécution de scripts T-SQL sur toutes les bases de données élastiques dans un pool. |
| [Installation du composant de tâche de base de données élastique](sql-database-elastic-jobs-service-installation.md) | Comment installer le service de tâche de base de données élastique. |
| [Sécurisation de votre base de données SQL](sql-database-security.md) | Pour exécuter un script de tâche de base de données élastique, vous devez ajouter les autorisations appropriées à chaque base de données du pool. |
| [Comment désinstaller des composants de tâche de base de données élastique](sql-database-elastic-jobs-uninstall.md) | Aidez le système à récupérer suite à des erreurs survenues lors d’une tentative d’installation du service de tâche de base de données élastique. |



## Détails sur les espaces de noms et les points de terminaison
Un pool élastique de bases de données est une ressource Azure Resource Manager de type « ElasticPool » dans la base de données SQL Microsoft Azure.

- **namespace** : Microsoft.Sql/ElasticPool
- **management-endpoint** pour les appels d’API REST (Resource Manager) : https://management.azure.com



## Propriétés du pool de bases de données élastiques

| Propriété | Description |
| :-- | :-- |
| creationDate | Date de création du pool. |
| databaseDtuMax | Nombre maximal d’eDTU qu’une base de données du pool peut utiliser. Ce nombre n’est pas une garantie concernant l’octroi des ressources. Il s’applique à toutes les bases de données du pool. |
| databaseDtuMin | Nombre minimal d’eDTU garanti pour une base de données du pool. Ce nombre peut être défini sur 0. Il s’applique à toutes les bases de données du pool. Remarque : le produit du nombre de bases de données du pool et du nombre minimal d’eDTU ne peut pas dépasser la quantité d’eDTU du pool lui-même. |
| Dtu | Nombre d’eDTU que se partagent toutes les bases de données dans le pool. |
| edition | Niveau de service du pool. Chaque base de données du pool présente cette édition. |
| elasticPoolId | GUID de l’instance du pool. |
| elasticPoolName | Nom du pool. Ce nom est unique par rapport au serveur parent correspondant. |
| location | Emplacement du centre de données dans lequel le pool a été créé. |
| state | La valeur correspondante est « Désactivé » si le paiement de la facture d’abonnement est négligé et « Prêt » dans les autres cas. |
| storageMB | Limite de stockage pour le pool (en Mo) Le stockage total utilisé par l’ensemble des bases de données du pool ne peut pas dépasser la limite de ce pool. |


## Limites relatives aux eDTU et au stockage pour les pools et bases de données élastiques


[AZURE.INCLUDE [Tableau des niveaux de service de base de données SQL pour les bases de données élastiques](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]



## Limites relatives à Microsoft Azure Resource Manager

Les serveurs de base de données SQL Azure version 12 sont situés dans des groupes de ressources.

- Chaque groupe de ressources peut inclure un maximum de 800 serveurs.
- Chaque serveur peut présenter un maximum de 800 pools élastiques.


## Latence des opérations du pool élastique

- En général, le processus de modification du nombre d’eDTU garanti à chaque base de données (paramètre databaseDtuMin) ou du nombre maximal d’eDTU pour chaque base de données (paramètre databaseDtuMax) prend 5 minutes au maximum.
- Le processus de modification du nombre d’eDTU/de la limite de stockage (paramètre storageMB) dépend quant à lui de la quantité totale d’espace utilisé par toutes les bases de données du pool. Ce processus prend en moyenne 90 minutes au maximum, pour chaque tranche de 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données du pool est égal à 200 Go, une opération de modification de la limite de stockage/eDTU du pool prend 3 heures au maximum.



## PowerShell, API REST et la bibliothèque cliente .NET

Plusieurs applets de commande PowerShell et commandes d’API REST ci-après permettent de créer et de gérer des pools élastiques. Pour plus d’informations et des exemples de code, consultez [Créer et gérer un pool élastique de la base de données de base de données SQL à l’aide de PowerShell](sql-database-elastic-pool-powershell.md) et [Créer et gérer la base de données SQL avec C#](sql-database-client-library.md).


| [Applets de commande PowerShell](https://msdn.microsoft.com/library/azure/mt574084.aspx) | [Commandes d’API REST](https://msdn.microsoft.com/library/mt163571.aspx) |
| :-- | :-- |
| [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) | [Créer un pool de bases de données élastiques](https://msdn.microsoft.com/library/mt163596.aspx) |
| [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) | [Définir les paramètres de performances d’un Pool de base de données élastique](https://msdn.microsoft.com/library/mt163641.aspx) |
| [Remove-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619355.aspx) | [Supprimer un pool de base de données élastique](https://msdn.microsoft.com/library/mt163672.aspx) |
| [Get-AzureRMSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603517.aspx) | [Obtient les pools de base de données élastique et les valeurs de propriété](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) | [Obtenir l’état des opérations de pool élastique de bases de données](https://msdn.microsoft.com/library/mt163669.aspx) |
| [Get-AzureRmSqlElasticPoolDatabase](https://msdn.microsoft.com/library/azure/mt619484.aspx) | [Obtenir toutes les bases de données dans un pool de base de données élastique](https://msdn.microsoft.com/library/mt163646.aspx) |
| [Get-AzureRmSqlElasticPoolDatabaseActivity]() | [Obtient l’état du déplacement de bases de données dans et hors d’un pool](https://msdn.microsoft.com/library/mt163669.aspx) |

## Transact-SQL

Vous pouvez utiliser Transact-SQL pour exécuter les tâches de gestion de la base de données élastique suivante :

| Task | Détails |
| :-- | :-- |
| Pour créer une base de données élastique (directement dans un pool) | [CRÉER UNE BASE DE DONNÉES (Base de données SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) |
| Déplacer des bases de données existante dans et hors d’un pool | [MODIFIER LA BASE DE DONNÉES (Transact-SQL).](https://msdn.microsoft.com/library/ms174269.aspx) |
| Obtenir les statistiques d’utilisation de ressources d’un pool. | [sys.elastic\_pool\_resource\_stats (Base de données SQL Azure)](https://msdn.microsoft.com/library/mt280062.aspx) |


## Informations sur la tarification et la facturation

Les pools de bases de données élastiques sont facturés en fonction des caractéristiques suivantes :

- Un pool élastique est facturé lors de sa création, même s’il n’inclut aucune base de données.
- Il est facturé toutes les heures. La fréquence de mesure est identique à celle des niveaux de performances associés aux bases de données uniques.
- Si un pool élastique est redimensionné en fonction d’un nouveau nombre d’eDTU, ce pool n’est pas facturé selon ce nouveau nombre, tant que l’opération de redimensionnement n’est pas terminée. Le processus est le même que lors de la modification du niveau de performances des bases de données autonomes.


- Le prix d’un pool élastique est basé sur le nombre d’eDTU de ce pool. Le prix d’un pool élastique est indépendant de l’utilisation des bases de données élastiques qu’il contient.
- Le prix est calculé comme suit : le nombre d’eDTUs d’un pool x le prix unitaire par eDTU.

Le prix unitaire des eDTU d’un pool élastique est supérieur au prix unitaire des DTU d’une base de données autonome au sein du même niveau de service. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## Erreurs du pool de bases de données élastiques

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX\_RESOURCE | Le pool élastique a atteint sa limite de stockage. Le taux d’utilisation du stockage pour le pool élastique ne doit pas dépasser (%d) Mo. | Limite de l’espace du pool élastique, en Mo. | Tentative d’écriture de données dans une base de données alors que la limite de stockage du pool élastique a été atteinte. | Envisagez d’augmenter le nombre de DTU du pool élastique, le cas échéant, afin d’accroître sa limite de stockage, de réduire l’espace de stockage utilisé par les bases de données individuelles qu’il inclut, ou de supprimer les bases de données dans ce dernier. |
| 10929 | EX\_USER | La garantie minimale de %s est %d ; la limite maximale est de %d et le taux d’utilisation actuel de la base de données est de %d. Toutefois, le serveur est trop occupé pour prendre en charge les requêtes supérieures à %d pour cette base de données. Voir [https://msdn.microsoft.com/library/azure/dn338078.aspx](http://go.microsoft.com/fwlink/?LinkId=267637) pour obtenir de l’aide. Sinon, réessayez plus tard. | Nombre minimal de DTU par base de données ; nombre maximal de DTU par base de données. | Le nombre total d’ouvriers simultanés (demandes) dans toutes les bases de données du pool élastique a failli dépasser la limite du pool. | Envisagez d’augmenter le nombre de DTU du pool élastique, le cas échéant, afin d’accroître la limite d’ouvriers associés, ou de supprimer des bases de données du pool élastique. |
| 40844 | EX\_USER | La base de données '%ls' sur le serveur '%ls' est une base de données présentant l’édition '%ls' dans un pool élastique. Elle ne peut pas présenter de relation de copie continue. | Nom de la base de données, édition de la base de données, nom du serveur | Une commande StartDatabaseCopy est émise pour une base de données non-Premium dans un pool élastique. | Bientôt disponible |
| 40857 | EX\_USER | Pool élastique introuvable pour le serveur : '%ls'. Nom du pool élastique: '%ls'. | Nom du serveur, nom du pool élastique | Le pool élastique spécifié n’existe pas sur le serveur spécifié. | Saisissez un nom de pool élastique valide. |
| 40858 | EX\_USER | Le pool élastique '%ls' existe déjà sur le serveur : '%ls'. | Nom du pool élastique, nom du serveur | Le pool élastique spécifié existe déjà sur le serveur logique spécifié. | Saisissez un nouveau nom pour le pool élastique. |
| 40859 | EX\_USER | Le pool élastique ne prend pas en charge le niveau de service '%ls'. | Niveau de service du pool élastique | Le niveau de service spécifié n’est pas pris en charge pour le provisioning du pool élastique. | Saisissez l’édition correcte, ou laissez le champ du niveau de service vide afin d’utiliser le niveau de service par défaut. |
| 40860 | EX\_USER | La combinaison du pool élastique '%ls' et de l’objectif de service '%ls' n’est pas valide. | Nom du pool élastique, nom de l’objectif de niveau de service | L’objectif de service et le pool élastique peuvent être spécifiés ensemble uniquement si le premier est indiqué en tant que « ElasticPool ». | Spécifiez la combinaison de pool élastique et d’objectif de service adéquate. |
| 40861 | EX\_USER | L’édition de base de données '%.*ls' ne peut pas être différente du niveau de service du pool élastique, qui correspond à '%.*ls'. | Édition de base de données, niveau de service du pool élastique | L’édition de base de données est différente du niveau de service du pool élastique. | Indiquez une édition de base de données identique au niveau de service du pool élastique. Remarque : l’édition de base de données n’a pas besoin d’être spécifiée. | | 40862 | EX\_USER | Si l’objectif de service du pool élastique est spécifié, le nom du pool élastique doit l’être également. | Aucun | L’objectif de service du pool élastique n’identifie pas de manière unique un pool élastique. | Si vous utilisez l’objectif de service du pool élastique, spécifiez le nom du pool élastique. | | 40864 | EX\_USER | Le nombre de DTU du pool élastique doit se monter au minimum à (%d) pour le niveau de service '%.*ls'. | Nombre de DTU du pool élastique, niveau de service du pool élastique. | Tentative de définition du nombre de DTU du pool élastique au-dessous de la limite minimale. | Essayez à nouveau de définir le nombre de DTU du pool élastique sur la limite minimale ou plus. | | 40865 | EX\_USER | Le nombre de DTU du pool élastique ne doit pas dépasser (%d) pour le niveau de service '%.*ls'. | Nombre de DTU du pool élastique, niveau de service du pool élastique. | Tentative de définition du nombre de DTU du pool élastique au-dessus de la limite maximale. | Essayez à nouveau de définir le nombre de DTU du pool élastique sur la limite maximale ou moins. | | 40867 | EX\_USER | Le nombre maximal de DTU par base de données doit être au minimum (%d) pour le niveau de service '%.*ls'. | Nombre maximal de DTU par base de données, niveau de service du pool élastique | Tentative de définition du nombre maximal de DTU par base de données en dessous de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. | | 40868 | EX\_USER | Le nombre maximal de DTU par base de données ne peut pas dépasser (%d) pour le niveau de service '%.*ls'. | Nombre maximal de DTU par base de données, niveau de service du pool élastique. | Tentative de définition du nombre maximal de DTU par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. | | 40870 | EX\_USER | Le nombre minimal de DTU par base de données ne peut pas dépasser (%d) pour le niveau de service '%.*ls'. | Nombre minimal de DTU par base de données, niveau de service du pool élastique. | Tentative de définition du nombre minimal de DTU par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. | | 40873 | EX\_USER | Le nombre de bases de données (%d) et le nombre minimal de DTU par base de données (%d) ne peuvent pas dépasser le nombre de DTU du pool élastique (%d). | Nombre de bases de données dans le pool élastique ; nombre minimal de DTU par base de données ; DTU du pool élastique. | Tentative de spécification d’un nombre minimal de DTU pour les bases de données dans le pool élastique dépassant le nombre de DTU du pool élastique. | Envisagez d’augmenter le nombre de DTU du pool élastique, ou de réduire le nombre minimal de DTU par base de données ou le nombre de bases de données dans le pool élastique. | | 40877 | EX\_USER | Impossible de supprimer un pool élastique, sauf s’il ne contient aucune base de données. | Aucun | Le pool élastique contient une ou plusieurs bases de données et ne peut donc pas être supprimé. | Supprimez les bases de données du pool élastique afin de pouvoir supprimer ce dernier. | | 40881 | EX\_USER | Le pool élastique '%.*ls' a atteint le nombre limite de bases de données. Le nombre limite de bases de données ne peut pas dépasser (%d) pour un pool élastique incluant (%d) DTU. | Nom du pool élastique, nombre limite de bases de données du pool élastique, eDTU du pool de ressources. | Tentative de création ou d’ajout d’une base de données au pool élastique alors que le nombre limite de bases de données du pool élastique a été atteint. | Envisagez d’augmenter le nombre de DTU du pool élastique, le cas échéant, afin d’accroître le nombre limite de bases de données, ou de supprimer des bases de données du pool élastique. | | 40889 | EX\_USER | La limite de stockage ou relative aux DTU pour le pool élastique '%.*ls' ne peut pas être abaissée, car l’espace de stockage risque de ne pas être suffisant pour les bases de données de ce pool. | Nom du pool élastique. | Tentative de définition de la limite de stockage du pool élastique en dessous du taux d’utilisation de son espace de stockage. | Envisagez de réduire le taux d’utilisation du stockage des bases de données dans le pool élastique, ou de supprimer des bases de données du pool afin de réduire sa limite de stockage ou relative aux DTU. | | 40891 | EX\_USER | Le nombre minimal de DTU par base de données (%d) ne peut pas dépasser le nombre maximal de DTU par base de données (%d). | Nombre minimal de DTU par base de données, nombre maximal de DTU par base de données. | Tentative de définition d’un nombre minimal de DTU par base de données supérieur au nombre maximal de DTU par base de données. | Vérifiez que le nombre minimal de DTU par base de données ne dépasse pas le nombre maximal de DTU par base de données. | | TBD | EX\_USER | La taille de l’espace de stockage d’une base de données individuelle dans un pool élastique ne peut pas dépasser la taille maximale autorisée par le pool élastique de niveau de service '%.*ls'. | Niveau de service du pool élastique | La taille maximale de la base de données dépasse la taille maximale autorisée par le niveau de service du pool élastique. | Définissez la taille maximale de la base de données dans les limites de la taille maximale autorisée par le niveau de service du pool élastique. |

<!---HONumber=AcomDC_0211_2016-->