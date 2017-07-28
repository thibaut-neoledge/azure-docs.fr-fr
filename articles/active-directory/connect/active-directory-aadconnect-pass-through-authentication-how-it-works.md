---
title: "Azure AD Connect : Fonctionnement de l’authentification directe | Microsoft Docs"
description: "Cet article décrit le fonctionnement de l’authentification directe Azure Active Directory."
services: active-directory
keywords: "Authentification directe Azure AD Connect, installation d’Active Directory, composants requis pour Azure AD, SSO, Authentification unique"
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
ms.openlocfilehash: 294ffde02fdf6b54e03837b2ace77b2940441459
ms.contentlocale: fr-fr
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Authentification directe Azure Active Directory : immersion technique

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Comment l’authentification directe Azure Active Directory fonctionne-t-elle ?

Quand un utilisateur tente de se connecter à une application sécurisée par Azure Active Directory (Azure AD) et que l’authentification directe est activée sur le client, les étapes suivantes se produisent :

1. L’utilisateur tente d’accéder à une application (par exemple, Outlook Web App - https://outlook.office365.com/owa/).
2. Si l’utilisateur n’est pas encore connecté, il est redirigé vers la page de connexion Azure AD.
3. L’utilisateur entre son nom d’utilisateur et sont mot de passe dans la page de connexion à Azure AD, puis clique sur le bouton « Se connecter ».
4. Azure AD, lors de la réception de la demande de connexion, place le nom d’utilisateur et le mot de passe (chiffré à l’aide d’une clé publique) dans une file d’attente.
5. Un agent d’authentification directe local effectue un appel sortant à la file d’attente et récupère le nom d’utilisateur et le mot de passe chiffré.
6. L’agent déchiffre le mot de passe à l’aide de sa clé privée.
7. L’agent valide ensuite le nom d’utilisateur et le mot de passe par rapport à votre annuaire Active Directory à l’aide des API Windows standard. Ce mécanisme est similaire à celui utilisé par les services de fédération Active Directory (AD FS). Le nom d’utilisateur peut être soit le nom d’utilisateur local par défaut (généralement, `userPrincipalName`), soit un autre attribut (appelé `Alternate ID`) configuré dans Azure AD Connect.
8. Le contrôleur de domaine Active Directory local évalue ensuite la demande et retourne la réponse appropriée (succès, échec, mot de passe expiré ou utilisateur verrouillé) à l’agent.
9. L’agent retourne à son tour cette réponse à Azure AD.
10. Azure AD évalue la réponse et répond à l’utilisateur de la manière appropriée. Par exemple, il connecte l’utilisateur immédiatement ou demande l’authentification multifacteur (MFA).
11. Si la connexion de l’utilisateur réussit, celui-ci peut accéder à l’application.

Le schéma suivant illustre les composants et les étapes impliqués dans ce processus.

![Authentification directe](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Étapes suivantes
- [**Limitations actuelles**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) : cette fonctionnalité est actuellement en préversion. Découvrez les scénarios pris en charge et ceux qui ne le sont pas.
- [**Démarrage rapide**](active-directory-aadconnect-pass-through-authentication-quick-start.md) : Prise en main de l’authentification directe Azure AD.
- [**Forum aux questions**](active-directory-aadconnect-pass-through-authentication-faq.md) : réponses aux questions fréquentes.
- [**Résolution des problèmes**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) : découvrez comment résoudre les problèmes courants susceptibles de se produire avec cette fonctionnalité.
- [**Authentification unique transparente Azure AD**](active-directory-aadconnect-sso.md) : explorez en détail cette fonctionnalité complémentaire.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour le dépôt de nouvelles demandes de fonctionnalités.

