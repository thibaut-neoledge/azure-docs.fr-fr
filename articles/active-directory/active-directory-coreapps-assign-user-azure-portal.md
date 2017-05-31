---
title: "Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory | Microsoft Docs"
description: "Comment sélectionner une application d’entreprise pour affecter un utilisateur ou un groupe dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: ee784704ada9238b5cd048f99aaa4cb192ec7d57
ms.contentlocale: fr-fr
ms.lasthandoff: 05/05/2017


---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory
Il est facile d’affecter un utilisateur ou un groupe à vos applications d’entreprise dans Azure Active Directory (Azure AD). Vous devez disposer des autorisations nécessaires pour gérer l’application d’entreprise, et vous devez être l’administrateur général du répertoire.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Comment attribuer l’accès à une application d’entreprise à un utilisateur ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, entrez Azure Active Directory dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le panneau **Azure Active Directory - *Nom_répertoire*** (autrement dit, le panneau Azure AD correspondant au répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. Dans le panneau **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Sur le panneau **Applications d’entreprise - Toutes les applications** , sélectionnez une application.
6. Dans le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Utilisateurs et groupes**.

    ![Sélection de la commande Toutes les applications](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. Dans le panneau ***NomApplication*** **- Affectation d’utilisateurs et de groupes**, sélectionnez la commande **Ajouter**.
8. Dans le panneau **Ajouter une affectation**, sélectionnez **Utilisateurs et groupes**.

    ![Affecter un utilisateur ou un groupe à l’application](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. Dans le panneau **Utilisateurs et groupes**, sélectionnez un ou plusieurs utilisateurs ou groupes dans la liste, puis sélectionnez le bouton **Sélectionner** en bas du panneau.
10. Dans le panneau **Ajouter une affectation**, sélectionnez **Rôle**. Puis, dans le panneau **Sélectionner un rôle**, choisissez un rôle à appliquer aux utilisateurs ou groupes sélectionnés, puis cliquez sur le bouton **OK** en bas du panneau.
11. Dans le panneau **Ajouter une affectation**, sélectionnez le bouton **Affecter** en bas du panneau. Les utilisateurs ou les groupes auront les autorisations définies par le rôle sélectionné pour cette application d’entreprise.

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-change-app-logo-user-azure-portal.md)

