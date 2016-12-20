---
title: "Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory | Microsoft Docs"
description: "Comment supprimer l’affectation de l’accès à un utilisateur ou à un groupe à une application d’entreprise dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b38a613a1f93d9c36254f7642655f716e048e9e3


---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory
Il est facile de supprimer l’affectation de l’accès à un utilisateur ou un groupe à vos applications d’entreprise dans la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)  Vous devez disposer des autorisations appropriées pour gérer l’application d’entreprise. Dans la version préliminaire actuelle, vous devez être administrateur global pour le répertoire.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Comment supprimer une affectation d’utilisateur ou de groupe ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le panneau **Azure Active Directory - *NomRépertoire*** (autrement dit, le panneau Azure AD pour le répertoire que vous gérez), sélectionnez **Applications d’entreprise**.
   
    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. Dans le panneau **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Sur le panneau **Applications d’entreprise - Toutes les applications** , sélectionnez une application.
6. Dans le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Utilisateurs et groupes**.
   
    ![Sélection d’utilisateurs ou groupes](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. Sur le panneau ***NomApplication*** ** - Affectation d’utilisateur et de groupe**, sélectionnez un ou plusieurs utilisateurs ou groupes, puis sélectionnez la commande **Supprimer**. Confirmez votre choix dans l’invite de commandes.
   
    ![Sélection de la commande Supprimer](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-change-app-logo-user-azure-portal.md)




<!--HONumber=Nov16_HO3-->


