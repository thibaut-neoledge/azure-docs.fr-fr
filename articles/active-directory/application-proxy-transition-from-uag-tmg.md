---
title: "Transition vers les proxys d’application Azure AD à partir de Microsoft Forefront | Microsoft Docs"
description: "Couvre les notions de base sur comment passer des solutions Microsoft Forefront TMG et UAG aux proxys d’application Azure Active Directory."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: kgremban
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 6e95e9abac988ae54a401927a92bdb397dd63eed
ms.lasthandoff: 04/21/2017

---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Transition vers les proxys d’application Azure AD à partir de Microsoft Forefront

Cet article décrit comment passer des solutions Microsoft Forefront Threat Management Gateway (TMG) et United Access Gateway (UAG) au proxy d’application Azure AD.

Pour plus d’informations sur la transition de Forefront TMG et UAG vers le proxy d’application, vous pouvez [consulter le livre blanc connexe de Microsoft](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Informations sur la fonctionnalité pour la conversion

|**Fonctionnalité TMG/UAG**|**Proxy d’application web/proxy d’application Azure AD**|
|:-----|:-----|
|Publication HTTP sélective pour les applications de navigateur|Disponible dans le proxy d’application web de Windows Server 2012 R2. Disponible dans le proxy d’application Azure AD actuel.|
|Intégration d’Active Directory Federation Services (AD FS)|Disponible dans le proxy d’application web de Windows Server 2012 R2. Disponible dans le proxy d’application Azure AD actuel.|
|Publication de protocoles riches (p. ex. Citrix, Lync, RDG)|Disponible dans le proxy d’application web de Windows Server 2012 R2. Disponible dans le proxy d’application Azure AD actuel.|
|Pré-authentification pour ActiveSync (HTTP de base) et RDG|Actuellement indisponible dans le proxy d’application web ou le proxy d’application Azure AD.|
|Portail|Utilisez Intune ou System Center pour le proxy d’application web. Utilisez le panneau d’accès Azure AD ou le lanceur d’applications Office 365 pour le proxy d’application Azure AD.|
|Détection d’intégrité de point de terminaison|Utilisez Intune ou System Center.|
|Tunneling SSH|Utilisez Windows SSL ou la fonctionnalité VPN.|
|Pare-feu de couche 2/3|Utilisez les fonctionnalités de Windows Server.|
|Pare-feu d’application web|Aucune solution actuelle de Microsoft.|
|Passerelle web sécurisée (proxy de transfert)|Aucune solution actuelle de Microsoft.|


## <a name="next-steps"></a>Étapes suivantes

[Blog: proxy d’application web](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Blog: Azure AD Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (Blog : Proxy d’application Azure AD)

