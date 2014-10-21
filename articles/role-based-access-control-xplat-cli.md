<properties pageTitle="Managing Role-Based Access Control with Azure Cross-Platform Command-Line Interface" metaKeywords="ResourceManager, Azure cross-platform command-line interface, Azure command-line, azure command-line, azure cli, RBAC" description="Managing role-based access control with cross-platform command-line interface" metaCanonical="" services="" documentationCenter="" title="Managing Role-Based Access Control with Cross-Platform Command-Line Interface" authors="guayan" solutions="" manager="terrylan" editor="mollybos" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="guayan"></tags>

# Gestion du contrôle d'accès basé sur un rôle à l'aide de l'interface de ligne de commande interplateforme

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/powershell-rbac.md" title="Interface de ligne de commande interplateforme" class="current">Interface de ligne de commande interplateforme</a><a href="/fr-fr/documentation/articles/xplat-cli-rbac.md" title="Windows PowerShell">Windows PowerShell</a></div>

Le contrôle d'accès basé sur un rôle (RBAC) dans l'API de la version préliminaire du portail Azure et l'API du gestionnaire de ressources Azure permet une gestion très fine de l'accès à votre abonnement et à vos ressources. Cette fonctionnalité vous permet d'accorder l'accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Dans ce didacticiel, vous allez apprendre à utiliser l'interface de ligne de commande interplateforme Azure (xplat-cli) pour gérer le contrôle d'accès basé sur un rôle. Il vous explique les diverses étapes du processus de création et de contrôle des affectations de rôles.

**Durée de réalisation estimée :** 15 minutes

## Configuration requise

Avant d'utiliser xplat-cli pour gérer le RBAC, vous devez disposer des composants suivants :

-   Interface de ligne de commande interplateforme Azure, version 0.8.8 ou ultérieure. Pour installer la dernière version et l'associer à votre abonnement Azure, voir la section [Installation et configuration de l'interface de ligne de commande interplateforme Azure][Installation et configuration de l'interface de ligne de commande interplateforme Azure].
-   Consultez également les didacticiels suivants afin de vous familiariser avec la configuration et l'utilisation du gestionnaire de ressources Azure dans l'interface de ligne de commande interplateforme Azure : [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources][Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources]

## Dans ce didacticiel

-   [Connexion à vos abonnements][Connexion à vos abonnements]
-   [Contrôle des affectations de rôles existantes][Contrôle des affectations de rôles existantes]
-   [Création d'une affectation de rôle][Création d'une affectation de rôle]
-   [Vérification des autorisations][Vérification des autorisations]
-   [Étapes suivantes][Étapes suivantes]

## <span id="connect"></span></a>Connexion à vos abonnements

Comme le RBAC ne fonctionne qu'avec le gestionnaire de ressources Azure, la première chose à faire est de passer en mode Gestionnaire de ressources Azure. Entrez :

    azure config mode arm

Pour plus d'informations, consultez la section [Utilisation de l'interface de ligne de commande interplateforme Azure avec le gestionnaire des ressources][Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources]

Pour vous connecter à vos abonnements Azure, entrez :

    azure login -u <username>

Dans l'invite de ligne de commande, entrez le mot de passe du compte Azure (uniquement un compte d'organisation). Xplat-cli obtient tous vos abonnements inclus dans ce compte et se configure lui-même pour utiliser le premier abonnement comme abonnement par défaut. Notez que le contrôle d'accès basé sur un rôle vous permet uniquement d'obtenir les abonnements pour lesquels vous disposez d'autorisations en tant que coadministrateur ou détenteur de certains rôles pour cet abonnement.

Si vous disposez de plusieurs abonnements et souhaitez changer d'abonnement, entrez :

    # This will show you the subscriptions under the account.
    azure account list
    # Use the subscription name to select the one you want to work on.
    azure account set <subscription name>

Pour plus d'informations, voir la section [Installation et configuration de l'interface de ligne de commande multiplateforme Azure][Installation et configuration de l'interface de ligne de commande interplateforme Azure].

## <span id="check"></span></a>Contrôle des affectations de rôles existantes

Voyons maintenant quelles affectations de rôles existent déjà dans l'abonnement. Entrez :

    azure role assignment list

Cela renverra toutes les affectations de rôles dans l'abonnement. Deux points à noter :

1.  Vous devrez disposer de l'accès en lecture au niveau de l'abonnement.
2.  Si l'abonnement inclut un grand nombre d'affectations de rôles, les obtenir toutes peut prendre un certain temps.

Vous pouvez également contrôler les affectations de rôles existantes pour une définition de rôle spécifique, avec une étendue spécifique, pour un utilisateur spécifique. Entrez :

    azure role assignment list -g group1 --upn <user's email> -o Owner

Cela renverra toutes les affectations de rôles pour un utilisateur spécifique dans votre annuaire Azure AD, qui dispose d'une affectation de rôle « Propriétaire » pour le groupe de ressources « group1 ». L'affectation de rôle peut avoir deux origines :

1.  Une affectation de rôle « Propriétaire » à l'utilisateur pour le groupe de ressources.
2.  Une affectation de rôle « Propriétaire » à l'utilisateur pour le parent du groupe de ressources (l'abonnement dans le cas présent), car toute autorisation à un certain niveau d'une ressource parente est héritée par toutes ses ressources enfants.

Tous les paramètres de cette cmdlet sont facultatifs. Vous pouvez les combiner afin de contrôler des affectations de rôles avec différents filtres.

## <span id="create"></span></a>Création d'une affectation de rôle

Pour créer une affectation de rôle, vous devez réfléchir aux éléments suivants :

-   Qui souhaitez-vous affecter au rôle : vous pouvez utiliser les applets de commande Azure Active Directory suivants pour savoir quels utilisateurs, groupes et principaux du service sont inclus dans votre annuaire.

    `azure ad user list
    azure ad user show
    azure ad group list
    azure ad group show
    azure ad group member list
    azure ad sp list
    azure ad sp show`

-   Quel rôle vous souhaitez affecter : vous pouvez utiliser la cmdlet suivante pour afficher les définitions de rôles prises en charge.

    `azure role list`

-   Quelle étendue vous souhaitez affecter : il existe trois niveaux d'étendue :

    -   L'abonnement actuel
    -   Un groupe de ressources. Pour obtenir la liste des groupes de ressources, entrez `azure group list`
    -   Une ressource. Pour obtenir la liste des ressources, entrez `azure resource list`

Ensuite, utilisez `azure role assignment create` pour créer une affectation de rôle. Par exemple :

-   Cela créera une affectation de rôle au niveau de l'abonnement actuel pour un utilisateur en tant que lecteur :

    `azure role assignment create --upn <user's email> -o Reader`

-   Cela créera une affectation de rôle au niveau d'un groupe de ressources :

    `PS C:PS C:\> azure role assignment create --upn <user's email> -o Contributor -g group1`

-   Cela créera une affectation de rôle au niveau d'une ressource :

    `azure role assignment create --upn <user's email> -o Owner -g group1 -r Microsoft.Web/sites -u site1`

## <span id="verify"></span></a>Vérification des autorisations

Après avoir contrôlé que votre abonnement inclut des affectations de rôles, vous pouvez afficher les autorisations que ces affectations de rôles vous octroient en exécutant :

    PS C:\> azure group list
    PS C:\> azure resource list

Ces deux cmdlets renverront uniquement les groupes de ressources ou ressources pour lesquels vous disposez d'une autorisation de lecture. Ils afficheront également les autorisations dont vous disposez.

Et donc, lorsque vous tentez d'exécuter d'autres cmdlets comme `azure group create`, vous recevez une erreur d’accès refusé si vous ne disposez pas de l'autorisation requise.

## <span id="next"></span></a>Étapes suivantes

Pour en savoir plus sur le contrôle d'accès basé sur un rôle à l'aide de xplat-cli, ainsi que les rubriques associées :

-   [Contrôle d'accès basé sur un rôle dans Windows Azure][Contrôle d'accès basé sur un rôle dans Windows Azure]
-   [Installation et configuration de l'interface de ligne de commande interplateforme Azure][Installation et configuration de l'interface de ligne de commande interplateforme Azure]
-   [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources][Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources]
-   [Utilisation des groupes de ressources pour gérer vos ressources Azure][Utilisation des groupes de ressources pour gérer vos ressources Azure] : apprenez à créer et gérer des groupes de ressources dans le portail de gestion Azure.
-   [Blog Azure][Blog Azure] : découvrez les nouvelles fonctionnalités d'Azure.
-   [Configuration du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell][Configuration du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell]
-   [Résolution des problèmes de contrôle d'accès basé sur un rôle][Résolution des problèmes de contrôle d'accès basé sur un rôle]

  [Interface de ligne de commande interplateforme]: /fr-fr/documentation/articles/powershell-rbac.md "Interface de ligne de commande interplateforme"
  [Windows PowerShell]: /fr-fr/documentation/articles/xplat-cli-rbac.md "Windows PowerShell"
  [Installation et configuration de l'interface de ligne de commande interplateforme Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/xplat-cli/
  [Utilisation de l'interface de ligne de commande interplateforme Azure avec le Gestionnaire de ressources]: http://azure.microsoft.com/fr-fr/documentation/articles/xplat-cli-azure-resource-manager/
  [Connexion à vos abonnements]: #connect
  [Contrôle des affectations de rôles existantes]: #check
  [Création d'une affectation de rôle]: #create
  [Vérification des autorisations]: #verify
  [Étapes suivantes]: #next
  [Contrôle d'accès basé sur un rôle dans Windows Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-configure/
  [Utilisation des groupes de ressources pour gérer vos ressources Azure]: http://azure.microsoft.com/fr-fr/documentation/articles/azure-preview-portal-using-resource-groups
  [Blog Azure]: http://blogs.msdn.com/windowsazure
  [Configuration du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell]: http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-powershell/
  [Résolution des problèmes de contrôle d'accès basé sur un rôle]: http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-troubleshooting/
