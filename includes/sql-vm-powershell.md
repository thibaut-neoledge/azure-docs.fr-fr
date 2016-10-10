
## Démarrer votre session PowerShell

Tout d’abord, la dernière version [d’Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) doit être installée et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../articles/powershell-install-configure.md).


>[AZURE.NOTE] Les exemples de cette rubrique utilisent le [modèle de déploiement Azure Resource Manager](../articles/resource-group-overview.md), et donc les [applets de commande Azure Resource Manager](http://msdn.microsoft.com/library/azure/mt125356.aspx).

Exécutez l’applet de commande [**Add-AzureRmAccount**](http://msdn.microsoft.com/library/mt619267.aspx) pour faire apparaître un écran de connexion dans lequel vous pouvez entrer vos informations d’identification. Utilisez les informations d’identification dont vous disposez pour vous connecter au portail Azure.

	Add-AzureRmAccount

Si vous possédez plusieurs abonnements, utilisez l’applet de commande [**Set-AzureRmContext**](http://msdn.microsoft.com/library/mt619263.aspx) pour sélectionner l’abonnement que votre session PowerShell doit utiliser. Pour savoir quel abonnement la session PowerShell en cours utilise, exécutez l’applet de commande [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx). Pour voir tous vos abonnements, exécutez l’applet de commande [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx).

	Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

<!---HONumber=AcomDC_0928_2016-->