---
title: "Configurer la géoréplication active pour Azure SQL Database avec PowerShell | Documents Microsoft"
description: "Configurer la géoréplication active pour Azure SQL Database avec PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: bc5e50e4-bbb2-4ce1-9ee5-9a632de6fa06
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/14/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: 85efdb2a5b9571d76338aeb0871b729693b63dcb
ms.lasthandoff: 02/16/2017


---
# <a name="configure-active-geo-replication-for-azure-sql-database-with-powershell"></a>Configurer la géoréplication active pour Azure SQL Database avec PowerShell

Cet article vous montre comment configurer la géoréplication active pour SQL Database à l’aide de PowerShell.

Pour lancer un basculement avec PowerShell, consultez [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure avec PowerShell](sql-database-geo-replication-failover-powershell.md).

> [!NOTE]
> La géo-réplication active (bases de données secondaires accessibles en lecture) est désormais disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017, le type secondaire non accessible en lecture sera retiré et les bases de données non accessibles en lecture deviendront automatiquement des bases de données secondaires accessibles en lecture.
> 
> 

Pour configurer la géoréplication active à l’aide de PowerShell, vous devez disposer des éléments suivants :

* Un abonnement Azure. 
* Une base de données SQL Azure : la base de données primaire que vous souhaitez répliquer.
* Azure PowerShell 1.0 ou version ultérieure. Vous pouvez télécharger et installer les modules Azure PowerShell en suivant la procédure décrite dans [Comment installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurer vos informations d'identification et sélectionner votre abonnement
Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

    Login-AzureRmAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.

### <a name="select-your-azure-subscription"></a>Sélectionner votre abonnement Azure
Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement. Vous pouvez copier l’identifiant d’abonnement à partir des informations affichées à l’étape précédente ou, si vous disposez de plusieurs abonnements et avez besoin de plus de détails, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copier les informations d’abonnement souhaitées affichées dans les résultats. L’applet de commande suivante utilise l’identifiant d’abonnement pour définir l’abonnement actuel :

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté **Select-AzureRmSubscription**, vous êtes redirigé vers l’invite PowerShell.

## <a name="add-secondary-database"></a>Ajout d'une base de données secondaire
Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat de géoréplication.  

Pour activer une base de données secondaire, vous devez être le propriétaire de l’abonnement ou le copropriétaire. 

Vous pouvez utiliser l’applet de commande **New-AzureRmSqlDatabaseSecondary** pour ajouter une base de données secondaire sur un serveur partenaire à une base de données locale sur le serveur auquel vous êtes connecté (base de données primaire). 

Cette applet de commande remplace **Start-AzureSqlDatabaseCopy** par le paramètre **-IsContinuous**.  Elle génère en sortie un objet **AzureRmSqlDatabaseSecondary** qui peut être utilisé par d’autres applets de commande pour identifier clairement un lien de réplication spécifique. Cette applet de commande est renvoyée lorsque la base de données secondaire est créée et entièrement amorcée. Notez que, selon la taille de la base de données, l’opération peut prendre de quelques minutes à plusieurs heures.

La base de données répliquée sur le serveur secondaire aura le même nom que la base de données sur le serveur primaire et aura, par défaut, le même niveau de service. La base de données secondaire peut être accessible en lecture ou non, et il peut s’agir d’une base de données autonome ou d’une base de données dans un pool élastique. Pour plus d’informations, consultez [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689\(v=azure.300\).aspx) et [Niveaux de service](sql-database-service-tiers.md).
Une fois la base de données secondaire créée et amorcée, une réplication asynchrone des données de la base de données primaire vers la base de données secondaire commence. Les étapes ci-dessous décrivent comment accomplir cette tâche à l’aide de PowerShell pour créer des bases de données secondaires, accessibles en lecture ou non, avec une base de données autonome ou une base de données élastique.

Si la base de données partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géoréplication précédente), la commande échoue.

### <a name="add-a-non-readable-secondary-standalone-database"></a>Ajouter une base de données secondaire non accessible en lecture (base de données autonome)
La commande suivante crée un réplica secondaire non accessible en lecture de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-standalone-database"></a>Ajouter une base de données secondaire accessible en lecture (base de données autonome)
La commande suivante crée un réplica secondaire accessible en lecture de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-pool"></a>Ajouter une base de données secondaire non accessible en lecture (pool élastique)
La commande suivante crée un réplica secondaire non accessible en lecture de la base de données « mydb » dans le pool élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-pool"></a>Ajouter une base de données secondaire accessible en lecture (pool élastique)
La commande suivante crée un réplica secondaire accessible en lecture de la base de données « mydb » dans le pool élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary -PartnerResourceGroupName "rg2" -PartnerServerName "srv2" -SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Supprimer une base de données secondaire
Utilisez l’applet de commande **Remove-AzureRmSqlDatabaseSecondary** pour mettre définitivement fin au partenariat de réplication entre une base de données secondaire et sa base de données primaire. Après la fin de la relation, la base de données secondaire devient une base de données en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue, cette commande réussit mais la base de données secondaire devient accessible en lecture-écriture une fois la connectivité rétablie. Pour plus d’informations, consultez [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457\(v=azure.300\).aspx) et [Niveaux de service](sql-database-service-tiers.md).

Cette applet de commande remplace Stop-AzureSqlDatabaseCopy pour la réplication. 

Cette suppression équivaut à un arrêt forcé qui supprime le lien de réplication et transforme l’ancienne base de données secondaire en base de données autonome qui n’est pas entièrement répliquée avant l’arrêt. Toutes les données de liaison seront supprimées sur le réplica de l’ancienne base de données primaire et de l’ancienne base de données secondaire, si ou lorsqu’elles sont disponibles. Cette applet de commande est renvoyée lorsque le lien de réplication est supprimé. 

Pour supprimer la base de données secondaire, les utilisateurs doivent avoir accès en écriture aux bases de données primaire et secondaire en fonction des rôles. Pour plus de détails, consultez Contrôle d’accès en fonction du rôle.

Ce qui suit permet de supprimer le lien de réplication de la base de données nommée « mydb » pour le serveur « srv2 » du groupe de ressources « rg2 ». 

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -SecondaryResourceGroup "rg2" -PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Surveillance de la configuration et de l’état de géo-réplication
Les tâches de surveillance incluent la surveillance de la configuration de géo-réplication et la surveillance de l’état de réplication de données.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330\(v=azure.300\).aspx) permet d’extraire des informations sur les liens de réplication directe visibles dans la vue de catalogue sys.geo_replication_links.

La commande suivante récupère l’état du lien de réplication entre la base de données primaire « mydb » et secondaire sur le serveur « srv2 » du groupe de ressources « rg2 ».

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink -PartnerResourceGroup "rg2” -PartnerServerName "srv2”


## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la géoréplication active, consultez [Géoréplication active](sql-database-geo-replication-overview.md)
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)


