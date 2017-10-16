---
title: "Gérer les accès des utilisateurs avec les révisions d’accès Azure AD | Microsoft Docs"
description: "Découvrez comment gérer les accès utilisateurs en tant qu’appartenance à un groupe ou en tant qu’affectation à une application avec les révisions d’accès Azure Active Directory."
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 0459eb5cc71939202c8491f6b2714e28bd8e202d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gérer les accès des utilisateurs avec les révisions d’accès Azure AD

Azure Active Directory vous permet de vous assurer facilement que les utilisateurs disposent de l’accès approprié, en demandant aux utilisateurs eux-mêmes, ou à un décisionnaire, de prendre part à une révision d’accès et de renouveler la certification (ou d’effectuer « l’attestation ») de l’accès utilisateur.  Les réviseurs peuvent donner leur avis sur les besoins de chaque utilisateur en matière d’accès continu, en fonction des suggestions d’Azure AD. Lorsqu’une révision d’accès est terminée, vous pouvez effectuer des modifications et supprimer l’accès des utilisateurs qui n’en ont plus besoin.

> [!NOTE]
> Si vous souhaitez examiner uniquement les accès des utilisateurs invités, plutôt que tous les types d’accès utilisateurs, consultez l’article décrivant la [gestion des accès utilisateurs invités avec les révisions d’accès](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).  Si vous voulez examiner l’appartenance d’un utilisateur à un rôle d’administrateur tel que le rôle Administrateur général, consultez l’article [Comment démarrer une révision de l’accès dans Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Composants requis 

Les révisions d’accès sont disponibles avec l’édition Premium P2 de Azure Active Directory, incluse dans EMS E5. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).  Chaque utilisateur interagissant avec cette fonctionnalité pour la création d’une révision, la révision d’un accès ou l’application d’une révision, doit disposer d’une licence.


## <a name="creating-and-performing-an-access-review"></a>Création et exécution d’une révision d’accès

Vous pouvez avoir un ou plusieurs utilisateurs comme réviseurs dans une révision d’accès.  

1. Sélectionnez dans Azure Active Directory un groupe comportant un ou plusieurs membres, ou sélectionnez une application connectée à Azure Active Directory à laquelle un ou plusieurs utilisateurs ont été affectés. 
2. Décidez si vous souhaitez que chaque utilisateur révise son propre accès, ou si vous voulez qu’un ou plusieurs utilisateurs révisent l’accès de tout le monde.
3. Activez les révisions d’accès pour qu’elles apparaissent sur les volets d’accès du réviseur.  En tant qu’administrateur global, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 
4. Démarrez la révision d’accès. Pour plus d’informations, consultez l’article sur la [création d’une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).
5. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux recevra de la part d’Azure AD un e-mail contenant un lien vers le volet d’accès, dans lequel ils pourront [effectuer leur révision d’accès](active-directory-azure-ad-controls-perform-access-review.md).
6. Si les réviseurs n’ont pas donné leur avis, vous pouvez demander à Azure AD de leur envoyer un rappel.  Par défaut, Azure AD envoie automatiquement un rappel à mi-parcours de l’échéance fixée pour les réviseurs n’ayant pas encore répondu.
7. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez l’article décrivant [comment effectuer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Étapes suivantes

- [Create an access review for members of a group or access to an application](active-directory-azure-ad-controls-create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)




