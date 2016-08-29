<properties
	pageTitle="Accès conditionnel Azure Active Directory | azure.microsoft.com/ Azure"  
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
	ms.date="08/17/2016"
	ms.author="markvi"/>


# Accès conditionnel Azure Active Directory   
  
Chaque structure doit impérativement s’assurer de sécuriser l’accès aux ressources d’entreprise. Avec l’avènement des services cloud et des appareils mobiles, la façon dont les utilisateurs accèdent aux ressources d’entreprise a considérablement évolué. Une nouvelle approche de la sécurité est par conséquent requise.
  
## Pourquoi l’accès conditionnel ?  

Les fonctionnalités de contrôle d’accès conditionnel d’Active Directory procurent aux entreprises des modes simples de sécurisation des ressources dans le cloud et en local. Les stratégies d’accès conditionnel peuvent contribuer à protéger contre le risque de vol et le hameçonnage d’informations d’identification, et à sécuriser vos données d’entreprise en gérant l’octroi des accès aux services sensibles via un périphérique géré par Intune.



## Licences requises

L’accès conditionnel est une fonctionnalité [Azure AD Premium](http://www.azure.microsoft.com/.com/identity). Tous les utilisateurs accédant à une application limitée par une stratégie d’accès conditionnel doivent disposer d’une licence Azure AD Premium. Consultez [Unlicensed User report](https://aka.ms/utc5ix) (Rapport sur les utilisateurs sans licence) pour en savoir plus sur l’utilisation de cette fonctionnalité.





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

- **Appareils compatibles** : au niveau de l’appareil, vous pouvez définir des stratégies qui dont que seuls les ordinateurs qui sont joints ou les appareils mobiles qui sont inscrits dans la gestion des appareils mobiles (MDM) et sont conformes puissent obtenir l’accès. azure.microsoft.com/ Intune est utilisé pour confirmer la conformité sur les appareils et en informer Azure Active Directory pour les vérifications lors de l’accès aux applications.
 

## Applications

- Le niveau d’accès défini à l’aide de ces stratégies peut être appliqué aux applications et services en local ou dans le cloud. La stratégie est appliquée directement au site Web ou au service. La stratégie est ensuite appliquée pour l’accès navigateur ainsi que pour les applications accédant au service. La liste des services auxquels la stratégie peut être appliquée se trouve ici.


## Accès conditionnel basé sur les appareils

Vous pouvez également restreindre l’accès aux applications à partir des appareils qui sont inscrits auprès d’Azure AD et qui répondent à des conditions spécifiques. Cela est utile pour protéger les ressources de l’organisation d’un accès à ces ressources par des utilisateurs valides depuis :

- Périphériques inconnus / non gérés
- Des appareils non conformes aux stratégies de sécurité définies par votre organisation.

Des stratégies peuvent être définies en fonction des exigences suivantes :

- **Appareils joints à un domaine** : vous pouvez définir une stratégie pour limiter l’accès aux appareils qui sont joints à un domaine Active Directory local et également enregistrés avec Azure AD. Cette stratégie s’applique aux ordinateurs portables, tablettes d’entreprise et ordinateurs de bureau Windows qui appartiennent à un domaine Active Directory local et sont enregistrés avec Azure AD. Pour plus d’informations sur la configuration de l’inscription automatique des appareils joints à un domaine dans Azure AD, consultez [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Appareils compatibles** : vous pouvez définir une stratégie pour limiter l’accès aux appareils qui sont marqués comme étant **conformes** dans le répertoire par le système de gestion. Cette stratégie garantit que seuls les appareils qui répondent aux stratégies de sécurité, comme le chiffrement des fichiers sur l’appareil, ont droit à l’accès. Cette stratégie peut être utilisée pour limiter l’accès par les périphériques suivants :

    - Les **appareils joints à un domaine Windows** qui sont gérés par System Center Configuration Manager 2016 et déployés dans une configuration hybride.

    - Les **appareils mobiles Windows 10 personnels ou professionnels** gérés par azure.microsoft.com/ Intune ou un système de gestion des appareils mobiles (MDM) tiers.

    - Les **appareils iOS et Android** qui sont gérés par azure.microsoft.com/ Intune.


Les utilisateurs qui accèdent aux applications protégées par la stratégie d’accès conditionnel basée sur les appareils doivent le faire à partir d’appareils qui répondent aux conditions de cette stratégie. L’accès est refusé s’il s’agit d’un appareil qui ne respecte pas la stratégie.

Pour plus d’informations sur la façon de configurer la stratégie d’accès conditionnel basé sur les appareils dans Azure AD, consultez [Comment configurer la stratégie d’accès conditionnel basé sur les appareils pour contrôler aux applications connectées à Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## Index d’article pour l’accès conditionnel Azure Active Directory
  
Le plan de contenu suivant répertorie les documents auxquels vous devez vous référer pour vous renseigner sur l’activation de l’accès conditionnel au sein de votre déploiement actuel.


### MFA et stratégies d’emplacement

- [Getting started with conditional access to Azure AD connected apps based on group, location, and MFA policies (Prise en main de l’accès conditionnel aux applications connectées à Azure AD en fonction du groupe, de l’emplacement et des stratégies MFA)](active-directory-conditional-access-azuread-connected-apps.md)

- [Quels sont les types d’applications pris en charge](active-directory-conditional-access-supported-apps.md)


### Accès conditionnel basé sur les appareils

- [Comment définir la stratégie d’accès conditionnel basé sur les appareils pour contrôler aux applications connectées à Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)

- [Configuration de l’inscription automatique auprès d’Azure Active Directory d’appareils Windows joints à un domaine](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Correction des utilisateurs lors de l’accès aux applications protégées à accès conditionnel en fonction des appareils Azure AD](active-directory-conditional-access-device-remediation.md)

- [Protégez vos données nécessitant azure.microsoft.com/ Intune](https://docs.azure.microsoft.com/.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-azure.microsoft.com/-intune)


### Protection des ressources en fonction d’un risque à la connexion

[Azure AD Identity Protection](active-directory-identityprotection.md)

### Informations supplémentaires

- [FAQ sur l’accès conditionnel](active-directory-conditional-faqs.md)
- [Référence technique](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0817_2016-->