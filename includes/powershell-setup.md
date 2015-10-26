<properties services="virtual-machines" title="Setting up PowerShell" authors="JoeDavies-MSFT" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm=""
   ms.workload="infrastructure"
   ms.date="05/12/2015"
   ms.author="josephd" />

## Configuration de PowerShell

Avant d’utiliser Azure PowerShell, effectuez les opérations ci-après.

### Vérifier les versions de PowerShell

Pour utiliser Windows PowerShell, vous devez disposer de Windows PowerShell version 3.0 ou 4.0. Pour trouver la version de Windows PowerShell, tapez cette commande à une invite de commandes Windows PowerShell.

	$PSVersionTable

Le résultat suivant doit s’afficher.

	Name                           Value
	----                           -----
	PSVersion                      3.0
	WSManStackVersion              3.0
	SerializationVersion           1.1.0.1
	CLRVersion                     4.0.30319.18444
	BuildVersion                   6.2.9200.16481
	PSCompatibleVersions           {1.0, 2.0, 3.0}
	PSRemotingProtocolVersion      2.2

Vérifiez que la valeur de **PSVersion** est 3.0 ou 4.0. Pour installer une version compatible, consultez la section [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Vous devez également disposer d’Azure PowerShell version 0.8.0 ou ultérieure. Vous pouvez vérifier la version d’Azure PowerShell que vous avez installée à l’aide de cette commande à l’invite de commandes Azure PowerShell.

	Get-Module azure | format-table version

Le résultat suivant doit s’afficher.

	Version
	-------
	0.8.16.1

Pour des instructions et un lien vers la dernière version, consultez la rubrique [Installation et configuration d’Azure PowerShell](powershell-install-configure.md).


### Configuration de votre compte et de votre abonnement Microsoft Azure

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

Pour plus d’informations sur les comptes et les abonnements Azure, consultez [Procédure : Connexion à votre abonnement](powershell-install-configure.md#Connect).

<!---HONumber=Oct15_HO3-->