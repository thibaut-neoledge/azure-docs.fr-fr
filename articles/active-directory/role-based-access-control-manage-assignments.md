---
title: "Afficher les affectations d’accès aux ressources Azure | Microsoft Docs"
description: "Affichez et gérez toutes les affectations de contrôle d’accès en fonction du rôle des utilisateurs ou groupes sur le Portail Azure"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 87fa4373e5496a91d4e808ceb45095a6aea9e884


---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Afficher les affectations d’accès des utilisateurs et des groupes sur le Portail Azure - Version préliminaire publique
> [!div class="op_single_selector"]
> * [Gérer l’accès par utilisateur ou par groupe](role-based-access-control-manage-assignments.md)
> * [Gérer l’accès par ressource](role-based-access-control-configure.md)
> 
> 

Avec le contrôle d’accès en fonction du rôle (RBAC), intégré à la version préliminaire d’Azure Active Directory, vous pouvez gérer l’accès à vos ressources Azure. [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)

Les accès affectés avec le RBAC sont précis car il existe deux façons de restreindre les autorisations :

* **Étendue :** les affectations de rôles RBAC sont limitées à un abonnement, à un groupe de ressources ou à une ressource donnés. Un utilisateur ayant accès à une ressource en particulier ne peut pas accéder aux autres ressources du même abonnement.
* **Rôle :** au sein de l’affectation d’étendue, l’accès est encore restreint par l’affectation d’un rôle. Les rôles peuvent être de haut niveau, comme propriétaire, ou spécifiques, comme lecteur de machines virtuelles.

Les rôles peuvent être affectés au sein de l’abonnement, du groupe de ressources ou de la ressource qui constitue l’étendue de l’affectation. Mais vous pouvez afficher toutes les affectations d’accès d’un groupe ou d’un utilisateur donné en un seul et même endroit.

Pour en savoir plus, consultez [Utiliser les affectations de rôle pour gérer l’accès à vos ressources d’abonnement Azure](role-based-access-control-configure.md).

## <a name="view-access-assignments"></a>Afficher les affectations d’accès
Pour rechercher les affectations d’accès d’un utilisateur ou d’un groupe en particulier, commencez dans Azure Active Directory sur le [Portail Azure](http://portal.azure.com).

1. Sélectionnez **Azure Active Directory**. Si cette option n’est pas visible dans votre liste de navigation, sélectionnez **Plus de services**, puis faites défiler vers le bas jusqu’à **Azure Active Directory**.
2. Sélectionnez **Utilisateurs et groupes**, puis **Tous les utilisateurs** ou **Tous les groupes**. Pour cet exemple, nous nous concentrons sur les utilisateurs.
    ![Gérer les utilisateurs et les groupes dans Azure Active Directory - capture d’écran](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Recherchez l’utilisateur par nom ou par nom d’utilisateur.
4. Sélectionnez **Ressources Azure** sur le panneau de l’utilisateur. Toutes les affectations d’accès de cet utilisateur s’affichent.

### <a name="read-permissions-to-view-assignments"></a>Autorisations de lecture pour afficher les affectations
Cette page affiche uniquement les affectations d’accès que vous êtes autorisé à lire. Par exemple, vous avez un accès en lecture à l’abonnement A et vous allez sur le panneau des ressources Azure pour consulter les affectations d’un utilisateur. Vous pouvez voir ses affectations d’accès pour l’abonnement A, mais vous ne verrez pas qu’il a également des affectations d’accès à l’abonnement B.

## <a name="delete-access-assignments"></a>Supprimer des affectations d’accès
Sur ce panneau, vous pouvez supprimer les affectations d’accès affectées directement à un utilisateur ou un groupe. Si l’affectation d’accès a été héritée d’un groupe parent, vous devez accéder à la ressource ou l’abonnement et y gérer l’affectation.

1. Dans la liste de toutes les affectations d’accès d’un utilisateur ou d’un groupe, sélectionnez celle que vous souhaitez supprimer.
2. Sélectionnez **Supprimer**, puis **Oui** pour confirmer.
    ![Supprimer l’affectation de l’accès - capture d’écran](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="related-topics"></a>Rubriques connexes
* Familiarisez-vous avec le contrôle d’accès en fonction du rôle afin [d’Utiliser les affectations de rôle pour gérer l’accès à vos ressources d’abonnement Azure](role-based-access-control-configure.md)
* Consultez les [rôles RBAC intégrés](role-based-access-built-in-roles.md)




<!--HONumber=Nov16_HO3-->


