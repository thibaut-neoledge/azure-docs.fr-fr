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
ms.date: 01/27/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: c887adaae811972efd7fcea86eaa67a899f3f4c2
ms.openlocfilehash: 542dd7df7e0b887298522f29cb597f1df73709cb
ms.lasthandoff: 02/27/2017


---
# <a name="transition-to-azure-ad-application-proxies-from-microsoft-forefront"></a>Transition vers les proxys d’application Azure AD à partir de Microsoft Forefront

Cet article décrit comment passer des solutions Microsoft Forefront Threat Management Gateway (TMG) et United Access Gateway (UAG) aux proxys d’application Azure Active Directory (Azure AD) : proxy d’application web et proxy d’application Azure AD.

> [!NOTE]
> Le proxy d’application Azure AD est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure AD. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).


Pour plus d’informations sur la transition de Forefront TMG et UAG vers les nouveaux proxys d’application, vous pouvez [télécharger un livre blanc connexe de Microsoft](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx).

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

