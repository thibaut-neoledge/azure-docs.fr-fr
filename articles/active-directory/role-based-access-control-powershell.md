<properties
	pageTitle="Guide sur le contrôle d’accès en fonction du rôle (RBAC) pour PowerShell"
	description="Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="kgremban"/>

# Guide sur le contrôle d’accès en fonction du rôle (RBAC) pour PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-powershell.md)
- [Interface de ligne de commande Azure](role-based-access-control-xplat-cli.md)


Le contrôle d'accès en fonction du rôle (RBAC) dans l'API du portail Azure et l'API de gestion de ressources Azure permet une gestion très fine de l'accès à votre abonnement. Cette fonctionnalité vous permet d’accorder l’accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser Windows PowerShell pour gérer le RBAC. Il vous explique les diverses étapes du processus de création et de contrôle des affectations de rôles.

**Durée estimée :** 15 minutes

## Composants requis

Avant d'utiliser Windows PowerShell pour gérer le RBAC, vous devez disposer des composants suivants :

- Windows PowerShell, version 3.0 ou 4.0. Pour trouver la version de Windows PowerShell, tapez : `$PSVersionTable` et vérifiez que la valeur de `PSVersion` est 3.0 ou 4.0. Pour installer une version compatible, consultez [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- Azure PowerShell, version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

>[AZURE.IMPORTANT] Pour pouvoir utiliser les applets de commande de cet article, vous devez [installer les applets de commande Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) dans PowerShell.

Ce didacticiel s'adresse aux utilisateurs novices de Windows PowerShell, mais il part du principe que vous comprenez les concepts fondamentaux (modules, applets de commande et sessions). Pour plus d'informations sur Windows PowerShell, consultez la page [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Pour accéder à l’aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l’applet de commande `Get-Help`.

	Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide sur l’applet de commande `Add-AzureAccount`, tapez :

	Get-Help Add-AzureAccount -Detailed

Consultez également les didacticiels suivants afin de vous familiariser avec la configuration et l’utilisation du gestionnaire de ressources Azure dans Windows PowerShell :

- [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md)
- [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)


## Connexion à vos abonnements

Comme le RBAC ne fonctionne qu’avec Azure Resource Manager, la première chose à faire est de passer en mode Azure Resource Manager :

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Pour vous connecter à vos abonnements Azure, tapez :

    PS C:\> Add-AzureAccount

Dans le contrôle contextuel de votre navigateur, saisissez votre nom d’utilisateur et votre mot de passe Azure. PowerShell obtient tous vos abonnements inclus dans ce compte et configure le premier d'entre eux pour être utilisé par défaut. Notez que le RBAC vous permet uniquement d'obtenir les abonnements pour lesquels vous disposez d'autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement.

Si vous disposez de plusieurs abonnements et souhaitez changer d’abonnement, utilisez les commandes suivantes :

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

## Contrôle des affectations de rôle existantes

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Type :

    PS C:\> Get-AzureRoleAssignment

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1. Vous devez disposer de l’accès en lecture au niveau de l’abonnement.
2. Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Entrez :

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre locataire AD, qui dispose d'une affectation de rôle « Propriétaire » pour le groupe de ressources « group1 ». L'affectation de rôle peut avoir deux origines :

1. Une affectation de rôle « Propriétaire » à l'utilisateur pour le groupe de ressources.
2. Une affectation de rôle « Propriétaire » à l’utilisateur pour le parent du groupe de ressources (à savoir l’abonnement dans le cas présent). Si vous affectez une autorisation au niveau du parent, tous les enfants ont les mêmes autorisations.

Tous les paramètres de cette applet de commande sont facultatifs. Vous pouvez les combiner pour contrôler des affectations de rôles avec différents filtres.

## Création d'une affectation de rôle

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants :

- Les utilisateurs auxquels vous voulez affecter le rôle : vous pouvez utiliser les applets de commande Azure Active Directory suivantes pour afficher les utilisateurs, groupes et principaux de service figurant dans votre locataire AD.  

	```
    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal
	```

- Quel rôle vous souhaitez affecter : vous pouvez utiliser l'applet de commande suivante pour afficher les définitions de rôles prises en charge.

    `PS C:\> Get-AzureRoleDefinition`

- L'étendue à laquelle s'applique l'affectation : il existe trois niveaux d'étendue
	- L'abonnement actuel
	- Un groupe de ressources. Pour obtenir la liste des groupes de ressources, entrez `PS C:\> Get-AzureResourceGroup`
	- Une ressource. Pour obtenir la liste des ressources, entrez `PS C:\> Get-AzureResource`

Ensuite, utilisez `New-AzureRoleAssignment` pour créer une affectation de rôle. Par exemple :

	#Create a role assignment at the current subscription level for a user as a reader.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

	#Create a role assignment at a resource group level.
	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

	#Create a role assignment for a group at a resource group level.
	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

	#Create a role assignment at a resource level.
	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## Vérification des autorisations

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant :

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Ces deux applets de commande renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation en lecture. Elles affichent également les autorisations dont vous disposez.

Ensuite, lorsque vous tentez d’exécuter d’autres applets de commande comme `New-AzureResourceGroup`, vous recevez une erreur d’accès refusé si vous ne disposez pas de l’autorisation requise.

## Étapes suivantes

Pour en savoir plus sur le contrôle d'accès en fonction du rôle à l'aide de Windows PowerShell, ainsi que les rubriques associées :

- [Contrôle d'accès en fonction du rôle dans Azure](role-based-access-control-configure.md)
- [Applets de commande Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409) : découvrez comment utiliser les applets de commande du module AzureResourceManager.
- [Utilisation du portail Azure pour gérer vos ressources Azure](../azure-portal/resource-group-portal.md) : en savoir plus sur Azure Resource Manager.
- [Blog Azure](http://blogs.msdn.com/windowsazure) : découvrez les nouvelles fonctionnalités d'Azure.
- [Blog Windows PowerShell](http://blogs.msdn.com/powershell) : découvrez les nouvelles fonctionnalités de Windows PowerShell.
- [Blog « Hey, Scripting Guy! »](http://blogs.technet.com/b/heyscriptingguy/) : bénéficiez des conseils et astuces de la communauté Windows PowerShell.
- [Configurer le contrôle d'accès en fonction du rôle à l'aide de l'interface de ligne de commande Azure](role-based-access-control-xplat-cli.md)
- [Résolution des problèmes de contrôle d’accès en fonction du rôle](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0323_2016-->