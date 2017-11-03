---
title: "Masquer une application tierce de l’expérience utilisateur dans Azure Active Directory | Microsoft Docs"
description: "Guide pratique pour masquer une application tierce de l’expérience utilisateur dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 28b6885e7e74a44f4d00f4a804a93e74f6eec685
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>Masquer une application tierce de l’expérience utilisateur dans Azure Active Directory

Si vous avez une application tierce (une application publiée par d’autres éditeurs que Microsoft) que vous ne souhaitez pas voir apparaître dans les panneaux d’accès des utilisateurs ou les lanceurs Office 365, une option vous permet de masquer sa vignette. En masquant l’application, les utilisateurs disposent toujours d’autorisations d’accès à cette application mais ils ne la voient pas dans leurs lanceurs d’applications. Vous devez disposer des autorisations nécessaires pour gérer l’application d’entreprise, et vous devez être l’administrateur général du répertoire.

## <a name="hiding-a-users-access-panel"></a>Masquage du panneau d’accès d’un utilisateur
Effectuez les étapes suivantes pour masquer le panneau d’accès d’un utilisateur et les lanceurs d’applications Office 365.

### <a name="how-do-i-hide-an-app-from-users-access-panel-and-o365-app-launchers"></a>Comment faire pour masquer une application dans le panneau d’accès de l’utilisateur et les lanceurs d’applications Office 365 ?

1.  Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2.  Sélectionnez **Plus de services**, saisissez **Azure Active Directory** dans la zone de texte, puis sélectionnez **Entrée**.
3.  Dans l’écran **Azure Active Directory - *NomRépertoire*** (autrement dit, l’écran Azure AD du répertoire que vous gérez), sélectionnez **Applications d’entreprise**.
![Applications d’entreprise](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  Dans l’écran **Applications d’entreprise**, sélectionnez **Toutes les applications**. Vous verrez une liste des applications que vous pouvez gérer.
5.  Dans l’écran **Applications d’entreprise - Toutes les applications**, sélectionnez une application.</br>
![Applications d’entreprise](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  Dans l’écran ***NomApplication*** (autrement dit, l’écran avec le nom de l’application sélectionnée dans le titre), sélectionnez Propriétés.
7.  Dans l’écran ***NomApplication* -Propriétés**, sélectionnez **Oui** pour **Visible par les utilisateurs ?**.
![Applications d’entreprise](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Sélectionnez la commande **Enregistrer** .

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Affecter un utilisateur ou un groupe à une application d’entreprise](active-directory-coreapps-assign-user-azure-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-change-app-logo-user-azure-portal.md)
