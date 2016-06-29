<properties
	pageTitle="Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure AD | Microsoft Azure"  
    description="Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."  
    services="active-directory" 
	keywords="accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel" 
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/15/2016"
	ms.author="femila"/>


# Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure AD  
  
Chaque structure doit impérativement s’assurer de sécuriser l’accès aux ressources d’entreprise. Avec l’avènement des services cloud et des appareils mobiles, les modes d’accès des utilisateurs aux ressources d’entreprise a considérablement évolué. Ce nouvel environnement nécessite une refonte de la stratégie de sécurisation des ressources d’entreprise.
  
## Pourquoi l’accès conditionnel ?  
 Les fonctionnalités de contrôle d’accès conditionnel d’Active Directory procurent aux entreprises des modes simples de sécurisation des ressources dans le cloud et en local. Vous avez besoin de prévenir tout accès à vos ressources au moyen d’un mot de passe volé ? Vous cherchez à vous doter d’un appareil fiable et géré, dédié à l’accès au contenu d’entreprise ? Azure Active Directory est la solution qu’il vous faut.

## Comment est appliqué le contrôle d’accès conditionnel ?  
 Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur, avant d’autoriser l’accès à l’application. Une fois que ces conditions d’accès sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Accès de l’utilisateur aux ressources
  
- **Attributs utilisateur** : au niveau des attributs utilisateur, vous pouvez appliquer des stratégies garantissant que seuls les utilisateurs autorisés ont accès aux ressources d’entreprise.   
- **Groupe d’appartenance d’un utilisateur** : vous pouvez également contrôler le niveau d’accès octroyé à un utilisateur en fonction de son appartenance à un ou plusieurs groupes.   
- **Multi-factor authentication (MFA)** : vous pouvez également appliquer des stratégies qui obligent l’utilisateur à s’authentifier à l’aide d’un système d’authentification à plusieurs facteurs. Par exemple, vous pouvez forcer un utilisateur à saisir un code secret sur un téléphone mobile afin de garantir une couche supplémentaire de sécurité. L’authentification MFA prévient tout accès de vos ressources par un utilisateur non autorisé qui a mis la main sur le nom d’utilisateur et le mot de passe d’un utilisateur valide. 
- **Risques liés à la connexion et à l’utilisateur** : des stratégies de risque d’accès conditionnel sont disponibles avec Azure AD Identity Protection et offrent une protection avancée selon différents risques et activités de connexion inhabituelles. 
 

## Accès conditionnel aux appareils 

**Appareils inscrits** : au niveau de l’appareil, vous pouvez définir des stratégies garantissant que seuls les appareils inscrits ou connus bénéficient d’un accès. Microsoft Intune est utilisé pour vérifier que l’appareil est inscrit et conforme. L’accès conditionnel de niveau appareil garantit que seuls les appareils conformes à vos stratégies de sécurité, comme le chiffrement de fichier, bénéficient d’un accès. En outre, en faisant appel aux solutions de gestion des périphériques mobiles, vous pouvez certifier que les données d’entreprise stockées sur un appareil perdu/volé peuvent être effacées à distance.
  

Le niveau d’accès défini à l’aide de ces stratégies peut être appliqué aux ressources en local ou dans le cloud. Les ressources dans le cloud peuvent être des applications, comme Office 365 ou des applications SaaS tierces. Il peut également s’agir d’applications métier hébergées sur le cloud.
  
## Accès conditionnel - Un plan de contenu  
Le plan de contenu suivant répertorie les documents auxquels vous devez vous référer pour vous renseigner sur l’activation de l’accès conditionnel au sein de votre déploiement actuel.


| Scénario | Articles |
|------------------------------------------------------|----------|
| Protection des ressources en fonction de la force d’authentification ou d’un utilisateur |[Prise en main de l’accès conditionnel aux applications Azure AD SaaS avec authentification multifacteur et extranet](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Quelles sont les applications prises en charge](active-directory-conditional-access-supported-apps.md)|
| Protection des données d’entreprise stockées sur les appareils perdus/volés |[Protection de vos données avec effacement complet ou sélectif à l’aide de Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)|
|Protection des ressources en fonction d’un risque à la connexion |[Azure AD Identity Protection](active-directory-identityprotection.md) |
| Informations supplémentaires |[FAQ sur l’accès conditionnel](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0615_2016-->