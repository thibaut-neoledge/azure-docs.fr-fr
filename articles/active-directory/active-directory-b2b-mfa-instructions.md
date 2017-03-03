---
title: Authentification multifacteur pour utilisateurs Azure Active Directory B2B | Microsoft Docs
description: "Azure Active Directory B2B Collaboration prend en charge l’authentification multifacteur (MF) pour un accès sélectif à vos applications d’entreprise"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 32d0b45080d57712209e0c5a3e5adf981fb4b66e
ms.lasthandoff: 02/17/2017


---

# <a name="multi-factor-authentication-for-azure-active-directory-b2b-collaboration-users"></a>Authentification multifacteur pour utilisateurs Azure Active Directory B2B

Avec cette actualisation de la version préliminaire publique d'Azure AD B2B Collaboration, nous offrons aux organisations la possibilité de mettre en œuvre des stratégies d'authentification multifacteur (MFA) également pour les utilisateurs B2B Collaboration. Dans cette actualisation, l’authentification MFA est toujours appliquée à la location de la ressource.

La procédure est la suivante :
1. L'administrateur ou professionnel de l'information de la société A invite des utilisateurs de la société B à l'application Foo dans la société A.
2. L'application *Foo* dans la société A est configurée de manière à exiger l’authentification MFA lors de l’accès.
3. Lorsque des utilisateurs de la société B tentent d’accéder à application Foo à partir d’un locataire de la société A, ils sont invités à effectuer une authentification MFA comme exigé par la stratégie d’authentification MFA de la société A.
4. Les utilisateurs peuvent configurer leur authentification MFA avec la société A et choisir leur option d’authentification MFA.
5. Cela fonctionne pour n’importe quelle identité (Azure AD ou MSA, par exemple, si les utilisateurs dans la société B s’authentifient à l’aide de leur ID social).
6. La société A nécessitera des références SKU Azure AD Premium adéquates qui prennent en charge l’authentification MFA. L’utilisateur de la société B utilisera cette licence à partir de la société A.
7. En résumé, la location d’invitation est *toujours* responsable de l’authentification MFA de B2B Collaboration aux utilisateurs de l’organisation partenaire, et non l’organisation partenaire elle-même (même si elle a des fonctionnalités d’authentification MFA). Dans des futures versions, nous permettrons à l’organisation qui invite de faire confiance à l’authentification MFA d'organisations partenaires spécifiques au lieu d’utiliser l’authentification MFA de l'organisation qui invite.

## <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configuration de MFA pour les utilisateurs de B2B Collaboration
Pour découvrir combien il est facile de configurer l’authentification MFA pour les utilisateurs de B2B Collaboration, consultez la vidéo suivante :

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

## <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Expérience MFA d'utilisation de l'invitation par des utilisateurs B2B
Regardez l’animation ci-dessous pour découvrir comment utiliser l'invitation, comme indiqué dans la vidéo suivante :

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

## <a name="mfa-reset-for-b2b-collaboration-users"></a>Réinitialisation de l'authentification MFA pour les utilisateurs de B2B de Collaboration
Actuellement, l’administrateur peut exiger que les utilisateurs de B2B Collaboration s'authentifient à nouveau uniquement en utilisant les applets de commande PowerShell suivantes. Par conséquent, les applets de commande PowerShell suivantes doivent être utilisées si vous souhaitez réinitialiser la méthode d'authentification de l'utilisateur B2B Collaboration.

> [!NOTE]
> Pour utiliser la nouvelle applet de commande, vous devez installer le module Azure AD PowerShell V2, que vous pouvez obtenir à l'adresse suivante : https://www.powershellgallery.com/packages/AzureADPreview

1. Se connecter à Azure AD

  ```
  Connect-MsolService and login
  ```
2. Obtenir tous les utilisateurs avec des méthodes d'authentification

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Voici un exemple :

  ```
  PS C:\Users\tjwasser> Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName,
  @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Réinitialiser la méthode d’authentification MFA pour un utilisateur spécifique. Vous pouvez ensuite utiliser ce UserPrincipalName pour exécuter la commande de réinitialisation pour obliger l’utilisateur B2B Collaboration à définir de nouveau des méthodes d'authentification. Exemple :

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

## <a name="next-steps"></a>Étapes suivantes

Consultez les autres articles sur la collaboration B2B d'Azure AD :

* [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Comment les administrateurs Azure Active Directory ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-admin-add-users.md)
* [Comment les professionnels de l’information ajoutent-ils des utilisateurs B2B Collaboration ?](active-directory-b2b-iw-add-users.md)
* [Éléments de l’e-mail d’invitation de B2B Collaboration](active-directory-b2b-invitation-email.md)
* [Utilisation d’une invitation B2B Collaboration](active-directory-b2b-redemption-experience.md)
* [Attribution de licences Azure AD B2B Collaboration](active-directory-b2b-licensing.md)
* [Résolution des problèmes d’Azure Active Directory B2B Collaboration](active-directory-b2b-troubleshooting.md)
* [Questions fréquemment posées (FAQ) sur Azure Active Directory B2B Collaboration](active-directory-b2b-faq.md)
* [API et personnalisation d’Azure Active Directory B2B Collaboration](active-directory-b2b-api.md)
* [Ajouter des utilisateurs B2B Collaboration sans invitation](active-directory-b2b-add-user-without-invite.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

