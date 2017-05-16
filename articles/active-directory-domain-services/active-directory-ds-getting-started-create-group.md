---
title: "Services de domaine Azure Active Directory : Créer le groupe d’administrateurs Azure AD DC | Microsoft Docs"
description: Prise en main des services de domaine Azure Active Directory
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 99607cf642bb6767c845ceb6fb4e62b1c15834f9
ms.lasthandoff: 04/12/2017


---
# <a name="get-started-with-azure-active-directory-domain-services"></a>Prise en main des services de domaine Azure Active Directory
Cet article décrit et vous guide à travers les tâches de configuration requises pour activer Azure Active Directory Domain Services (Azure AD DS) pour votre client Azure Active Directory (Azure AD).

## <a name="task-1-create-the-azure-ad-dc-administrators-group"></a>Tâche 1 : Créer le groupe d’administrateurs Azure AD DC
La première tâche consiste à créer un groupe d’administration dans votre client Azure AD. Ce groupe d’administration spécial est appelé *AAD DC Administrators*. Les membres de ce groupe se voient accorder des autorisations d’administrateur sur les ordinateurs joints au domaine géré des services de domaine Azure Active Directory. Sur les ordinateurs joints au domaine, ce groupe est ajouté au groupe « Administrateurs ». En outre, les membres de ce groupe sont également autorisés à utiliser le Bureau à distance pour se connecter à distance aux ordinateurs joints au domaine.  

> [!NOTE]
> Vous ne bénéficiez pas des autorisations d’administrateur de domaine ou d’administrateur d’entreprise sur le domaine géré que vous avez créé à l’aide des services de domaine Azure Active Directory. Dans un domaine géré, ces autorisations sont réservées par le service et ne sont pas accessibles aux utilisateurs au sein du client. Toutefois, vous pouvez utiliser le groupe d’administrateurs spécial créé dans cette tâche de configuration, afin d’exécuter des opérations privilégiées. Ces opérations comprennent l’ajout d’ordinateurs au domaine, l’appartenance au groupe Administrateurs sur les ordinateurs joints au domaine, la configuration de stratégie de groupe.
>

Au cours de cette tâche de configuration, vous créez le groupe d’administration et y ajoutez un ou plusieurs utilisateurs de votre répertoire. Pour créer le groupe d’administration pour les services de domaine Azure Active Directory, procédez comme suit :

1. Connectez-vous au [Portail Azure Classic](https://manage.windowsazure.com).
2. Dans le volet de gauche, cliquez sur le bouton **Active Directory**.
3. Sélectionnez le client Azure AD (annuaire) pour lequel vous souhaitez activer Azure Active Directory Domain Services. Vous ne pouvez créer qu’un seul domaine par annuaire Azure AD.

    ![Sélectionnez un annuaire Azure AD.](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. Sur la page de la **version préliminaire de l’annuaire**, cliquez sur l’onglet **Groupes**.
5. Cliquez sur **Ajouter un groupe** à partir du volet de tâches en bas de la fenêtre pour ajouter un groupe à votre client Azure AD.

    ![Le bouton Ajouter un groupe](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. Dans la boîte de dialogue **Ajouter un groupe**, créez un groupe nommé **Administrateurs AAD DC**, puis définissez **Type de groupe** sur **Sécurité**.

   > [!WARNING]
   > Vous devez créer un groupe portant exactement ce nom pour activer l’accès au sein des services de domaine Azure Active Directory.
   >
   >

    ![La boîte de dialogue Ajouter un groupe](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. Dans la zone **Description**, entrez une description qui permettra à d’autres de comprendre que ce groupe accorde des autorisations administratives dans Azure Active Directory Domain Services.
8. Une fois que vous avez créé le groupe, cliquez sur le nom du groupe pour afficher ses propriétés. 
9. Cliquez sur le bouton **Ajouter des membres** en bas de la fenêtre pour ajouter des utilisateurs en tant que membres de ce groupe.

    ![Bouton Ajouter des membres au groupe](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
10. Dans la boîte de dialogue **Ajouter des membres** , sélectionnez les utilisateurs qui doivent être membres de ce groupe et cliquez sur l’icône de coche en bas à droite.

    ![Ajouter des utilisateurs au groupe d’administrateurs](./media/active-directory-domain-services-getting-started/add-group-members.png)

## <a name="next-steps"></a>Étapes suivantes
Tâche 2 : [Créer ou sélectionner un réseau virtuel Azure](active-directory-ds-getting-started-vnet.md)
  

