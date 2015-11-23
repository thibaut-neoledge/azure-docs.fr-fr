<properties 
    pageTitle="Configurer la géo-réplication pour la base de données SQL Azure avec PowerShell | Microsoft Azure" 
    description="géo-réplication active pour la base de données SQL Azure avec PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="11/10/2015"
    ms.author="sstein"/>

# Configurez la géo-réplication pour la base de données SQL Azure avec PowerShell



> [AZURE.SELECTOR]
- [Azure preview portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Cet article vous montre comment configurer la géo-réplication pour une base de données SQL à l’aide de PowerShell.

La géo-réplication permet de créer jusqu’4 réplicas de bases de données (secondaires) dans différents centres de données (régions). Les bases de données secondaires sont disponibles en cas d’indisponibilité d’un centre de données ou l’incapacité à se connecter à la base de données primaire.

La géo-réplication graphique est uniquement disponible pour les bases de données Standard et Premium.

Les bases de données standard peuvent avoir un serveur secondaire non accessible en lecture et doivent utiliser la région recommandée. Les bases de données Premium peuvent générer jusqu’à quatre réplicas secondaires dans des régions disponibles.

Pour configurer la géo-réplication, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure, la base de données primaire que vous souhaitez répliquer vers une autre région géographique.
- Version préliminaire 1.0 d’Azure PowerShell. Vous pouvez télécharger et installer les modules Azure PowerShell en suivant la procédure décrite dans [Comment installer et configurer Azure PowerShell](powershell-install-configure.md).

> [AZURE.IMPORTANT]À compter de la publication de la version préliminaire d’Azure PowerShell 1.0, l’applet de commande Switch-AzureMode n’est plus disponible, et les applets de commande présentes dans le module Azure ResourceManger ont été renommées. Les exemples de cet article utilisent les nouvelles conventions d’affectation de noms de la version préliminaire de PowerShell 1.0. Pour plus d’informations, consultez [Désapprobation de Switch-AzureMode dans Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).





## Configurer vos informations d'identification et sélectionner votre abonnement

Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.


	Login-AzureRmAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement. Vous pouvez copier l’identifiant d’abonnement à partir des informations affichées à l’étape précédente ou, si vous disposez de plusieurs abonnements et avez besoin de plus de détails, vous pouvez exécuter l’applet de commande **Get-AzureRmSubscription** et copier les informations d’abonnement souhaitées affichées dans les résultats. L’applet de commande suivante utilise l’identifiant d’abonnement pour définir l’abonnement actuel :

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Après avoir exécuté **Select-AzureRmSubscription**, vous êtes redirigé vers l’invite PowerShell.



## Ajouter une base de données secondaire


Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat géo-réplication.
  
Pour activer une base de données secondaire, vous devez être le propriétaire de l’abonnement ou le copropriétaire.

Vous pouvez utiliser l’applet de commande **New-AzureRmSqlDatabaseSecondary** pour ajouter une base de données secondaire sur un serveur partenaire à une base de données locale sur le serveur auquel vous êtes connecté (base de données primaire).

Cette applet de commande remplace **Start-AzureSqlDatabaseCopy** avec le paramètre **– IsContinuous**. Elle génère en sortie un objet **AzureRmSqlDatabaseSecondary** qui peut être utilisé par d’autres applets afin d’identifier clairement un lien de réplication spécifique. Cette applet de commande est renvoyée lorsque la base de données secondaire est créée et entièrement amorcée. Notez que selon la taille de la base de données, l’opération peut prendre quelques minutes à plusieurs heures.

La base de données répliquée sur le serveur secondaire aura le même nom que la base de données sur le serveur primaire et aura, par défaut, le même niveau de service. La base de données secondaire peut être accessible en lecture ou non, et il peut s’agir d’une base de données unique ou une base de données élastique. Pour plus d’informations, consultez [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) et [niveaux de Service](sql-database-service-tiers.md). Une fois la seconde base de données secondaire créée et amorcée, les données vont commencer une réplication asynchrone de la base de données primaire vers la base de données secondaire. Les étapes ci-dessous décrivent comment accomplir cette tâche à l’aide de PowerShell pour créer des bases de données secondaires, accessibles en lecture ou non, avec une base de données unique ou une base de données élastique.

Si la base de données de partenaire existe déjà (par exemple, suite à l’arrêt d’une relation de géo-réplication précédente), la commande échoue.



### Ajouter une base de données secondaire non accessible en lecture (base de données unique)

La commande suivante crée un réplica secondaire non accessible en lecture de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "None"



### Ajouter une base de données secondaire accessible en lecture (base de données unique)

La commande suivante crée un réplica secondaire accessible en lecture de la base de données « mydb » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Ajouter une base de données secondaire non accessible en lecture (base de données élastique)

La commande suivante crée un réplica secondaire non accessible en lecture de la base de données « mydb » dans le pool de la base de données élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "None"


### Ajouter un réplica secondaire accessible en lecture (base de données élastique)

La commande suivante crée un réplica secondaire accessible en lecture de la base de données « mydb » dans le pool de la base de données élastique nommé « ElasticPool1 » du serveur « srv2 » dans le groupe de ressources « rg2 » :

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Supprimer une base de données secondaire

Utilisez l’applet de commande **Remove-AzureRmSqlDatabaseSecondary** pour mettre définitivement fin au partenariat de réplication entre une base de données secondaire et sa base de données primaire. Après la fin de la relation, la base de données secondaire devient une base de données en lecture-écriture. Si la connectivité à la base de données secondaire est interrompue, cette commande réussit mais la base de données secondaire devient accessible en lecture-écriture une fois la connectivité rétablie. Pour plus d’informations, consultez [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) et [niveaux de Service](https://azure.microsoft.com/documentation/articles/sql-database-service-tiers/).

Cette applet de commande remplace Stop-AzureSqlDatabaseCopy pour la réplication.

Cette suppression équivaut à un arrêt forcé qui supprime le lien de réplication et transforme l’ancienne base de données secondaire en base de données autonome qui n’est pas entièrement répliquée avant l’arrêt. Toutes les données de liaison seront supprimées sur le réplica de l’ancienne base de données primaire et de l’ancienne base de données secondaire, si ou lorsqu’elles sont disponibles. Cette applet de commande est renvoyée lorsque le lien de réplication est supprimé.


Pour supprimer la base de données secondaire, les utilisateurs doivent avoir accès en écriture aux bases de données primaire et secondaire en fonction des rôles. Consultez la section Contrôle d’accès basé sur les rôles pour plus d’informations.

Ce qui suit permet de supprimer le lien de réplication de la base de données nommée « mydb » pour le serveur « srv2 » du groupe de ressources « rg2 ».

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 




## Initier un basculement planifié

Utilisez l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec le paramètre **- Failover ** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire, la rétrogradation la base de données primaire existante pour qu’elle se transforme en base de données secondaire. Cette fonctionnalité est conçue pour un basculement planifié, comme au cours des exercices de récupération d’urgence et exige que la base de données primaire soit disponible.

La commande exécute le flux de travail suivant :

1. Basculer temporairement la réplication en mode synchrone. Les transactions en attente seront alors vidées sur la base de données secondaire.

2. Changer les rôles des deux bases de données dans le partenariat de géo-réplication.

Cette séquence garantit qu’aucune perte de données n’aura lieu. Il existe une courte période pendant laquelle les deux bases de données ne sont pas disponibles (de l’ordre de 0 à 25 secondes) pendant que les rôles sont activés. Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales. Pour plus d’informations, consultez [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).


> [AZURE.NOTE]Si la base de données primaire n’est pas disponible lorsque la commande est émise, elle échoue avec un message d’erreur indiquant que le serveur principal n’est pas disponible. Dans de rares cas, il est possible que l’opération ne puisse pas se terminer et apparaisse bloquée. Dans ce cas, l’utilisateur peut appeler la commande de basculement forcé (basculement non planifié) et accepter une perte de données.



Cette applet de commande sera renvoyée lorsque le processus de basculement de la base de données secondaire vers la primaire sera terminé.

La commande suivante bascule les rôles de la base de données nommée « mydb » sur le serveur « srv2 » sous le groupe de ressources « rg2 » vers la base de données primaire. La base de données primaire d’origine à laquelle « db2 » a été connectée bascule sur la base de données secondaire une fois que les deux bases de données sont entièrement synchronisées.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover



## Toute l’opération devrait prendre moins d’une minute pour se terminer dans des circonstances normales


Vous pouvez utiliser l’applet de commande **Set-AzureRmSqlDatabaseSecondary** avec les paramètres **– Failover ** et **- AllowDataLoss** pour promouvoir une base de données secondaire afin qu’elle devienne la nouvelle base de données primaire de manière non planifiée, en forçant la rétrogradation de la base de données primaire existante vers la base de données secondaire lorsque la base de données primaire n’est plus disponible.

Cette fonctionnalité est conçue pour la récupération d’urgence lorsque la restauration de la disponibilité de la base de données est essentielle et une perte de données est acceptable. Lorsque le basculement forcé est appelé, la base de données secondaire devient la base de données primaire immédiatement et commence à accepter des transactions d’écriture. Dès que la base de données primaire d’origine est en mesure de se reconnecter à la nouvelle base de données primaire après l’opération de basculement forcé, une sauvegarde incrémentielle est effectuée sur la base de données primaire d’origine et l’ancienne base de données primaire est transformée en base de données secondaire de la nouvelle base de données primaire. Par conséquent, il s’agit simplement d’un réplica de la nouvelle base de données primaire.

Mais comme la limite de restauration dans le temps n’est pas prise en charge sur les bases de données secondaires, si vous souhaitez récupérer des données validées dans l’ancienne base de données primaire qui n’avait pas été répliquée sur la nouvelle base de données primaire, vous devez engager CSS pour restaurer une base de données dans la sauvegarde de journal connue.

> [AZURE.NOTE]Si la commande est émise lorsque les deux bases de données primaire et secondaire sont en ligne, l’ancienne base de données primaire devient la nouvelle base de données secondaire, mais la synchronisation des données n’a pas lieu et une perte de données est possible.


Si la base de données primaire compte plusieurs bases de données secondaires, la commande réussit partiellement. La base de données secondaire sur laquelle la commande a été exécutée deviendra la base de données primaire. L’ancienne base de données primaire reste toutefois primaire, en d’autres termes, les deux bases de données primaires sont finalement incompatibles et connectées par un lien de réplication suspendu. L’utilisateur doit alors réparer manuellement cette configuration à l’aide d’une API « suppression de base de données secondaire » sur une de ces bases de données primaires.


La commande suivante bascule les rôles de la base de données nommée « mydb » vers la base de données primaire si le serveur principal n’est pas disponible. La base de données d’origine à laquelle « mydb » était connectée va basculer sur la base de données secondaire une fois qu’elle est revenue est en ligne. À ce stade, la synchronisation peut entraîner une perte de données.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss



## Surveillance de la configuration et de l’état de géo-réplication

Les tâches de surveillance incluent la surveillance de la configuration de géo-réplication et la surveillance de l’état de réplication de données.

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) peut être utilisé pour extraire des informations sur les liens de réplication directe visibles dans la vue de catalogue sys.geo\_replication\_links.

La commande suivante récupère l’état du lien de réplication entre la base de données primaire « mydb » et secondaire sur le serveur « srv2 » du groupe de ressources « rg2 ».

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb”
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”



   

## Étapes suivantes

- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)




## Ressources supplémentaires

- [Coup de projecteur sur les nouvelles fonctionnalités de géo-réplication](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication)
- [Conception d’applications cloud pour la continuité d’activité à l’aide de la géo-réplication](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Nov15_HO3-->