<properties
	pageTitle="Accès conditionnel Azure Active Directory | Microsoft Azure"  
    description="Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application."  
    services="active-directory" 
	keywords="accès conditionnel aux applications, accès conditionnel à Azure AD, accès sécurisé aux ressources d’entreprise, stratégies d’accès conditionnel" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="07/26/2016"
	ms.author="markvi"/>


# Accès conditionnel Azure Active Directory   
  
Chaque structure doit impérativement s’assurer de sécuriser l’accès aux ressources d’entreprise. Avec l’avènement des services cloud et des appareils mobiles, la façon dont les utilisateurs accèdent aux ressources d’entreprise a considérablement évolué. Une nouvelle approche de la sécurité est par conséquent requise.
  
## Pourquoi l’accès conditionnel ?  

Les fonctionnalités de contrôle d’accès conditionnel d’Active Directory procurent aux entreprises des modes simples de sécurisation des ressources dans le cloud et en local. Les stratégies d’accès conditionnel peuvent contribuer à protéger contre le risque de vol et le hameçonnage d’informations d’identification, et à sécuriser vos données d’entreprise en gérant l’octroi des accès aux services sensibles via un périphérique géré par Intune.



## Licences requises

L’accès conditionnel est une fonctionnalité [Azure AD Premium](http://www.microsoft.com/identity). Tous les utilisateurs accédant à une application limitée par une stratégie d’accès conditionnel doivent disposer d’une licence Azure AD Premium. Consultez [Unlicensed User report](https://aka.ms/utc5ix) (Rapport sur les utilisateurs sans licence) pour en savoir plus sur l’utilisation de cette fonctionnalité.





## Comment est appliqué le contrôle d’accès conditionnel ?  

Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur, avant d’autoriser l’accès à l’application. Une fois que ces conditions d’accès sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Conditions
  
- **Groupe d’appartenance** : vous pouvez contrôler le niveau d’accès d’un utilisateur en fonction de son appartenance à un groupe.

- **Emplacement**: vous pouvez utiliser l’emplacement de l’utilisateur pour déclencher la MFA et bloquer les contrôles lorsqu’un utilisateur n’est pas sur un réseau approuvé.

- **Plateforme d’appareil**: vous pouvez définir le type de plateforme utilisée par un appareil (par exemple iOS, Android, Windows Mobile et Windows) comme condition d’application d’une stratégie.

- **Device enabled **(appareil activé) : le satut d’appareil activé / désactivé est validé au cours de l’évaluation de la stratégie d’appareil. En désactivant un appareil perdu ou volé dans le répertoire, celui-ci n’est plus utilisable conformément à la stratégie.

- **Risques liés à la connexion et à l’utilisateur** : des stratégies de risque d’accès conditionnel sont disponibles avec Azure AD Identity Protection et offrent une protection avancée selon différents risques et activités de connexion inhabituelles.


## Commandes
   
- **Multi-factor authentication (MFA)**: la MFA vous permet d’appliquer une authentification renforcée. La MFA peut être fournie par Azure MFA ou par un fournisseur MFA local à l’aide d’AD FS. L’authentification MFA contribue à empêcher tout accès à vos ressources par un utilisateur non autorisé qui aurait mis la main sur le nom d’utilisateur et le mot de passe d’un utilisateur valide.

- **Bloc** (blocage) : l’accès peuvent être autorisé en fonction de certaines conditions, telles que l’emplacement d’un utilisateur. L’accès peut par exemple être bloqué pour les utilisateurs qui ne sont pas sur un réseau approuvé.

- **Enrolled / Compliant devices** (appareils inscrits / conformes) : vous pouvez définir au niveau de l’appareil, des stratégies garantissant que seuls les appareils inscrits et conformes bénéficient d’un accès. Microsoft Intune est utilisé pour vérifier que l’appareil est inscrit et conforme. L’accès conditionnel de niveau appareil garantit alors que seuls les appareils conformes à votre stratégie de MDM bénéficient d’un accès.
 

## Applications

- Le niveau d’accès défini à l’aide de ces stratégies peut être appliqué aux applications et services en local ou dans le cloud. La stratégie est appliquée directement au site Web ou au service. La stratégie est ensuite appliquée pour l’accès navigateur ainsi que pour les applications accédant au service. La liste des services auxquels la stratégie peut être appliquée se trouve ici.


  
## Accès conditionnel - Un plan de contenu  
Le plan de contenu suivant répertorie les documents auxquels vous devez vous référer pour vous renseigner sur l’activation de l’accès conditionnel au sein de votre déploiement actuel.


### MFA et stratégies d’emplacement

- [Getting started with conditional access to Azure AD connected apps based on group, location, and MFA policies](active-directory-conditional-access-azuread-connected-apps.md) (Prise en main de l’accès conditionnel aux applications connectées à Azure AD en fonction du groupe, de l’emplacement et des stratégies MFA)
- [Quels sont les types d’applications pris en charge](active-directory-conditional-access-supported-apps.md)


### Stratégies d’appareils

[Protégez vos données nécessitant Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### Protection des ressources en fonction d’un risque à la connexion

[Azure AD Identity Protection](active-directory-identityprotection.md)

### Informations supplémentaires

- [FAQ sur l’accès conditionnel](active-directory-conditional-faqs.md)
- [Référence technique](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0727_2016-->