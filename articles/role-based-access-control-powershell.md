<properties 
	pageTitle="Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell" 
	description="Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell" 
	services="" 
	documentationCenter="" 
	authors="guangyang" 
	manager="terrylan" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2014" 
	ms.author="guayan"/>

# Gestion du contrôle d'accès en fonction du rôle avec Windows PowerShell #

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/role-based-access-control-powershell.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/fr-fr/documentation/articles/role-based-access-control-xplat-cli.md" title="Cross-Platform CLI">CLI multiplateforme</a></div>

Le contrôle d'accès en fonction du rôle (RBAC) dans l'API de la version préliminaire du portail Azure et l'API du gestionnaire de ressources Azure permet une gestion très fine de l'accès à votre abonnement. Cette fonctionnalité vous permet d'accorder l'accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser Windows PowerShell pour gérer le RBAC. Il vous explique les diverses étapes du processus de création et de contrôle des affectations de rôles.

**Durée estimée :** 15 minutes

## Configuration requise ##

Avant d'utiliser Windows PowerShell pour gérer le RBAC, vous devez disposer des composants suivants :

- Windows PowerShell, version 3.0 ou 4.0. Pour rechercher la version de Windows PowerShell, tapez : $PSVersionTable et vérifiez que la valeur de PSVersion est 3.0 ou 4.0. Pour installer une version compatible, consultez [Windows Management Framework 3.0 ](http://www.microsoft.com/fr-fr/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/fr-fr/download/details.aspx?id=40855).

- Azure PowerShell, version 0.8.8 ou ultérieure. Pour installer la dernière version et l'associer à votre abonnement Azure, consultez [Installation et configuration de Windows Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

Ce didacticiel s'adresse aux utilisateurs novices de Windows PowerShell, mais il part du principe que vous comprenez les concepts fondamentaux (modules, applets de commande et sessions). Pour plus d'informations sur Windows PowerShell, consultez [Prise en main de Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Pour accéder à l'aide détaillée de toute applet de commande présentée dans ce didacticiel, utilisez l'applet de commande Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l'aide sur l'applet de commande Add-AzureAccount, tapez :

	Get-Help Add-AzureAccount -Detailed

Consultez également les didacticiels suivants afin de vous familiariser avec la configuration et l'utilisation du gestionnaire de ressources Azure dans Windows PowerShell :

- [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)
- [Utilisation de Windows PowerShell avec le gestionnaire de ressources](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/)

## Dans ce didacticiel ##

* [Connexion à vos abonnements](#connect)
* [Contrôle des affectations de rôle existantes](#check)
* [Création d'une affectation de rôle](#create)
* [Vérification des autorisations](#verify)
* [Étapes suivantes](#next)

## <a id="connect"></a>Connexion à vos abonnements ##

Comme le RBAC ne fonctionne qu'avec le gestionnaire de ressources Azure, la première chose à faire est de passer en mode Gestionnaire de ressources Azure ; entrez :

    PS C:> Switch-AzureMode -Name AzureResourceManager

Pour plus d'informations, consultez [Utilisation de Windows PowerShell avec le gestionnaire de ressources](http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/).

Pour vous connecter à vos abonnements Azure, entrez :

    PS C:> Add-AzureAccount

Dans le contrôle contextuel de votre navigateur, entrez votre nom d'utilisateur et votre mot de passe Azure. PowerShell obtient tous vos abonnements inclus dans ce compte et configure le premier d'entre eux pour être utilisé par défaut. Notez que le RBAC vous permet uniquement d'obtenir les abonnements pour lesquels vous disposez d'autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement. 

Si vous disposez de plusieurs abonnements et souhaitez changer d'abonnement, entrez :

    # Cette commande affiche les abonnements associés au compte.
    PS C:> Get-AzureSubscription
    # Utilisez le nom d'abonnement pour sélectionner celui que vous souhaitez utiliser.
    PS C:> Select-AzureSubscription -SubscriptionName <subscription name>

Pour plus d'informations, consultez [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).

## <a id="check"></a>Contrôle des affectations de rôle existantes ##

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Type :

    PS C:> Get-AzureRoleAssignment

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1. Vous devrez disposer de l'accès en lecture au niveau de l'abonnement.
2. Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Type :

    PS C:> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre locataire AD, qui dispose d'une affectation de rôle " Propriétaire " pour le groupe de ressources " group1 ". L'affectation de rôle peut avoir deux origines :

1. Une affectation de rôle " Propriétaire " à l'utilisateur pour le groupe de ressources.
2. Une affectation de rôle " Propriétaire " à l'utilisateur pour le parent du groupe de ressources (l'abonnement, dans le cas présent), car toute autorisation à un certain niveau est héritée par ses enfants.

Tous les paramètres de cet applet de commande sont facultatifs. Vous pouvez les combiner afin de contrôler des affectations de rôles avec différents filtres.

## <a id="create"></a>Création d'une affectation de rôle ##

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants

- Les utilisateurs auxquels vous voulez affecter le rôle : vous pouvez utiliser les applets de commande Azure Active Directory suivantes pour afficher les utilisateurs, groupes et principaux de service figurant dans votre locataire AD.

    `PS C:> Get-AzureADUser
    PS C:> Get-AzureADGroup
    PS C:> Get-AzureADGroupMember
    PS C:> Get-AzureADServicePrincipal` 

- Le rôle que vous voulez affecter : vous pouvez utiliser l'applet de commande suivante pour afficher les définitions de rôle prises en charge.

    `PS C:> Get-AzureRoleDefinition`

- L'étendue à laquelle s'applique l'affectation : il existe trois niveaux d'étendue

    - L'abonnement actuel
    - Un groupe de ressources. Pour obtenir la liste des groupes de ressources, tapez " PS C:> Get-AzureResourceGroup "
    - Une ressource. Pour obtenir la liste des ressources, tapez " PS C:> Get-AzureResource "

Ensuite, utilisez New-AzureRoleAssignment pour créer une affectation de rôle. Par exemple :

 - Cela créera une affectation de rôle au niveau de l'abonnement actuel pour un utilisateur en tant que lecteur.

    `PS C:> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

- Cela créera une affectation de rôle au niveau d'un groupe de ressources

    `PS C:> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

- Cela créera une affectation de rôle au niveau d'une ressource

    `PS C:> $resources = Get-AzureResource
    PS C:> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <a id="verify"></a>Vérification des autorisations ##

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant

    PS C:> Get-AzureResourceGroup
    PS C:> Get-AzureResource

Ces deux applets de commande renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation de lecture. Ils afficheront également les autorisations dont vous disposez.

Ainsi, quand vous essayez d'exécuter une autre applet de commande comme New-AzureResourceGroup, vous obtenez une erreur d'accès refusé si vous ne disposez pas de l'autorisation requise.

## <a id="next"></a>Étapes suivantes ##

Pour en savoir plus sur le contrôle d'accès en fonction du rôle à l'aide de Windows PowerShell, ainsi que les rubriques associées :
 
- [Contrôle d'accès en fonction du rôle dans Microsoft Azure](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)
- [Applets de commande du gestionnaire de ressources Azure](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409) : découvrez comment utiliser les applets de commande dans le module AzureResourceManager.
- [Utilisation des groupes de ressources pour gérer vos ressources Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups) : découvrez comment créer et gérer des groupes de ressources dans le portail de gestion Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure) : découvrez les nouvelles fonctionnalités d'Azure.
- [Blog Windows PowerShell](http://blogs.msdn.com/powershell) : découvrez les nouvelles fonctionnalités de Windows PowerShell.
- [Blog " Hey, Scripting Guy! " ](http://blogs.technet.com/b/heyscriptingguy/) : bénéficiez des conseils et astuces de la communauté Windows PowerShell.
- [Configuration du contrôle d'accès en fonction du rôle à l'aide de l'interface CLI XPLAT](http://azure.microsoft.com/documentation/articles/role-based-access-control-xplat-cli/)
- [Résolution des problèmes de contrôle d'accès en fonction du rôle](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)

<!--HONumber=35.2-->

<!--HONumber=46--> 
 