<properties linkid="manage-services-identity-multi-factor-authentication" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="What is Azure Multi-Factor Authentication?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />

<tags ms.service="multi-factor-authentication" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="billmath" />

# Présentation d'Azure Multi-Factor Authentication

L'authentification multi-facteur ou à deux facteurs est une méthode d'authentification qui nécessite l'utilisation de plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. Cette authentification fonctionne en nécessitant au minimum deux des méthodes de vérification suivantes :

-   Un élément que vous connaissez (généralement un mot de passe)
-   Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
-   Un élément vous concernant particulièrement (biométrie)

La sécurité de l'authentification multi-facteur repose sur son approche en couche. Compromettre plusieurs facteurs d'authentification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître le mot de passe de l'utilisateur, ce dernier est inutile sans posséder l'appareil de confiance. De même, si l'utilisateur venait à perdre l'appareil, celui qui l'a trouvé ne pourra pas l'utiliser sans connaître le mot de passe de l'utilisateur.
Azure Multi-Factor Authentication est le service d'authentification multi-facteur qui demande aux utilisateurs de vérifier également les connexions à l'aide d'une application mobile, d'un appel téléphonique ou d'un message texte. Ce service est disponible pour une utilisation avec Azure Active Directory afin de sécuriser les ressources locales avec le serveur Azure Multi-Factor Authentication, ainsi qu'avec des applications et répertoires personnalisés, en utilisant le Kit de développement logiciel (SDK).

![Azure Multi-Factor Authentication][Azure Multi-Factor Authentication]

### Sécurisation du cloud Azure Active Directory

Activez l'authentification multi-facteur pour les identités Azure AD. Les utilisateurs seront invités à configurer une vérification supplémentaire la prochaine fois qu'ils se connectent. Utilisez l'authentification multi-facteur pour sécuriser l'accès à Azure, à Microsoft Online Services, par exemple Office 365 et Dynamics CRM Online, ainsi qu'à des services cloud tiers qui intègrent Azure AD sans configuration supplémentaire. L'authentification multi-facteur peut être rapidement activée pour un grand nombre d'utilisateurs et d'applications globaux. [En savoir plus][En savoir plus]

### Sécurisation des ressources locales et d'Active Directory

Activez l'authentification multi-facteur pour vos ressources locales, telles qu'IIS et Active Directory, à l'aide du serveur Azure Multi-Factor Authentication. Le serveur Azure Multi-Factor Authentication permet à l'administrateur d'intégrer l'authentification IIS afin de sécuriser les applications Web Microsoft IIS, l'authentification RADIUS, l'authentification LDAP et l'authentification Windows. [En savoir plus][1]

### Sécurisation des applications personnalisées

</p>
Un Kit de développement logiciel (SDK) permet une intégration à vos services cloud. Intégrez la vérification par appel téléphonique et message texte de l'authentification active à vos processus de connexion aux applications ou de transaction et exploitez la base de données utilisateur existante de votre application. [En savoir plus][2]

### Authentification multi-facteur pour Office 365

L'authentification multi-facteur Office 365, optimisée par Azure Multi-Factor Authentication, fonctionne exclusivement avec les applications Office 365 et est gérée depuis le portail Office 365. De ce fait, les administrateurs peuvent désormais sécuriser leurs ressources Office 365 avec une authentification multi-facteur. [En savoir plus][3]

### Authentification multi-facteur pour administrateurs Azure

Le même sous-ensemble de fonctionnalités d'authentification multi-facteur pour Office 365 est disponible sans frais à tous les administrateurs Azure. Tout compte administratif possédant un abonnement Azure peut désormais bénéficier d'une protection supplémentaire en activant cette fonctionnalité principale d'authentification multi-facteur. Ainsi, un administrateur qui souhaite accéder au portail Azure afin de créer une machine virtuelle, un site Web, gérer le stockage, les services mobiles ou tout autre service Azure peut ajouter une authentification multi-facteur à son compte d'administrateur. [En savoir plus][4]

### Comparaison des fonctionnalités de l'authentification multi-facteur

La capture d'écran ci-dessous présente les versions de l'authentification multi-facteur disponibles et un bref résumé des fonctionnalités qu'elle fournit. Vous pouvez l'utiliser pour déterminer la version de l'authentification multi-facteur qui vous convient. [En savoir plus][4]

![Comparaison des fonctionnalités de l'authentification multi-facteur Azure][Comparaison des fonctionnalités de l'authentification multi-facteur Azure]

**Ressources supplémentaires**

-   [Inscription à Azure en tant qu'organisation][Inscription à Azure en tant qu'organisation]
-   [Identité Azure][Identité Azure]
-   [Bibliothèque Azure Multi-Factor Authentication][Bibliothèque Azure Multi-Factor Authentication]

  [Azure Multi-Factor Authentication]: ./media/multi-factor-authentication/WAMFA1.png
  [En savoir plus]: http://msdn.microsoft.com/fr-fr/library/dn249466.aspx
  [1]: http://msdn.microsoft.com/fr-fr/library/dn249467.aspx
  [2]: http://msdn.microsoft.com/fr-fr/library/dn249464.aspx
  [3]: http://msdn.microsoft.com/fr-fr/library/dn383636.aspx
  [4]: http://msdn.microsoft.com/fr-fr/library/dn249471.aspx
  [Comparaison des fonctionnalités de l'authentification multi-facteur Azure]: ./media/multi-factor-authentication/mfacomparison1.png
  [Inscription à Azure en tant qu'organisation]: /fr-fr/manage/services/identity/organizational-account/
  [Identité Azure]: /fr-fr/manage/windows/fundamentals/identity/
  [Bibliothèque Azure Multi-Factor Authentication]: http://technet.microsoft.com/fr-fr/library/dn249471.aspx
