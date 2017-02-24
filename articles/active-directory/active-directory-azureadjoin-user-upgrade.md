---
title: "Configuration d’un appareil Windows 10 avec Azure AD à partir des paramètres | Microsoft Docs"
description: "Explique comment les utilisateurs peuvent rejoindre Azure AD via le menu Paramètres."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: b844e1d9-ad43-4e4a-a398-5c4a43bf2f5c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 70610ec2c5a056d63ed854b33d84e29951940c2f


---
# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configuration d’un appareil Windows 10 avec Azure AD à partir des paramètres
Si vous utilisez déjà Windows 7 ou Windows 8 et que votre ordinateur ou appareil a été mis à niveau vers Windows 10, vous pouvez le joindre à Azure Active Directory (Azure AD) avec le menu Paramètres.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Joindre votre ordinateur à Azure AD à partir du menu Paramètres
1. Dans le menu **Démarrer**, cliquez sur l’icône **Paramètres**.
2. Accédez à **Paramètres**, sélectionnez **Système**->**Info produit**->**Rejoindre Azure AD**.
   
   <center>
   ![Joindre votre ordinateur à Azure AD à partir du menu Paramètres](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png) </center>
3. Cliquez sur **Continuer** dans la fenêtre de message Azure AD Join.
   
   <center>
   ![Fenêtre de message Joindre Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png) </center>
4. Indiquez vos informations d’identification de connexion. Ce processus de connexion inclut toutes les étapes requises pour l’authentification. Si vous faites partie d’un client fédéré, votre administrateur vous proposera un processus hébergé par votre organisation.
   <center>
   ![Indiquer les informations d’identification de connexion](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Si votre organisation a configuré Azure Multi-Factor Authentication pour joindre vos appareils à Azure AD, vous devrez fournir le second facteur avant de pouvoir continuer.
6. Cliquez sur **Accepter** sur l’écran **Autoriser la gestion de ce périphérique**.
7. Le message « Votre appareil est maintenant joint à votre organisation dans Azure AD » doit apparaître.

## <a name="additional-information"></a>Informations supplémentaires
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)




<!--HONumber=Dec16_HO4-->


