<properties 
	pageTitle="Référence du pool de bases de données élastique SQL Microsoft Azure" 
	description="Ce document de référence fournit des liens et des informations sur les articles relatifs aux pools de bases de données élastiques, ainsi que sur la programmabilité." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/29/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Référence du pool de bases de données élastiques SQL (version préliminaire)

Pour les développeurs SaaS chargés d’administrer des dizaines, des centaines, voire des milliers de bases de données, un pool de bases de données élastiques simplifie la création, la gestion et la maintenance des coûts autant que des performances, sur l’ensemble du groupe de bases de données.

Ce document de référence fournit des liens et des informations sur les articles relatifs aux pools élastiques, ainsi que sur la programmabilité.

## Vue d’ensemble

Un pool élastique est un ensemble d’unités de débit de base de données (DTU) et d’unités de stockage (Go) qui sont partagées par plusieurs bases de données. Vous pouvez à tout moment ajouter des éléments dans les bases de données élastiques, et supprimer des bases de données dans le pool. Les bases de données élastiques du pool utilisent uniquement les ressources qu’il leur faut dans le pool, libérant les ressources disponibles à l’attention des bases de données qui les requièrent, exclusivement.



## Conditions requises pour créer et gérer des pools élastiques


- Les pools élastiques sont uniquement disponibles sur les serveurs de base de données SQL Microsoft Azure version 12.   
- Les API PowerShell et REST destinées aux pools élastiques sont prises en charge sur Azure Resource Manager (ARM) uniquement. Les commandes de gestion de service (RDFE) ne sont pas gérées. 
- La création et la gestion des pools élastiques sont prises en charge sur le [portail Microsoft Azure](https:portal.azure.com) uniquement. 


## Restrictions relatives à la version préliminaire actuelle

- Le niveau de tarification d’un pool élastique dans la version préliminaire actuelle est « Standard ».  
- L’importation directe d’une base de données dans un pool élastique n’est pas prise en charge. Vous pouvez l’importer dans une base de données autonome, puis déplacer cette dernière vers le pool. Par contre, il est possible d’exporter une base de données depuis un pool.


## Liste d’articles

Les articles suivants vous aideront à vous familiariser avec l’utilisation des bases de données et tâches élastiques :

| Article | Description |
| :-- | :-- |
| [Pools élastiques de bases de données SQL](sql-database-elastic-pool.md) | Vue d’ensemble des pools élastiques. |
| [Créer et gérer un pool élastique de bases de données SQL avec le portail Microsoft Azure](sql-database-elastic-pool-portal.md) | Comment créer et gérer un pool élastique avec le portail Microsoft Azure. |
| [Créer et gérer un pool élastique de bases de données SQL avec PowerShell](sql-database-elastic-pool-powershell.md) | Comment créer et gérer un pool élastique avec des applets de commande PowerShell. |
| [Vue d’ensemble des tâches de base de données élastique](sql-database-elastic-jobs-overview.md) | Vue d’ensemble du service de tâches élastiques, qui permet l’exécution de scripts T-SQL sur toutes les bases de données élastiques dans un pool. |
| [Installation du composant de tâche de base de données élastique](sql-database-elastic-jobs-service-installation.md) | Comment installer le service de tâche de base de données élastique. |
| [Création de l’utilisateur requis pour le service de tâches élastiques](sql-database-elastic-jobs-add-logins-to-dbs.md) | Pour exécuter un script de tâche de base de données élastique, vous devez ajouter les autorisations appropriées à chaque base de données du pool. |
| [Comment désinstaller des composants de tâche de base de données élastique](sql-database-elastic-jobs-uninstall.md) | Aidez le système à récupérer suite à des erreurs survenues lors d’une tentative d’installation du service de tâche de base de données élastique. |



## Détails sur les espaces de noms et les points de terminaison
Un pool élastique est une ressource ARM de type « ElasticPool » dans la base de données SQL Microsoft Azure.

- **namespace** : Microsoft.Sql/ElasticPool
- **secondary-endpoint** pour les appels d’API REST (gestionnaire de ressources Microsoft Azure) : https://management.azure.com



## Propriétés du pool de bases de données élastiques

| Propriété | Description |
| :-- | :-- |
| creationDate | Date de création du pool. |
| databaseDtuMax | Nombre maximal de DTU qu’une base de données du pool peut utiliser. Ce nombre n’est pas une garantie concernant l’octroi des ressources. Il s’applique à toutes les bases de données du pool. |
| databaseDtuMin | Nombre minimal de DTU garanti pour une base de données du pool. Ce nombre peut être défini sur 0. Il s’applique à toutes les bases de données du pool. Remarque : le produit du nombre de bases de données du pool et du nombre minimal de DTU ne peut pas dépasser la quantité de DTU du pool lui-même. |
| Dtu | Nombre de DTU que se partagent toutes les bases de données dans le pool. |
| edition | Niveau de service du pool. Chaque base de données du pool présente cette édition. |
| elasticPoolId | GUID de l’instance du pool. |
| elasticPoolName | Nom du pool. Ce nom est unique par rapport au serveur parent correspondant. |
| location | Emplacement du centre de données dans lequel le pool a été créé. |
| state | La valeur correspondante est « Désactivé » si le paiement de la facture d’abonnement est négligé et « Prêt » dans les autres cas. |
| storageMB | Limite de stockage pour le pool (en Mo) Toute base de données unique dans le pool peut utiliser un espace de stockage pouvant atteindre la limite prescrite par la version Standard Edition (250 Go), mais le total de stockage utilisé par toutes les bases de données du pool ne peut pas dépasser la limite de ce pool. |


## Limites relatives aux DTU et au stockage pour les pools et bases de données élastiques

La limite de stockage du pool est déterminée par le nombre de DTU de ce dernier. Chaque DTU égale un espace de stockage d’1 Go. Ainsi, un pool de 200 DTU présente une limite de stockage de 200 Go.

| Propriété | Valeur par défaut | Valeurs valides |
| :-- | :-- | :-- |
| Dtu | 200 | 200, 400, 800, 1200 |
| databaseDtuMax | 100 | 10, 20, 50, 100 |
| databaseDtuMin | 0 | 0, 10, 20, 50 |
| storageMB | 200 Go* | 200 Go, 400 Go, 800 Go, 1 200 Go |

*Les unités des API sont exprimées en Mo, et non en Go

## Limites relatives aux sessions et aux ouvriers

Le nombre maximal d’ouvriers et de sessions simultanés pris en charge pour toutes les bases de données d’un pool élastique dépend du paramètre DTU du pool :

| DTU | Nombre maximal d’ouvriers simultanés | Nombre maximal de sessions simultanées |
| :-- | :-- | :-- |
| 200 | 400 | 4 800 |
| 400 | 800 | 9 600 |
| 800 | 1 600 | 19 200 |
| 1 200 | 2 400 | 28 800 |


## Limites relatives à Microsoft Azure Resource Manager

Un pool élastique nécessite un serveur de base de données SQL Microsoft Azure version 12. Les serveurs sont situés au sein d’un groupe de ressources.

- Chaque groupe de ressources peut inclure un maximum de 800 serveurs.
- Chaque serveur peut présenter un maximum de 800 pools élastiques.
- Chaque pool élastique peut inclure un maximum de 100 bases de données.


## Latence des opérations du pool élastique

- En général, le processus de modification du nombre de DTU garanti à chaque base de données (paramètre databaseDtuMin) ou du nombre maximal de DTU pour chaque base de données (paramètre databaseDtuMax) prend 5 minutes au maximum. 
- Le processus de modification du nombre de DTU/de la limite de stockage (paramètre storageMB) dépend quant à lui de la quantité totale d’espace utilisé par toutes les bases de données du pool. Ce processus prend en moyenne 90 minutes au maximum, pour chaque tranche de 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données du pool est égal à 200 Go, une opération de modification de la limite de stockage/DTU du pool prend 3 heures au maximum. 



## Applets de commande PowerShell et commandes d’API REST du pool de bases de données élastiques (Microsoft Azure Resource Manager uniquement)

Les applets de commande PowerShell et commandes d’API REST ci-après permettent de créer et de gérer des pools élastiques :

| [Applets de commande PowerShell](https://msdn.microsoft.com/library/mt125356.aspx) | [Commandes d’API REST](https://msdn.microsoft.com/library/azure/mt163571.aspx) |
| :-- | :-- |
| Get-AzureSqlDatabase | Get Azure SQL database |
| Get-AzureSqLElasticPool | Get Azure SQL Database elastic database pool |
| Get-AzureSqlElasticPoolActivity | Get Azure SQL Database elastic database pool operations |
| Get-AzureSqlElasticPoolDatabase | Get Azure SQL Database elastic database |
| Get-AzureSqlElasticPoolDatabaseActivity | Get Azure SQL Database elastic database operations |
| Get-AzureSqlServer | Get Azure SQL Database server |
| Get-AzureSqlServerFirewallRule | Get Azure SQL Database server firewall rule |
| Get-AzureSqlServerServiceObjective | Get Azure SQL Database server service objective |
| New-AzureSqlDatabase | Create Azure SQL database |
| New-AzureSqlElasticPool | Create Azure SQL Database elastic database pool |
| New-AzureSqlServer | Create Azure SQL Database server |
| New-AzureSqlServerFirewallRule | Create Azure SQL Database server firewall rule) |
| Remove-AzureSqlDatabase | Remove Azure SQL database |
| Remove-AzureSqlElasticPool | Remove Azure SQL Database elastic database pool |
| Remove-AzureSqlServer | Remove Azure SQL Database server |
| Set-AzureSqlDatabase | Set Azure SQL database |
| Set-AzureSqlElasticPool | Set Azure SQL Database elastic database pool |
| Set-AzureSqlServer | Set Azure SQL Database server |
| Set-AzureSqlServerFirewallRule | Set Azure SQL Database server firewall rule |
| Get-Metrics | Get Metrics |


## Informations sur la tarification et la facturation

Les pools de bases de données élastiques sont facturés en fonction des caractéristiques suivantes :

- Un pool élastique est facturé lors de sa création, même s’il n’inclut aucune base de données. 
- Il est facturé toutes les heures. La fréquence de mesure est identique à celle des niveaux de performances associés aux bases de données autonomes. 
- Si un pool élastique est redimensionné en fonction d’un nouveau nombre de DTU, ce pool n’est pas facturé selon ce nouveau nombre, tant que l’opération de redimensionnement n’est pas terminée. Le processus est le même que lors de la modification du niveau de performances des bases de données autonomes. 


- Le prix d’un pool élastique est basé sur le nombre de DTU de ce pool, ainsi que le nombre de bases de données qu’il inclut.
- Voici le calcul appliqué : (nombre de DTU du pool) x (prix de chaque DTU) + (nombre de bases de données) x (prix de chaque base de données)

Le prix unitaire des DTU d’un pool élastique est supérieur au prix unitaire des DTU d’une base de données autonome au sein du même niveau de service. Pour en savoir plus, voir [Tarification de la base de données SQL](http://azure.microsoft.com/pricing/details/sql-database/).

## Erreurs du pool de bases de données élastiques

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX_RESOURCE | Le pool élastique a atteint sa limite de stockage. Le taux d’utilisation du stockage pour le pool élastique ne doit pas dépasser (%d) Mo. | Limite de l’espace du pool élastique, en Mo. | Tentative d’écriture de données dans une base de données alors que la limite de stockage du pool élastique a été atteinte. | Envisagez d’augmenter le nombre de DTU du pool élastique, le cas échéant, afin d’accroître sa limite de stockage, de réduire l’espace de stockage utilisé par les bases de données individuelles qu’il inclut, ou de supprimer les bases de données dans ce dernier. |
| 10929 | EX_USER | La garantie minimale de %s est %d ; la limite maximale est de %d et le taux d’utilisation actuel de la base de données est de %d. Toutefois, le serveur est trop occupé pour prendre en charge les requêtes supérieures à %d pour cette base de données. Voir [https://msdn.microsoft.com/library/azure/dn338078.aspx](http://go.microsoft.com/fwlink/?LinkId=267637) pour obtenir de l’aide. Sinon, réessayez plus tard. | Nombre minimal de DTU par base de données ; nombre maximal de DTU par base de données. | Le nombre total d’ouvriers simultanés (demandes) dans toutes les bases de données du pool élastique a failli dépasser la limite du pool. | Envisagez d’augmenter le nombre de DTU du pool élastique, le cas échéant, afin d’accroître la limite d’ouvriers associés, ou de supprimer des bases de données du pool élastique. |
| 40844 | EX_USER | La base de données '%ls' sur le serveur '%ls' est une base de données présentant l’édition '%ls' dans un pool élastique. Elle ne peut pas présenter de relation de copie continue. | Nom de la base de données, édition de la base de données, nom du serveur | Une commande StartDatabaseCopy est émise pour une base de données non-Premium dans un pool élastique. | Bientôt disponible |
| 40857 | EX_USER | Pool élastique introuvable pour le serveur : '%ls'. Nom du pool élastique: '%ls'. | Nom du serveur, nom du pool élastique | Le pool élastique spécifié n’existe pas sur le serveur spécifié. | Saisissez un nom de pool élastique valide. |
| 40858 | EX_USER | Le pool élastique '%ls' existe déjà sur le serveur : '%ls'. | Nom du pool élastique, nom du serveur | Le pool élastique spécifié existe déjà sur le serveur logique spécifié. | Saisissez un nouveau nom pour le pool élastique. |
| 40859 | EX_USER | Le pool élastique ne prend pas en charge le niveau de service '%ls'. | Niveau de service du pool élastique | Le niveau de service spécifié n’est pas pris en charge pour le provisioning du pool élastique. | Saisissez l’édition correcte, ou laissez le champ du niveau de service vide afin d’utiliser le niveau de service par défaut. |
| 40860 | EX_USER | La combinaison du pool élastique '%ls' et de l’objectif de service '%ls' n’est pas valide. | Nom du pool élastique, nom de l’objectif de niveau de service | L’objectif de service et le pool élastique peuvent être spécifiés ensemble uniquement si le premier est indiqué en tant que « ElasticPool ». | Spécifiez la combinaison de pool élastique et d’objectif de service adéquate. |
| 40861 | EX_USER | L’édition de base de données '%.*ls' ne peut pas être différente du niveau de service du pool élastique, qui correspond à '%.*ls'. | Édition de base de données, niveau de service du pool élastique | L’édition de base de données est différente du niveau de service du pool élastique. | Veuillez indiquer une édition de base de données identique au niveau de service du pool élastique. Remarque : l’édition de base de données n’a pas besoin d’être spécifiée. |
| 40862 | EX_USER | Si l’objectif de service du pool élastique est spécifié, le nom du pool élastique doit l’être également. | Aucun | L’objectif de service du pool élastique n’identifie pas de manière unique un pool élastique. | Si vous utilisez l’objet de service du pool élastique, spécifiez le nom du pool élastique. |
| 40864 | EX_USER | Le nombre de DTU du pool élastique doit se monter au minimum à (%d) pour le niveau de service '%.*ls'. | Nombre de DTU du pool élastique, niveau de service du pool élastique | Tentative de définition du nombre de DTU du pool élastique au-dessous de la limite minimale. | Essayez à nouveau de définir le nombre de DTU du pool élastique sur la limite minimale ou plus. |
| 40865 | EX_USER | Le nombre de DTU du pool élastique ne doit pas dépasser (%d) pour le niveau de service '%.*ls'. | Nombre de DTU du pool élastique, niveau de service du pool élastique | Tentative de définition du nombre de DTU du pool élastique au-dessus de la limite maximale. | Essayez à nouveau de définir le nombre de DTU du pool élastique sur la limite maximale ou moins. |
| 40867 | EX_USER | Le nombre maximal de DTU par base de données doit être au minimum (%d) pour le niveau de service '%.*ls'. | Nombre maximal de DTU par base de données, niveau de service du pool élastique | Tentative de définition du nombre maximal de DTU par base de données en dessous de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. |
| 40868 | EX_USER | Le nombre maximal de DTU par base de données ne peut pas dépasser (%d) pour le niveau de service '%.*ls'. | Nombre maximal de DTU par base de données, niveau de service du pool élastique | Tentative de définition du nombre maximal de DTU par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. |
| 40870 | EX_USER | Le nombre minimal de DTU par base de données ne peut pas dépasser (%d) pour le niveau de service '%.*ls'. | Nombre minimal de DTU par base de données, niveau de service du pool élastique | Tentative de définition du nombre minimal de DTU par base de données au-delà de la limite prise en charge. | Envisagez d’utiliser le niveau de service du pool élastique prenant en charge le paramètre souhaité. |
| 40873 | EX_USER | Le nombre de bases de données (%d) et le nombre minimal de DTU par base de données (%d) ne peuvent pas dépasser le nombre de DTU du pool élastique (%d). | Nombre de bases de données dans le pool élastique ; nombre minimal de DTU par base de données ; DTU du pool élastique | Tentative de spécification d’un nombre minimal de DTU pour les bases de données dans le pool élastique dépassant le nombre de DTU du pool élastique. | Envisagez d’augmenter le nombre de DTU du pool élastique, ou de réduire le nombre minimal de DTU par base de données ou le nombre de bases de données dans le pool élastique. |
| 40877 | EX_USER | Impossible de supprimer un pool élastique, sauf s’il ne contient aucune base de données. | Aucun | Le pool élastique contient une ou plusieurs bases de données et ne peut donc pas être supprimé. | Supprimez les bases de données du pool élastique afin de pouvoir supprimer ce dernier. |
| 40881 | EX_USER | Le pool élastique '%.*ls' a atteint le nombre limite de bases de données. Le nombre limite de bases de données ne peut pas dépasser (%d) pour un pool élastique incluant (%d) DTU. | Nom du pool élastique ; nombre limite de bases de données du pool élastique ; DTU du pool de ressources. | Tentative de création ou d’ajout d’une base de données au pool élastique alors que le nombre limite de bases de données du pool élastique a été atteint. | Envisagez d’augmenter le nombre de DTU du pool élastique, le cas échéant, afin d’accroître le nombre limite de bases de données, ou de supprimer des bases de données du pool élastique. |
| 40889 | EX_USER | La limite de stockage ou relative aux DTU pour le pool élastique '%.*ls' ne peut pas être abaissée, car l’espace de stockage risque de ne pas être suffisant pour les bases de données de ce pool. | Nom du pool élastique. | Tentative de définition de la limite de stockage du pool élastique en dessous du taux d’utilisation de son espace de stockage. | Envisagez de réduire le taux d’utilisation du stockage des bases de données dans le pool élastique, ou de supprimer des bases de données dans le pool afin de réduire sa limite de stockage ou relative aux DTU. |
| 40891 | EX_USER | Le nombre minimal de DTU par base de données (%d) ne peut pas dépasser le nombre maximal de DTU par base de données (%d). | Nombre minimal de DTU par base de données ; nombre maximal de DTU par base de données | Tentative de définition d’un nombre minimal de DTU par base de données supérieur au nombre maximal de DTU par base de données. | Vérifiez que le nombre minimal de DTU par base de données ne dépasse pas le nombre maximal de DTU par base de données. |
| TBD | EX_USER | La taille de l’espace de stockage d’une base de données individuelle dans un pool élastique ne peut pas dépasser la taille maximale autorisée par le pool élastique de niveau de service '%.*ls'. | Niveau de service du pool élastique | La taille maximale de la base de données dépasse la taille maximale autorisée par le niveau de service du pool élastique. | Définissez la taille maximale de la base de données dans les limites de la taille maximale autorisée par le niveau de service du pool élastique. |

<!---HONumber=58-->