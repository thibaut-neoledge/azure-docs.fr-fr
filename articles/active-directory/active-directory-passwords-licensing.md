---
title: "Liences : réinitialisation de mot de passe en libre-service Azure AD | Microsoft Docs"
description: "Conditions de licence pour la réinitialisation du mot de passe en libre-service dans Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 3ed13c819f8e32cab44013cdcbf1b3a921ba98b8
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Conditions de licence pour la réinitialisation du mot de passe en libre-service Azure AD

Pour que la réinitialisation de mot de passe Azure AD fonctionne, vous **devez disposer d’au moins une licence affectée dans votre organisation**. Nous n’appliquons pas de licence par utilisateur lors de l’expérience de réinitialisation de mot de passe. Pour conserver la conformité avec votre contrat de licence Microsoft, vous devez attribuer des licences à tous les utilisateurs qui utilisent des fonctionnalités Premium.

* **Utilisateurs du cloud uniquement** : n’importe quelle référence Office 365 (O365) payante ou Azure AD Basic
* Utilisateurs **cloud** et/ou **locaux** : Azure AD Premium P1 ou P2, Enterprise Mobility + Security (EMS) ou Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>Licences requises pour l’écriture différée du mot de passe

Pour utiliser l’écriture différée du mot de passe, vous devez avoir attribué une des licences suivantes dans votre locataire.

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Secure Productive Enterprise E3
* Secure Productive Enterprise E5

> [!NOTE]
> Les plans de licences Office 365 édition autonome **ne prennent pas en charge l’écriture différée de mot de passe** et nécessitent l’un des plans précédents pour que cette fonctionnalité soit opérationnelle.

Vous trouverez des informations de licence supplémentaires, y compris les coûts, sur les pages suivantes

* [Site de tarification d’Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

## <a name="enable-group-or-user-based-licensing"></a>Activer les licences utilisateur ou groupe

Azure AD prend maintenant en charge les licences en groupe, ce qui permet aux administrateurs d’attribuer des licences en bloc à un groupe d’utilisateurs, plutôt que de leur en attribuer une à la fois. [Affecter, vérifier et résoudre les problèmes de licences](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Certains services Microsoft ne sont pas disponibles dans tous les emplacements. Avant de pouvoir attribuer une licence à un utilisateur, l’administrateur doit spécifier la propriété Emplacement d’utilisation sur l’utilisateur. L’attribution de licences peut être effectuée à la section Utilisateur > Profil > Paramètres sur le portail Azure. **Lorsque vous attribuez une licence à un groupe, tous les utilisateurs sans emplacement d’utilisation spécifié héritent de l’emplacement du répertoire.**

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Démarrage rapide**](active-directory-passwords-getting-started.md) : soyez rapidement opérationnel avec la gestion des mots de passe en libre-service d’Azure AD. 
* [**Données**](active-directory-passwords-data.md) : comprenez mieux les données requises et leur utilisation dans la gestion des mots de passe.
* [**Déploiement**](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation de mot de passe en libre-service pour vos utilisateurs grâce aux conseils figurant ici.
* [**Personnalisation**](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation de mot de passe en libre-service de votre société.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service.
* [**Présentation technique approfondie**](active-directory-passwords-how-it-works.md) : découvrez ce qui se passe sous le capot pour mieux comprendre le fonctionnement.
* [**Forum Aux Questions (FAQ)**](active-directory-passwords-faq.md) : Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous vouliez poser depuis toujours.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants susceptibles de survenir avec la réinitialisation de mot de passe en libre-service.


