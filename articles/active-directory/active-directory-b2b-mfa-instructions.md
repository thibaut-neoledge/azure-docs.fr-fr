---
title: "Accès conditionnel pour utilisateurs Azure Active Directory B2B Collaboration | Microsoft Docs"
description: "Azure Active Directory B2B Collaboration prend en charge l’authentification multifacteur (MFA) pour un accès sélectif à vos applications d’entreprise"
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
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: fac4657e9b78e900c052384e212aa9f3915f970d
ms.lasthandoff: 04/13/2017


---

# <a name="conditional-access-for-b2b-collaboration-users"></a>Accès conditionnel pour les utilisateurs de B2B Collaboration

## <a name="multi-factor-authentication-for-b2b-users"></a>Authentification MFA pour utilisateurs B2B
Avec Azure AD B2B Collaboration, les organisations ont exclusivement la possibilité d’appliquer des stratégies d’authentification multifacteur pour les utilisateurs B2B. Ces stratégies peuvent être appliquées au niveau locataire, application ou utilisateur individuel, de la même façon que ces stratégies peuvent être activées pour les employés à plein temps et les membres de l’organisation. Les stratégies MFA sont appliquées à l’organisation de ressources.

La procédure est la suivante :
1. L’administrateur ou le professionnel de l’information de la société A invite des utilisateurs de la société B à l’application Foo dans la société A.
2. L'application *Foo* dans la société A est configurée de manière à exiger l’authentification MFA lors de l’accès.
3. Lorsque des utilisateurs de la société B tentent d’accéder à application Foo à partir d’un locataire de la société A, ils sont invités à effectuer une authentification MFA comme exigé par la stratégie d’authentification MFA de la société A.
4. Les utilisateurs peuvent configurer leur authentification MFA avec la société A et choisir leur option d’authentification MFA.
5. Cela fonctionne pour n’importe quelle identité (Azure AD ou MSA, par exemple, si les utilisateurs dans la société B s’authentifient à l’aide de leur ID social).
6. La société A nécessitera des références SKU Azure AD Premium adéquates qui prennent en charge l’authentification MFA. L’utilisateur de la société B utilisera cette licence à partir de la société A.

En résumé, la location d’invitation est *toujours* responsable de l’authentification MFA de B2B Collaboration aux utilisateurs de l’organisation partenaire, et non l’organisation partenaire elle-même (même si elle a des fonctionnalités d’authentification MFA).

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Configuration de MFA pour les utilisateurs de B2B Collaboration
Pour découvrir combien il est facile de configurer l’authentification MFA pour les utilisateurs de B2B Collaboration, consultez la vidéo suivante :

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Expérience MFA d'utilisation de l'invitation par des utilisateurs B2B
Regardez l’animation ci-dessous pour découvrir comment utiliser l'invitation, comme indiqué dans la vidéo suivante :

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Réinitialisation de l'authentification MFA pour les utilisateurs de B2B de Collaboration
Actuellement, l’administrateur peut exiger que les utilisateurs de B2B Collaboration s'authentifient à nouveau uniquement en utilisant les applets de commande PowerShell suivantes. Par conséquent, les applets de commande PowerShell suivantes doivent être utilisées si vous souhaitez réinitialiser la méthode d'authentification de l'utilisateur B2B Collaboration.

1. Se connecter à Azure AD

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Obtenir tous les utilisateurs avec des méthodes d'authentification

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Voici un exemple :

  ```
  PS C:\Users\tjwasserGet-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Réinitialiser la méthode d’authentification MFA pour un utilisateur spécifique. Vous pouvez ensuite utiliser ce UserPrincipalName pour exécuter la commande de réinitialisation pour obliger l’utilisateur B2B Collaboration à définir de nouveau des méthodes d'authentification. Exemple :

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Pourquoi effectuer l’authentification MFA au niveau de la location de la ressource ?

Dans la version actuelle, l’authentification MFA s’effectue toujours dans la location de la ressource. La raison est la prévisibilité.

Par exemple, un utilisateur de Contoso (Catherine) est invité à Fabrikam, et Fabrikam a activé l’authentification MFA pour les utilisateurs B2B.

Si Contoso utilise la stratégie MFA sur App1 mais pas sur App2, et si nous examinons ensuite la revendication MFA de Contoso dans le jeton pour déterminer si Catherine doit effectuer l’authentification MFA dans Fabrikam ou non, le problème suivant peut se produire :

* Jour 1 : Catherine a effectué l’authentification MFA dans Contoso car elle accède à App1, elle ne voit donc pas l’invite d’authentification MFA dans Fabrikam.

* Jour 2 : Catherine a accès à App2 dans Contoso, donc maintenant, lorsqu’elle accède à Fabrikam, elle devra réaliser l’authentification MFA dans Fabrikam.

Cela peut être très déroutant pour Catherine et entraîner probablement des abandons de connexion.

En outre, même si Contoso dispose de la fonctionnalité MFA, il n’est pas toujours certain que Fabrikam approuve la stratégie MFA de Contoso.

Enfin, l’authentification MFA du client de la ressource fonctionne également pour les MSA et les ID sociaux ainsi que pour les organisations partenaires au sein desquelles l’authentification MFA n’est pas configurée.

Par conséquent, la recommandation pour l’authentification MFA pour les utilisateurs B2B consiste à toujours demander l’authentification MFA des ressources. Dans certains cas, cela peut entraîner une authentification MFA double, mais à chaque accès à la location de la ressource, l’expérience des utilisateurs finaux est prévisible : Catherine doit s’inscrire à MFA avec la location de la ressource.

### <a name="device-location-and-risk-based-conditional-access-for-b2b-users"></a>Accès conditionnel en fonction des appareils, des emplacements et des risques pour les utilisateurs B2B

Lorsque l’organisation Contoso active les stratégies d’accès conditionnel en fonction des appareils pour ses données d’entreprise, l’accès est protégé contre les appareils non gérés (autrement dit, les appareils qui ne sont pas gérés par l’organisation Contoso et qui ne sont pas conformes aux stratégies d’appareils de Contoso).

Si l’appareil de l’utilisateur B2B n’est pas géré par Contoso, cela signifie que l’accès des utilisateurs B2B des organisations partenaires sera bloqué quel que soit le contexte d’application de ces stratégies.

Cependant, le fait d’exiger que l’appareil des utilisateurs d’une autre organisation soit géré par l’organisation qui invite représente une exigence trop importante. Par conséquent, dans les futures mises à jour, nous permettrons à Contoso d’approuver l’état de compatibilité des appareils d’un partenaire spécifique. Cela permettrait à Contoso d’appliquer les stratégies où un utilisateur de Fabrikam peut accéder aux ressources de Contoso s’il utilise un appareil également géré par Fabrikam.

En attendant, Contoso peut créer des listes d’exclusion contenant des utilisateurs spécifiques du partenaire à partir de la stratégie d’accès conditionnel en fonction des appareils.

#### <a name="location-based-conditional-access-for-b2b"></a>Accès conditionnel en fonction des emplacements pour B2B

Les stratégies d’accès conditionnel en fonction des emplacements peuvent être appliquées pour les utilisateurs B2B si l’organisation qui invite (par exemple, Contoso) est en mesure de créer un périmètre de réseau approuvé (c'est-à-dire, une plage d’adresses IP) qui définit leurs organisations partenaires (par exemple, Fabrikam).

#### <a name="risk-based-conditional-access-for-b2b"></a>Accès conditionnel en fonction des risques pour B2B

Actuellement, les stratégies en fonction des risques ne peuvent pas être appliquées aux utilisateurs B2B, car l’évaluation des risques est effectuée au niveau de l’organisation d’origine de l’utilisateur B2B (en d’autres termes, la location d’identité de l’utilisateur B2B).

Pour les futures mises à jour, nous envisageons de fédérer la note de risque à partir des partenaires (avec leur consentement) afin que Contoso puisse protéger ses applications et données partagées en externe non seulement contre les risques connus, mais également contre les risques qu’ils n’ont aucun moyen de connaître car pouvant se produire ailleurs.

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

