---
title: "Désactiver les connexions utilisateur pour une application d’entreprise dans la préversion d’Azure Active Directory | Microsoft Docs"
description: "Comment désactiver une application d’entreprise afin qu’aucun utilisateur ne puisse s’y connecter dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 33d3d50980341e0810b1222ce1199fa8689d95d0


---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>Désactiver les connexions utilisateur pour une application d’entreprise dans la version préliminaire d’Azure Active Directory
Il est facile de désactiver une application d’entreprise afin qu’aucun utilisateur ne puisse s’y connecter dans la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)  Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans la version préliminaire actuelle, vous devez être administrateur global pour le répertoire.

## <a name="how-do-i-disable-user-sign-ins"></a>Comment désactiver les connexions des utilisateurs ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le panneau **Azure Active Directory - *NomRépertoire*** (autrement dit, le panneau Azure AD pour le répertoire que vous gérez), sélectionnez **Applications d’entreprise**.
   
    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. Dans le panneau **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Sur le panneau **Applications d’entreprise - Toutes les applications** , sélectionnez une application.
6. Dans le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Propriétés**.
   
    ![Sélection de la commande Toutes les applications](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. Dans le panneau ***NomApplication*** **- Propriétés**, sélectionnez **Non** pour **Les utilisateurs peuvent-ils se connecter ?**.
8. Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-change-app-logo-user-azure-portal.md)




<!--HONumber=Dec16_HO4-->


