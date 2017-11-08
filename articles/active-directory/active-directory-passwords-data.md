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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 7936f47007285e3f7fa1d3220efa022a6e3881ca
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
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

* [Comment réussir le lancement de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-best-practices.md)
* [Réinitialisez ou modifiez votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [Inscrivez-vous pour la réinitialisation du mot de passe en libre-service](active-directory-passwords-reset-register.md).
* [Vous avez une question relative à la licence ?](active-directory-passwords-licensing.md)
* [Quelles méthodes d'authentification sont accessibles aux utilisateurs ?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quelles sont les options de stratégie disponibles avec la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-policy.md)
* [Quelle est l’écriture différée de mot de passe et pourquoi dois-je m’y intéresser ?](active-directory-passwords-writeback.md)
* [Comment puis-je générer des rapports sur l’activité dans la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-reporting.md)
* [Quelles sont toutes les options disponibles dans la réinitialisation de mot de passe en libre-service et que signifient-elles ?](active-directory-passwords-how-it-works.md)
* [Je pense qu’il y a une panne quelque part. Comment puis-je résoudre les problèmes de la réinitialisation de mot de passe en libre-service ?](active-directory-passwords-troubleshoot.md)
* [J’ai une question à laquelle je n’ai pas trouvé de réponse ailleurs](active-directory-passwords-faq.md)
