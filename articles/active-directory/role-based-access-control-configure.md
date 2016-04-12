<properties
	pageTitle="Contrôle d’accès en fonction du rôle Azure Active Directory | Microsoft Azure"
	description="Découvrez la gestion de l’accès avec le contrôle d’accès en fonction du rôle Azure dans le portail Azure. Utilisez des attributions de rôle pour attribuer des autorisations dans votre répertoire."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/30/2016"
	ms.author="kgremban"/>

# Contrôle d’accès en fonction du rôle Azure

## Contrôle d’accès en fonction du rôle
Le contrôle d’accès en fonction du rôle (RBAC) Azure permet une gestion précise de l’accès pour Azure. Avec le contrôle d’accès en fonction du rôle, vous pouvez séparer les tâches au sein de votre équipe chargée des opérations de développement et accorder aux utilisateurs uniquement les accès nécessaires pour accomplir leur travail. Cet article présente les principes fondamentaux de la gestion de l’accès, puis vous aide à utiliser le contrôle d’accès en fonction du rôle dans le portail Azure.

### Concepts de base de la gestion d’accès dans Azure
Chaque abonnement Azure est associé à un répertoire Azure Active Directory (AD). Les utilisateurs, groupes et applications de ce répertoire peuvent gérer les ressources de l’abonnement Azure. Ces droits d’accès sont accordés à l’aide du portail Azure, des outils en ligne de commande Azure ou des API de gestion Azure.

L’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans une étendue donnée. L’étendue d’une attribution de rôle peut être une seule ressource, un groupe de ressources ou un abonnement. Un rôle attribué à une étendue parent accorde également l’accès aux enfants qu’elle contient. Par exemple, un utilisateur ayant accès à un groupe de ressources peut gérer toutes les ressources qu’il contient, telles que les sites Web, les machines virtuelles et les sous-réseaux.

![Relation entre les éléments d’Azure Active Directory - diagramme](./media/role-based-access-control-configure/rbac_aad.png)

Le rôle RBAC que vous attribuez détermine les ressources que l’utilisateur, le groupe ou l’application peut gérer au sein de l’étendue.

### Rôles intégrés
Le contrôle d’accès en fonction du rôle Azure comporte trois rôles de base qui s’appliquent à tous les types de ressources :

- Le **Propriétaire** dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes.
- Le **Contributeur** peut créer et gérer tous les types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes.
- Le **Lecteur** peut consulter les ressources Azure existantes.

Les autres rôles RBAC dans Azure permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle de contributeur de machine virtuelle permet à l’utilisateur de créer et gérer des machines virtuelles. Il ne lui donne pas accès au réseau virtuel ou au sous-réseau auquel la machine virtuelle se connecte.

L’article [Rôles RBAC intégrés](role-based-access-built-in-roles.md) répertorie les rôles disponibles dans Azure. Elle indique les opérations et l’étendue que chaque rôle intégré accorde aux utilisateurs. Si vous avez besoin de définir vos propres rôles pour un meilleur contrôle, découvrez comment créer [des rôles personnalisés dans RBAC Azure](role-based-access-control-custom-roles.md).

### Hiérarchie des ressources et héritage d’accès
- Chaque **abonnement** Azure appartient à un seul répertoire.
- Chaque **groupe de ressources** appartient à un seul abonnement.
- Chaque **ressource** appartient à un seul groupe de ressources.

L’accès que vous accordez à des étendues parentes est hérité par les étendues enfant. Par exemple :

- Vous attribuez le rôle de lecteur à un groupe Azure AD dans l’étendue de l’abonnement. Les membres de ce groupe peuvent consulter tous les groupes de ressources et les ressources de l’abonnement.  
- Vous attribuez le rôle de contributeur à une application dans l’étendue du groupe de ressources. Il peut gérer tous les types de ressources dans ce groupe de ressources, mais pas dans les autres groupes de ressources de l’abonnement.

### Comparaison entre RBAC Azure et Administrateur d’abonnement classiques
Les administrateurs et coadministrateurs d’abonnement classiques ont un accès total à l’abonnement Azure. Ils peuvent gérer des ressources à l’aide du [portail Azure](https://portal.azure.com) avec des API Azure Resource Manager ou du [portail Azure Classic](https://manage.windowsazure.com) et des API Gestion des services Azure. Dans le modèle RBAC, les administrateurs classiques se voient attribuer le rôle de propriétaire dans l’étendue de l’abonnement.

RBAC Azure est pris en charge uniquement par le portail Azure et les nouvelles API Azure Resource Manager. Les utilisateurs et les applications qui se voient attribuer des rôles RBAC ne peuvent pas utiliser le portail de gestion classique et les API de gestion des services Azure.

### Autorisation pour les opérations de gestion et les opérations de données
RBAC Azure prend uniquement en charge les opérations de gestion des ressources Azure dans le portail Azure et les API Azure Resource Manager. Toutes les opérations de niveau de données pour les ressources Azure ne peuvent pas être autorisées via le RBAC. Par exemple, les comptes de stockage peuvent être gérés avec RBAC, mais pas les blobs ou les tables qu’ils contiennent. De même, une base de données SQL peut être gérée, mais pas les tables qu’elle contient.

## Gérer l’accès à l’aide du portail Azure
### Voir l’accès
Vous pouvez voir qui a accès à une ressource, un groupe de ressources ou un abonnement à partir de son panneau principal dans le [portail Azure](https://portal.azure.com). Par exemple, nous voulons voir qui a accès à l’un de nos groupes de ressources :

1. Sélectionnez l’icône **Groupe de ressources** dans la barre de navigation de gauche.
2. Sélectionnez le nom du groupe de ressources que vous souhaitez examiner à partir du panneau **Groupes de ressources**.
3. Sélectionnez l’icône **Utilisateurs** en haut à droite du panneau de groupe de ressources.
4. Le panneau **Utilisateurs** répertorie l’ensemble des utilisateurs, groupes et applications qui peuvent accéder au groupe de ressources.

![Panneau Utilisateurs - comparaison Accès hérité et accès affecté (capture d’écran)](./media/role-based-access-control-configure/view-access.png)

Notez que certains utilisateurs se sont vu **attribuer** l’accès tandis que d’autres en ont **hérité**. L’accès est attribué spécifiquement au groupe de ressources ou hérité d’une affectation à l’abonnement parent.

> [AZURE.NOTE] Les administrateurs et coadministrateurs d’abonnement classiques sont considérés comme propriétaires de l’abonnement dans le nouveau modèle RBAC.


### Ajout d’un accès
Vous accordez l’accès à partir de la ressource, du groupe de ressources ou de l’abonnement qui correspond à l’étendue de l’attribution du rôle.

1. Cliquez sur l’icône **Ajouter** du panneau **Utilisateurs**. ![Panneau Ajouter un accès - sélectionner un rôle (capture d’écran)](./media/role-based-access-control-configure/grant-access1.png)
2. Sélectionnez le rôle que vous voulez attribuer.
3. Sélectionnez l’utilisateur, le groupe ou l’application de votre répertoire auquel vous voulez accorder l’accès. Vous pouvez effectuer votre recherche dans le répertoire à l’aide de noms d’affichage, d’adresses de messagerie et d’identificateurs d’objet. ![Panneau Ajouter des utilisateurs - rechercher (capture d’écran)](./media/role-based-access-control-configure/grant-access2.png)

### Supprimer un accès

1. Dans le panneau **Users** (Utilisateurs), sélectionnez l’affectation de rôle à supprimer.
2. Cliquez sur l’icône **Remove** (Supprimer) dans le panneau des détails de l’affectation.
3. Cliquez sur **Yes** pour confirmer.

![Panneau Utilisateurs - suppression du rôle (capture d’écran)](./media/role-based-access-control-configure/remove-access1.png)

Les attributions héritées ne peuvent pas être supprimées des étendues enfant. Accédez à l’étendue parent pour supprimer l’attribution du rôle.

![Panneau Utilisateurs - l’accès hérité désactive le bouton (capture d’écran)](./media/role-based-access-control-configure/remove-access2.png)

## Autres outils pour gérer l’accès
Vous pouvez attribuer des rôles et gérer l’accès avec les commandes RBAC Azure dans des outils autres que le portail Azure. Suivez les liens pour en savoir plus sur les conditions préalables et pour vous familiariser avec les commandes RBAC Azure.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
- [API REST](role-based-access-control-manage-access-rest.md)

## Étapes suivantes
- [Créer un rapport d’historique des modifications d’accès](role-based-access-control-access-change-history-report.md)
- Voir les [rôles RBAC intégrés](role-based-access-built-in-roles.md)
- Définir vos propres [rôles personnalisés dans RBAC Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0406_2016-->