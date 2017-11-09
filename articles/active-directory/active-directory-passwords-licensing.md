---
title: "Liences : réinitialisation de mot de passe en libre-service Azure AD | Microsoft Docs"
description: "Conditions de licence pour la réinitialisation du mot de passe en libre-service dans Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Conditions de licence pour la réinitialisation du mot de passe en libre-service Azure AD

Pour que la réinitialisation de mot de passe Azure AD fonctionne, vous **devez disposer d’au moins une licence affectée dans votre organisation**. Nous n’appliquons pas de licence par utilisateur lors de l’expérience de réinitialisation de mot de passe. Pour conserver la conformité avec votre contrat de licence Microsoft, vous devez attribuer des licences à tous les utilisateurs qui utilisent des fonctionnalités Premium.

* **Utilisateurs du cloud uniquement** : n’importe quelle référence Office 365 (O365) payante ou Azure AD Basic
* Utilisateurs **cloud** et/ou **locaux** : Azure AD Premium P1 ou P2, Enterprise Mobility + Security (EMS) ou Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>Licences requises pour la réécriture du mot de passe

Pour utiliser l’écriture différée du mot de passe, vous devez avoir attribué une des licences suivantes dans votre locataire.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!WARNING]
> Les plans de licences Office 365 édition autonome **ne prennent pas en charge la réécriture du mot de passe** et nécessitent l’un des plans précédents pour que cette fonctionnalité soit opérationnelle.

Vous trouverez des informations de licence supplémentaires, y compris les coûts, sur les pages suivantes

* [Site de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Fonctionnalités Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Activer les licences utilisateur ou groupe

Azure AD prend maintenant en charge les licences en groupe, ce qui permet aux administrateurs d’attribuer des licences en bloc à un groupe d’utilisateurs, plutôt que de leur en attribuer une à la fois. [Affecter, vérifier et résoudre les problèmes de licences](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété Emplacement d’utilisation sur l’utilisateur. L’attribution de licences peut être effectuée à la section Utilisateur > Profil > Paramètres sur le portail Azure. **Lorsque vous attribuez une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement du répertoire.**

## <a name="next-steps"></a>Étapes suivantes

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données devez-vous renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)
