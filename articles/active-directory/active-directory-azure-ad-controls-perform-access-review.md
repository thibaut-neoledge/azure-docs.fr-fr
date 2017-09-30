---
title: "Réviser les accès avec les révisions d’accès Azure AD | Microsoft Docs"
description: "Réviser les accès avec les révisions d’accès Azure AD."
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
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: c9115bb5bc3d22c67ea0ac77b8972ae7f919926b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---

# <a name="how-to-review-access-with-azure-ad-access-reviews"></a>Réviser les accès avec les révisions d’accès Azure AD

Azure Active Directory simplifie la manière dont les entreprises gèrent l’accès aux applications et aux membres des groupes dans Azure AD et d’autres services en ligne Microsoft, avec une fonction appelée révision d’accès.  Vous avez peut-être reçu un e-mail de la part de Microsoft vous invitant à réviser l’accès, les membres d’un groupe ou les utilisateurs ayant accès à une application. 

## <a name="opening-an-access-review"></a>Ouverture d’une révision d’accès

Vous pouvez voir les révisions d’accès en attente en cliquant sur le lien dans l’e-mail.  Si vous n’avez pas reçu l’e-mail, vous pouvez localiser les révisions d’accès en procédant comme suit :

1. Connectez-vous au [panneau d’accès Azure AD](https://myapps.microsoft.com).
2. Cliquez sur l’icône de l’utilisateur dans le coin supérieur droit de la page, qui affiche votre nom et organisation par défaut. Si plusieurs organisations sont listées, sélectionnez l’organisation qui a demandé une révision d’accès.
3. Si vous voyez une vignette étiquetée **Révisions d’accès** à droite de la page, cliquez dessus. Si la vignette n’est pas visible, cela signifie qu’il n’y a pas de révisions d’accès à effectuer pour cette organisation et qu’aucune action n’est nécessaire pour l’instant.

## <a name="filling-out-an-access-review"></a>Remplissage d’une révision d’accès

Sélectionnez une révision d’accès dans la liste et vous verrez les noms des utilisateurs qui doivent être révisés.  Si la demande portait sur la révision d’un de vos propres accès, vous ne verrez que votre nom.

Pour chaque ligne de la liste, vous pouvez décider d’accepter ou de refuser l’accès de l’utilisateur. Vous pouvez cliquer sur la ligne et choisir d’accepter ou de refuser. (Si vous ne connaissez pas l’utilisateur, vous pouvez aussi répondre cela.)

Le réviseur peut demander que vous fournissiez une justification en cas d’acceptation d’un accès permanent ou d’une appartenance à un groupe.

## <a name="next-steps"></a>Étapes suivantes

Notez que l’accès refusé de l’utilisateur n’est pas immédiatement supprimé. Il peut être supprimé à la fin de la révision ou quand un administrateur arrête la révision. Par conséquent, si vous souhaitez modifier votre réponse et accepter un utilisateur précédemment refusé, ou refuser un utilisateur accepté précédemment, cliquez sur la ligne, réinitialisez la réponse et choisissez une nouvelle réponse.  Vous pouvez effectuer cela jusqu'à la fin de la révision d’accès.




