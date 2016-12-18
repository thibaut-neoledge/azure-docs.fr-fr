---
title: "Modifier le nom ou le logo d’une application d’entreprise dans la préversion d’Azure Active Directory | Microsoft Docs"
description: "Comment modifier le nom ou le logo d’une application d’entreprise personnalisée dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: d01303ce-e6cb-4f3b-a4d6-ec29dfd68146
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 541efc3bdc192d21fd75aa4de9d902adb70b6407


---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory-preview"></a>Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory
Il est facile de modifier le nom ou le logo d’une application d’entreprise personnalisée dans la version préliminaire d’Azure Active Directory (Azure AD). [Nouveautés de la version préliminaire](active-directory-preview-explainer.md)  Vous devez disposer des autorisations appropriées pour effectuer ces modifications. Dans la version préliminaire actuelle, vous devez être le créateur de l’application personnalisée.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Comment modifier le nom ou le logo d’une application d’entreprise ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le panneau **Azure Active Directory - *NomRépertoire*** (autrement dit, le panneau Azure AD pour le répertoire que vous gérez), sélectionnez **Applications d’entreprise**.
   
    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-change-app-logo-azure-portal/open-enterprise-apps.png)
4. Dans le panneau **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5. Sur le panneau **Applications d’entreprise - Toutes les applications** , sélectionnez une application.
6. Dans le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Propriétés**.
   
    ![Sélection de la commande Propriétés](./media/active-directory-coreapps-change-app-logo-azure-portal/select-app.png)
7. Dans le panneau ***nom_application*** **- Propriétés**, accédez à un fichier à utiliser en tant que nouveau logo ou modifiez le nom de l’application, ou les deux.
   
    ![Modification du logo de l’application ou de la commande PropriétésNom](./media/active-directory-coreapps-change-app-logo-azure-portal/change-logo.png)
8. Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)




<!--HONumber=Nov16_HO3-->


