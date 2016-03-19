<properties
	pageTitle="Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure AD | Microsoft Azure"  
    description="Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."  
    services="active-directory" 
	keywords="accès aux applications, un accès sécurisé aux ressources d’entreprise, les stratégies d’accès conditionnel" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/10/2016"
	ms.author="femila"/>


# Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure AD  
  
Chaque structure doit impérativement s’assurer de sécuriser l’accès aux ressources d’entreprise. Avec l’avènement des services cloud et des appareils mobiles, les modes d’accès des utilisateurs aux ressources d’entreprise a considérablement évolué. Ce nouvel environnement nécessite une refonte de la stratégie de sécurisation des ressources d’entreprise.
  
## Pourquoi l’accès conditionnel ?  
 Les fonctionnalités de contrôle d’accès conditionnel d’Active Directory procurent aux entreprises des modes simples de sécurisation des ressources dans le cloud et en local. Vous avez besoin de prévenir tout accès à vos ressources au moyen d’un mot de passe volé ? Vous cherchez à vous doter d’un appareil fiable et géré, dédié à l’accès au contenu d’entreprise ? Azure Active Directory est la solution qu’il vous faut.

## Comment est appliqué le contrôle d’accès conditionnel ?  
 Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur, avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Accès de l’utilisateur aux ressources
  
- **Attributs utilisateur** : au niveau des attributs utilisateur, vous pouvez appliquer des stratégies garantissant que seuls les utilisateurs autorisés ont accès aux ressources d’entreprise.   
- **Groupe d’appartenance d’un utilisateur** : vous pouvez également contrôler le niveau d’accès octroyé à un utilisateur en fonction de son appartenance à un ou plusieurs groupes.   
- **Multi-factor authentication (MFA)** : par ailleurs, vous pouvez également appliquer des stratégies qui obligent l’utilisateur à s’authentifier à l’aide d’un système d’authentification à plusieurs facteurs. Par exemple, vous pouvez forcer un utilisateur à saisir un code secret sur un téléphone mobile afin de garantir une couche supplémentaire de sécurité. L’authentification MFA prévient tout accès de vos ressources par un utilisateur non autorisé qui a mis la main sur le nom d’utilisateur et le mot de passe d’un utilisateur valide. 

## Accès conditionnel aux appareils 

- **Appareils inscrits** : au niveau de l’appareil, vous pouvez définir des stratégies qui garantissent que les appareils inscrits ou connus sont les seuls à bénéficier d’un accès. Par ailleurs, vous pouvez utiliser une solution de gestion des périphériques mobiles comme Microsoft Intune pour vous assurer que seuls les périphériques gérés peuvent accéder à vos ressources. L’accès conditionnel de niveau appareil garantit que seuls les appareils conformes à vos stratégies de sécurité, comme la saisie d’un code secret, bénéficient d’un accès. En outre, en faisant appel aux solutions de gestion des périphériques mobiles, vous pouvez certifier que les données d’entreprise stockées sur un appareil perdu/volé peuvent être effacées à distance.  
- **Stratégies d’appareils** : vous pouvez également définir des stratégies qui restreignent l’accès en fonction des applications. De plus, il est également possible de configurer le niveau d’accès suivant l’emplacement physique de l’appareil, selon que la requête soit issue d’un réseau connu d’entreprise ou de l’extérieur.  

Le niveau d’accès défini à l’aide de ces stratégies peut être appliqué aux ressources en local ou dans le cloud. Les ressources dans le cloud peuvent être des applications, comme Office 365 ou des applications SaaS tierces. Il peut également s’agir d’applications métier hébergées sur le cloud.
  
## Accès conditionnel - Un plan de contenu  
Le plan de contenu suivant répertorie les documents auxquels vous devez vous référer pour vous renseigner sur l’activation de l’accès conditionnel au sein de votre déploiement actuel.


| Scénario | Articles |
|------------------------------------------------------|----------|
| Protection des ressources d’entreprises contre les attaques par hameçonnage |[Vue d’ensemble de l’accès conditionnel Azure pour les applications SaaS](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Conditional access to Azure AD apps (Accès conditionnel aux applications Azure AD)](active-directory-conditional-access-technical-reference.md)<br><br>[Prise en main avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-get-started-cloud.md)<br><br>[Configurer des méthodes d’authentification supplémentaires pour AD FS](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[Problèmes avec Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)<br><br>[Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS](multi-factor-authentication-get-started-adfs-cloud.md)|
| Protection des données d’entreprise stockées sur les appareils perdus/volés |[Vue d’ensemble du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)<br><br> [Configuration d’Azure AD Join dans votre organisation](active-directory-azureadjoin-setup.md)<br><br> [Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory](active-directory-conditional-access-on-premises-setup.md) <br><br>[Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md) <br><br>[Configurer l’inscription automatique des appareils pour les appareils joints à un domaine Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows8_1.md) <br><br>[Stratégies d’accès conditionnel basées sur les appareils pour les services Office 365](active-directory-conditional-access-device-policies.md)|
| Informations supplémentaires |[FAQ sur l’accès conditionnel](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0218_2016-->