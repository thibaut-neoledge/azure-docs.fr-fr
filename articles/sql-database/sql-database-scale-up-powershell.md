<properties 
    pageTitle="Modification des niveaux de service et de performances d’une base de données SQL Azure à l’aide de PowerShell" 
    description="Cet article explique comment faire monter ou descendre en puissance une base de données SQL avec PowerShell. Modification du niveau de tarification d’une base de données SQL Azure avec PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modification des niveaux de service et de performances (niveau de tarification) d’une base de données SQL avec PowerShell

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Cet article explique comment modifier les niveaux de service et de performances de votre base de données SQL à l’aide de PowerShell.

Exploitez les informations des sections [Mise à jour des bases de données SQL des éditions Web ou Business vers les nouveaux niveaux de service](sql-database-upgrade-new-service-tiers.md) et [Niveaux de service et de performance de base de données SQL Azure](sql-database-service-tiers.md) pour déterminer le niveau de service et de performances adéquat pour votre base de données SQL Microsoft Azure.

> [AZURE.IMPORTANT]La modification des niveaux de service et de performances d’une base de données SQL s’effectue en ligne. Cela signifie que votre base de données doit être en ligne et accessible pendant toute la durée de l’opération, sans interruption de service.

- Pour qu’une base de données puisse passer à une version antérieure, sa taille doit être inférieure à la taille maximale autorisée par le niveau de service voulu. 
- Lors de la mise à niveau d’une base de données avec la [géo-réplication standard](https://msdn.microsoft.com/library/azure/dn758204.aspx) ou la [géo-réplication active](https://msdn.microsoft.com/library/azure/dn741339.aspx), vous devez d'abord mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant la mise à niveau de la base de données principale.
- Avant d’effectuer le passage à une version antérieure depuis un niveau de service Premium, vous devez arrêter toutes les relations de géo-réplication. Vous pouvez suivre les étapes décrites dans la section [Arrêt d’une relation de copie continue](https://msdn.microsoft.com/library/azure/dn741323.aspx) pour arrêter le processus de réplication entre la base de données principale et les bases de données secondaires actives.
- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour en savoir plus, voir [Sauvegarde et restauration de base de données SQL Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Vous pouvez apporter jusqu’à quatre modifications à une base de données individuelle (concernant un niveau de service ou de performances) par période de 24 heures.
- Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.



**Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :**

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en procédant de la manière décrite dans [Créer votre première base de données SQL Azure](sql-database-get-started.md).
- Azure PowerShell.


Pour exécuter les applets de commande PowerShell, Azure PowerShell doit être installé et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).



## Configurer vos informations d'identification et sélectionner votre abonnement

Tout d'abord, vous devez établir l'accès à votre compte Azure : lancez PowerShell, puis exécutez l’applet de commande suivante. Sur l’écran de connexion, saisissez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

	Add-AzureRmAccount

Après vous être connecté, vous voyez des informations sur l’écran, notamment l’ID avec lequel vous vous êtes connecté et les abonnements Azure auxquels vous avez accès.


### Sélectionner votre abonnement Azure

Pour sélectionner l’abonnement, vous avez besoin de votre ID d’abonnement ou votre nom d’abonnement (**-SubscriptionName**). Vous pouvez copier l’identifiant d’abonnement à partir des informations affichées à l’étape précédente ou, si vous avez plusieurs abonnements et besoin de plus de détails, vous pouvez exécuter l’applet de commande **Get-AzureSubscription** et copier les informations d’abonnement souhaitées affichées dans les résultats. Une fois votre abonnement sélectionné, exécutez l'applet de commande suivante :

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId

Après avoir exécuté **Select-AzureSubscription**, vous êtes redirigé vers l’invite PowerShell. Si vous avez plusieurs abonnements, vous pouvez exécuter **Get-AzureSubscription** et vérifier que l’abonnement que vous voulez utiliser affiche **IsCurrent: True**.


 


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
- [Se connecter à une base de données SQL et l’interroger avec SSMS](sql-database-connect-query-ssms.md)
- [Exporter une base de données SQL Azure](sql-database-export-powershell.md)

## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Applets de commande de la base de données SQL Azure.](https://msdn.microsoft.com/library/azure/mt163521.aspx)

<!---HONumber=AcomDC_1210_2015-->