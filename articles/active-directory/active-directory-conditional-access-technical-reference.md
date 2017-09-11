---
title: "Référence technique d’Azure Active Directory Conditional Access | Microsoft Docs"
description: "Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: f96189735512090f993f61c0d64a249f650ea2a2
ms.contentlocale: fr-fr
ms.lasthandoff: 08/29/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Référence technique d’Azure Active Directory Conditional Access

Avec l’[accès conditionnel Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), vous pouvez préciser les méthodes d’accès des utilisateurs autorisés aux ressources.  
Cette rubrique fournit des informations de support sur les éléments suivants d’une stratégie d’accès conditionnel : 

- Affectations des applications cloud

- Conditions des applications clientes



## <a name="cloud-apps-assignments"></a>Affectations des applications cloud

Lorsque vous configurez une stratégie d’accès conditionnel, vous devez [sélectionner les applications cloud auxquelles s’applique votre stratégie](active-directory-conditional-access-azure-portal.md#who). 

![Contrôle](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Applications cloud Microsoft

Vous pouvez affecter une stratégie d’accès conditionnel aux applications cloud Microsoft  suivantes :

- Application distante Azure

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (y compris OneDrive Entreprise)

- Microsoft Power BI 

- Visual Studio Team Services

- Microsoft Teams


### <a name="other-apps"></a>Autres applications 

Outre les applications cloud Microsoft, vous pouvez affecter une stratégie d’accès conditionnel aux types d’applications cloud suivants :

- applications connectées à Azure Active Directory (Azure AD) ;

- applications software as a service (SaaS) fédérées préintégrées ;

- applications qui utilisent l’authentification unique (SSO) par mot de passe ;

- applications métier ;

- applications utilisant le proxy d’application Azure AD. 


## <a name="client-apps-conditions"></a>Conditions des applications clientes 

Lorsque vous configurez une stratégie d’accès conditionnel, vous pouvez définir une [condition d’applications clientes](active-directory-conditional-access-azure-portal.md#client-apps). La condition d’applications clientes permet d’accorder ou de bloquer l’accès en cas de tentative d’accès à partir de ces types d’applications clientes :

- Browser
- Applications mobiles et de bureau

![Contrôle](./media/active-directory-conditional-access-technical-reference/03.png)


### <a name="supported-browsers"></a>Navigateurs pris en charge 

Si vous sélectionnez *Navigateurs* dans votre stratégie d’accès conditionnel pour accorder un accès aux ressources, celui-ci n’est accordé que si la tentative d’accès est effectuée à partir d’un navigateur pris en charge. Dans le cas contraire, la tentative est bloquée.

![Navigateurs pris en charge](./media/active-directory-conditional-access-technical-reference/05.png)

Dans une stratégie d’accès conditionnel, les navigateurs suivants sont pris en charge : 


| SE                     | Navigateurs                 | Support     |
| :--                    | :--                      | :-:         |
| Win 10                 | IE, Edge                 | ![Vérification][1] |
| Win 10                 | Chrome                   | VERSION PRÉLIMINAIRE     |
| Win 8/8.1            | IE, Chrome               | ![Vérification][1] |
| Win 7                  | IE, Chrome               | ![Vérification][1] |
| iOS                    | Safari                   | ![Vérification][1] |
| Android                | Chrome                   | ![Vérification][1] |
| Windows Phone          | IE, Edge                 | ![Vérification][1] |
| Windows Server 2016    | IE, Edge                 | ![Vérification][1] |
| Windows Server 2016    | Chrome                   | Bientôt disponible |
| Windows Server 2012 R2 | IE, Chrome               | ![Vérification][1] |
| Windows Server 2008 R2 | IE, Chrome               | ![Vérification][1] |
| Mac OS                 | Safari                   | ![Vérification][1] |
| Mac OS                 | Chrome                   | Bientôt disponible |

> [!NOTE]
> Pour la prise en charge de Chrome, vous devez utiliser Windows 10 Creators Update et installer l’extension disponible [ici](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).


### <a name="supported-mobile-apps-and-desktop-clients"></a>Applications mobiles et clients de bureau

Si vous sélectionnez **Applications mobiles et clients de bureau** dans votre stratégie d’accès conditionnel pour accorder un accès aux ressources, celui-ci n’est accordé que si la tentative d’accès est effectuée à partir d’applications mobiles ou de clients de bureau pris en charge. Dans le cas contraire, la tentative est bloquée.

![Contrôle](./media/active-directory-conditional-access-technical-reference/06.png)

Les applications mobiles et clients de bureau suivants prennent en charge l’accès conditionnel à Office 365 et à d’autres applications de service connectées à Azure AD :


| Applications clientes| Service cible| Plateforme |
| :-- | --- | --- |
| Stratégie MFA et d’emplacement pour les applications. Les stratégies basées sur les appareils ne sont pas prises en charge.| Tout service d’application Mes applications| Android et iOS|
| Application distante Azure| Service Application distante Azure| Windows 10, Windows 8.1, Windows 7, iOS, Android et MAC OS X|
| Application Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Services Microsoft Teams, soit tous les services qui prennent en charge Microsoft Teams et toutes ses applications clientes : Bureau Windows, MAC OS X, iOS, Android, WP et client web| Microsoft Teams| Windows 10, Windows 8.1, Windows 7, iOS, Android et MAC OS X|
| Messagerie/Calendrier/Contacts, Outlook 2016, Outlook 2013 (avec authentification moderne), Skype Entreprise (avec authentification moderne)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (avec authentification moderne), Skype Entreprise (avec authentification moderne)| Office 365 Exchange Online| Windows 8.1, Windows 7|
| Application Outlook Mobile| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office pour Mac OS)| Office 365 Exchange Online| Mac OS X|
| Applications Office 2016, applications Universal Office, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), prise en charge à venir des groupes Office et de l’application SharePoint| Office 365 SharePoint Online| Windows 10|
| Applications Office 2016, Office 2013 (avec authentification moderne), client de synchronisation OneDrive (voir [notes](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7|
| Applications mobiles Office| Office 365 SharePoint Online| iOS, Android|
| Office 2016 pour Mac OS (Word, Excel, PowerPoint, OneNote uniquement). Prise en charge de OneDrive Entreprise prévue| Office 365 SharePoint Online| Mac OS X|
| Application Yammer Office| Office 365 Yammer| Windows 10, iOS, Android|
| Application PowerBI. L’application Power BI pour Android ne prend pas en charge actuellement l’accès conditionnel en fonction de l’appareil.| Service PowerBI| Windows 10, Windows 8.1, Windows 7 et iOS|
| Application Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS, Android|














## <a name="next-steps"></a>Étapes suivantes

- Vous trouverez une vue d’ensemble de l’accès conditionnel sur la page [Accès conditionnel Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Si vous êtes prêt à configurer des stratégies d’accès conditionnel dans votre environnement, consultez les [Meilleures pratiques d’accès conditionnel dans Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



