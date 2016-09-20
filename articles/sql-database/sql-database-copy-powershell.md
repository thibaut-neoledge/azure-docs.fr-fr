<properties 
    pageTitle="Copier une base de données SQL Azure à l’aide de PowerShell | Microsoft Azure" 
    description="Création d'une copie d'une base de données SQL Azure à l'aide de PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copier une base de données SQL Azure à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-copy.md)
- [Portail Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Les étapes suivantes vous montrent comment copier une base de données SQL avec PowerShell sur le même serveur ou un serveur différent. L’opération de copie de la base de données utilise l’applet de commande [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx).


Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **VERSION D’ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en procédant de la manière décrite dans [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- Azure PowerShell. Vous pouvez télécharger et installer les modules Azure PowerShell en exécutant [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).



## Copie de votre base de données SQL

Il existe quelques variables pour lesquelles vous devez remplacer les valeurs d'exemple par les valeurs spécifiques de vos serveurs et de votre base de données. Remplacez les valeurs d'espace réservé par les valeurs de votre environnement :

    # The name of the server on which the source database resides.
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy). 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server that hosts the target database. This server must be in the same Azure subscription as the source database server. 
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy).
    $PartnerDatabaseName = "partnerDatabaseName" 





### Copie d'une base de données SQL sur le même serveur

Cette commande envoie la demande de copie de la base de données au service. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    # Copy a database to the same server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerDatabase $PartnerDatabaseName

Utilisation des applets de commande d’Azure Resource Manager :

    # Copy a database to the same server
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -CopyDatabaseName $PartnerDatabaseName

### Copie d'une base de données SQL vers un autre serveur

Cette commande envoie la demande de copie de la base de données au service. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
Utilisation des applets de commande d’Azure Resource Manager :

    # Copy a database to a different server
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -CopyServerName $PartnerServerName -CopyDatabaseName $PartnerDatabaseName

## Contrôle de la progression de l'opération de copie

Après l'exécution de **Start-AzureSqlDatabaseCopy**, vous pouvez vérifier l'état de la demande de copie. En cas d'exécution immédiatement après la demande, cela renvoie généralement **État : En attente** ou **État : En cours d'exécution**. Vous pouvez donc exécuter cette opération plusieurs fois jusqu'à ce que vous voyiez **État : TERMINÉ** en sortie.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName

## Résolution des connexions

Pour résoudre les connexions à l’issue de l’opération de copie, consultez [Résoudre les connexions](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## Exemple de script PowerShell

    # The name of the server where the source database resides
    $ServerName = "sourceServerName"

    # The name of the source database (the database to copy) 
    $DatabaseName = "sourceDatabaseName" 
    
    # The name of the server to host the database copy. This server must be in the same Azure subscription as the source database server
    $PartnerServerName = "partnerServerName"

    # The name of the target database (the name of the copy)
    $PartnerDatabaseName = "partnerDatabaseName" 


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionName "myAzureSubscriptionName"
      
    # Copy a database to a different server (remove the -PartnerServer parameter to copy to the same server)
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    
    # Monitor the status of the copy
    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName
    

## Étapes suivantes

- Consultez la page [Copie d’une base de données SQL Azure](sql-database-copy.md) pour une vue d’ensemble de la copie d’une base de données SQL Azure.
- Consultez la page [Copier une base de données SQL Azure à l’aide du portail Azure](sql-database-copy-portal.md) pour copier une base de données à l’aide du portail Azure.
- Consultez la page [Copier une base de données SQL Azure à l’aide de T-SQL](sql-database-copy-transact-sql.md) pour copier une base de données à l’aide de Transact-SQL.
- Consultez la page [Gestion de la sécurité d’une base de données SQL Azure après la récupération d’urgence](sql-database-geo-replication-security-config.md) pour en savoir plus sur la gestion des utilisateurs et des connexions lors de la copie d’une base de données vers un autre serveur logique.


## Ressources supplémentaires

- [Gérer les connexions](sql-database-manage-logins.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données vers un fichier BACPAC](sql-database-export.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0907_2016-->