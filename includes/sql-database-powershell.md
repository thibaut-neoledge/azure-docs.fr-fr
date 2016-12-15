
## <a name="start-your-powershell-session"></a>Démarrer votre session PowerShell
Tout d’abord, la dernière version [d’Azure PowerShell](https://msdn.microsoft.com/library/mt619274\(v=azure.300\).aspx) doit être installée et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Nombre des nouvelles fonctionnalités de SQL Database ne sont prises en charge que si vous utilisez le [modèle de déploiement Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md). Les exemples cités utilisent donc les [applets de commande PowerShell Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) pour Resource Manager. Les [applets de commande Azure SQL Database](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) de gestion des services du modèle de déploiement de gestion des services existant sont prises en charge à des fins de compatibilité descendante. Cependant, nous vous recommandons l’utilisation d’applets de commande Resource Manager.
> 
> 

Exécutez l’applet de commande [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) pour faire apparaître un écran de connexion dans lequel vous pouvez entrer vos informations d’identification. Utilisez les informations d’identification dont vous disposez pour vous connecter au portail Azure.

    Add-AzureRmAccount

Si vous possédez plusieurs abonnements, utilisez l’applet de commande [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) pour sélectionner l’abonnement que votre session PowerShell doit utiliser. Pour savoir quel abonnement la session PowerShell en cours utilise, exécutez l’applet de commande [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx). Pour voir tous vos abonnements, exécutez l’applet de commande [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


<!--HONumber=Dec16_HO1-->


