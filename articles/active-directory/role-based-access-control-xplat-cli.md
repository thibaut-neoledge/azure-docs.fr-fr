<properties
	pageTitle="Guide sur le contrôle d’accès en fonction du rôle (RBAC) pour l’interface de ligne de commande Azure"
	description="Gestion du contrôle d’accès en fonction du rôle avec l’interface de ligne de commande Azure"
	services="active-directory"
	documentationCenter="na"
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="kgremban"/>

# Guide sur le contrôle d’accès en fonction du rôle (RBAC) pour l’interface de ligne de commande Azure

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-powershell.md)
- [Interface de ligne de commande Azure](role-based-access-control-xplat-cli.md)

Le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure et l’API Azure Resource Manager permet une gestion très fine de l’accès à votre abonnement et à vos ressources. Cette fonctionnalité vous permet d’accorder l’accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser l’interface de ligne de commande Azure pour gérer le contrôle d’accès en fonction du rôle (RBAC). Vous suivrez les différentes étapes du processus de création et de contrôle des affectations de rôles.

**Durée estimée :** 15 minutes

## Composants requis

Pour pouvoir utiliser Azure CLI pour gérer le contrôle d’accès en fonction du rôle, vous devez disposer des composants suivants :

- Azure CLI version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez [Installer et configurer Azure CLI](../xplat-cli-install.md).
- Consultez également les didacticiels suivants afin de vous familiariser avec la configuration et l’utilisation d’Azure Resource Manager dans Azure CLI : [Utilisation d’Azure CLI avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md)

## <a id="connect"></a>Connexion à vos abonnements

Comme le RBAC ne fonctionne qu’avec Azure Resource Manager, la première chose à faire est de passer en mode Azure Resource Manager. Type :

    azure config mode arm

Pour vous connecter à vos abonnements Azure, tapez :

    azure login -u <username>

Dans l’invite de ligne de commande, entrez le mot de passe du compte Azure (uniquement un compte professionnel). Azure CLI obtient tous vos abonnements inclus dans ce compte et se configure lui-même pour utiliser le premier abonnement comme abonnement par défaut. Notez que le contrôle d’accès basé sur un rôle vous permet uniquement d’obtenir les abonnements pour lesquels vous disposez d’autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement.

Si vous disposez de plusieurs abonnements et souhaitez changer d’abonnement, tapez :

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

## <a id="check"></a>Vérifier les affectations de rôles existantes

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Type :

    azure role assignment list

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1. Vous devrez disposer de l'accès en lecture au niveau de l'abonnement.
2. Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Entrez :

    azure role assignment list -g group1 --upn <user email> -o Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre annuaire Azure AD, qui dispose d'une affectation de rôle « Propriétaire » pour le groupe de ressources « group1 ». L'affectation de rôle peut avoir deux origines :

1. Une affectation de rôle « Propriétaire » à l'utilisateur pour le groupe de ressources.
2. Une affectation de rôle « Propriétaire » à l’utilisateur pour le parent du groupe de ressources (à savoir l’abonnement dans le cas présent). Si vous affectez une autorisation au niveau du parent, tous les enfants ont les mêmes autorisations.

Tous les paramètres de cette applet de commande sont facultatifs. Vous pouvez les combiner pour contrôler des affectations de rôles avec différents filtres.

## <a id="create"></a>Créer une affectation de rôle

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants :

- À qui vous affectez le rôle : vous pouvez utiliser les applets de commande Azure Active Directory suivants pour savoir quels utilisateurs, groupes et principaux du service sont inclus dans votre annuaire.

    ```
    azure ad user list  
    azure ad user show  
    azure ad group list  
    azure ad group show  
    azure ad group member list  
    azure ad sp list  
    azure ad sp show  
    ```

- Quel rôle vous souhaitez affecter : vous pouvez utiliser l'applet de commande suivante pour afficher les définitions de rôles prises en charge.

    `azure role list`

- L'étendue à laquelle s'applique l'affectation : il existe trois niveaux d'étendue

    - L'abonnement actuel
    - Un groupe de ressources. Pour obtenir la liste des groupes de ressources, entrez `azure group list`
    - Une ressource. Pour obtenir la liste des ressources, entrez `azure resource list`

Ensuite, utilisez `azure role assignment create` pour créer une affectation de rôle. Par exemple :

 	#Create a role assignment at the current subscription level for a user as a reader:
    azure role assignment create --upn <user email> -o Reader

	#Create a role assignment at a resource group level:
    PS C:\> azure role assignment create --upn <user email> -o Contributor -g group1

	#Create a role assignment at a resource level:
    azure role assignment create --upn <user email> -o Owner -g group1 -r Microsoft.Web/sites -u site1

## <a id="verify"></a>Vérifier les autorisations

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant :

    PS C:\> azure group list
    PS C:\> azure resource list

Ces deux applets de commande renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation en lecture. Elles affichent également les autorisations dont vous disposez.

Ensuite, lorsque vous tentez d’exécuter d’autres applets de commande comme `azure group create`, vous recevez une erreur d’accès refusé si vous ne disposez pas de l’autorisation requise.

## <a id="next"></a>Étapes suivantes

Pour en savoir plus sur le contrôle d’accès en fonction du rôle à l’aide d’Azure CLI, ainsi que sur les rubriques associées :

- [Contrôle d’accès en fonction du rôle dans Azure](role-based-access-control-configure.md)
- [Installer et configurer Azure CLI](../xplat-cli-install.md)
- [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md)
- [Utilisation du portail Azure pour gérer vos ressources Azure](../azure-portal/resource-group-portal.md) : découvrez comment créer et gérer des groupes de ressources dans le portail Azure.
- [Blog Azure](http://blogs.msdn.com/windowsazure) : découvrez les nouvelles fonctionnalités d'Azure.
- [Configurer le contrôle d’accès en fonction du rôle à l’aide de Windows PowerShell](role-based-access-control-powershell.md)
- [Résolution des problèmes de contrôle d’accès en fonction du rôle](role-based-access-control-troubleshooting.md)

<!---HONumber=AcomDC_0323_2016-->