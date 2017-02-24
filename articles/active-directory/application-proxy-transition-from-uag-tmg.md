---
title: "Transition vers le proxy d’application Azure AD à partir de Microsoft Forefront | Microsoft Docs"
description: "Couvre les bases sur les connecteurs de proxy d’application Azure AD."
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
ms.sourcegitcommit: 9945512d18d66c321c0d24ee1050497d4716fd47
ms.openlocfilehash: 699112846cc1e4e9fc6b04b1b8509152d7aecdef


---
#<a name="transition-to-azure-ad-app-proxy-from-microsoft-forefront"></a>Transition vers le proxy d’application Azure AD à partir de Microsoft Forefront

Cet article décrit comment passer des solutions Microsoft Forefront Threat Management Gateway (TMG) et United Access Gateway (UAG) aux proxys d’application Azure AD : Web Application Proxy (WAP) et Azure Active Directory Application Proxy (AADAP). 

> [!NOTE]
> Le proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou De base d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
> 
 
De nombreux clients nous demandent comment passer de Forefront UAG et TMG aux nouveaux proxys d’application. Pour plus d’informations à ce sujet, consultez ce [livre blanc](http://download.microsoft.com/download/3/E/3/3E335D93-6DB8-4834-90A8-B86105419F05/Microsoft%20TMG%20and%20UAG%20EOL%20and%20transitioning%20to%20WAP%20and%20AADAP.docx) qui décrit la transition en détail. 
 
## <a name="tmguag-conversion-to-azure-ad-application-proxy-table"></a>Conversion TMG/UAG en table de proxy d’application Azure AD
 
|**Fonctionnalités limitées**|**Web Application Proxy (WAP) / Azure AD Application Proxy (AADAP)**|
|:-----|:-----|
|Publication HTTP sélective pour les applications de navigateur|Disponible en WAP dans Windows Server 2012 R2 (disponible en AADAP aujourd'hui)|
|Intégration ADFS|Disponible en WAP dans Windows Server 2012 R2 (disponible en AADAP aujourd'hui)|
|Publication de protocoles riches (par ex. Citrix, Lync, RDG)|Disponible en WAP dans Windows Server 2012 R2 (disponible en AADAP aujourd'hui)|
|Pré-authentification pour ActiveSync (HTTP de base) et RDG|Sera disponible dans WAP dans Windows Server vNext (prochainement sur AADAP)|
|Portail|Utiliser Intune / System Center pour WAP (utilisation du panneau d’accès AAD ou du Lanceur d’applications Office 365 disponible pour AADAP)|
|Détection d’intégrité de point de terminaison|Utiliser Intune / System Center|
|Tunneling SSH|Utiliser Windows SSL / la fonctionnalité VPN|
|Pare-feu de couche 2/3|Utilisation des fonctionnalités de Windows Server|
|Pare-feu d’applications web|Aucune solution actuelle de Microsoft|
|Passerelle web sécurisée (proxy de transfert)|Aucune solution actuelle de Microsoft|


## <a name="next-steps"></a>Étapes suivantes

[Blog: proxy d’application web](https://blogs.technet.microsoft.com/applicationproxyblog/tag/web-application-proxy)<br>
[Blog : Proxy d’application](https://blogs.technet.microsoft.com/applicationproxyblog/tag/aad-ap)



<!--HONumber=Feb17_HO1-->


