---
title: 'Azure AD Connect : authentification unique transparente | Microsoft Docs'
description: "Cette rubrique décrit l’authentification unique transparente Azure Active Directory (Azure AD) et explique comment cette fonction vous permet de fournir une véritable authentification unique aux utilisateurs du réseau d’entreprise."
services: active-directory
keywords: "Qu’est-ce qu’Azure AD Connect, Installation d’Active Directory, Composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 72b5217bd8de29fdad753d89f34934f64c551ff2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Authentification unique transparente Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Qu’est-ce que l’authentification unique transparente Azure Active Directory ?

L’authentification unique transparente Azure Active Directory connecte automatiquement les utilisateurs lorsque leurs appareils d’entreprise sont connectés au réseau de l’entreprise. Lorsque cette fonctionnalité est activée, les utilisateurs n’ont plus besoin de taper leur mot de passe pour se connecter à Azure AD ni même, dans la plupart des cas, leur nom d’utilisateur. Cette fonctionnalité offre à vos utilisateurs un accès facilité à vos applications cloud sans nécessiter de composants locaux supplémentaires.

L’authentification unique transparente peut être combinée avec la [synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) et [l’authentification directe](active-directory-aadconnect-pass-through-authentication.md).

![Authentification unique transparente](./media/active-directory-aadconnect-sso/sso1.png)

>[!NOTE]
>Cette fonctionnalité _n’est pas_ applicable à Active Directory Federation Services (ADFS), qui fournit déjà cette possibilité.

## <a name="key-benefits-of-using-azure-ad-seamless-sso"></a>Principaux avantages de l’authentification unique transparente Azure AD

- *Une meilleure expérience utilisateur*
  - Les utilisateurs sont automatiquement connectés aux applications cloud et locales.
  - Les utilisateurs n’ont pas à saisir leur mot de passe plusieurs fois.
- *Facile à déployer et à gérer*
  - Aucun composant local supplémentaire n’est nécessaire pour que cela fonctionne.
  - Fonctionne avec n’importe quelle méthode d’authentification gérée : [Synchronisation de hachage de mot de passe](active-directory-aadconnectsync-implement-password-synchronization.md) ou [Authentification directe](active-directory-aadconnect-pass-through-authentication.md).
  - Peut être déployée pour certains utilisateurs ou pour l’ensemble de vos utilisateurs à l’aide d’une stratégie de groupe.
  - Permet d’inscrire les appareils non Windows 10 dans Azure AD. Le [client Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) version 2.1 ou ultérieure est nécessaire.

## <a name="feature-highlights"></a>Présentation des fonctionnalités

- Le nom d’utilisateur peut être soit le nom d’utilisateur local par défaut (`userPrincipalName`), soit un autre attribut configuré dans Azure AD Connect (`Alternate ID`).
- L’authentification unique transparente est une fonctionnalité opportuniste. Si elle échoue pour une raison quelconque, l’expérience de connexion utilisateur retrouve son comportement normal (l’utilisateur doit alors entrer son mot de passe dans la page de connexion).
- Si une application transfère un `domain_hint` (correspondant à votre locataire) ou le paramètre `login_hint` (correspondant à l’utilisateur) dans sa demande de connexion Azure AD, les utilisateurs sont automatiquement connectés, sans qu’ils n’aient à entrer leurs nom d’utilisateur et mot de passe.
- L’authentification unique transparente peut être activée par le biais d’Azure AD Connect.
- Cette fonctionnalité est gratuite et il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser.
- L’authentification unique est prise en charge par les clients basés sur le navigateur web et les clients Office qui prennent en charge [l’authentification moderne](https://aka.ms/modernauthga) sur les plateformes et navigateurs compatibles avec l’authentification Kerberos :

| Système d’exploitation\Navigateur |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Oui|Non pris en charge|Oui|Oui\*|N/A
|Windows 8.1|Oui|Non pris en charge|Oui|Oui\*|N/A
|Windows 8|Oui|Non pris en charge|Oui|Oui\*|N/A 
|Windows 7|Oui|Non pris en charge|Oui|Oui\*|N/A
|Mac OS X|N/A|N/A|Oui\*|Oui\*|Non pris en charge

\*Nécessite une [configuration supplémentaire](active-directory-aadconnect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>Concernant Windows 10, il est recommandé d’utiliser [Azure AD Join](../active-directory-azureadjoin-overview.md) pour une expérience optimale d’authentification unique dans Azure AD.

## <a name="next-steps"></a>Étapes suivantes

- [**Démarrage rapide**](active-directory-aadconnect-sso-quick-start.md) : découvrez l’authentification unique transparente Azure AD.
- [**Immersion technique**](active-directory-aadconnect-sso-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Questions fréquentes (FAQ)**](active-directory-aadconnect-sso-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-sso.md) : découvrez comment résoudre les problèmes courants susceptibles de se produire avec cette fonctionnalité.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.

