<properties
	pageTitle="Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell"
	description="Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell"
	services="active-directory"
	documentationCenter="na"
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="inhenk"/>

# Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell #

> [AZURE.SELECTOR]
- [Windows PowerShell](role-based-access-control-powershell.md)
- [Azure CLI](role-based-access-control-xplat-cli-install.md)


Le contrôle d'accès en fonction du rôle (RBAC) dans l'API du portail Azure et l'API de gestion de ressources Azure permet une gestion très fine de l'accès à votre abonnement. Cette fonctionnalité vous permet d'accorder l'accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser Windows PowerShell pour gérer le RBAC. Il vous explique les diverses étapes du processus de création et de contrôle des affectations de rôles.

**Durée estimée :** 15 minutes

## Composants requis

Avant d'utiliser Windows PowerShell pour gérer le RBAC, vous devez disposer des composants suivants :

- Windows PowerShell, version 3.0 ou 4.0. Pour trouver la version de Windows PowerShell, tapez : `$PSVersionTable` et vérifiez que la valeur de `PSVersion` est 3.0 ou 4.0. Pour installer une version compatible, consultez [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

- Azure PowerShell, version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la page [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md).

Ce didacticiel s'adresse aux utilisateurs novices de Windows PowerShell, mais il part du principe que vous comprenez les concepts fondamentaux (modules, applets de commande et sessions). Pour plus d'informations sur Windows PowerShell, consultez la page [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Pour accéder à l'aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l'applet de commande Get-Help.

	Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l'aide sur l'applet de commande Add-AzureAccount, tapez :

	Get-Help Add-AzureAccount -Detailed

Consultez également les didacticiels suivants afin de vous familiariser avec la configuration et l'utilisation du gestionnaire de ressources Azure dans Windows PowerShell :

- [Installation et configuration d'Azure PowerShell](../install-configure-powershell.md)
- [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md)


## Connexion à vos abonnements

Comme le RBAC ne fonctionne qu'avec le gestionnaire de ressources Azure, la première chose à faire est de passer en mode Gestionnaire de ressources Azure ; entrez :

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Pour plus d'informations, voir la section [Utilisation de Windows PowerShell avec le gestionnaire de ressources](../powershell-azure-resource-manager.md).

Pour vous connecter à vos abonnements Azure, entrez :

    PS C:\> Add-AzureAccount

Dans le contrôle contextuel de votre navigateur, entrez votre nom d'utilisateur et votre mot de passe Azure. PowerShell obtient tous vos abonnements inclus dans ce compte et configure le premier d'entre eux pour être utilisé par défaut. Notez que le RBAC vous permet uniquement d'obtenir les abonnements pour lesquels vous disposez d'autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement.

Si vous disposez de plusieurs abonnements et souhaitez changer d'abonnement, entrez :

    # This will show you the subscriptions under the account.
    PS C:\> Get-AzureSubscription
    # Use the subscription name to select the one you want to work on.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Pour plus d'informations, consultez la section [Installation et configuration d'Azure PowerShell](../install-configure-powershell.md).

## Contrôle des affectations de rôle existantes

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Type :

    PS C:\> Get-AzureRoleAssignment

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1. Vous devrez disposer de l'accès en lecture au niveau de l'abonnement.
2. Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Type :

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre locataire AD, qui dispose d'une affectation de rôle « Propriétaire » pour le groupe de ressources « group1 ». L'affectation de rôle peut avoir deux origines :

1. Une affectation de rôle « Propriétaire » à l'utilisateur pour le groupe de ressources.
2. Une affectation de rôle « Propriétaire » à l'utilisateur pour le parent du groupe de ressources (l'abonnement, dans le cas présent), car toute autorisation à un certain niveau est héritée par ses enfants.

Tous les paramètres de cet applet de commande sont facultatifs. Vous pouvez les combiner pour contrôler des affectations de rôles avec différents filtres.

## Création d'une affectation de rôle

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants :

Les utilisateurs auxquels vous voulez affecter le rôle : vous pouvez utiliser les applets de commande Azure Active Directory suivantes pour afficher les utilisateurs, groupes et principaux de service figurant dans votre locataire AD.

    PS C:\> Get-AzureADUser
	PS C:\> Get-AzureADGroup
	PS C:\> Get-AzureADGroupMember
	PS C:\> Get-AzureADServicePrincipal

Quel rôle vous souhaitez affecter : vous pouvez utiliser l'applet de commande suivante pour afficher les définitions de rôles prises en charge.

    PS C:\> Get-AzureRoleDefinition

L'étendue à laquelle s'applique l'affectation : il existe trois niveaux d'étendue

    - The current subscription
    - A resource group, to get a list of resource groups, type `PS C:\> Get-AzureResourceGroup`
    - A resource, to get a list of resources, type `PS C:\> Get-AzureResource`

Ensuite, utilisez `New-AzureRoleAssignment` pour créer une affectation de rôle. Par exemple :


Cela créera une affectation de rôle au niveau de l'abonnement actuel pour un utilisateur en tant que lecteur.

	 PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Reader

Cela créera une affectation de rôle au niveau d'un groupe de ressources.

	PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Contributor -ResourceGroupName group1

Cela créera une affectation de rôle pour un groupe au niveau d'un groupe de ressources.

	PS C:\> New-AzureRoleAssignment -ObjectID <group object ID> -RoleDefinitionName Reader -ResourceGroupName group1

Cela créera une affectation de rôle au niveau d'une ressource.

	PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId


## Vérification des autorisations

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Ces deux applets de commande renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation de lecture. Elles affichent également les autorisations dont vous disposez.

Et donc, quand vous tentez d'exécuter une autre applet de commande telle que `New-AzureResourceGroup`, vous recevez une erreur d'accès refusé si vous ne disposez pas de l'autorisation requise.

## Étapes suivantes

Pour en savoir plus sur le contrôle d'accès en fonction du rôle à l'aide de Windows PowerShell, ainsi que les rubriques associées :

- [Contrôle d'accès en fonction du rôle dans Azure](../role-based-access-control-configure.md)
- [Applets de commande Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409) : découvrez comment utiliser les applets de commande du module AzureResourceManager.
- [Utilisation de groupes de ressources pour gérer vos ressources Azure](../azure-preview-portal-using-resource-groups.md) : découvrez comment créer et gérer des groupes de ressources dans le portail de gestion Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure) : découvrez les nouvelles fonctionnalités d'Azure.
- [Blog Windows PowerShell](http://blogs.msdn.com/powershell) : découvrez les nouvelles fonctionnalités de Windows PowerShell.
- [Blog « Hey, Scripting Guy! »](http://blogs.technet.com/b/heyscriptingguy/) : bénéficiez des conseils et astuces de la communauté Windows PowerShell.
- [Configurer le contrôle d'accès en fonction du rôle à l'aide de l'interface de ligne de commande Azure](role-based-access-control-xplat-cli-install.md)
- [Résolution des problèmes de contrôle d'accès basé sur les rôles](role-based-access-control-troubleshooting.md)

<!---HONumber=Oct15_HO2-->