---
title: 'Azure AD Connect : authentification directe - Limitations actuelles | Microsoft Docs'
description: "Cet article décrit les limitations actuelles de l’authentification directe Azure Active Directory (Azure AD)."
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 37c0ea094d02208f2516a4a040f75894e046c670
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---

# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Authentification directe Azure Active Directory : limitations actuelles

>[!IMPORTANT]
>L’authentification directe Azure AD est actuellement en préversion. Cette fonctionnalité est gratuite et il est inutile de disposer des éditions payantes d’Azure AD pour l’utiliser. L’authentification directe est disponible seulement dans l’instance mondiale d’Azure AD, et pas sur [Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) ni sur [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Scénarios pris en charge

Les scénarios suivants sont entièrement pris en charge dans la version préliminaire :

- L’utilisateur se connecte dans toutes les applications basées sur un navigateur web.
- L’utilisateur se connecte dans les applications clientes Office 365 prenant en charge [l’authentification moderne](https://aka.ms/modernauthga).
- Azure AD Join pour les appareils Windows 10.
- Prise en charge d’Exchange ActiveSync.

## <a name="unsupported-scenarios"></a>Scénarios non pris en charge

Les scénarios suivants ne sont _pas_ pris en charge dans la version préliminaire :

- Connexions des utilisateurs dans les applications clientes Office héritées (Office 2013 ou version antérieure). Nous recommandons aux entreprises de basculer si possible vers l’authentification moderne. L’authentification moderne permet non seulement de prendre en charge l’authentification directe, mais également de sécuriser les comptes d’utilisateur à l’aide des fonctionnalités [d’accès conditionnel](../active-directory-conditional-access.md), comme l’authentification multifacteur.
- Connexions des utilisateurs à des applications clientes Skype Entreprise, y compris Skype Entreprise 2016.
- L’utilisateur se connecte dans PowerShell v1.0. Il est recommandé d’utiliser à la place PowerShell v2.0.

>[!IMPORTANT]
>Comme solution de contournement pour les scénarios non pris en charge, activez la synchronisation du hachage de mot de passe sur la page [Fonctionnalités facultatives](active-directory-aadconnect-get-started-custom.md#optional-features) dans l’Assistant Azure AD Connect. La synchronisation du hachage de mot de passe agit comme solution de secours _uniquement_ pour les scénarios précédents (et _non pas_ comme solution de secours générique pour l’authentification directe). Si vous n’avez pas besoin de ces scénarios, désactivez la synchronisation du hachage de mot de passe.

## <a name="next-steps"></a>Étapes suivantes
- [**Démarrage rapide**](active-directory-aadconnect-pass-through-authentication-quick-start.md) : Mise en route de l’authentification directe Azure AD.
- [**Immersion technique**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) : découvrez comment fonctionne cette fonctionnalité.
- [**Forum aux questions**](active-directory-aadconnect-pass-through-authentication-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de se produire avec cette fonctionnalité.
- [**Authentification unique transparente Azure AD**](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour formuler des demandes de nouvelles fonctionnalités.

