---
title: "Configurer des appareils joints à Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer des appareils joints à Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>Configurer des appareils joints à Azure Active Directory

La fonction de gestion des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que les utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. Pour plus d’informations, consultez [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md).

Avec des [appareils joints à Azure AD](device-management-introduction.md#azure-ad-joined-devices), vous pouvez placer sous le contrôle d’Azure AD des appareils Windows 10 qui appartiennent à votre organisation. Ces appareils ne doivent pas être déjà [joints à un service AD local](device-management-introduction.md#hybrid-azure-ad-joined-devices).

Cette rubrique vous explique comment inscrire des appareils Windows 10 à Azure AD. 

## <a name="prerequisites"></a>Composants requis

Avant de commencer, vous devez vérifier que :

- Vous disposez d’autorisations pour joindre des appareils à Azure AD

    ![Connecté](./media/device-management-azuread-joined-devices-setup/21.png)

- Vous n’avez pas encore dépassé le nombre maximal d’appareils par utilisateur 

    ![Connecté](./media/device-management-azuread-joined-devices-setup/22.png)


Pour plus d’informations, consultez [Configurer les paramètres de l’appareil](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Ce que vous devez savoir


- Windows inscrit l’appareil à l’annuaire de l’entreprise dans Azure AD.

- Vous devrez peut-être répondre à une demande d’authentification multifacteur. Votre administrateur informatique peut configurer cette demande.

- Azure AD vérifie si un appareil nécessite une inscription auprès de la gestion des appareils mobiles. Il inscrit l’appareil, le cas échéant.

- Windows redirige les utilisateurs gérés vers le Bureau par le biais de la connexion automatique.

- Les utilisateurs fédérés sont redirigés vers une page de connexion Windows afin d’entrer les informations d’identification.


## <a name="joining-a-device"></a>Joindre un appareil

Cette section fournit les étapes permettant de joindre votre appareil Windows 10 à Azure AD. Si vous avez correctement joint votre appareil dans Azure AD, la boîte de dialogue **Accès Professionnel ou Scolaire** vous le confirme à l’aide de l’entrée **Connecté à \<votre annuaire Azure AD\>**.

![Connecté](./media/device-management-azuread-joined-devices-setup/13.png)


**Pour joindre votre appareil Windows 10 :**

1. Dans le menu **Démarrer**, cliquez sur **Paramètres**.

    ![Paramètres](./media/device-management-azuread-joined-devices-setup/01.png)

2. Cliquez sur **Comptes**.

    ![Comptes](./media/device-management-azuread-joined-devices-setup/02.png)


3. Cliquez sur **Accès Professionnel ou Scolaire**.

    ![Accès Professionnel ou Scolaire](./media/device-management-azuread-joined-devices-setup/03.png)

4. Dans la boîte de dialogue **Accès Professionnel ou Scolaire**, cliquez sur **Se connecter**.

    ![Connecter](./media/device-management-azuread-joined-devices-setup/04.png)


5. Dans la boîte de dialogue **Configurer un compte professionnel ou scolaire**, cliquez sur **Joindre cet appareil à Azure Active Directory**.

    ![Connecter](./media/device-management-azuread-joined-devices-setup/08.png)


6. Dans la boîte de dialogue **Let’s get you signed in** (Connectez-vous), entrez le nom de votre compte (par exemple, someone@example.com), puis cliquez sur **Suivant**.

    ![Boîte de dialogue Let’s get you signed in (Connectez-vous)](./media/device-management-azuread-joined-devices-setup/10.png)


6. Dans la boîte de dialogue **Saisie du mot de passe**, entrez votre mot de passe, puis cliquez sur **Se connecter**.

    ![Boîte de dialogue Saisie du mot de passe](./media/device-management-azuread-joined-devices-setup/05.png)


7. Dans la boîte de dialogue **Vérifiez qu’il s’agit de votre organisation**, cliquez sur **Joindre**.

    ![Boîte de dialogue Vérifiez qu’il s’agit de votre organisation](./media/device-management-azuread-joined-devices-setup/11.png)


8. Dans la boîte de dialogue **Vous avez terminé**, cliquez sur **Terminé**.

    ![Boîte de dialogue You’re all set (C’est terminé !)](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Vérification

Pour vérifier si un appareil est joint à un annuaire Azure AD, consultez la boîte de dialogue **Accès Professionnel ou Scolaire** sur votre appareil.

![Connecté](./media/device-management-azuread-joined-devices-setup/13.png)

Vous pouvez également exécuter la commande suivante : `dsregcmd /status`  
Sur un appareil correctement joint, **Joint à Azure AD** est défini sur **Oui**.

![Connecté](./media/device-management-azuread-joined-devices-setup/14.png)

Vous pouvez également examiner les paramètres de l’appareil dans le portail Azure AD.

![Connecté](./media/device-management-azuread-joined-devices-setup/15.png)

Pour plus d’informations, consultez [Localiser des appareils](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes : 

- [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)

- [Gestion des appareils via le portail Azure](device-management-azure-portal.md)





