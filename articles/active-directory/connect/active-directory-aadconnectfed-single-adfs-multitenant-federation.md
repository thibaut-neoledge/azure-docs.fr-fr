---
title: "Fédérer plusieurs instances d’Azure AD avec une seule instance d’AD FS | Microsoft Docs"
description: "Dans ce document, vous allez apprendre à fédérer plusieurs instances d’Azure AD avec une seule instance d’AD FS."
keywords: "fédérer, ADFS, AD FS, plusieurs locataires, AD FS unique, un seul ADFS, fédération multi-client, adfs à forêts multiples, aad connect, fédération, fédération multi-locataire"
services: active-directory
documentationcenter: 
author: anandyadavmsft
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/09/2017
ms.author: anandy; billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 22f2bcfdd8c3978a6924c8c8cdea2744001000fe
ms.contentlocale: fr-fr
ms.lasthandoff: 04/10/2017

---

#Fédérer plusieurs instances d’Azure AD avec une seule instance d’AD FS
<a id="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs" class="xliff"></a>

Une même batterie AD FS à haute disponibilité peut fédérer plusieurs forêts si elles entretiennent une relation d’approbation bidirectionnelle. Ces forêts multiples ne correspondent pas nécessairement à la même instance d’Azure Active Directory. Cet article explique comment configurer la fédération entre un déploiement AD FS unique et plusieurs forêts qui se synchronisent sur différentes instances d’Azure AD.

![Fédération multi-locataire avec une seule instance d’AD FS](media/active-directory-aadconnectfed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> L’écriture différée sur les appareils et la jonction automatique d’appareils ne sont pas prises en charge dans ce scénario.

> [!NOTE]
> Azure AD Connect ne permet pas de configurer la fédération dans ce scénario, car Azure AD Connect peut configurer la fédération pour des domaines dans une seule instance d’Azure AD.

##Étapes de la fédération d’AD FS avec plusieurs instances d’Azure AD
<a id="steps-for-federating-ad-fs-with-multiple-azure-ad" class="xliff"></a>

Prenons un domaine contoso.com dans Azure Active Directory : contoso.onmicrosoft.com est déjà fédéré à AD FS local installé dans l’environnement Active Directory local contoso.com. Fabrikam.com est un domaine dans Azure Active Directory fabrikam.onmicrosoft.com.

##Étape 1 : Établir une relation d’approbation bidirectionnelle
<a id="step-1-establish-a-two-way-trust" class="xliff"></a>
 
Pour qu’AD FS dans contoso.com puisse authentifier les utilisateurs de fabrikam.com, une approbation bidirectionnelle est nécessaire entre contoso.com et fabrikam.com. Suivez les instructions de cet [article](https://technet.microsoft.com/library/cc816590.aspx) pour créer l’approbation bidirectionnelle.
 
##Étape 2 : Modifier les paramètres de fédération contoso.com
<a id="step-2-modify-contosocom-federation-settings" class="xliff"></a> 
 
L’émetteur par défaut définie pour un seul domaine fédéré sur AD FS est « http://FQDN_du_service_AD_FS/adfs/services/trust », par exemple « http://fs.contoso.com/adfs/services/trust ». Azure Active Directory nécessite un émetteur unique pour chaque domaine fédéré. Étant donné qu’une même instance d’AD FS va fédérer deux domaines, la valeur de l’émetteur doit être modifiée pour être propre à chaque domaine qu’AD FS fédère avec Azure Active Directory. 
 
Sur le serveur AD FS, ouvrez Azure AD PowerShell et suivez les étapes suivantes :
 
Connectez-vous à l’instance d’Azure Active Directory qui contient le domaine contoso.com : Connect-MsolService. Mettez à jour les paramètres de fédération de contoso.com : Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain
 
L’émetteur du paramètre de fédération de domaines sera modifié en « http://contoso.com/adfs/services/trust » et une règle de revendication d’émission sera ajoutée pour que l’approbation de la partie de confiance Azure AD émette la valeur issuerId correspondant au suffixe UPN.
 
##Étape 3 : Fédérer fabrikam.com avec AD FS
<a id="step-3-federate-fabrikamcom-with-ad-fs" class="xliff"></a>
 
Dans la session PowerShell Azure AD, suivez les étapes suivantes : connectez-vous à l’instance d’Azure Active Directory qui contient le domaine fabrikam.com.

    Connect-MsolService
Convertissez le domaine managé fabrikam.com en un domaine fédéré :

    Convert-MsolDomainToFederated -DomainName anandmsft.com -Verbose -SupportMultipleDomain
 
L’opération ci-dessus fédère le domaine fabrikam.com avec la même instance d’AD FS. Vous pouvez vérifier les paramètres de domaine en utilisant Get-MsolDomainFederationSettings pour les deux domaines.

## Étapes suivantes
<a id="next-steps" class="xliff"></a>
[Connecter Active Directory à Azure Active Directory](active-directory-aadconnect.md)

