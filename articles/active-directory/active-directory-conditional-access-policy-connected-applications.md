---
title: "Définition de la stratégie d’accès conditionnel en fonction de l’appareil pour les applications connectées à Azure Active Directory | Microsoft Docs"
description: "Définissez des stratégies d’accès conditionnel en fonction de l’appareil pour les applications connectées à Azure AD."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133


---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Définir la stratégie d’accès conditionnel en fonction de l’appareil pour les applications connectées à Azure Active Directory
L’accès conditionnel en fonction de l’appareil à Azure Active Directory (Azure AD) vous permet de protéger les ressources de l’organisation des risques suivants :

* Tentatives d’accès à partir d’appareils inconnus ou non gérés.
* Appareils non conformes aux stratégies de sécurité de votre organisation.

Pour une vue d’ensemble de l’accès conditionnel, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md).

Vous pouvez définir les stratégies d’accès conditionnel en fonction de l’appareil pour protéger les applications suivantes :

* Office 365 SharePoint Online pour protéger les documents et les sites de votre organisation
* Office 365 Exchange Online pour protéger la messagerie de votre organisation
* Applications Software as a service (SaaS) connectées à Azure AD pour l’authentification
* Applications locales publiées via les services de proxy d’application Azure AD

Pour définir une stratégie d’accès conditionnel en fonction de l’appareil, dans le portail Azure, accédez à l’application spécifique dans le répertoire.

  ![Liste des applications dans le répertoire du portail Azure](./media/active-directory-conditional-access-policy-connected-applications/01.png "Applications")

Sélectionnez l’application, puis cliquez sur l’onglet **Configurer** pour définir la stratégie d’accès conditionnel.  

  ![Configuration de l’application](./media/active-directory-conditional-access-policy-connected-applications/02.png "Règles d’accès basées sur les appareils")

Pour définir une stratégie d’accès conditionnel en fonction de l’appareil, dans la section **Règles d’accès basé sur l’appareil**, dans **Activer les règles d’accès**, sélectionnez **Activé**.

Une stratégie d’accès conditionnel en fonction de l’appareil comporte trois composants :

* **S’applique à**. L’étendue d’utilisateurs auxquels la stratégie s’applique.
* **Règles d’appareil**. Les conditions qu’un appareil doit respecter avant de pouvoir accéder à l’application.
* **Mise en œuvre de l’application**. Les applications clientes (natives ou de navigateur) auxquelles la stratégie s’applique.
  
  ![Les trois composants d’une stratégie d’accès en fonction de l’appareil](./media/active-directory-conditional-access-policy-connected-applications/03.png "Règles d’accès basées sur les appareils")

## <a name="select-the-users-the-policy-applies-to"></a>Sélectionner les utilisateurs auxquels la stratégie s’applique
Dans la section **S’applique à** , vous pouvez sélectionner les utilisateurs auxquels s’applique cette stratégie.

Vous avez deux options lorsque vous créez une étendue de stratégie d’accès pour les utilisateurs :

* **Tous les utilisateurs**. Appliquez la stratégie à tous les utilisateurs qui accèdent à l’application.
* **Groupes**. Limitez la stratégie aux utilisateurs qui sont membres d’un groupe spécifique.

![Appliquer la stratégie à tous les utilisateurs ou à un groupe](./media/active-directory-conditional-access-policy-connected-applications/11.png "Appliquer à")

 Pour exclure un utilisateur d’une stratégie, cochez la case **Sauf**. Cela est utile lorsque vous avez besoin d’accorder des autorisations à un utilisateur spécifique devant accéder temporairement à l’application. Sélectionnez cette option, si par exemple certains de vos utilisateurs possèdent des appareils qui ne sont pas prêts pour l’accès conditionnel. Les appareils peuvent ne pas être encore inscrits ou sur le point de ne plus être conformes.

## <a name="select-the-conditions-that-devices-must-meet"></a>Sélectionner les conditions que les appareils doivent respecter
Utilisez **Règles d’appareil** pour définir les conditions qu’un appareil doit respecter pour accéder à l’application.

Vous pouvez définir l’accès conditionnel en fonction de l’appareil pour les types d’appareil suivants :

* Mise à jour anniversaire Windows 10, Windows 8.1 et Windows 7
* Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 R2
* Appareils iOS (iPad, iPhone).
* Appareils Android

La prise en charge pour Mac sera bientôt disponible.

  ![Appliquer la stratégie aux appareils](./media/active-directory-conditional-access-policy-connected-applications/04.png "Applications")

> [!NOTE]
> Pour plus d’informations sur les différences entre les appareils joints à un domaine et les appareils joints à Azure AD, consultez [Utilisation d’appareils Windows 10 sur votre lieu de travail](active-directory-azureadjoin-windows10-devices.md).
> 
> 

Vous disposez de deux options pour les règles d’appareil :

* **Tous les appareils doivent être conformes**. Toutes les plateformes d’appareil qui accèdent à l’application doivent être conformes. Pour les appareils s’exécutant sur des plateformes qui ne prennent pas en charge l’accès conditionnel en fonction de l’appareil, l’accès est interdit.
* **Seuls les appareils sélectionnés doivent être conformes**. Seules les plateformes d’appareils spécifiques doivent être conformes. Les autres plateformes, ou d’autres plateformes qui peuvent accéder à l’application, ont accès.
  
  ![Définir l’étendue des règles d’appareil](./media/active-directory-conditional-access-policy-connected-applications/05.png "Applications")

Les appareils joints à Azure AD sont conformes s’ils sont marqués comme étant **conformes** dans le répertoire par Intune ou un système de gestion des appareils mobiles tiers qui s’intègre à Azure AD.

Un appareil joint à un domaine est conforme si :

* Il est inscrit auprès d’Azure AD. De nombreuses organisations traitent les appareils joints à un domaine comme des appareils approuvés.
* Il est marqué comme étant **conforme** dans Azure AD par System Center Configuration Manager.
  
  ![Appareils joints à un domaine qui sont conformes](./media/active-directory-conditional-access-policy-connected-applications/06.png "Règles d’appareil")

Les appareils personnels Windows sont conformes s’ils sont marqués comme étant **conformes** dans le répertoire par Intune ou un système de gestion des appareils mobiles tiers qui s’intègre à Azure AD.

Les appareils non Windows sont conformes s’ils sont marqués comme étant **conformes** dans le répertoire par Intune.

> [!NOTE]
> Pour plus d’informations sur la configuration d’Azure AD pour la compatibilité des appareils dans différents systèmes de gestion, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md).
> 
> 

Vous pouvez sélectionner une ou plusieurs plateformes d’appareils pour une stratégie d’accès en fonction de l’appareil. Cela inclut Android, iOS, Windows Mobile (téléphones et tablettes Windows 8.1) et Windows (tous les autres appareils Windows, y compris tous les appareils Windows 10).
L’évaluation de stratégie a lieu uniquement sur les plateformes sélectionnées. Si un appareil qui tente d’accéder à l’application n’exécute pas une des plateformes sélectionnées, l’appareil peut accéder à l’application si l’utilisateur a accès. Aucune stratégie d’appareil n’est évaluée.

![Sélectionner les plates-formes pour les règles d’appareil](./media/active-directory-conditional-access-policy-connected-applications/07.png "Règles d’appareil")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>Définir l’évaluation de stratégie pour un type d’application
Dans la section **Mise en œuvre de l’application**, définissez le type d’application que la stratégie évaluera pour l’accès utilisateur ou appareil.

Vous avez deux options pour le type d’application à inclure :

* Applications de navigateur ou natives
* Applications natives uniquement

![Choisir des applications de navigateur ou natives](./media/active-directory-conditional-access-policy-connected-applications/08.png "Applications")

Pour appliquer la stratégie d’accès des applications, sélectionnez **Pour les applications de navigateur ou natives**. Ensuite, vous pouvez inclure :

* Des navigateurs (par exemple, Microsoft Edge dans Windows 10 ou Safari dans iOS).
* Les applications utilisant la bibliothèque d’authentification Active Directory (ADAL) sur n’importe quelle plate-forme ou utilisant l’API de gestionnaire de compte web (WAM) sous Windows 10.

> [!NOTE]
> Pour plus d’informations sur la prise en charge du navigateur et autres considérations pour l’utilisateur accédant à une application protégée par l’autorité de certification en fonction de l’appareil, consultez [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md).
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Protéger l’accès à la messagerie depuis les applications basées sur Exchange ActiveSync
Dans les applications Office 365 Exchange Online, vous pouvez utiliser Exchange ActiveSync pour bloquer l’accès à la messagerie aux applications de messagerie basées sur Exchange ActiveSync.

![Options de conformité Exchange ActiveSync](./media/active-directory-conditional-access-policy-connected-applications/09.png "Applications")

![Exiger un appareil compatible pour accéder à la messagerie](./media/active-directory-conditional-access-policy-connected-applications/10.png "Applications")

## <a name="next-steps"></a>Étapes suivantes
* [Accès conditionnel Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Dec16_HO4-->


