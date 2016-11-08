
---
title: Accès conditionnel - applications prises en charge | Microsoft Docs
description: Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: markvi

---
# <a name="conditional-access-support-for-applications"></a>Prise en charge de l’accès conditionnel des applications
Les règles d’accès conditionnel sont prises en charge dans les applications connectées à Azure Active Directory, les applications SaaS fédérées préintégrées, les applications qui utilisent l’authentification unique par mot de passe, les applications métier et le proxy d’application Azure AD. Pour obtenir la liste détaillée des applications dans lesquelles vous pouvez activer l’accès conditionnel, consultez l’article [Services activés avec accès conditionnel](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). L’accès conditionnel fonctionne avec les applications mobiles et de bureau qui utilisent l’authentification moderne. Cette rubrique décrit les fonctionnalités prises en charge dans les versions mobiles et de bureau de ces applications.

Les applications avec authentification moderne peuvent afficher les pages de connexion Azure AD. Cela permet d’inviter un utilisateur à utiliser l’authentification multifacteur ou de présenter à l’utilisateur final un message lui indiquant que l’accès est bloqué. L’authentification moderne est également requise pour permettre à l’appareil de s’authentifier auprès d’Azure AD afin d’évaluer les stratégies d’accès conditionnel en fonction de l’appareil.

Il est important de connaître les applications prises en charge ainsi que les actions qui peuvent être nécessaires pour sécuriser les autres points d’entrée.

## <a name="applications-using-modern-authentication"></a>Applications utilisant l’authentification moderne
Les applications suivantes prennent en charge l’accès conditionnel lors de l’accès à Office 365 et autres applications de service connectées à Azure AD :

| Service cible | Plateforme | Application |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |Application Messagerie/Calendrier/Contacts, Outlook 2016, Outlook 2013 (avec authentification moderne activée), Skype Entreprise (avec authentification moderne) |
| Office 365 Exchange Online |Windows 7, Windows 8.1, |Outlook 2016, Outlook 2013 (avec authentification moderne activée), Skype Entreprise (avec authentification moderne) |
| Office 365 Exchange Online |iOS, Android |Application Outlook Mobile |
| Office 365 Exchange Online |Mac OSX |Outlook 2016 pour MFA/emplacement uniquement. Prise en charge à venir des stratégies basées sur l’appareil, prise en charge à venir de Skype Entreprise |
| Office 365 SharePoint Online |Windows 10 |Applications Office 2016, applications Office Universal, Office 2013 (avec authentification moderne activée), prise en charge à venir de l’application OneDrive Entreprise (NGSC ou client de synchronisation de génération suivante), prise en charge à venir des groupes Office, prise en charge à venir de l’application SharePoint |
| Office 365 SharePoint Online |Windows 7, Windows 8.1, |Applications Office 2016, Office 2013 (avec authentification moderne activée), application OneDrive Entreprise (client de synchronisation Groove) |
| Office 365 SharePoint Online |iOS, Android |Applications mobiles Office |
| Office 365 SharePoint Online |Mac OSX |Applications Office 2016 pour MFA/emplacement uniquement. Prise en charge à venir des stratégies basées sur l’appareil |
| Office 365 Yammer |Windows 10, iOS et Android |Application Yammer Office |
| Dynamics CRM |Windows 10, 7, 8.1, iOS et Android |Application Dynamics CRM |
| Service PowerBI |Windows 10, 7, 8.1, iOS et Android |Application PowerBI |
| Service Application distante Azure |Windows 10, 7, 8.1, iOS et Android, Mac OSX |Application distante Azure |
| Tout service d’application Mes applications |Android et iOS |Tout service d’application Mes applications |

## <a name="applications-that-do-not-use-modern-authentication"></a>Applications qui n’utilisent pas l’authentification moderne
Actuellement, l’accès aux applications qui n’utilisent pas l’authentification moderne doit être bloqué par d’autres méthodes car l’accès conditionnel ne peut pas leur être appliqué. Cela concerne principalement l’accès à Exchange et à SharePoint, car les versions d’applications précédentes ont été générées à l’aide d’anciens protocoles.

## <a name="office-365-sharepoint-online"></a>Office 365 SharePoint Online
Les protocoles hérités peuvent être désactivés au niveau de SharePoint à l’aide de l’applet de commande Set-SPOTenant. Cette applet de commande empêchera les clients Office qui utilisent des protocoles d’authentification non moderne d’accéder aux ressources SharePoint Online. 

**Exemple de commande** :    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

## <a name="office-365-exchange-online"></a>Office 365 Exchange Online
Dans Exchange, il existe principalement deux catégories de protocoles. Examinez et sélectionnez la stratégie appropriée à votre organisation :

1. Exchange ActiveSync. Par défaut, la stratégie d’accès conditionnel pour l’authentification multifacteur et d’emplacement n’est pas appliquée pour Exchange ActiveSync. Cela permet de protéger l’accès en configurant directement une stratégie Exchange ActiveSync, ou en bloquant Exchange ActiveSync à l’aide de règles AD FS.
2. Protocoles hérités. Les protocoles hérités peuvent être bloqués au niveau d’AD FS. Cela bloquera l’accès pour les anciens clients Office, notamment Office 2013 sans authentification moderne activée ainsi que les versions antérieures.

### <a name="example-ad-fs-rules"></a>Exemples de règles AD FS
Les règles suivantes peuvent être utilisées pour bloquer l’accès d’un protocole hérité à AD FS, dans les deux configurations courantes.

### <a name="option-1:-allow-exchange-activesync-and-only-allow-legacy-apps-on-the-intranet"></a>Option 1 : autoriser Exchange ActiveSync et autoriser uniquement les applications héritées sur l’intranet
En appliquant les trois règles suivantes à la partie de confiance AD FS de la plateforme d’identité Microsoft Office 365, le trafic Exchange ActiveSync sera autorisé, tout comme le trafic du navigateur et de l’authentification moderne. Les applications héritées ne pourront pas accéder à l’extranet. 

Règle 1

    `@RuleName = “Allow all intranet traffic”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

Règle 2

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

Règle 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="option-2:-allow-exchange-activesync-and-block-legacy-apps"></a>Option 2 : autoriser Exchange ActiveSync et bloquer les applications héritées
En appliquant les trois règles suivantes à la partie de confiance AD FS de la plateforme d’identité Microsoft Office 365, le trafic Exchange ActiveSync sera autorisé, tout comme le trafic du navigateur et de l’authentification moderne. Les applications héritées ne pourront accéder à aucun emplacement. 

Règle 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Règle 2 

    @RuleName = “Allow EAS”
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Règle 3 

    @RuleName = “Allow Extranet browser or browser dialog traffic”
    c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");











<!--HONumber=Oct16_HO2-->


