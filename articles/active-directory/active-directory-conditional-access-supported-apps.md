---
title: "Applications qui utilisent des règles d’accès conditionnel dans Azure Active Directory | Microsoft Docs"
description: "Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie des conditions spécifiques lors de l’authentification de l’utilisateur et pour autoriser l’accès à l’application."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/26/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 87c73981c74fc763fd1aec6c283e934c77008441
ms.openlocfilehash: 94a212b88d080510ad8a96bb40bb2c48d7f88a4f


---
# <a name="applications-that-use-conditional-access-rules-in-azure-active-directory"></a>Applications qui utilisent des règles d’accès conditionnel dans Azure Active Directory
Les règles d’accès conditionnel sont prises en charge dans les applications connectées à Azure Active Directory (Azure AD), les applications SaaS fédérées préintégrées, les applications qui utilisent l’authentification unique par mot de passe, les applications métier et les applications qui utilisent le proxy d’application Azure AD. Pour obtenir la liste détaillée des applications dans lesquelles vous pouvez utiliser l’accès conditionnel, consultez [Services activés avec accès conditionnel](active-directory-conditional-access-technical-reference.md). L’accès conditionnel fonctionne avec les applications mobiles et de bureau qui utilisent l’authentification moderne. Dans cet article, nous abordons le fonctionnement de l’accès conditionnel dans les applications mobiles et de bureau.

Vous pouvez utiliser les pages de connexion Azure AD dans les applications qui emploient l’authentification moderne. Avec une page de connexion, un utilisateur est invité à utiliser l’authentification multifacteur. Un message s’affiche si l’accès de l’utilisateur est bloqué. L’authentification moderne est requise pour permettre à l’appareil de s’authentifier auprès d’Azure AD afin d’évaluer les stratégies d’accès conditionnel en fonction de l’appareil.

Il est important de connaître les applications qui peuvent utiliser les règles d’accès conditionnel et les étapes que vous devrez suivre pour sécuriser les autres points d’entrée des applications.

## <a name="applications-that-use-modern-authentication"></a>Applications qui utilisent l’authentification moderne
> [!NOTE]
> Si vous avez une stratégie d’accès conditionnel dans Azure AD qui a un équivalent dans Office 365, configurez les deux stratégies d’accès conditionnel. Par exemple, cela s’applique aux stratégies d’accès conditionnel pour Exchange Online ou SharePoint Online.
>
>

Les applications suivantes prennent en charge l’accès conditionnel pour Office 365 et les autres applications de service connectées à Azure AD :

| Service cible | Plateforme | Application |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |Messagerie/Calendrier/Contacts, Outlook 2016, Outlook 2013 (avec authentification moderne), Skype Entreprise (avec authentification moderne) |
| Office 365 Exchange Online |Windows 8.1, Windows 7 |Outlook 2016, Outlook 2013 (avec authentification moderne), Skype Entreprise (avec authentification moderne) |
| Office 365 Exchange Online |iOS, Android |Application Outlook Mobile |
| Office 365 Exchange Online |Mac OS X |Outlook 2016 pour l’authentification multifacteur et l’emplacement uniquement ; prise en charge à venir de la stratégie en fonction de l’appareil, prise en charge à venir de Skype Entreprise |
| Office 365 SharePoint Online |Windows 10 |Applications Office 2016, applications Office Universal, Office 2013 (avec authentification moderne), prise en charge à venir de l’application OneDrive Entreprise (NGSC ou client de synchronisation de génération suivante), prise en charge à venir des groupes Office, prise en charge à venir de l’application SharePoint |
| Office 365 SharePoint Online |Windows 8.1, Windows 7 |Applications Office 2016, Office 2013 (avec authentification moderne), application OneDrive Entreprise (client de synchronisation Groove) |
| Office 365 SharePoint Online |iOS, Android |Applications mobiles Office |
| Office 365 SharePoint Online |Mac OS X |Applications Office 2016 pour l’authentification multifacteur et l’emplacement uniquement ; prise en charge à venir de la stratégie en fonction de l’appareil |
| Office 365 Yammer |Windows 10, iOS et Android |Application Yammer Office |
| Dynamics CRM |Windows 10, Windows 8.1, Windows 7, iOS, Android |Application Dynamics CRM |
| Service PowerBI |Windows 10, Windows 8.1, Windows 7, iOS, Android |Application PowerBI |
| Service Application distante Azure |Windows 10, Windows 8.1, Windows 7, iOS, Android et MAC OS X |Application distante Azure |
| Tout service d’application Mes applications |Android et iOS |Tout service d’application Mes applications |

## <a name="applications-that-do-not-use-modern-authentication"></a>Applications qui n’utilisent pas l’authentification moderne
Actuellement, vous devez utiliser d’autres méthodes pour bloquer l’accès aux applications qui n’emploient pas l’authentification moderne. Les règles d’accès pour les applications qui n’utilisent pas l’authentification moderne ne sont pas appliquées par l’accès conditionnel. Cela concerne principalement l’accès Exchange et SharePoint. La plupart des versions antérieures des applications utilisent d’anciens protocoles de contrôle d’accès.

### <a name="control-access-in-office-365-sharepoint-online"></a>Contrôle d’accès dans Office 365 SharePoint Online
Vous pouvez désactiver les protocoles hérités pour l’accès SharePoint à l’aide de l’applet de commande Set-SPOTenant. Utilisez cette applet de commande pour empêcher les clients Office qui utilisent des protocoles d’authentification non moderne d’accéder aux ressources SharePoint Online.

**Exemple de commande** :    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Contrôle d’accès dans Office 365 Exchange Online
Exchange propose deux catégories principales de protocoles. Passez en revue les options suivantes, puis sélectionnez la stratégie appropriée pour votre organisation.

* **Exchange ActiveSync**. Par défaut, les stratégies d’accès conditionnel pour l’authentification multifacteur et d’emplacement ne sont pas appliquées pour Exchange ActiveSync. Vous devez protéger l’accès à ces services en configurant directement la stratégie Exchange ActiveSync ou en bloquant Exchange ActiveSync à l’aide de règles des services de fédération Active Directory (AD FS).
* **Protocoles hérités**. Vous pouvez bloquer les protocoles hérités avec AD FS. Cela bloquera l’accès aux anciens clients Office, notamment Office 2013 sans authentification moderne activée, ainsi qu’aux versions antérieures d’Office.

### <a name="use-ad-fs-to-block-legacy-protocol"></a>Utiliser AD FS pour bloquer le protocole hérité
Vous pouvez utiliser les exemples de règles suivants pour bloquer l’accès de protocole hérité au niveau AD FS. Choisissez parmi deux configurations courantes.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Option 1 : autoriser Exchange ActiveSync et autoriser uniquement les applications héritées sur l’intranet
En appliquant les trois règles suivantes à la partie de confiance AD FS de la plateforme d’identité Microsoft Office 365, le trafic Exchange ActiveSync sera autorisé, tout comme le trafic du navigateur et de l’authentification moderne. Les applications héritées ne pourront pas accéder à l’extranet.

##### <a name="rule-1"></a>Règle 1
    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Règle 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Règle 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Option 2 : autoriser Exchange ActiveSync et bloquer les applications héritées
En appliquant les trois règles suivantes à la partie de confiance AD FS de la plateforme d’identité Microsoft Office 365, le trafic Exchange ActiveSync sera autorisé, tout comme le trafic du navigateur et de l’authentification moderne. Les applications héritées ne pourront accéder à aucun emplacement.

##### <a name="rule-1"></a>Règle 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Règle 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Règle 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");



<!--HONumber=Nov16_HO3-->


