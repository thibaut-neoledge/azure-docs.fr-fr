---
title: Authentification avec Active Directory en local dans votre application Azure | Microsoft Docs
description: "En savoir plus sur les différentes options pour les applications cœur de métier dans Azure App Service pour l’authentification auprès d’Active Directory local"
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: dde6b7e6-bf6a-4fa5-8390-3a18155d21bd
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 08/31/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f989ea86c76706af9413b2bb945956b1c6432e1d


---
# <a name="authenticate-with-on-premises-active-directory-in-your-azure-app"></a>Authentification avec Active Directory en local dans votre application Azure
Cet article vous explique comment utiliser Active Directory (AD) local dans [Azure App Service](../app-service/app-service-value-prop-what-is.md)pour l’authentification. Une application Azure est hébergée dans le cloud, toutefois, il existe des façons d’authentifier en local des utilisateurs Active Directory en toute sécurité. 

## <a name="authenticate-through-azure-active-directory"></a>S’authentifier via Azure Active Directory
Un client Azure Active Directory peut être synchronisé avec un annuaire Active Directory local. Cette approche permet aux utilisateurs AD d’accéder à votre application à partir d’internet et de s’authentifier à l’aide de leurs informations d’identification locales. En outre, Azure App Service fournit une [solution clé en main pour cette méthode](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). En quelques clics, vous pouvez activer l’authentification avec un client synchronisé avec l’annuaire pour votre application Azure. Cette approche offre les avantages suivants :

* Ne nécessite pas de code d’authentification dans votre application. Permet à App Service d’effectuer l’authentification pour vous afin que vous puissiez consacrer votre temps à assurer le fonctionnement de votre application.
* [API Graph Azure AD](http://msdn.microsoft.com/library/azure/hh974476.aspx) permet à l’application d’accéder aux données d’annuaire depuis votre application Azure AD.
* Il fournit une authentification unique (SSO) à [toutes les applications prises en charge par Azure Active Directory](/marketplace/active-directory/), y compris Office 365, Dynamics CRM Online, Microsoft InTune et des milliers d’applications de cloud autres que Microsoft. 
* Azure Active Directory prend en charge le contrôle d’accès en fonction du rôle Azure Active Directory. Vous pouvez utiliser le modèle [Authorize(Roles="X")] en modifiant au minimum votre code.

Pour savoir comment écrire une application Azure cœur de métier qui s’authentifie auprès d’Azure Active Directory, voir [Créer une application Azure cœur de métier avec authentification Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md).

## <a name="authenticate-through-an-on-premises-sts"></a>S’authentifier via un service STS local
Si vous disposez d’un STS local, tel que Active Directory Federation Services (ADFS), vous pouvez l’utiliser pour fédérer l’authentification pour votre application Azure. Cette approche est recommandée lorsque la stratégie d’entreprise interdit le stockage de données AD dans Azure. Cependant, notez les points suivants :

* La topologie des services STS doit être déployée localement, ce qui implique une surcharge de gestion et de coût.
* Seuls les administrateurs AD FS peuvent configurer les [approbations et les règles de revendication des parties de confiance](http://technet.microsoft.com/library/dd807108.aspx), ce qui limite les possibilités du développeur. En revanche, il est possible gérer et personnaliser les [revendications](http://technet.microsoft.com/library/ee913571.aspx) application par application.
* L’accès aux données AD locales via le pare-feu de l’entreprise requiert une solution distincte.

Pour savoir comment écrire une application Azure cœur de métier qui s’authentifie auprès d’un service STS local, voir [Créer une application cœur de métier Azure avec authentification AD FS](web-sites-dotnet-lob-application-adfs.md).




<!--HONumber=Nov16_HO3-->


