---
title: "Gérer l’accès invité avec les révisions d’accès Azure AD | Documents Microsoft"
description: "Gérer les utilisateurs invités en tant que membres d’un groupe ou affectés à une application avec les révisions d’accès Azure Active Directory"
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
ms.openlocfilehash: b2a76945482e0fe51dc7cc46740cbb8f3f2ff643
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gérer l’accès invité avec les révisions d’accès Azure AD


Avec Azure Active Directory, vous pouvez facilement mettre en place une collaboration au-delà des limites de l’organisation à l’aide de la [fonctionnalité B2B de Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md). Les utilisateurs invités depuis d’autres locataires peuvent être [invités par les administrateurs](active-directory-b2b-admin-add-users.md) ou par [les utilisateurs finaux](active-directory-b2b-how-it-works.md).  Cela s’applique également aux identités de réseaux sociaux tels que des comptes Microsoft.

Vous pouvez également facilement vous assurer que les utilisateurs invités disposent de droits d’accès appropriés.  Vous pouvez le faire en demandant à un décisionnaire, ou bien aux invités eux-mêmes, de prendre part à une révision d’accès et de renouveler la certification (ou d’effectuer une « attestation ») pour l’accès invité. Les réviseurs peuvent donner leur avis sur les besoins de chaque utilisateur en matière d’accès continu, en fonction des suggestions d’Azure AD. Une fois la révision d’accès terminée, vous pouvez effectuer des modifications et supprimer l’accès des invités qui n’en ont plus besoin.

> [!NOTE]
> Ce document se concentre sur la revue de l’accès des utilisateurs invités. Si vous souhaitez vérifier l’accès de tous les utilisateurs, pas seulement celui des invités, veuillez lire le guide de [gestion des accès utilisateurs avec les révisions d’accès](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md).  Si vous voulez examiner l’appartenance d’un utilisateur à un rôle d’administrateur tel que le rôle Administrateur général, consultez l’article [Comment démarrer une révision de l’accès dans Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Composants requis 

Les révisions d’accès sont disponibles avec l’édition Premium P2 de Azure Active Directory, incluse dans EMS E5. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).  Chaque utilisateur interagissant avec cette fonctionnalité pour la création d’une révision, la révision d’un accès ou l’application d’une révision, doit disposer d’une licence.  

Si vous allez demander aux utilisateurs invités de revoir leur propre accès, apprenez-en plus sur la licence d’utilisateur invité dans le [guide d’attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md).

## <a name="creating-and-performing-an-access-review-for-guests"></a>Création et exécution d’une révision d’accès pour les invités

Tout d’abord, activez les révisions d’accès pour qu’elles apparaissent sur les volets d’accès du réviseur.  En tant qu’administrateur global, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

Azure AD permet plusieurs scénarios de revue des utilisateurs invités.  

Sélectionnez l’un des suivants :
 - un groupe dans Azure Active Directory qui dispose d’un ou plusieurs invités parmi ses membres 
 - ou une application connectée à Azure Active Directory possédant un ou plusieurs utilisateurs invités qui lui sont affectés et décidez de leur faire eux-mêmes revoir les accès, ou de les faire revoir par un ou plusieurs utilisateurs définis.

 Chacun de ces scénarios est traité dans l’une des sections suivantes.

* [Demander aux invités de revoir leur propre appartenance à un groupe](#asking-guests-to-review-their-own-membership-in-a-group)
* [Demander aux commanditaires de revoir l’appartenance des invités à un groupe](#asking-sponsors-to-review-guests-membership-in-a-group)
* [Demander aux invités de revoir leur propre accès à une application](#asking-guests-to-review-their-own-access-to-an-application)
* [Demander aux commanditaires de revoir l’accès des invités à une application](#asking-sponsors-to-review-guests-access-to-an-application) 
* [Demander aux invités de revoir leurs besoins d’accès en général](#asking-guests-to-review-their-need-for-access-in-general)


### <a name="asking-guests-to-review-their-own-membership-in-a-group"></a>Demander aux invités de revoir leur propre appartenance à un groupe

Les révisions d’accès peuvent être utilisées pour s’assurer que les utilisateurs qui ont été invités et ajoutés à un groupe ont toujours besoin de leur accès.  Un moyen simple consiste à demander aux invités eux-mêmes de revoir leur appartenance au groupe.

1. Démarrez une révision d’accès pour le groupe, en choisissant d’y inclure uniquement les utilisateurs invités, et faites-leur faire la révision eux-mêmes. Pour plus d’informations, consultez l’article sur la [création d’une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).
2. Demandez à chaque invité de revoir leur propre appartenance.  Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part de Azure AD comprenant un lien vers la révision d’accès.  Azure AD dispose d’instructions destinées aux invités concernant [la vérification de leur accès](active-directory-azure-ad-controls-perform-access-review.md).
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez l’article décrivant [comment effectuer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md). 
4. En plus des utilisateurs indiquant n’avoir plus besoin d’un accès continu, vous pouvez souhaiter de retirer les utilisateurs n’ayant pas répondu.  La raison en est que les utilisateurs n’ayant pas répondu ne reçoivent peut-être plus de courrier électronique.
5. Si le groupe n’est pas utilisé pour la gestion des accès, vous pouvez souhaiter le retrait des utilisateurs qui n’ont pas été sélectionnés pour participer à la révision car ils ne pas accepté leur invitation.  Cela peut indiquer une faute de frappe dans l’adresse de messagerie de l’utilisateur invité.  Toutefois, si un groupe est utilisé comme groupe de distribution, il est possible que certains utilisateurs invités n’aient pas été sélectionnés pour participer car ce sont des objets de contact.

### <a name="asking-sponsors-to-review-guests-membership-in-a-group"></a>Demander aux commanditaires de revoir l’appartenance des invités à un groupe

Vous pouvez demander à un commanditaire, tel que le propriétaire d’un groupe, de revoir le besoin de l’invité de disposer d’une appartenance continue au groupe.

1. Démarrez une révision d’accès pour le groupe, en choisissant d’inclure uniquement les utilisateurs invités, et en spécifiant un ou plusieurs réviseurs. Pour plus d’informations, consultez l’article sur la [création d’une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).
2. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux recevra de la part d’Azure AD un e-mail contenant un lien vers le volet d’accès, dans lequel ils pourront [effectuer leur révision d’accès](active-directory-azure-ad-controls-perform-access-review.md).
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez l’article décrivant [comment effectuer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-own-access-to-an-application"></a>Demander aux invités de revoir leur propre accès à une application

Les révisions d’accès peuvent être utilisées pour s’assurer que les utilisateurs invités pour une application particulière ont toujours besoin de leur accès.  Un moyen simple consiste à demander aux invités eux-mêmes de revoir leur besoin d’accès.

1. Démarrez une révision d’accès pour l’application, en choisissant d’inclure uniquement les invités, et en choisissant de leur faire revoir leur accès eux-mêmes. Pour plus d’informations, consultez l’article sur la [création d’une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).
2. Demandez à chaque invité de revoir son propre accès à l’application.  Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part de Azure AD comprenant un lien vers la révision d’accès dans le panneau d’accès de votre organisation.  Azure AD dispose d’instructions destinées aux invités concernant [la vérification de leur accès](active-directory-azure-ad-controls-perform-access-review.md).
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez l’article décrivant [comment effectuer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).
4. En plus des utilisateurs indiquant n’avoir plus besoin d’un accès continu, vous pouvez souhaiter de retirer les utilisateurs invités qui n’ont pas répondu, étant donné qu’ils ne reçoivent peut-être plus de courrier électronique.  Vous pouvez également souhaiter le retrait des utilisateurs invités n’ayant pas été sélectionnés pour participer, surtout si l’invité n’a pas été invité récemment, étant donné que ces utilisateurs n’ont pas accepté leur invitation et n’auraient donc pas accès à l’application. 

### <a name="asking-sponsors-to-review-guests-access-to-an-application"></a>Demander aux commanditaires de revoir l’accès des invités à une application


Vous pouvez demander à un commanditaire, tel que le propriétaire d’une application, de revoir le besoin de l’invité à disposer d’un accès continu à l’application.

1. Démarrez une révision d’accès pour l’application, en choisissant d’inclure uniquement les invités, et en nommant un ou plusieurs réviseurs parmi les utilisateurs. Pour plus d’informations, consultez l’article sur la [création d’une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).
2. Demandez aux réviseurs de donner leur avis. Par défaut, chacun d’eux recevra de la part d’Azure AD un e-mail contenant un lien vers le volet d’accès, dans lequel ils pourront [effectuer leur révision d’accès](active-directory-azure-ad-controls-perform-access-review.md).
3. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès et appliquez les modifications. Pour plus d’informations, consultez l’article décrivant [comment effectuer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="asking-guests-to-review-their-need-for-access-in-general"></a>Demander aux invités de revoir leurs besoins d’accès d’une façon générale

Dans certaines organisations, les invités peuvent ne pas être conscients de leur appartenance au groupe.

> [!NOTE]
> Les versions antérieures du portail Azure ne permettaient pas un accès administratif par les utilisateurs invités, et dans certains cas, il est possible qu’un administrateur de votre répertoire ait modifié la valeur UserType d’un invité pour en faire un membre, en utilisant PowerShell.  Si cela s’est déjà produit dans votre répertoire, la requête ci-dessus est susceptible de ne pas inclure tous les utilisateurs invités disposant de droits d’accès administratifs depuis toujours, vous devrez donc modifier la valeur UserType de l’invité, ou bien les inclure manuellement dans le groupe.

1. Créez un groupe de sécurité dans Azure AD avec les invités comme membres, si un groupe approprié n’existe pas déjà.  Par exemple, vous pouvez souhaiter la création d’un groupe disposant d’une appartenance des invités gérée manuellement.  Ou bien, vous pouvez également souhaiter la création d’un groupe dynamique doté d’un nom tel que « Invités de Contoso » pour les utilisateurs dans le client Contoso disposant d’une valeur d’attribut UserType configurée sur Invité.
2. Démarrez une révision d’accès pour ce groupe, en choisissant les membres comme réviseurs. Pour plus d’informations, consultez l’article sur la [création d’une révision d’accès](active-directory-azure-ad-controls-create-access-review.md).
3. Demandez à chaque invité de revoir leur propre appartenance.  Par défaut, chaque invité ayant accepté une invitation recevra un message électronique de la part de Azure AD comprenant un lien vers la révision d’accès dans le panneau d’accès de votre organisation.  Azure AD dispose d’instructions destinées aux invités concernant [la vérification de leur accès](active-directory-azure-ad-controls-perform-access-review.md).
4. Une fois que les réviseurs ont fait part de leur avis, arrêtez la révision d’accès. Pour plus d’informations, consultez l’article décrivant [comment effectuer une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md).
5. Retirez l’accès invité aux invités ayant été refusés, n’ayant pas terminés la révision ou bien n’ayant pas acceptés leur invitation.   Si certains des invités sont des contacts sélectionnés pour participer à la révision car ils n’avaient précédemment pas accepté une invitation, vous pouvez souhaiter de désactiver la connexion à leur compte, à l’aide du portail Azure ou de PowerShell.  Si l’invité n’a plus besoin d’un accès et n’est pas un contact, son objet utilisateur peut être supprimé de votre répertoire, à l’aide du portail Azure ou de PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- [Create an access review for members of a group or access to an application](active-directory-azure-ad-controls-create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)







