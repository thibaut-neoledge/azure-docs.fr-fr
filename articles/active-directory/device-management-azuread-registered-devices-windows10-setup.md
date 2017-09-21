---
title: "Configurer les appareils Windows 10 inscrits à Azure Active Directory | Microsoft Docs"
description: "Découvrez comment configurer les appareils Windows 10 inscrits à Azure Active Directory."
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
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configurer les appareils Windows 10 inscrits à Azure Active Directory

La fonction de gestion des appareils intégrée à Azure Active Directory (Azure AD) vous permet de vous assurer que les utilisateurs accèdent à vos ressources à partir d’appareils qui répondent à vos normes de conformité et de sécurité. Pour plus d’informations, consultez [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md).

Avec des [appareils inscrits à Azure AD](device-management-introduction.md#azure-ad-registered-devices), vous pouvez activer le scénario **BYOD (Bring Your Own Device)** qui permet à vos utilisateurs d’accéder aux ressources de votre organisation à l’aide d’appareils personnels.  

Dans Azure AD, vous pouvez configurer des appareils inscrits à Azure AD pour :

- Windows 10
- iOS
- Android
- macOS  

Cette rubrique vous explique comment inscrire des appareils Windows 10 à Azure AD. 


## <a name="prerequisites"></a>Composants requis

Avant de commencer, vous devez vérifier que :

- Vous disposez des autorisations pour inscrire les appareils 

    ![S’inscrire](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- Vous n’avez pas encore dépassé le nombre maximal d’appareils par utilisateur 

    ![S’inscrire](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

Pour plus d’informations, consultez [Configurer les paramètres de l’appareil](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Ce que vous devez savoir

Lorsque vous inscrivez un appareil, notez les points suivants :

- Windows inscrit l’appareil à l’annuaire de l’entreprise dans Azure AD.

- Vous devrez peut-être répondre à une demande d’authentification multifacteur. Votre administrateur informatique peut configurer cette demande.

- Azure AD vérifie si un appareil nécessite une inscription auprès de la gestion des appareils mobiles. Il inscrit l’appareil, le cas échéant.

- Windows redirige les utilisateurs gérés vers le Bureau par le biais de la connexion automatique.

- Les utilisateurs fédérés sont redirigés vers une page de connexion Windows afin d’entrer les informations d’identification.


## <a name="register-a-device"></a>Inscrire un appareil

Pour inscrire votre appareil Windows 10 à Azure AD, effectuez les étapes suivantes. Si vous avez correctement inscrit votre appareil à Azure AD, la page **Accès Professionnel ou Scolaire** vous le confirme avec l’entrée **Compte professionnel ou scolaire**.

![S’inscrire](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


Pour inscrire votre appareil Windows 10 :

1. À partir du menu **Démarrer**, cliquez sur **Paramètres**.

    ![Sélection de Paramètres](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Cliquez sur **Comptes**.

    ![Sélection de Comptes](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Cliquez sur **Accès Professionnel ou Scolaire**.

    ![Sélection de Accès Professionnel ou Scolaire](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Dans la page **Accès Professionnel ou Scolaire**, cliquez sur **Se connecter**.

    ![La page Accès Professionnel ou Scolaire](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Dans la page **Configurer un compte professionnel ou scolaire**, entrez le nom de votre compte (par exemple, someone@example.com), puis cliquez sur **Suivant**.

    ![La page Configurer un compte professionnel ou scolaire](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Dans la page **Saisie du mot de passe**, entrez votre mot de passe, puis cliquez sur **Suivant**.

    ![Boîte de dialogue Saisie du mot de passe](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Dans la page **Vous avez terminé**, cliquez sur **Terminé**.

    ![La page Vous avez terminé](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Vérification

Pour vérifier si un appareil est joint à Azure AD, consultez la page **Accès Professionnel ou Scolaire** sur votre appareil.

![État du compte professionnel ou scolaire](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Sinon, vous pouvez contrôler les paramètres de l’appareil dans le portail Azure AD.

![Appareils inscrits sur Azure AD](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations, consultez les pages suivantes : 

- [Présentation de la gestion des appareils dans Azure Active Directory](device-management-introduction.md)

- [Gestion des appareils via le portail Azure](device-management-azure-portal.md)





