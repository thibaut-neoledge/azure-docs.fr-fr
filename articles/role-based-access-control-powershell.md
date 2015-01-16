<properties pageTitle="Gestion du contrôle d'accès en fonction du rôle avec Windows PowerShell" metaKeywords="ResourceManager, PowerShell, Azure PowerShell, RBAC" description="Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Windows PowerShell" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="11/03/2014" ms.author="guayan" />

# Gestion du contrôle d'accès en fonction du rôle à l'aide de Windows PowerShell #

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/fr-fr/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">Interface de ligne de commande interplateforme</a></div>

Le contrôle d'accès en fonction du rôle dans l'API de la version préliminaire du portail Azure et l'API du gestionnaire de ressources Azure permet une gestion très fine de l'accès à votre abonnement. Cette fonctionnalité vous permet d'accorder l'accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser Windows PowerShell pour gérer le RBAC. Il vous explique les diverses étapes du processus de création et de contrôle des affectations de rôles.

**Durée de réalisation estimée :**  15 minutes

## Conditions préalables ##

Avant d'utiliser Windows PowerShell pour gérer le RBAC, vous devez disposer des composants suivants :

- Windows PowerShell, version 3.0 ou 4.0. Pour trouver la version de Windows PowerShell requise, tapez : " $PSVersionTable " et vérifiez que la valeur de " PSVersion " est 3.0 ou 4.0. Pour installer une version compatible, consultez les pages [Windows Management Framework 3.0](http://www.microsoft.com/fr-fr/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/fr-fr/download/details.aspx?id=40855).

- Azure PowerShell, version 0.8.8 ou ultérieure. Pour installer la dernière version et l'associer à votre abonnement Azure, accédez à la page [Installation et configuration d'Azure PowerShell](http://www.windowsazure.com/fr-fr/documentation/articles/install-configure-powershell/).

Ce didacticiel s'adresse aux utilisateurs novices de Windows PowerShell, mais il part du principe que vous comprenez les concepts fondamentaux (modules, cmdlets et sessions). Pour plus d'informations sur Windows PowerShell, consultez la page [Prise en main de Windows PowerShell](http://technet.microsoft.com/fr-fr/library/hh857337.aspx).

Pour accéder à l'aide détaillée de toute cmdlet présentée dans ce didacticiel, utilisez la cmdlet Get-Help. 

	Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l'aide sur la cmdlet Add-AzureAccount, tapez :

	Get-Help Add-AzureAccount -Detailed

Consultez également les didacticiels suivants afin de vous familiariser avec la configuration et l'utilisation du gestionnaire de ressources Azure dans Windows PowerShell :

- [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/)
- [Utilisation de Windows PowerShell avec le gestionnaire des ressources](http://azure.microsoft.com/fr-fr/documentation/articles/powershell-azure-resource-manager/)

## Dans ce didacticiel ##

* [Connexion à vos abonnements](#connect)
* [Contrôle des affectations de rôles existantes](#check)
* [Création d'une affectation de rôle](#create)
* [Vérification des autorisations](#verify)
* [Étapes suivantes](#next)

## <a id="connect"></a>Connexion à vos abonnements ##

Comme le RBAC ne fonctionne qu'avec le gestionnaire de ressources Azure, la première chose à faire est de passer en mode Gestionnaire de ressources Azure ; entrez :

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Pour plus d'informations, consultez la section [Utilisation de Windows PowerShell avec le gestionnaire de ressources](http://azure.microsoft.com/fr-fr/documentation/articles/powershell-azure-resource-manager/).

Pour vous connecter à vos abonnements Azure, entrez :

    PS C:\> Add-AzureAccount

Dans le contrôle contextuel de votre navigateur, entrez votre nom d'utilisateur et votre mot de passe Azure. PowerShell obtient tous vos abonnements inclus dans ce compte et configure le premier d'entre eux pour être utilisé par défaut. Notez que le RBAC vous permet uniquement d'obtenir les abonnements pour lesquels vous disposez d'autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement. 

Si vous disposez de plusieurs abonnements et souhaitez changer d'abonnement, entrez :

    # Cette commande affiche les abonnements du compte.
    PS C:\> Get-AzureSubscription
    # Utilisez le nom d'abonnement pour sélectionner celui que vous souhaitez utiliser.
    PS C:\> Select-AzureSubscription -SubscriptionName <subscription name>

Pour plus d'informations, consultez la section [Installation et configuration d'Azure PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/).

## <a id="check"></a>Contrôle des affectations de rôles existantes ##

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Saisissez :

    PS C:\> Get-AzureRoleAssignment

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1. Vous devrez disposer de l'accès en lecture au niveau de l'abonnement.
2. Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Saisissez :

    PS C:\> Get-AzureRoleAssignment -ResourceGroupName group1 -Mail <user email> -RoleDefinitionName Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre locataire AD, qui dispose d'une affectation de rôle " Propriétaire " pour le groupe de ressources " group1 ". L'affectation de rôle peut avoir deux origines :

1. Une affectation de rôle " Propriétaire " à l'utilisateur pour le groupe de ressources.
2. Une affectation de rôle " Propriétaire " à l'utilisateur pour le parent du groupe de ressources (l'abonnement, dans le cas présent), car toute autorisation à un certain niveau est héritée par ses enfants.

Tous les paramètres de ce cmdlet sont facultatifs. Vous pouvez les combiner afin de contrôler des affectations de rôles avec différents filtres.

## <a id="create"></a>Création d'une affectation de rôle ##

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants

- À qui vous affectez le rôle : vous pouvez utiliser les cmdlets Azure Active Directory suivants pour savoir quels utilisateurs, groupes et principaux du service sont inclus dans votre annuaire.

    `PS C:\> Get-AzureADUser
    PS C:\> Get-AzureADGroup
    PS C:\> Get-AzureADGroupMember
    PS C:\> Get-AzureADServicePrincipal` 

- Quel rôle vous souhaitez affecter : vous pouvez utiliser le cmdlet suivant pour afficher les définitions de rôles prises en charge.

    `PS C:\> Get-AzureRoleDefinition`

- Quelle étendue vous souhaitez affecter : il existe trois niveaux d'étendue.

    - L'abonnement actuel
    - Un groupe de ressources ; pour obtenir la liste des groupes de ressources, entrez " PS C:\> Get-AzureResourceGroup ".
    - Une ressource ; pour obtenir la liste des groupes de ressources, entrez " PS C:\> Get-AzureResource ".

Ensuite, utilisez " AzureRoleAssignment " pour créer une affectation de rôle. Par exemple :

 - Cela créera une affectation de rôle au niveau de l'abonnement actuel pour un utilisateur en tant que lecteur.

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Reader`

- Cela créera une affectation de rôle au niveau d'un groupe de ressources

    `PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Contributor -ResourceGroupName group1`

- Cela créera une affectation de rôle au niveau d'une ressource

    `PS C:\> $resources = Get-AzureResource
    PS C:\> New-AzureRoleAssignment -Mail <user's email> -RoleDefinitionName Owner -Scope $resources[0].ResourceId`

## <a id="verify"></a>Vérification des autorisations ##

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant

    PS C:\> Get-AzureResourceGroup
    PS C:\> Get-AzureResource

Ces deux cmdlets renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation de lecture. Ils afficheront également les autorisations dont vous disposez.

Et donc, lorsque vous tentez d'exécuter un autre cmdlet tel que " New-AzureResourceGroup ", vous recevez une erreur d'accès refusé si vous ne disposez pas de l'autorisation requise.

## <a id="next"></a>Étapes suivantes ##

Pour en savoir plus sur le contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell, ainsi que les rubriques associées :
 
- [Contrôle d'accès basé sur un rôle dans Windows Azure](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-configure/)
- [Cmdlets Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkID=394765&clcid=0x409) : découvrez comment utiliser les cmdlets dans le module AzureResourceManager.
- [Utilisation des groupes de ressources pour gérer vos ressources Azure](http://azure.microsoft.com/fr-fr/documentation/articles/azure-preview-portal-using-resource-groups) : découvrez comment créer et gérer des groupes de ressources dans le portail de gestion Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure) : découvrez les nouvelles fonctionnalités d'Azure.
- [Blog Windows PowerShell](http://blogs.msdn.com/powershell) : découvrez les nouvelles fonctionnalités de Windows PowerShell.
- [Blog Hey, Scripting Guy!](http://blogs.technet.com/b/heyscriptingguy/) : bénéficiez des conseils et astuces de la communauté Windows PowerShell.
- [Configuration du contrôle d'accès basé sur un rôle à l'aide de l'interface de ligne de commande XPLAT](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-xplat-cli/)
- [Résolution des problèmes de contrôle d'accès basé sur un rôle](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-troubleshooting/)
