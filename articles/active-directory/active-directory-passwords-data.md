---
title: "Exigences en matière de données Azure AD SSPR | Microsoft Docs"
description: "Exigences en matière de données pour la réinitialisation du mot de passe en libre-service Azure AD et comment les satisfaire"
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
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: f7ecb22ee46d83867453e035f8a639bc7f7f2d81
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017

---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Déployer la réinitialisation du mot de passe sans demander l’inscription de l’utilisateur final

Le déploiement de la réinitialisation de mot du passe en libre-service (SSPR) exige que les données d’authentification soient présentes. Certaines organisations demandent à leurs utilisateurs d’entrer leurs données d’authentification eux-mêmes, mais de nombreuses organisations préfèrent se synchroniser avec les données existantes dans Active Directory. Si vous avez correctement mis en forme les données dans votre répertoire local et configurez [Azure AD Connect à l’aide des paramètres express](./connect/active-directory-aadconnect-get-started-express.md), ces données sont rendues disponibles pour Azure AD et SSPR sans aucune interaction utilisateur requise.

Les numéros de téléphone doivent être au format +CodePays NuméroTéléphone (exemple : +1 4255551234) pour fonctionner correctement.

> [!NOTE]
> La réinitialisation du mot de passe ne prend pas en charge les extensions de téléphone. Même au format +1 4255551234X12345, les extensions sont supprimées avant l’appel.

## <a name="fields-populated"></a>Champs renseignés

Si vous utilisez les paramètres par défaut dans Azure AD Connect, les mappages suivants sont effectués.

| AD local | Azure AD | Informations de contact de l’authentification AD Azure |
| --- | --- | --- |
| telephoneNumber | Téléphone de bureau | Autre téléphone |
| mobile | Téléphone mobile | Téléphone |


## <a name="security-questions-and-answers"></a>Questions et réponses de sécurité

Les questions et les réponses de sécurité sont stockées de manière sécurisée sur votre locataire Azure AD et sont uniquement accessibles aux utilisateurs par le biais du [portail d’inscription SSPR](https://aka.ms/ssprsetup). Les administrateurs ne peuvent pas voir ou modifier le contenu des questions et des réponses des autres utilisateurs.

### <a name="what-happens-when-a-user-registers"></a>Ce qu’il se passe lorsqu'un utilisateur s'inscrit

Lorsqu’un utilisateur s'inscrit, la page d’inscription définit les champs suivants :

* Téléphone d’authentification
* E-mail d’authentification
* Questions et réponses de sécurité

Si vous avez fourni une valeur pour **Téléphone mobile** ou **Autre adresse de messagerie**, les utilisateurs peuvent immédiatement l’utiliser pour réinitialiser leur mot de passe, même s’ils ne se sont pas inscrits au service. Les utilisateurs voient ces valeurs lorsqu’ils s’inscrivent pour la première fois et ils ont la possibilité de les modifier. Une fois les utilisateurs inscrits, ces valeurs sont conservées respectivement dans les champs **Téléphone d’authentification** et **E-mail d’authentification**.

## <a name="set-and-read-authentication-data-using-powershell"></a>Définir et lire les données d’authentification à l’aide de PowerShell

Les champs suivants peuvent être définis à l’aide de PowerShell

* Autre adresse de messagerie
* Téléphone mobile
* Téléphone de bureau : ne peut être défini qu’en l’absence de synchronisation avec un répertoire local

### <a name="using-powershell-v1"></a>Utiliser PowerShell V1

Pour commencer, vous devez [télécharger et installer le module Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Une fois le module installé, vous pouvez suivre les étapes suivantes pour configurer chaque champ.

#### <a name="set-authentication-data-with-powershell-v1"></a>Définir les données d’authentification avec PowerShell V1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>Lire les données d’authentification avec PowerShell V1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>Téléphone d’authentification et E-mail d’authentification ne peuvent être lus qu’à l’aide de Powershell V1 en utilisant les commandes qui suivent

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>Utiliser PowerShell V2

Pour commencer, vous devez [télécharger et installer le module Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Une fois le module installé, vous pouvez suivre les étapes suivantes pour configurer chaque champ.

Pour installer rapidement des versions récentes de PowerShell compatibles avec Install-Module, exécutez ces commandes (la première ligne vérifie simplement si le produit est déjà installé) :

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>Définir les données d’authentification avec PowerShell V2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>Lire les données d’authentification avec PowerShell V2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Étapes suivantes

Les liens suivants fournissent des informations supplémentaires sur la réinitialisation de mot de passe à l’aide d’Azure AD.

* [**Démarrage rapide**](active-directory-passwords-getting-started.md) : soyez rapidement opérationnel avec la gestion des mots de passe en libre-service Azure AD. 
* [**Licences**](active-directory-passwords-licensing.md) : configurez vos licences Azure AD.
* [**Déploiement**](active-directory-passwords-best-practices.md) : planifiez et déployez la réinitialisation de mot de passe en libre-service pour vos utilisateurs grâce aux conseils figurant ici.
* [**Personnalisation**](active-directory-passwords-customize.md) : personnalisez l’apparence de l’interface de réinitialisation de mot de passe en libre-service de votre société.
* [**Stratégie**](active-directory-passwords-policy.md) : comprenez mieux et définissez les stratégies de mot de passe d’Azure AD.
* [**Rapports**](active-directory-passwords-reporting.md) : découvrez si, quand et où vos utilisateurs accèdent aux fonctionnalités de réinitialisation de mot de passe en libre-service.
* [**Présentation technique approfondie**](active-directory-passwords-how-it-works.md) : découvrez les rouages de cette fonctionnalité pour mieux en comprendre le fonctionnement.
* [**Questions fréquentes (FAQ)**](active-directory-passwords-faq.md) - Comment ? Pourquoi ? Quoi ? Où ? Qui ? Quand ? - Les réponses aux questions que vous avez toujours voulu poser.
* [**Résolution des problèmes**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre les problèmes courants que vous êtes susceptibles de rencontrer avec SSPR.

