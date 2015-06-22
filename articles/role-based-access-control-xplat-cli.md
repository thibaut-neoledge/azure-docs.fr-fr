<properties 
	pageTitle="MGestion du contrôle d'accès en fonction du rôle avec l'interface de ligne de commande interplateforme Azure" 
	description="Gestion du contrôle d'accès en fonction du rôle avec l'interface en ligne de commande interplateforme" 
	services="" 
	documentationCenter="" 
	authors="guangyang" 
	manager="terrylan" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2014" 
	ms.author="guayan"/>

# Gestion du contrôle d'accès en fonction du rôle à l'aide de l'interface de ligne de commande interplateforme #

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/powershell-rbac.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/fr-fr/documentation/articles/xplat-cli-rbac.md" title="Cross-Platform CLI">Interface de ligne de commande interplateforme</a></div>

Le contrôle d'accès en fonction du rôle (RBAC) dans l'API de la version préliminaire du portail Azure et l'API du gestionnaire de ressources Azure permet une gestion très fine de l'accès à votre abonnement et à vos ressources. Cette fonctionnalité vous permet d'accorder l'accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser l'interface de ligne de commande interplateforme Azure (xplat-cli) pour gérer le contrôle d'accès basé sur un rôle. Il vous explique les diverses étapes du processus de création et de contrôle des affectations de rôles.

**Durée de réalisation estimée :**  15 minutes

## Conditions préalables ##

Avant d'utiliser xplat-cli pour gérer le RBAC, vous devez disposer des composants suivants :

- Interface de ligne de commande interplateforme Azure, version 0.8.8 ou ultérieure. Pour installer la dernière version et l'associer à votre abonnement Azure, voir la section [Installation et configuration de l'interface de ligne de commande interplateforme Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/).
- Consultez également les didacticiels suivants afin de vous familiariser avec la configuration et l'utilisation du gestionnaire de ressources Azure dans l'interface de ligne de commande interplateforme Azure : [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)

## Dans ce didacticiel ##

* [Connexion à vos abonnements](#connect)
* [Contrôle des affectations de rôles existantes](#check)
* [Création d'une affectation de rôle](#create)
* [Vérification des autorisations](#verify)
* [Étapes suivantes](#next)

## <a id="connect"></a>Connexion à vos abonnements ##

Comme le RBAC ne fonctionne qu'avec le gestionnaire de ressources Azure, la première chose à faire est de passer en mode Gestionnaire de ressources Azure. Saisissez :

    azure config mode arm

Pour plus d'informations, consultez la section [Utilisation de l'interface de ligne de commande interplateforme Azure avec le gestionnaire des ressources.](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)

Pour vous connecter à vos abonnements Azure, entrez :

    azure login -u <username>

Dans l'invite de ligne de commande, entrez le mot de passe du compte Azure (uniquement un compte professionnel). Xplat-cli obtient tous vos abonnements inclus dans ce compte et se configure lui-même pour utiliser le premier abonnement comme abonnement par défaut. Notez que le contrôle d'accès basé sur un rôle vous permet uniquement d'obtenir les abonnements pour lesquels vous disposez d'autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement. 

Si vous disposez de plusieurs abonnements et souhaitez changer d'abonnement, entrez :

    # Cette commande affiche les abonnements du compte.
    azure account list
    # Utilisez le nom d'abonnement pour sélectionner celui que vous souhaitez utiliser.
    azure account set <nom d'abonnement>

Pour plus d'informations, consultez la section [Installation et configuration de l'interface de ligne de commande multiplateforme Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/).

## <a id="check"></a>Contrôle des affectations de rôles existantes ##

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Saisissez :

    azure role assignment list

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1. Vous devrez disposer de l'accès en lecture au niveau de l'abonnement.
2. Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Saisissez :

    azure role assignment list -g group1 --upn <user email> -o Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre annuaire Azure AD, qui dispose d'une affectation de rôle " Propriétaire " pour le groupe de ressources " group1 ". L'affectation de rôle peut avoir deux origines :

1. Une affectation de rôle " Propriétaire " à l'utilisateur pour le groupe de ressources.
2. Une affectation de rôle " Propriétaire " à l'utilisateur pour le parent du groupe de ressources (l'abonnement dans le cas présent), car toute autorisation à un certain niveau d'une ressource parente est héritée par toutes ses ressources enfants.

Tous les paramètres de ce cmdlet sont facultatifs. Vous pouvez les combiner afin de contrôler des affectations de rôles avec différents filtres.

## <a id="create"></a>Création d'une affectation de rôle ##

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants :

- À qui vous affectez le rôle : vous pouvez utiliser les cmdlets Azure Active Directory suivants pour savoir quels utilisateurs, groupes et principaux du service sont inclus dans votre annuaire.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure ad sp list
    azure ad sp show`

- Quel rôle vous souhaitez affecter : vous pouvez utiliser le cmdlet suivant pour afficher les définitions de rôles prises en charge.

    `azure role list`

- Quelle étendue vous souhaitez affecter : il existe trois niveaux d'étendue

    - L'abonnement actuel
    - Un groupe de ressources. Pour obtenir la liste des groupes de ressources, entrez " azure group list "
    - Une ressource. Pour obtenir la liste des ressources, entrez " azure resource list "

Utilisez ensuite " azure role assignment create " pour créer une attribution de rôle. Par exemple :

 - Cela créera une affectation de rôle au niveau de l'abonnement actuel pour un utilisateur en tant que lecteur :

    `azure role assignment create --upn <user's email> -o Reader`

- Cela créera une affectation de rôle au niveau d'un groupe de ressources :

    " PS C:> azure role assignment create --upn <adresse de messagerie de l'utilisateur> -o Contributor -g group1 "

- Cela créera une affectation de rôle au niveau d'une ressource :

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <a id="verify"></a>Vérification des autorisations ##

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant :

    PS C:> azure group list
    PS C:> azure resource list

Ces deux cmdlets renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation de lecture. Ils afficheront également les autorisations dont vous disposez.

Et donc, lorsque vous tentez d'exécuter un autre cmdlet tel que " azure group create ", vous recevez une erreur d'accès refusé si vous ne disposez pas de l'autorisation requise.

## <a id="next"></a>Étapes suivantes ##

Pour en savoir plus sur le contrôle d'accès basé sur un rôle à l'aide de xplat-cli, ainsi que les rubriques associées :

- [Contrôle d'accès basé sur un rôle dans Windows Azure](http://azure.microsoft.com/documentation/articles/role-based-access-control-configure/)
- [Installation et configuration de l'interface de ligne de commande interplateforme Azure](http://azure.microsoft.com/documentation/articles/xplat-cli/)
- [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources](http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/)
- [Utilisation des groupes de ressources pour gérer vos ressources Azure](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups) : découvrez comment créer et gérer des groupes de ressources dans le portail de gestion Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure) : découvrez les nouvelles fonctionnalités d'Azure.
- [Configuration du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell](http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/)
- [Résolution des problèmes de contrôle d'accès basé sur un rôle](http://azure.microsoft.com/documentation/articles/role-based-access-control-troubleshooting/)

<!--HONumber=46--> 
 