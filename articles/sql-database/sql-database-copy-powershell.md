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
	ms.date="03/21/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Copier une base de données SQL Azure à l’aide de PowerShell

**Base de données unique**

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)



Les étapes suivantes vous montrent comment copier une base de données SQL avec PowerShell. L'opération de copie de base de données copie une base de données SQL dans une nouvelle base de données en utilisant l'applet de commande [Start-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720220.aspx). La copie est une sauvegarde instantanée de votre base de données que vous créez sur le même serveur ou sur un autre serveur.

> [AZURE.NOTE] La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur que vous pouvez restaurer, et en assure la maintenance. Pour plus d'informations, consultez [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md).

Lorsque le processus de copie est terminé, la nouvelle base de données est une base de données entièrement fonctionnelle qui est indépendante de la base de données source. La nouvelle base de données est cohérente au niveau transactionnel avec la base de données source au moment où la copie s'achève. Le niveau de service et le niveau de performances (niveau de tarification) de la copie de base de données sont les mêmes que ceux de la base de données source. Une fois la copie terminée, la copie devient une base de données indépendante et entièrement fonctionnelle. Les connexions, les utilisateurs et les autorisations peuvent être gérés indépendamment.


Lorsque vous copiez une base de données sur le même serveur logique, les mêmes connexions peuvent être utilisées sur les deux bases de données. L'élément principal de sécurité que vous utilisez pour copier la base de données devient le propriétaire de la base de données (DBO) sur la nouvelle base de données. Tous les utilisateurs de la base de données, leurs autorisations et leurs identificateurs de sécurité (SID) sont copiés vers la copie de base de données.


Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Une base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans cet article : [Créer votre première base de données SQL Azure](sql-database-get-started.md).
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

### Copie d'une base de données SQL vers un autre serveur

Cette commande envoie la demande de copie de la base de données au service. Selon la taille de votre base de données, l'opération de copie peut prendre plus ou moins longtemps.

    # Copy a database to a different server
    Start-AzureSqlDatabaseCopy -ServerName $ServerName -DatabaseName $DatabaseName -PartnerServer $PartnerServerName -PartnerDatabase $PartnerDatabaseName
    

## Contrôle de la progression de l'opération de copie

Après l'exécution de **Start-AzureSqlDatabaseCopy**, vous pouvez vérifier l'état de la demande de copie. En cas d'exécution immédiatement après la demande, cela renvoie généralement **État : En attente** ou **État : En cours d'exécution**. Vous pouvez donc exécuter cette opération plusieurs fois jusqu'à ce que vous voyiez **État : TERMINÉ** en sortie.


    Get-AzureSqlDatabaseOperation -ServerName $ServerName -DatabaseName $DatabaseName


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

- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)
- [Exporter la base de données vers un fichier BACPAC](sql-database-export-powershell.md)


## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Exercices de récupération d'urgence](sql-database-disaster-recovery-drills.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0601_2016-->