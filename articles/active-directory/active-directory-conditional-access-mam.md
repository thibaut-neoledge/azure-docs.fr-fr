---
title: "Accès conditionnel avec la gestion des applications mobiles dans Azure Active Directory | Microsoft Docs"
description: "Découvrez comment fonctionne l’accès conditionnel avec la gestion des applications mobiles dans Azure Active Directory."
services: active-directory
keywords: "accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: c6bc39dc151c80ffe1306464da60a029e54cc6b1
ms.contentlocale: fr-fr
ms.lasthandoff: 09/05/2017

---
# <a name="conditional-access-with-mobile-app-management-in-azure-active-directory"></a>Accès conditionnel avec la gestion des applications mobiles dans Azure Active Directory  

L’accès conditionnel Azure Active Directory (Azure AD) basé sur l’application dans le portail Azure, combiné avec les stratégies de protection d’application Intune, aide à restreindre l’accès aux applications cloud aux applications mobiles qui prennent en charge la protection d’application Intune. Vous pouvez par exemple limiter l’accès à Exchange Online à l’application Outlook. Cette prise en charge permet aux appareils qui ne sont pas inscrits pour la gestion par Intune MDM de protéger quand même les données d’entreprise.   

Vous pouvez combiner l’accès conditionnel de la gestion des applications mobiles avec d’autres stratégies, telles que les stratégies d’accès conditionnel basé sur l’appareil, afin de bénéficier d’une flexibilité dans la protection des données pour les appareils personnels et d’entreprise. 

##<a name="before-you-begin"></a>Avant de commencer

Cette rubrique part du principe que vous connaissez :

- Les concepts de base de l’[accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal.md)

- Comment [configurer votre stratégie d’accès conditionnel](active-directory-conditional-access-azure-portal-get-started.md)


En outre, vous souhaiterez peut-être examiner les [bonnes pratiques pour l’accès conditionnel dans Azure Active Directory ](active-directory-conditional-access-best-practices.md).  



## <a name="prerequisites"></a>Prérequis

1.  Avant de créer une stratégie d’accès conditionnel basé sur l’application, vous devez avoir un abonnement à Enterprise Mobility + Security ou à Azure Active Directory Premium, et les utilisateurs doivent disposer d’une licence pour EMS ou Azure AD. 
2.  Avant de créer une stratégie d’accès conditionnel avec la gestion des applications mobiles, vous devez passer en revue les scénarios et les considérations sur la migration.

## <a name="supported-platforms"></a>Plateformes prises en charge

-   iOS

-   Android

## <a name="approved-client-applications"></a>Applications clientes approuvées 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Microsoft Teams

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## <a name="exchange-online-policy"></a>Stratégie Exchange Online 

Ce scénario se compose d’une stratégie d’accès conditionnel avec la gestion des applications mobiles pour l’accès à Exchange Online avec les applications approuvées.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange

- Reçoit un e-mail qui indique que l’accès est disponible uniquement à l’aide de l’application Outlook

- Télécharge l’application avec le lien

- Ouvre l’application Outlook et se connecte avec les informations d’identification Azure AD

- Est invité à installer Authenticor (iOS) ou le Portail d’entreprise (Android) pour continuer

- Installe l’application et peut revenir à l’application Outlook pour continuer

- Est invité à inscrire un appareil

- Est en mesure d’accéder aux e-mails

Les stratégies de protection d’application Intune sont activées au moment de l’accès aux données d’entreprise, et peuvent inviter l’utilisateur à redémarrer l’application, à utiliser un code PIN supplémentaire, etc. (si ceci est configuré pour l’application et la plateforme).

### <a name="configuration"></a>Configuration 

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/01.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes** :

    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)
 

**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/06.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.


3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes**. 

    a. Comme **Applications clientes**, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

    b. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)


**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="exchange-online-and-sharepoint-online-policy"></a>Stratégie Exchange Online et SharePoint Online

Ce scénario se compose d’une stratégie d’accès conditionnel avec la gestion des applications mobiles pour l’accès à Exchange Online et SharePoint Online avec les applications approuvées.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :

- Tente d’utiliser l’application SharePoint pour se connecter à ses sites d’entreprise et les afficher

- Tente de se connecter avec les mêmes informations d’identification que celles de l’application Outlook

- N’est pas obligé de se réinscrire et peut accéder aux ressources


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/71.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.


3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes** :

    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)




**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/06.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. En ligne 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes** :

    a. Comme **Applications clientes**, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

    b. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/05.png)




**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).


## <a name="mobile-application-management-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Gestion des applications mobiles ou stratégie d’appareil conforme pour Exchange Online et SharePoint Online

Ce scénario se compose d’une stratégie d’accès conditionnel avec la gestion des applications mobiles ou d’une stratégie d’appareil conforme pour l’accès à Exchange Online avec les applications approuvées.


### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe que :
 
- Un utilisateur a déjà été inscrit (avec ou sans appareils d’entreprise)

- Les utilisateurs qui ne sont pas enregistrés et inscrits auprès d’Azure AD à l’aide d’une application protégée approuvée doivent inscrire un appareil pour accéder aux ressources

- Les utilisateurs inscrits à l’aide de l’application protégée approuvée ne sont pas obligés de réinscrire l’appareil


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/62.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 

     ![Accès conditionnel](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
 
    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

    - **Exiger que l’appareil soit marqué comme conforme**

    - **Demander une application cliente approuvée (préversion)**

    - **Demander un des contrôles sélectionnés**   
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/11.png)



**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/61.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes**. 

    Comme **Applications clientes*, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

5. Comme **Contrôles d’accès**, **Demander une application cliente approuvée (préversion)** doit être sélectionné.
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/11.png)




**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).





## <a name="mobile-application-management-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Gestion des applications mobiles et stratégie d’appareil conforme pour Exchange Online et SharePoint Online

Ce scénario se compose d’une stratégie d’accès conditionnel avec la gestion des applications mobiles et d’une stratégie d’appareil conforme pour l’accès à Exchange Online avec les applications approuvées.

### <a name="scenario-playbook"></a>Description du scénario

Ce scénario part du principe qu’un utilisateur :
 
-   Configure l’e-mail à l’aide d’une application de messagerie native sur iOS ou Android pour se connecter à Exchange
-   Reçoit un e-mail qui indique que l’accès exige l’inscription de l’appareil
-   Télécharge le portail d’entreprise et s’y connecte
-   Vérifie ses e-mails et est invité à utiliser l’application Outlook
-   Télécharge l’application Outlook
-   Ouvre l’application Outlook et entre les informations d’identification utilisées lors de l’inscription
-   L’utilisateur est en mesure d’accéder aux e-mails

Les stratégies de protection d’application Intune sont activées au moment de l’accès aux données d’entreprise, et peuvent inviter l’utilisateur à redémarrer l’application, à utiliser un code PIN supplémentaire, etc. (si ceci est configuré pour l’application et la plateforme)


### <a name="configuration"></a>Configuration

**Étape 1 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online et SharePoint Online**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/62.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online** et **Office 365 SharePoint Online**. 

     ![Accès conditionnel](./media/active-directory-conditional-access-mam/02.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Plateformes d’appareils** et **Applications clientes**. 
 
    a. Comme **Plateformes d’appareils**, sélectionnez **Android** et **iOS**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/03.png)

    b. Comme **Applications clientes**, sélectionnez **Applications mobiles et de bureau**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/04.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

    - **Exiger que l’appareil soit marqué comme conforme**

    - **Demander une application cliente approuvée (préversion)**

    - **Demander un des contrôles sélectionnés**   
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/11.png)



**Étape 2 : configurer une stratégie d’accès conditionnel Azure AD pour Exchange Online avec Active Sync (EAS)**

Pour la stratégie d’accès conditionnel de cette étape, vous devez configurer les composants suivants :

![Accès conditionnel](./media/active-directory-conditional-access-mam/61.png)

1. Le **Nom** de votre stratégie d’accès conditionnel.

2. **Utilisateurs et groupes** : au moins un utilisateur ou un groupe doit être sélectionné pour chaque stratégie d’accès conditionnel.

3. **Applications cloud** : comme applications cloud, vous devez sélectionner **Office 365 Exchange Online**. 

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/07.png)

4. **Conditions :** comme **Conditions**, vous devez configurer **Applications clientes**. 

    Comme **Applications clientes**, sélectionnez **Exchange Active Sync**.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/08.png)

5. Comme **Contrôles d’accès**, vous devez sélectionner les éléments suivants :

    - **Exiger que l’appareil soit marqué comme conforme**

    - **Demander une application cliente approuvée (préversion)**

    - **Demander tous les contrôles sélectionnés**   
 
    ![Accès conditionnel](./media/active-directory-conditional-access-mam/64.png)




**Étape 3 : configurer la stratégie de protection d’application Intune pour les applications clientes iOS et Android**


![Accès conditionnel](./media/active-directory-conditional-access-mam/09.png)

Pour plus d’informations, consultez [Protéger les données et les applications avec Microsoft Intune](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune).



## <a name="migration-considerations"></a>Considérations relatives à la migration

Si des stratégies sont configurées dans le portail Azure Classic, vous devez les migrer vers le portail Azure, car :


- Un utilisateur qui figure dans une stratégie du portail Azure Classic et une stratégie du portail Azure doit remplir les conditions requises dans les deux stratégies 

- Si vous ne migrez vos stratégies existantes, vous ne pourrez pas implémenter de stratégies qui accordent l’accès


## <a name="migration-from-the-azure-classic-portal"></a>Migration à partir du portail Azure Classic

Dans ce scénario : 

- Dans votre [portail Azure Classic](https://manage.windowsazure.com), vous avez configuré :

    - SharePoint Online

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/14.png)

    - Une stratégie d’accès conditionnel basé sur l’appareil

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/15.png)

- Vous souhaitez configurer une stratégie d’accès conditionnel de gestion des applications mobiles dans le portail Azure 
 

### <a name="configuration"></a>Configuration 

- Passez en revue vos stratégies d’accès conditionnel basé sur l’appareil

- Migrez-les vers le portail Azure 

- Ajoutez des stratégies d’accès conditionnel de gestion des applications mobiles


## <a name="migrating-from-intune"></a>Migration à partir d’Intune 

Dans ce scénario :

- Dans [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ), vous avez configuré une stratégie d’accès conditionnel de gestion des applications mobiles pour Exchange Online ou SharePoint Online.

    ![Accès conditionnel](./media/active-directory-conditional-access-mam/15.png)

- Vous souhaitez migrer afin d’utiliser l’accès conditionnel de gestion des applications mobiles dans le portail Azure


### <a name="configuration"></a>Configuration 
 
- Passez en revue vos stratégies d’accès conditionnel basé sur l’appareil

- Migrez-les vers le portail Azure 

- Passez en revue vos stratégies d’accès conditionnel de gestion des applications mobiles configurées pour Exchange Online ou SharePoint Online dans Intune

- Ajoutez le contrôle pour **Exiger des applications approuvées** en plus du contrôle basé sur l’appareil 
 

## <a name="migrating-from-the-azure-classic-portal-and-intune"></a>Migration à partir du portail Azure Classic et Intune

Dans ce scénario :

- Vous avez configuré ce qui suit :

    - **Portail Azure Classic** : accès conditionnel basé sur l’appareil 

    - **Intune** : stratégies d’accès conditionnel de gestion des applications mobiles 
    
- Vous souhaitez migrer les deux stratégies afin d’utiliser des stratégies d’accès conditionnel de gestion des applications mobiles dans le portail Azure


### <a name="configuration"></a>Configuration

- Passez en revue vos stratégies d’accès conditionnel basé sur l’appareil

- Migrez-les vers le portail Azure 

- Passez en revue votre stratégie d’accès conditionnel de gestion des applications mobiles configurée pour Exchange Online ou SharePoint Online dans Intune

- Ajoutez le contrôle pour **Exiger des applications approuvées** en plus du contrôle basé sur l’appareil 




## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment configurer une stratégie d’accès conditionnel, consultez [Prise en main de l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez les [Meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md). 
