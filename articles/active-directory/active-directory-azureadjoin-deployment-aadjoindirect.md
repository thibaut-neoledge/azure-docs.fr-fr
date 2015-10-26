<properties 
	pageTitle="Scénarios d’utilisation et considérations relatives au déploiement pour Azure AD Join | Microsoft Azure" 
	description="Répertorie et décrit les différents scénarios de déploiement disponibles pour Azure AD Join." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="femila"/>

# Scénarios d’utilisation et considérations relatives au déploiement pour Azure AD Join 

## Scénarios d’utilisation pour Azure AD Join
Scénario 1 : entreprises dont l’activité a lieu en grande partie dans le cloud
--------------------------------------------------------
Azure AD Join possède de nombreux avantages si vous utilisez et gérez actuellement votre activité dans le cloud ou si vous envisagez bientôt de passer au cloud. Vous pouvez utiliser un compte que vous avez créé dans Azure AD pour vous connecter à Windows 10. À l’aide de l’[introduction de l’interface logicielle lors de la première utilisation ](active-directory-azureadjoin-user-frx.md) ou en rejoignant Azure AD via les [paramètres](active-directory-azureadjoin-user-upgrade.md), vos utilisateurs peuvent joindre leurs ordinateurs à Azure AD. Ils bénéficient ainsi d’un accès via l’authentification unique à leurs ressources cloud comme Office 365 soit dans le navigateur soit dans les applications Office.

Scenario 2 : établissements d’enseignement ---------------------------------------------------------------------------------- Les établissements d’enseignement ont généralement deux types d’utilisateur : les enseignants et les étudiants. Les enseignants sont des membres à plus long terme de l’organisation et la création de comptes locaux pour eux est souhaitée. À l’inverse, les étudiants sont des membres à plus court terme de l’organisation et peuvent être gérés dans Azure AD afin que la mise à l’échelle de l’annuaire puisse être effectuée dans le cloud plutôt que localement. Ces étudiants peuvent maintenant se connecter à Windows avec leur compte Azure AD et accéder aux ressources Office 365 dans les applications Office.

Scénario 3 : entreprises de vente au détail
---------------------------------------------------------------------------------------
Les entreprises de vente au détail font appel à des saisonniers et à des salariés à long terme. Les employés à plein temps qui restent plus longtemps au sein de l’entreprise sont créés en tant que comptes locaux et utilisent généralement des ordinateurs joints au domaine. À l’inverse, les saisonniers sont présents pour une période plus courte et sont donc gérés là où les licences utilisateur peuvent être plus facilement déplacées. La création de ces utilisateurs dans le cloud avec des licences Office 365 leur permet de bénéficier des avantages de la connexion à Windows et aux applications Office avec un compte Azure AD, tout en conservant la mobilité de leurs licences une fois qu’ils ont quitté l’établissement.

Scénario 4 : autres cas de figure
------------------------------------------------------------------------------------------
Outre les scénarios décrits ci-dessus, le fait que les utilisateurs joignent leurs appareils à Azure AD offre les avantages suivants : expérience de jonction au domaine simplifiée, gestion des appareils dans Azure AD, inscription GPM automatique et authentification unique auprès d’Azure AD et des ressources locales.


##Considérations relatives au déploiement pour Azure AD Join

Permettre aux utilisateurs de joindre un appareil appartenant à l’entreprise directement à Azure AD
-----------------------------------------------------------------------------------------

Les entreprises peuvent fournir des comptes gérés sur le cloud uniquement aux organisations et sociétés partenaires. Ces derniers bénéficient ensuite d’un accès et d’une authentification unique facilités aux applications et ressources de l’entreprise. Ce scénario s’applique aux utilisateurs qui utilisent leurs appareils principalement pour avoir accès à des ressources dans le cloud comme Office 365 ou les applications SaaS qui s’appuient sur Azure AD pour l’authentification.

### Composants requis
**Au niveau de l’entreprise (administrateur)**

*	Abonnement Azure avec Azure Active Directory  

**Au niveau de l’utilisateur**

*	Windows 10 (références SKU Professionnel et Entreprise)

### Tâches de l’administrateur
* [Configuration de l’inscription des appareils et de MFA](active-directory-azureadjoin-setup.md)

### Tâches de l’utilisateur
* [Configuration d’un nouvel appareil Windows 10 avec Azure AD lors de l’installation](active-directory-azureadjoin-user-frx.md)
* [Configuration d’un nouvel appareil Windows 10 avec Azure AD à partir des paramètres](active-directory-azureadjoin-user-upgrade.md)
* [Association d’un appareil Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)
  


## Activation de BYOD (Apportez votre propre appareil) dans votre organisation pour Windows 10
Vous pouvez configurer vos employés et utilisateurs afin qu’ils utilisent leurs appareils personnels Windows pour accéder aux applications et ressources de l’entreprise. Vos utilisateurs peuvent ajouter leurs comptes Azure AD (professionnels) à un appareil Windows personnel pour accéder aux ressources de travail de manière sécurisée et conforme.

### Composants requis
**Au niveau de l’entreprise (administrateur)**

*	Abonnement Azure AD

**Au niveau de l’utilisateur**

*	Windows 10 (références SKU Professionnel et Entreprise)


### Tâches de l’administrateur

* [Configuration de l’inscription des appareils et de MFA](active-directory-azureadjoin-setup.md)

### Tâches de l’utilisateur
* [Association d’un appareil Windows 10 personnel à votre organisation](active-directory-azureadjoin-personal-device.md)


## Informations supplémentaires
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-overview.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Oct15_HO3-->