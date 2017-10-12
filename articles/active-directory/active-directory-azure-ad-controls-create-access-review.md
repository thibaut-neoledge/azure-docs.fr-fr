---
title: "Créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application avec Azure AD| Microsoft Docs"
description: "Découvrez comment créer une révision d’accès pour les membres d’un groupe ou des utilisateurs ayant accès à une application."
services: active-directory
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
ms.openlocfilehash: c2c5aee3db11255be5a6fe405424c2cbb92c5b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Créer une révision d’accès des membres du groupe ou un accès aux applications avec Azure AD

Les attributions d’accès deviennent « obsolètes » lorsque les utilisateurs bénéficient d’un accès dont ils n’ont plus besoin.  Afin de réduire les risques associés aux affectations d’accès par état, les administrateurs peuvent demander une révision des membres du groupe ou des utilisateurs affectés à une application en créant une révision d’accès. Les guides relatifs à la [gestion des accès utilisateur](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) et à la [gestion des accès invité](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md) fournissent davantage d’informations sur ces scénarios.  

## <a name="how-to-create-an-access-review"></a>Comment créer une révision d’accès


1. En tant qu’administrateur général, accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) et ouvrez l’onglet **Programmes**.
2. Cliquez sur le programme qui contiendra le contrôle de révision d’accès que vous souhaitez créer.  Il existe un programme, « programme par défaut », toujours présent, sinon vous pouvez créer un autre programme.  Par exemple, vous pouvez choisir de disposer d’un programme pour chaque initiative de conformité ou objectif de l’entreprise.
3. Dans le programme, cliquez sur Contrôles, puis cliquez sur le bouton **Ajouter** pour ajouter un contrôle.
4. Chaque révision d’accès doit avoir un nom et peut également avoir une description.  Le nom est visible par les réviseurs.  
5. Par défaut, une révision d’accès démarre le jour même de sa création et est planifiée pour s’arrêter un mois plus tard.  Vous pouvez modifier les dates de début et de fin pour que la révision d’accès démarre ultérieurement et dure le nombre de jours souhaité.
6. Les révisions d’accès peuvent porter sur les membres d’un groupe ou sur les utilisateurs qui ont été assignés à une application.  Vous pouvez affiner davantage la révision d’accès pour passer en revue uniquement les utilisateurs invités qui sont membres (ou assignés à l’application) au lieu d’examiner tous les utilisateurs qui sont membres ou qui ont accès à l’application.
7. Vous pouvez sélectionner une ou plusieurs personnes pour passer en revue tous les utilisateurs ciblés, ou vous pouvez choisir de laisser les membres examiner leur propre accès.  Si la ressource est un groupe, vous pouvez demander aux propriétaires de groupe d’effectuer la révision.  Vous pouvez également demander à ce que les réviseurs fournissent un motif lorsqu’ils approuvent l’accès.
8. Enfin, cliquez sur **Démarrer**.


## <a name="managing-the-access-review"></a>Gestion de la révision d’accès

Par défaut, Azure AD envoie un e-mail aux réviseurs peu de temps après le démarrage de la révision.  Si vous ne souhaitez pas qu’Azure AD envoie cet e-mail, veillez à informer les réviseurs qu’une révision d’accès leur a été assignée.  Vous pouvez leur indiquer les instructions sur la [façon de réviser les accès](active-directory-azure-ad-controls-perform-access-review.md), ou si la révision s’adresse aux invités qui doivent réviser leur propre accès, les instructions sur la [façon de réviser son propre accès](active-directory-azure-ad-controls-perform-access-review.md).

Notez que si certains réviseurs sont invités, ces derniers seront uniquement notifiés par e-mail s’ils ont déjà accepté leur invitation.


Vous pouvez suivre la progression des révisions des réviseurs dans le tableau de bord Azure AD, dans la section des révisions d’accès. Aucun droit d’accès ne sera modifié dans le répertoire avant que [la révision ne soit terminée](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Étapes suivantes

Lorsqu’une révision d’accès a démarré, Azure AD envoie automatiquement aux réviseurs un e-mail les invitant à réviser l’accès. Si un utilisateur n’a pas reçu d’e-mail, vous pouvez lui envoyer les instructions sur la [façon de réviser un accès](active-directory-azure-ad-controls-perform-access-review.md).  

Une fois la période de révision d’accès terminée ou si l’administrateur a arrêté la révision d’accès, suivez les étapes décrivant la [réalisation d’une révision d’accès](active-directory-azure-ad-controls-complete-access-review.md) pour voir et appliquer les résultats.


