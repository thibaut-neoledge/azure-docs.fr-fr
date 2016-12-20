## <a name="setting-up-powershell-for-resource-manager-templates"></a>Configuration de PowerShell pour les modèles du Gestionnaire de ressources
Avant de pouvoir utiliser Azure PowerShell avec Resource Manager, vous devez avoir les bonnes versions de Windows PowerShell et Azure PowerShell.

### <a name="verify-powershell-versions"></a>Vérifier les versions de PowerShell
Vérifiez que vous avez Windows PowerShell version 3.0 ou 4.0. Pour trouver la version de Windows PowerShell, tapez cette commande à une invite de commandes Windows PowerShell.

    $PSVersionTable

Le type d'information suivant s'affiche :

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Vérifiez que la valeur de **PSVersion** est 3.0 ou 4.0. Dans le cas contraire, consultez l’article [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Configurer votre compte et votre abonnement Microsoft Azure
Si vous ne possédez pas encore un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire pour une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

Ouvrez une invite de commandes PowerShell Azure et connectez-vous à Azure avec cette commande.

    Login-AzureRmAccount

Si vous avez plusieurs abonnements Azure, vous pouvez les répertorier avec cette commande.

    Get-AzureRmSubscription

Le type d'information suivant s'affiche :

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName :
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Vous pouvez configurer l'abonnement Azure actuel en exécutant ces commandes à l'invite de commandes Azure PowerShell. Remplacez tous les éléments entre guillemets, notamment les caractères < et >, par le nom approprié.

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Pour plus d’informations sur les comptes et les abonnements Azure, consultez la [procédure de connexion à votre abonnement](/powershell/azureps-cmdlets-docs#step-3-connect).



<!--HONumber=Dec16_HO1-->


