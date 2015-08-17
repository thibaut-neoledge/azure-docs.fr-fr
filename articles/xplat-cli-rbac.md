<properties
	pageTitle="Gestion du contrôle d’accès en fonction du rôle avec l’interface de ligne de commande Azure pour Mac, Linux et Windows"
	description="Gestion du contrôle d’accès en fonction du rôle à l’aide de l’interface de ligne de commande Azure."
	services=""
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tomfitz"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2015"
	ms.author="tomfitz"/>

# Gestion du contrôle d’accès en fonction du rôle avec l’interface de ligne de commande Azure (Azure CLI)#

Le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure et dans l’API Azure Resource Manager permet une gestion très fine de l’accès à votre abonnement. Cette fonctionnalité vous permet d'accorder l'accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser l’interface de ligne de commande Azure pour gérer RBAC. Il vous explique les diverses étapes du processus de création et de contrôle des affectations de rôles.

**Durée estimée :** 15 minutes

## Composants requis

Avant d’utiliser l’interface de ligne de commande Azure pour gérer le contrôle d’accès en fonction du rôle, vous devez disposer des composants suivants :

- Interface de ligne de commande version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez la procédure d’[installation](xplat-cli-install.md).
- Pour vous familiariser avec la configuration et l’utilisation d’Azure Resource Manager dans l’interface de ligne de commande Azure, consultez également le didacticiel suivant : [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).

## Connexion à vos abonnements 

Comme le RBAC ne fonctionne qu'avec le gestionnaire de ressources Azure, la première chose à faire est de passer en mode Gestionnaire de ressources Azure ; entrez :

    azure config mode arm

Pour plus d’informations, voir [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](xplat-cli-azure-resource-manager.md).

Pour vous connecter à vos abonnements Azure, entrez :

    azure login -u <username>

Dans l’invite de ligne de commande, entrez le mot de passe de votre compte Azure (seuls les ID professionnels ou scolaires, également appelés **ID d’organisation**, sont pris en charge). L’interface de ligne de commande Azure obtient tous vos abonnements inclus dans ce compte et se configure elle-même pour utiliser le premier abonnement par défaut. Notez que le RBAC vous permet uniquement d'obtenir les abonnements pour lesquels vous disposez d'autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement.

Si vous disposez de plusieurs abonnements et souhaitez changer d'abonnement, entrez :

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Pour plus d’informations, voir les [commandes de l’interface de ligne de commande Azure](azure-cli-arm-commands.md).

## <a id="check"></a>Vérifier les affectations de rôles existantes ##

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Type :

    azure role assignment list

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1. Vous devrez disposer de l'accès en lecture au niveau de l'abonnement.
2. Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Type :

    azure role assignment list -g group1 --mail <user's email> -o Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre locataire AD, qui dispose d'une affectation de rôle « Propriétaire » pour le groupe de ressources « group1 ». L'affectation de rôle peut avoir deux origines :

1. Une affectation de rôle « Propriétaire » à l'utilisateur pour le groupe de ressources.
2. Une affectation de rôle « Propriétaire » à l'utilisateur pour le parent du groupe de ressources (l'abonnement, dans le cas présent), car toute autorisation à un certain niveau est héritée par ses enfants.

Tous les paramètres de cet applet de commande sont facultatifs. Vous pouvez les combiner pour contrôler des affectations de rôles avec différents filtres.

## Création d'une affectation de rôle 

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants

- Les utilisateurs auxquels vous voulez affecter le rôle : vous pouvez utiliser les applets de commande Azure Active Directory suivantes pour afficher les utilisateurs, groupes et principaux de service figurant dans votre locataire AD.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure sp list
    azure sp show`

- Quel rôle vous souhaitez affecter : vous pouvez utiliser l'applet de commande suivante pour afficher les définitions de rôles prises en charge.

    `azure role list`

- L'étendue à laquelle s'applique l'affectation : il existe trois niveaux d'étendue

    - L'abonnement actuel
    - Un groupe de ressources ; pour obtenir la liste des groupes de ressources, tapez `azure group list`
    - Une ressource ; pour obtenir la liste des ressources, tapez `azure resource list`

Ensuite, utilisez `azure role assignment create` pour créer une affectation de rôle. Par exemple :

 - Cela créera une affectation de rôle au niveau de l'abonnement actuel pour un utilisateur en tant que lecteur.

    `azure role assignment create --mail <user's email> -o Reader`

- Cela créera une affectation de rôle au niveau d'un groupe de ressources

    `PS C:\> azure role assignment create --mail <user's email> -o Contributor -g group1`

- Cela créera une affectation de rôle au niveau d'une ressource

    `azure role assignment create --mail <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## Vérification des autorisations 

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant

    PS C:\> azure group list
    PS C:\> azure resource list

Ces deux applets de commande renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation de lecture. Elles affichent également les autorisations dont vous disposez.

Et donc, quand vous tentez d'exécuter une autre applet de commande telle que `azure group create`, vous recevez une erreur d'accès refusé si vous ne disposez pas de l'autorisation requise.

## Étapes suivantes 

Pour en savoir plus sur le contrôle d’accès en fonction du rôle à l’aide d’Azure CLI, ainsi que sur les rubriques associées :

- [Installer et configurer l’interface de ligne de commande Azure](xplat-cli-install.md)
- [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](xplat-cli-azure-resource-manager.md)
- [Utilisation de groupes de ressources pour gérer vos ressources Azure](resource-groups-overview.md) : découvrez comment créer et gérer des groupes de ressources dans le portail de gestion Azure.

<!---HONumber=August15_HO6-->