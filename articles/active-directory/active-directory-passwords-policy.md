---
title: "Stratégie : réinitialisation du mot de passe en libre-service Azure AD | Microsoft Docs"
description: "Options de stratégie de réinitialisation de mot de passe en libre-service Azure AD"
services: active-directory
keywords: "Gestion des mots de passe Active Directory, gestion des mots de passe, réinitialisation de mot de passe en libre-service Azure AD"
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
ms.openlocfilehash: 9d61f46070e6956c60f1135b98a9ebe71011b922
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Stratégies et restrictions de mot de passe dans Azure Active Directory

Cet article décrit les stratégies de mot de passe et les exigences en matière de complexité associées aux comptes d’utilisateur stockés sur votre client Azure AD.

## <a name="administrator-password-policy-differences"></a>Différences en matière de stratégie de mot de passe administrateur

Microsoft met en œuvre une stratégie de réinitialisation des mots de passe **à deux entrées** par défaut pour n’importe quel rôle d’administrateur Azure (exemple : administrateur général, administrateur de support technique, administrateur de mots de passe, etc.)

Cela empêche les administrateurs d’utiliser les questions de sécurité et applique ce qui suit.

Une stratégie à deux portes, nécessitant deux parties de données d’authentification (adresse e-mail **et** numéro de téléphone), s’applique dans les cas suivants

* Tous les rôles administrateur Azure
  * Administrateur du support technique
  * Administrateur de support de service
  * Administrateur de facturation
  * Prise en charge de niveau 1 de partenaire
  * Prise en charge de niveau 2 de partenaire
  * Administrateur de services Exchange
  * Administrateur de services Lync
  * Administrateur de compte utilisateur
  * Enregistreurs de répertoire
  * Administrateur général/Administrateur de l’entreprise
  * Administrateur de services SharePoint
  * Administrateur de conformité
  * Administrateur d’application
  * Security Administrator
  * Administrateur de rôle privilégié
  * Administrateur de services Intune
  * Administrateur du service de proxy d’application
  * Administrateur de services CRM
  * Administrateur du service Power BI
  
* 30 jours se sont écoulés dans un essai **OU**
* Le domaine personnel est présent (contoso.com) **OU**
* Azure AD Connect synchronise les identités à partir de votre répertoire local

### <a name="exceptions"></a>Exceptions
Une stratégie de porte, nécessitant une partie de données d’authentification (adresse e-mail **et** numéro de téléphone), s’applique dans les cas suivants

* 30 premiers jours d’un essai **OU**
* Le domaine personnel n’est pas présent (*.onmicrosoft.com) **ET** Azure AD Connect ne synchronise pas les identités


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Stratégies UserPrincipalName s'appliquant à tous les comptes d'utilisateur

Chaque compte d’utilisateur devant se connecter à Azure AD doit être doté d’une valeur d’attribut unique de nom d’utilisateur principal (UPN) associée à son compte. Le tableau ci-dessous décrit les stratégies qui s’appliquent à la fois aux comptes d’utilisateurs Active Directory locaux synchronisés sur le cloud et aux comptes d’utilisateurs uniquement dans le cloud.

| Propriété | Conditions requises pour UserPrincipalName |
| --- | --- |
| Caractères autorisés |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Caractères non autorisés |<ul> <li>Tout caractère « @ » qui ne sépare pas le nom d’utilisateur du domaine.</li> <li>Ne peut pas contenir un point « . » précédant immédiatement le symbole « @ »</li></ul> |
| Contraintes de longueur |<ul> <li>La longueur totale ne doit pas dépasser 113 caractères</li><li>64 caractères avant le symbole « @ »</li><li>48 caractères après le symbole « @ »</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>Stratégies de mot de passe s'appliquant uniquement aux comptes d'utilisateur dans le cloud

La table suivante décrit les paramètres de stratégie de mot de passe disponibles pouvant être appliqués aux comptes d'utilisateurs créés et gérés dans Azure AD.

| Propriété | Configuration requise |
| --- | --- |
| Caractères autorisés |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caractères non autorisés |<ul><li>Caractères Unicode</li><li>Espaces</li><li> **Mots de passe forts uniquement** : ne peut pas contenir de point « . » précédant immédiatement le symbole « @ »</li></ul> |
| Restrictions de mot de passe |<ul><li>8 caractères au minimum et 16 caractères au maximum</li><li>**Mots de passe forts uniquement** : 3 des 4 éléments suivants sont requis :<ul><li>Caractères minuscules</li><li>Caractères majuscules</li><li>Chiffres (0-9)</li><li>Symboles (voir les restrictions de mot de passe ci-dessus)</li></ul></li></ul> |
| Délai d'expiration du mot de passe |<ul><li>Valeur par défaut : **90** jours </li><li>La valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy à partir du Module Azure Active Directory pour Windows PowerShell.</li></ul> |
| Notification d'expiration du mot de passe |<ul><li>Valeur par défaut : **14** jours (avant l’expiration du mot de passe)</li><li>La valeur est configurable à l’aide de l’applet de commande Set-MsolPasswordPolicy.</li></ul> |
| Expiration du mot de passe |<ul><li>Valeur par défaut : **false** jours (indique que l’expiration du mot de passe est activée) </li><li>La valeur peut être configurée pour des comptes d'utilisateur individuels à l'aide de l'applet de commande Set-MsolUser. </li></ul> |
| Historique de **modification** du mot de passe |L’ancien mot de passe **ne peut pas** être réutilisé lors de la **modification** du mot de passe. |
| Historique de **réinitialisation** du mot de passe | L’ancien mot de passe **peut** être réutilisé lors de la **réinitialisation** d’un mot de passe oublié. |
| Verrouillage de compte |Au bout de 10 tentatives de connexion infructueuses (mot de passe incorrect), l’utilisateur est bloqué pendant une minute. La durée de blocage de l’utilisateur augmente au fil des nouvelles tentatives de connexion incorrectes. |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>Définir des stratégies d’expiration de mot de passe dans Azure Active Directory

Un administrateur global d’un service cloud Microsoft, peut utiliser le Module Microsoft Azure Active Directory pour Windows PowerShell afin de configurer des mots de passe utilisateur qui n’expirent pas. Vous pouvez également utiliser des applets de commande Windows PowerShell pour supprimer la configuration de non-expiration, ou voir quels mots de passe utilisateur sont configurés pour ne pas expirer. Ces conseils s’appliquent à d’autres fournisseurs tels que Microsoft Intune et Office 365, qui s’appuient également sur Microsoft Azure Active Directory pour les services d’annuaire et d’identité. Il s’agit de la seule partie de la stratégie qui peut être modifiée.

> [!NOTE]
> Seuls les mots de passe de comptes d’utilisateurs non synchronisés via une synchronisation d’annuaires peuvent être configurés pour ne pas expirer. Pour plus d’informations sur la synchronisation d’annuaires, consultez [Connecter Active Directory à Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>Définir ou vérifier les stratégies de mot de passe à l’aide de PowerShell

Pour commencer, vous devez [télécharger et installer le module Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Une fois installé, vous pouvez suivre les étapes ci-dessous pour configurer chaque champ.

### <a name="how-to-check-expiration-policy-for-a-password"></a>Vérifier la stratégie d’expiration d’un mot de passe
1. Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.
2. Exécutez l’une des commandes suivantes :

   * Pour voir si le mot de passe d’un utilisateur donné est défini pour ne jamais expirer, exécutez l’applet de commande suivante en utilisant le nom d’utilisateur principal (UPN) (par exemple, aprilr@contoso.onmicrosoft.com) ou l’identifiant utilisateur de l’utilisateur à vérifier : `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Pour afficher le paramètre « Le mot de passe n’expire jamais » pour tous les utilisateurs, exécutez l’applet de commande suivante : `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Définir un mot de passe pour qu’il expire

1. Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.
2. Exécutez l’une des commandes suivantes :

   * Pour définir le mot de passe d’un utilisateur afin qu’il expire, exécutez l’applet de commande suivante en utilisant le nom d’utilisateur principal (UPN) ou l’identifiant utilisateur de l’utilisateur : `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Pour définir les mots de passe de tous les utilisateurs de l’organisation afin qu’ils expirent, utilisez l’applet de commande suivante : `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Définir un mot de passe pour qu’il n’expire jamais

1. Connectez-vous à Windows PowerShell à l’aide de vos informations d’identification d’administrateur de la société.
2. Exécutez l’une des commandes suivantes :

   * Pour définir le mot de passe d’un utilisateur afin qu’il n’expire jamais, exécutez l’applet de commande suivante en utilisant le nom d’utilisateur principal (UPN) ou l’identifiant d’utilisateur de l’utilisateur : `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Pour définir les mots de passe de tous les utilisateurs de l’organisation afin qu’ils n’expirent jamais, utilisez l’applet de commande suivante : `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Si vous définissez `-PasswordNeverExpires $true`, l’ancienneté du mot de passe continuera à être prise en compte selon l’attribut `pwdLastSet`. Par conséquent, si vous configurez les mots de passe de manière qu’ils n’expirent jamais, puis que vous définissez `-PasswordNeverExpires $false` alors que plus de 90 jours se sont écoulés selon `pwdLastSet`, tous les mots de passe qui présentent un attribut `pwdLastSet` dont l’ancienneté est supérieure à 90 jours devront être modifiés à la prochaine ouverture de session. Cette modification de stratégie est susceptible d’avoir un impact sur un grand nombre d’utilisateurs. 

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles données sont utilisées par la réinitialisation de mot de passe en libre-service et quelles données vous devez renseigner pour vos utilisateurs ?](active-directory-passwords-data.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)
