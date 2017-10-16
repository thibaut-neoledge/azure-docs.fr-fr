---
title: "Réviser les accès à une application des membres d’un groupe ou des utilisateurs avec Azure AD| Microsoft Docs"
description: "Découvrez comment réviser les accès des membres d’un groupe ou des utilisateurs ayant accès à une application dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: e83bc42d658c4d6304f98b99d0e0942595bd65d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Complete an access review of members of a group or user access to an application in Azure AD (Effectuer une révision d’accès des membres d’un groupe ou des accès des utilisateurs à une application dans Azure AD)

Les administrateurs peuvent demander une révision des membres du groupe ou des utilisateurs assignés à une application en [créant une révision d’accès](active-directory-azure-ad-controls-create-access-review.md). Azure AD envoie automatiquement aux réviseurs un e-mail les invitant à réviser l’accès. Si un utilisateur n'a pas reçu d’e-mail, vous pouvez lui envoyer les instructions dans [Comment réviser un accès](active-directory-azure-ad-controls-perform-access-review.md).  Une fois la période de révision d’accès terminée ou si l’administrateur a arrêté la révision d’accès, suivez les étapes décrites dans cet article pour voir et appliquer les résultats.

## <a name="viewing-an-access-review-in-the-azure-portal"></a>Affichage d’une révision d’accès dans le portail Azure

1. Accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), passez à l’onglet **Programmes** et sélectionnez le programme qui contient le contrôle de révision d’accès.
2. Cliquez sur Gérer et sélectionnez le contrôle de révision d’accès.  S’il existe plusieurs contrôles dans le programme, vous pouvez filtrer les contrôles d’un type spécifique, les trier selon leur état ou effectuer une recherche sur le nom du contrôle de révision d’accès ou le nom d’affichage du propriétaire qui l’a créé. 

## <a name="stopping-a-review-that-has-not-yet-completed"></a>Arrêt d’une révision non terminée

Si la révision n’a pas encore atteint sa date de fin planifiée, un administrateur peut l’arrêter plus tôt en cliquant sur le bouton **Arrêter**.  Si les utilisateurs n’ont pas été révisés à cette date, ils ne pourront plus le faire une fois la révision arrêtée. Vous ne pouvez pas redémarrer une révision arrêtée.

## <a name="applying-the-changes"></a>Application des changements 

Lorsqu’une révision d’accès est terminée, soit parce qu’elle a atteint sa date de fin soit parce qu’un administrateur y a mis fin manuellement, le bouton **Appliquer** implémente le résultat de la révision en mettant à jour le groupe ou l’application. Si l’accès d’un utilisateur a été refusé lors de la révision, lorsque l’administrateur clique sur ce bouton, Azure AD supprime l’appartenance de cet utilisateur ou son assignation à l’application. 

Le bouton Appliquer n’aura pas d’effet pour un groupe qui provient d’un répertoire local ou d’un groupe dynamique.  Si vous souhaitez modifier un groupe qui provient d’un répertoire local, téléchargez les résultats et appliquez ces modifications à la représentation du groupe dans ce répertoire.

## <a name="downloading-the-results-of-the-review"></a>Téléchargement des résultats de la révision

Pour récupérer les résultats de la révision, accédez à l’onglet Approbations et cliquez sur le bouton **Télécharger**.  Le fichier CSV généré peut être affiché dans Excel ou dans d’autres programmes qui permettent d’ouvrir les fichiers CSV.

## <a name="optional-deleting-a-review"></a>Facultatif : suppression d’une révision
Si vous n'êtes plus intéressé par la révision, supprimez-la. Le bouton **Supprimer** permet de supprimer la révision d’Azure AD.

> [!IMPORTANT]
> Il n’y a pas d’avertissement avant la suppression, alors assurez-vous que vous voulez bien supprimer cette révision.
> 
> 

## <a name="next-steps"></a>Étapes suivantes

- [Manage user access with Azure AD access reviews](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) (Gérer l’accès des utilisateurs avec les révisions d’accès Azure AD)
- [Manage guest access with Azure AD access reviews](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) (Gérer l’accès des invités avec les révisions d’accès Azure AD)
- [Manage programs and controls for Azure AD access reviews](active-directory-azure-ad-controls-manage-programs-controls.md) (Gérer les programmes et les contrôles pour la révision d’accès Azure AD)
- [Create an access review for members of a group or access to an application](active-directory-azure-ad-controls-create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)
- [Create an access review of users in an Azure AD administrative role](active-directory-privileged-identity-management-how-to-start-security-review.md) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)
