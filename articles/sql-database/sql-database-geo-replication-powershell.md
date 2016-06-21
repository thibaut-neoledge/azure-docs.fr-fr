<properties 
    pageTitle="Configurer la géo-réplication active pour base de données SQL Azure avec PowerShell | Microsoft Azure" 
    description="Configurer la géoréplication active pour Base de données SQL Azure avec PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/27/2016"
    ms.author="sstein"/>

# Configurer la géoréplication pour Base de données SQL Azure avec PowerShell



> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Cet article vous montre comment configurer la géoréplication pour Base de données SQL à l’aide de PowerShell.

Pour lancer un basculement, consultez [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] La géo-réplication active (bases de données secondaires accessibles en lecture) est désormais disponible pour toutes les bases de données de tous les niveaux de service. En avril 2017 sera retiré le type secondaire non accessible en lecture et les bases de données non accessibles en lecture deviendront automatiquement des bases de données secondaires accessibles en lecture.

Vous pouvez configurer jusqu'à 4 bases de données secondaires accessibles en lecture dans des emplacements de centres de données identiques ou différents (régions). Les bases de données secondaires sont disponibles en cas d’indisponibilité d’un centre de données ou l’incapacité à se connecter à la base de données primaire.

Pour configurer la géoréplication, vous devez disposer des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **COMPTE GRATUIT** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure : base de données primaire que vous souhaitez répliquer vers une autre région géographique.
- Azure PowerShell 1.0 ou version ultérieure. Vous pouvez télécharger et installer les modules Azure PowerShell en suivant la procédure décrite dans [Comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## Configurer vos informations d'identification et sélectionner votre abonnement

Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.


	Login-AzureRmAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement. Vous pouvez copier l’identifiant d’abonnement à partir des informations affichées à l’étape précédente ou, si vous disposez de plusieurs abonnements et avez besoin de plus de détails, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copier les informations d’abonnement souhaitées affichées dans les résultats. L’applet de commande suivante utilise l’identifiant d’abonnement pour définir l’abonnement actuel :

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté **Select-AzureRmSubscription**, vous êtes redirigé vers l’invite PowerShell.


## Ajout d'une base de données secondaire


Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat de géoréplication.
  
Pour activer une base de données secondaire, vous devez être le propriétaire de l’abonnement ou le copropriétaire.

Vous pouvez utiliser l’applet de commande **New-AzureRmSqlDatabaseSecondary** pour ajouter une base de données secondaire sur un serveur partenaire à une base de données locale sur le serveur auquel vous êtes connecté (base de données primaire).

Cette applet de commande remplace **Start-AzureSqlDatabaseCopy** avec le paramètre **–IsContinuous**. Elle génère en sortie un objet **AzureRmSqlDatabaseSecondary** qui peut être utilisé par d’autres applets de commande pour identifier clairement un lien de réplication spécifique. Cette applet de commande est renvoyée lorsque la base de données secondaire est créée et entièrement amorcée. Notez que, selon la taille de la base de données, l’opération peut prendre de quelques minutes à plusieurs heures.

La base de données répliquée sur le serveur secondaire aura le même nom que la base de données sur le serveur primaire et aura, par défaut, le même niveau de service. La base de données secondaire peut être accessible en lecture ou non, et il peut s’agir d’une base de données unique ou d’une base de données élastique. Pour plus d’informations, consultez [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) et [Niveaux de Service](sql-database-service-tiers.md). Une fois la base de données secondaire créée et amorcée, une réplication asynchrone des données de la base de données primaire vers la base de données secondaire commence. Les étapes ci-dessous décrivent comment accomplir cette tâche à l’aide de PowerShell pour créer des bases de données secondaires, accessibles en lecture ou non, avec une base de données unique ou une base de données élastique.

Si la base de données partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géoréplication précédente), la commande échoue.



### Ajouter une base de données secondaire non accessible en lecture (base de données unique)

La commande suivante crée un réplica secondaire non accessible en lecture de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### Ajouter une base de données secondaire accessible en lecture (base de données unique)

La commande suivante crée un réplica secondaire accessible en lecture de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Ajouter une base de données secondaire non accessible en lecture (base de données élastique)

La commande suivante crée un réplica secondaire non accessible en lecture de la base de données « mydb » dans le pool de la base de données élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### Ajouter un réplica secondaire accessible en lecture (base de données élastique)

La commande suivante crée un réplica secondaire accessible en lecture de la base de données « mydb » dans le pool de la base de données élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Supprimer une base de données secondaire

Utilisez l’applet de commande **Remove-AzureRmSqlDatabaseSecondary** pour mettre définitivement fin au partenariat de réplication entre une base de données secondaire et sa base de données primaire. Après la fin de la relation, la base de données secondaire devient une base de données en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue, cette commande réussit mais la base de données secondaire devient accessible en lecture-écriture une fois la connectivité rétablie. Pour plus d’informations, consultez [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) et [Niveaux de Service](sql-database-service-tiers.md).

Cette applet de commande remplace Stop-AzureSqlDatabaseCopy pour la réplication.

Cette suppression équivaut à un arrêt forcé qui supprime le lien de réplication et transforme l’ancienne base de données secondaire en base de données autonome qui n’est pas entièrement répliquée avant l’arrêt. Toutes les données de liaison seront supprimées sur le réplica de l’ancienne base de données primaire et de l’ancienne base de données secondaire, si ou lorsqu’elles sont disponibles. Cette applet de commande est renvoyée lorsque le lien de réplication est supprimé.


Pour supprimer la base de données secondaire, les utilisateurs doivent avoir accès en écriture aux bases de données primaire et secondaire en fonction des rôles. Pour plus de détails, consultez Contrôle d’accès en fonction du rôle.

Ce qui suit permet de supprimer le lien de réplication de la base de données nommée « mydb » pour le serveur « srv2 » du groupe de ressources « rg2 ».

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## Surveillance de la configuration et de l’état de géo-réplication

Les tâches de surveillance incluent la surveillance de la configuration de géo-réplication et la surveillance de l’état de réplication de données.

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) peut être utilisé pour extraire des informations sur les liens de réplication directe visibles dans la vue de catalogue sys.geo\_replication\_links.

La commande suivante récupère l’état du lien de réplication entre la base de données primaire « mydb » et secondaire sur le serveur « srv2 » du groupe de ressources « rg2 ».

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


  

## Étapes suivantes

- [Lancer un basculement planifié ou non planifié pour une base de données SQL Azure](sql-database-geo-replication-failover-powershell.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)




## Ressources supplémentaires

- [Configuration de la sécurité de la géo-réplication](sql-database-geo-replication-security-config.md)
- [Coup de projecteur sur les nouvelles fonctionnalités de géoréplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [FAQ sur la continuité d’activité et la récupération d’urgence des bases de données SQL](sql-database-bcdr-faq.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0608_2016-->