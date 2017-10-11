---
title: "Contrôle d’accès en fonction du rôle (RBAC) Azure pour contrôler les droits d’accès en vue de créer et de gérer des demandes de support | Microsoft Docs"
description: "Contrôle d’accès en fonction du rôle (RBAC) Azure pour contrôler les droits d’accès en vue de créer et de gérer des demandes de support"
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 20ebd324cbf379980b43d255d468673de2b6d950
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Contrôle d’accès en fonction du rôle (RBAC) Azure pour contrôler les droits d’accès en vue de créer et de gérer des demandes de support

Le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) permet une gestion précise de l’accès pour Azure.
La création de demandes de support dans le portail Azure, [portal.azure.com](https://portal.azure.com), utilise le modèle RBAC d’Azure pour définir qui peut créer et gérer les demandes de support.
L’accès est octroyé en attribuant le rôle RBAC approprié aux utilisateurs, groupes et applications, dans une étendue donnée, qui peut correspondre à un abonnement, un groupe de ressources ou une ressource.

Prenons un exemple : en tant que propriétaire d’un groupe de ressources avec des autorisations de lecture dans l’étendue de l’abonnement, vous pouvez gérer toutes les ressources du groupe de ressources, telles que les sites Web, les machines virtuelles et les sous-réseaux.
Toutefois, lorsque vous essayez de créer une demande de support par rapport à la ressource de machine virtuelle, vous rencontrez l’erreur suivante :

![Erreur relative à l’abonnement](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Dans la gestion des demandes de support, vous devez disposer d’autorisations d’écriture ou d’un rôle qui possède l’action de support Microsoft.Support/* dans l’étendue de l’abonnement pour pouvoir créer et gérer des demandes de support.

L’article suivant explique comment vous pouvez utiliser le contrôle RBAC personnalisé d’Azure pour créer et gérer les demandes de support dans le portail Azure.

## <a name="getting-started"></a>Mise en route

À l’aide de l’exemple ci-dessus, vous devriez être en mesure de créer une demande de support pour votre ressource, si un rôle RBAC personnalisé vous a été affecté pour l’abonnement par son propriétaire.
Il est possible de créer des [rôles RBAC personnalisés](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) à l’aide d’Azure PowerShell, de l’interface de ligne de commande Azure et de l’API REST.

La propriété Actions d’un rôle personnalisé spécifie les opérations Azure auxquelles le rôle accorde l’accès.
Pour créer un rôle personnalisé pour la gestion des demandes de support, le rôle doit disposer de l’action Microsoft.Support/*.

Voici un exemple de rôle personnalisé que vous pouvez utiliser pour créer et gérer des demandes de support.
Nous avons appelé ce rôle « Collaborateur de demande de support » et c’est ainsi que nous nous référerons au rôle personnalisé dans cet article.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Suivez les étapes décrites dans [cette vidéo](https://www.youtube.com/watch?v=-PaBaDmfwKI) pour apprendre à créer un rôle personnalisé pour votre abonnement.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Créer et gérer des demandes de support dans le portail Azure

Prenons un exemple : vous êtes le propriétaire de l’abonnement « Abonnement Visual Studio MSDN ».
Jean, votre collègue, est propriétaire des ressources de certains groupes de ressources dans cet abonnement et dispose des autorisations de lecture sur l’abonnement.
Vous souhaitez que votre collègue Jean ait la possibilité de créer et de gérer les tickets de support pour les ressources de cet abonnement.

1. La première étape consiste à accéder à l’abonnement, puis, sous « Paramètres », vous verrez la liste des utilisateurs. Cliquez sur l’utilisateur Jean qui a accès en lecture à l’abonnement et affectez-lui un nouveau rôle personnalisé.

    ![Ajouter un rôle](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Cliquez sur « Ajouter » dans le panneau « Utilisateurs ». Sélectionnez le rôle personnalisé « Collaborateur de demande de support » dans la liste des rôles.

    ![Ajouter le rôle Collaborateur du support](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. Après avoir sélectionné le nom du rôle, cliquez sur « Ajouter des utilisateurs » et entrez les informations d’identification de la messagerie de Jean. Cliquez sur « Sélectionner ».

    ![Ajouter des utilisateurs](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Cliquez sur « OK » pour continuer.

    ![Ajout d'accès](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Vous voyez maintenant l’utilisateur avec le rôle personnalisé que vous venez d’ajouter « Collaborateur de demande de support » sous l’abonnement dont vous êtes le propriétaire.

    ![Utilisateur ajouté](./media/create-manage-support-requests-using-access-control/user-added.png)

    Lorsque Jean ouvre une session dans le portail, il voit l’abonnement auquel il a été ajouté.

7. Jean clique sur « Nouvelle demande de support » dans le panneau « Aide et support » et il peut créer des demandes de support pour « Visual Studio Ultimate avec MSDN ».

    ![Nouvelle demande de support](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Cliquant sur « Tous en charge les demandes » Joe peut afficher la liste des demandes de support créé pour cet abonnement ![cas afficher les détails](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Supprimer l’accès aux demandes de support dans le portail Azure

De la même manière qu’il est possible d’accorder l’accès à un utilisateur pour créer et gérer des demandes de support, il est possible de le supprimer.
Pour supprimer la possibilité de créer et de gérer les demandes de support, accédez à l’abonnement, cliquez sur « Paramètres », puis sur l’utilisateur (dans ce cas, Jean).
Cliquez avec le bouton droit sur le nom du rôle, « Collaborateur de demande de support » et cliquez sur « Supprimer ».

![Supprimer l’accès aux demandes de support](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Lorsque Jean se connecte au portail et tente de créer une demande de support, il rencontre l’erreur suivante :

![Erreur relative à l’abonnement-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Jean ne peut pas voir les demandes de support lorsqu’il clique sur « All support requests » (Toutes les demandes de support).

![Vue Détails des cas-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
