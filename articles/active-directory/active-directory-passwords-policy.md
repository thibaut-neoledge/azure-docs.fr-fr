---
title: "Stratégies et restrictions de mot de passe dans Azure Active Directory | Microsoft Docs"
description: "Décrit les stratégies s&quot;appliquant dans Azure Active Directory, notamment les caractères autorisés, la longueur et l&quot;expiration des mots de passe"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e7cae5cf-e0ee-467b-9a90-db6fdf56cd52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: fc73b667112fe35473872c33bc98423a424c5c65
ms.openlocfilehash: d6bee5afea6afcd756b53f41baef5dd984423c0a
ms.lasthandoff: 02/17/2017


---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Stratégies et restrictions de mot de passe dans Azure Active Directory
Cet article décrit les stratégies de mot de passe et les exigences en matière de complexité associées aux comptes d’utilisateur stockés dans votre annuaire Azure AD.

> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
>
>

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Stratégies UserPrincipalName s'appliquant à tous les comptes d'utilisateur
Chaque compte d’utilisateur devant se connecter au système d’authentification Azure AD doit être doté d’une valeur d’attribut unique de nom d’utilisateur principal (UPN) associée à ce compte. Le tableau suivant décrit les stratégies qui s’appliquent aux comptes d’utilisateurs Active Directory sur site (synchronisés sur le cloud) et aux comptes d’utilisateurs uniquement dans le cloud.

| Propriété | Conditions requises pour UserPrincipalName |
| --- | --- |
| Caractères autorisés |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caractères non autorisés |<ul> <li>Tout caractère '@' qui ne sépare pas le nom d’utilisateur du domaine.</li> <li>Ne peut pas contenir un point « . » précédant immédiatement le symbole '@'</li></ul> |
| Contraintes de longueur |<ul> <li>La longueur totale ne doit pas dépasser 113 caractères</li><li>64 caractères avant le symbole ‘@’</li><li>48 caractères après le symbole ‘@’</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Stratégies de mot de passe s'appliquant uniquement aux comptes d'utilisateur dans le cloud
Le tableau suivant décrit les paramètres de stratégie de mot de passe disponibles pouvant être appliqués aux comptes d’utilisateurs créés et gérés dans Azure AD.

| Propriété | Configuration requise |
| --- | --- |
| Caractères autorisés |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caractères non autorisés |<ul><li>Caractères Unicode</li><li>Espaces</li><li> **Mots de passe forts uniquement** : ne peut pas contenir de point « . » précédant immédiatement le symbole '@'</li></ul> |
| Restrictions de mot de passe |<ul><li>8 caractères au minimum et 16 caractères au maximum</li><li>**Mots de passe forts uniquement** : 3 des 4 éléments suivants sont requis :<ul><li>Caractères minuscules</li><li>Caractères majuscules</li><li>Chiffres (0-9)</li><li>Symboles (voir les restrictions de mot de passe ci-dessus)</li></ul></li></ul> |
| Délai d'expiration du mot de passe |<ul><li>Valeur par défaut : **90** jours </li><li>La valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy à partir du Module Azure Active Directory pour Windows PowerShell.</li></ul> |
| Notification d'expiration du mot de passe |<ul><li>Valeur par défaut : **14** jours (avant l’expiration du mot de passe)</li><li>La valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy.</li></ul> |
| Expiration du mot de passe |<ul><li>Valeur par défaut : **false** jours (indique que l’expiration du mot de passe est activée) </li><li>La valeur peut être configurée pour des comptes d'utilisateur individuels à l'aide de l'applet de commande Set-MsolUser. </li></ul> |
| Historique de **modification** du mot de passe |L’ancien mot de passe **ne peut pas** être réutilisé lors de la **modification** du mot de passe. |
| Historique de **réinitialisation** du mot de passe | L’ancien mot de passe **peut** être réutilisé lors de la **réinitialisation** d’un mot de passe oublié. |
| Verrouillage de compte |Au bout de 10 tentatives de connexion infructueuses (mot de passe incorrect), l’utilisateur est bloqué pendant une minute. La durée de blocage de l’utilisateur augmente au fil des nouvelles tentatives de connexion incorrectes. |

## <a name="next-steps"></a>Étapes suivantes
* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Gestion de vos mots de passe en tout lieu](active-directory-passwords.md)
* [Fonctionnement de la gestion des mots de passe](active-directory-passwords-how-it-works.md)
* [Prise en main de la gestion des mots de passe](active-directory-passwords-getting-started.md)
* [Personnalisation de la gestion des mots de passe](active-directory-passwords-customize.md)
* [Meilleures pratiques de gestion des mots de passe](active-directory-passwords-best-practices.md)
* [Obtention d’informations grâce aux rapports sur la gestion des mots de passe](active-directory-passwords-get-insights.md)
* [FAQ sur la gestion des mots de passe](active-directory-passwords-faq.md)
* [Résolution des problèmes de gestion des mots de passe](active-directory-passwords-troubleshoot.md)
* [En savoir plus](active-directory-passwords-learn-more.md)

