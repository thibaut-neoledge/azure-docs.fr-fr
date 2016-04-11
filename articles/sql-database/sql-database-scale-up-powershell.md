<properties 
    pageTitle="Modification des niveaux de service et de performances d’une base de données SQL Azure à l’aide de PowerShell" 
    description="Cet article explique comment faire monter ou descendre en puissance une base de données SQL avec PowerShell. Modification du niveau de tarification d’une base de données SQL Azure avec PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modification des niveaux de service et de performances (niveau de tarification) d’une base de données SQL avec PowerShell


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Les niveaux de service et de performances décrivent les fonctionnalités et ressources disponibles pour votre base de données SQL, et peuvent être mis à jour à mesure que les besoins de votre application évoluent. Pour plus d’informations, voir [Niveaux de service](sql-database-service-tiers.md).

Notez que la modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Cette fenêtre de désactivation varie, mais dure moins de 4 secondes en moyenne, et ne dépasse pas 30 secondes dans plus de 99 % des cas. En de très rare occasions, cette durée peut se révéler supérieure, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service Standard ne demande pas plus de 6 heures. Le changement des niveaux de performances d’une base de données de la même taille dans le niveau de service Premium doit s’effectuer en moins de 3 heures.


- Pour qu’une base de données puisse passer à une version antérieure, sa taille doit être inférieure à la taille maximale autorisée par le niveau de service voulu. 
- Lors de la mise à niveau d’une base de données avec la [géo-réplication](sql-database-geo-replication-portal) activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données principale.
- Avant d’effectuer le passage à une version antérieure depuis un niveau de service Premium, vous devez arrêter toutes les relations de géo-réplication. Vous pouvez suivre la procédure décrite à la section [Récupération suite à une indisponibilité de service](sql-database-disaster-recovery.md) pour arrêter le processus de réplication entre la base de données principale et les bases de données secondaires actives.
- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour en savoir plus, voir [Sauvegarde et restauration de base de données SQL Azure](sql-database-business-continuity.md).
- Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.



**Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :**

- Un abonnement Azure. Si vous avez besoin d’un abonnement Azure, cliquez simplement sur **COMPTE GRATUIT** en haut de cette page, puis continuez la lecture de cet article.
- base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en procédant de la manière décrite dans [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- Azure PowerShell.


Pour exécuter les applets de commande PowerShell, Azure PowerShell doit être installé et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).



## Configurer vos informations d'identification et sélectionner votre abonnement

Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Login-AzureRmAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l'abonnement, vous avez besoin de votre identifiant ou de votre nom d'abonnement (**-SubscriptionName**). Vous pouvez copier l'identifiant d'abonnement à partir des informations affichées à l'étape précédente ou, si vous avez plusieurs abonnements et besoin de plus de détails, vous pouvez exécuter l'applet de commande **Get-AzureSubscription** et copier les informations d'abonnement souhaitées affichées dans les résultats. Une fois votre abonnement sélectionné, exécutez l'applet de commande suivante :

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId




## Modification des niveaux de service et de performances de votre base de données SQL

Exécutez l’applet de commande **Set-AzureRmSqlDatabase** et affectez à **-RequestedServiceObjectiveName** le niveau de performances correspondant au niveau tarifaire souhaité. Par exemple *S0*, *S1*, *S2*, *S3*, *P1*, *P2* et ainsi de suite.

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Exemple de script PowerShell pour modifier les niveaux de service et de performances de votre base de données SQL

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Étapes suivantes

- [Faire monter ou descendre en puissance](sql-database-elastic-scale-get-started.md)
- [Se connecter à une base de données SQL et l’interroger avec SSMS](sql-database-connect-query-ssms.md)
- [Exporter une base de données SQL Azure](sql-database-export-powershell.md)

## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Applets de commande de la base de données SQL Azure.](http://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0330_2016-->