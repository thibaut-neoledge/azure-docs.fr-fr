---
title: "Mise à niveau vers le proxy d’application Azure AD | Microsoft Docs"
description: "Choisissez la meilleure solution de proxy si vous effectuez une mise à niveau à partir de Microsoft Forefront ou de Unified Access Gateway."
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
ms.date: 04/27/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: e9d5caa4d11012744ce9f26648166371f3aa17ba
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017

---
# <a name="upgrade-to-azure-ad-proxies-from-microsoft-forefront-or-unified-access-gateway"></a>Mise à niveau vers des proxys Azure Active Directory à partir de Microsoft Forefront ou de Unified Access Gateway

Cet article décrit comment passer des solutions Microsoft Forefront Threat Management Gateway (TMG) et United Access Gateway (UAG) au proxy d’application Azure AD.

Pour plus d’informations sur la transition de Forefront TMG et UAG vers le proxy d’application, vous pouvez [consulter le livre blanc connexe de Microsoft](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/).

## <a name="functionality-details-for-the-conversion"></a>Informations sur la fonctionnalité pour la conversion

|**Fonctionnalité TMG/UAG**|**Solution moderne**|
|:-----|:-----|
|Publication HTTP sélective pour les applications de navigateur|Activation du Proxy d’application Azure AD|
|Intégration d’Active Directory Federation Services (AD FS)|Activation du Proxy d’application Azure AD|
|Publication de protocoles riches (p. ex. Citrix, Lync, RDG)|Activation du Proxy d’application Azure AD|
|Portail|Panneau d’accès Azure AD ou lanceur d’applications Office 365 pour le proxy d’application Azure AD|
|Détection d’intégrité de point de terminaison|Intune ou System Center|
|Tunneling SSH|Windows SSL ou VPN|
|Pare-feu de couche 2/3|Windows Server|
|Pré-authentification pour ActiveSync (HTTP de base) et RDG|Aucune solution actuelle de Microsoft|
|Pare-feu d’application web|Aucune solution actuelle de Microsoft.|
|Passerelle web sécurisée (proxy de transfert)|Aucune solution actuelle de Microsoft.|


## <a name="next-steps"></a>Étapes suivantes

[Blog: proxy d’application web](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Blog: Azure AD Application Proxy](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap) (Blog : Proxy d’application Azure AD)

