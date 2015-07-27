## Configuration de PowerShell pour les modèles du Gestionnaire de ressources
 
Avant de pouvoir utiliser Azure PowerShell avec le Gestionnaire de ressources, vous devez avoir les bonnes versions de Windows PowerShell et Azure PowerShell.

### Vérifier les versions de PowerShell

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


Vérifiez que la valeur de **PSVersion** est 3.0 ou 4.0. Dans le cas contraire, consultez [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Vous devez également disposer d'Azure PowerShell version 0.9.0 ou ultérieure. Si vous n'avez pas installé et configuré Azure PowerShell, cliquez [ici](powershell-install-configure.md) pour obtenir les instructions.

Vous pouvez vérifier la version d'Azure PowerShell que vous avez installée à l'aide de cette commande à l'invite de commandes Azure PowerShell.

	Get-Module azure | format-table version

Le type d'information suivant s'affiche :

	Version
	-------
	0.9.0

Si vous n'avez pas la version 0.9.0 ou version ultérieure, vous devez supprimer Azure PowerShell à l'aide de Programmes et fonctionnalités du Panneau de configuration, puis installer la version la plus récente. Pour plus d'informations, consultez la rubrique [Comment installer et configurer Azure PowerShell](powershell-install-configure.md).

### Configurer vos compte et abonnement Azure

Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

Ouvrez une invite de commandes PowerShell Azure et connectez-vous à Azure avec cette commande.

	Add-AzureAccount

Si vous avez plusieurs abonnements Azure, vous pouvez les répertorier avec cette commande.

	Get-AzureSubscription

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

Vous pouvez configurer l'abonnement Azure actuel en exécutant ces commandes à l'invite de commandes Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<SubscriptionName from the display of Get-AzureSubscription>"
	Select-AzureSubscription -SubscriptionName $subscr -Current	

Pour plus d'informations sur les comptes et les abonnements Azure, consultez [Procédure : connexion à votre abonnement](powershell-install-configure.md#Connect).

### Basculer sur le module Azure Resource Manager

Pour utiliser le module Azure Resource Manager, vous devez passer de l'ensemble de commandes Azure par défaut à l'ensemble de commandes Azure Resource Manager. Exécutez cette commande.

	Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]Vous pouvez revenir à l’ensemble de commandes avec la commande **Switch-AzureMode AzureServiceManagement**.

<!---HONumber=July15_HO3-->