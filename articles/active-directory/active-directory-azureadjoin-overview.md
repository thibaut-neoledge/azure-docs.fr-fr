<properties 
	pageTitle="Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join | Microsoft Azure" 
	description="Fournit une présentation détaillée de la manière dont les appareils Windows 10 peuvent utiliser Azure AD Join pour s'inscrire à Azure Active Directory." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join

## Qu’est-ce qu’Azure Active Directory Join ? 
Azure Active Directory (Azure AD) Join est la fonctionnalité qui inscrit un appareil appartenant à l'entreprise dans Azure Active Directory pour permettre une gestion centralisée. Cela signifie que les utilisateurs (employés, étudiants) peuvent maintenant être connectés au cloud de l’entreprise via Azure Active Directory. Cela permet des déploiements Windows simplifiés, l’accès aux applications et ressources de l’entreprise à partir de n'importe quel appareil Windows, appartenant à l’entreprise ou à l’utilisateur (BYOD).

Azure AD Join est destiné aux entreprises fonctionnant principalement dans le cloud (généralement les petites et moyennes entreprises qui n'ont pas d’infrastructure Active Directory locale). Cela étant dit, Azure AD Join peut également être utilisé par les grandes entreprises sur des appareils qui sont incapables de joindre un domaine de manière classique (comme les appareils mobiles, par exemple) ou par les utilisateurs qui doivent avoir accès à Office 365 ou à d’autres applications SaaS Azure AD.

Alors que la jonction au domaine classique vous offre toujours une expérience locale optimale sur les appareils capables de se joindre à un domaine, Azure AD Join est adapté aux appareils qui ne peuvent pas se joindre à un domaine et aux situations dans lesquelles vous souhaitez gérer les utilisateurs dans le cloud avec des fonctionnalités GPM plutôt que de faire appel aux fonctionnalités de gestion de domaine classiques avec une stratégie de groupe et SCCM.

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## Pourquoi les entreprises doivent-elles adopter Azure AD Join ? 

* **Si l’activité de votre entreprise a lieu en grande partie dans le cloud** : si vous migrez ou prévoyez de migrer vers un modèle où vous réduisez votre empreinte locale et souhaitez passer par le cloud, Azure AD Join peut constituer un avantage certain. Vous avez peut-être créé des comptes Azure AD manuellement ou via la synchronisation de votre annuaire Active Directory local. Dans ce cas, vous avez un compte dans Azure AD et vous pouvez l’utiliser pour vous connecter à Windows 10. Vos utilisateurs peuvent joindre leurs ordinateurs à Azure AD via le processus OOBE (introduction de l'interface logicielle lors de la première utilisation ) ou via les paramètres. Ils bénéficient ainsi d’un accès via l’authentification unique à leurs ressources cloud comme Office 365 soit dans le navigateur soit dans les applications Office. 
* **Établissements d'enseignement** : l'un des scénarios principaux connu est celui des établissements d'enseignement qui ont deux types d'utilisateur : les enseignants et les étudiants. Les enseignants sont des membres à plus long terme de l’organisation et la création de comptes locaux pour eux est souhaitée. À l’inverse, les étudiants sont des membres à plus court terme de l'organisation et peuvent être gérés dans Azure AD afin que la mise à l'échelle de l’annuaire puisse être effectuée dans le cloud plutôt que localement. Ces étudiants peuvent maintenant se connecter à Windows avec leur compte Azure AD et accéder aux ressources Office 365 dans les applications Office. 
* **Entreprises de vente au détail** : les clients nous ont fait part de leur souhait de pouvoir gérer plus facilement les travailleurs saisonniers. Comme indiqué plus haut, les employés à plein temps qui restent plus longtemps au sein de l’entreprise sont créés en tant que comptes locaux et utilisent généralement des ordinateurs joints au domaine. À l’inverse, les saisonniers sont présents pour une période plus courte et sont donc gérés là où les licences utilisateur peuvent être plus facilement déplacées. La création de ces utilisateurs dans le cloud avec des licences Office 365 leur permet de bénéficier des avantages de la connexion à Windows et aux applications Office avec un compte Azure AD, tout en conservant la mobilité de leurs licences une fois qu’ils ont quitté l’établissement. 
* **Autres entreprises** : d’un point de vue plus large, vous pouvez remarquer que, même si vous tenez à jour les utilisateurs dans votre annuaire AD local, il peut être avantageux pour les utilisateurs de rejoindre Azure AD pour les raisons suivantes : jonction simplifiée, gestion des appareils dans Azure AD, inscription GPM automatique et authentification unique à Azure AD et aux ressources locales.  

## Fonctionnalités d’Azure AD Join
Azure AD Join a les caractéristiques suivantes :

* **Approvisionnement autonome des appareils appartenant à l'entreprise** : avec Windows 10, les utilisateurs finaux peuvent configurer un nouvel appareil via la fonctionnalité OOBE (introduction de l'interface logicielle lors de la première utilisation ) sans faire appel au service informatique.


* **Prise en charge des facteurs de forme modernes** : Azure AD Join fonctionne sur les appareils qui n'ont pas les fonctionnalités classiques de jonction au domaine.


* **Utilisation des comptes professionnels existants** : les utilisateurs finaux n'ont plus besoin de créer et de gérer un compte personnel Microsoft pour bénéficier d’une expérience optimale sur les appareils fournis par l’entreprise, comme c’était le cas sous Windows 8. Ils peuvent utiliser leur compte professionnel existant dans Azure AD. Pour de nombreuses organisations, cela signifie essentiellement qu’elles peuvent configurer Windows et s’y connecter avec les informations d'identification utilisées pour accéder à Office 365.


* **Inscription GPM automatique** : les appareils peuvent être inscrits automatiquement dans la gestion lors de leur connexion à Azure AD. Cela fonctionne avec Microsoft Intune et des applications GPM tierces. Dans le cas d’une gestion avec Intune, le service informatique est en mesure de contrôler et de gérer aussi bien les appareils joints à Azure AD que ceux joints à un domaine dans la console de gestion SCCM.


* **Authentification unique aux ressources de l'entreprise** : les utilisateurs apprécient de pouvoir se connecter via l’authentification unique depuis leur bureau Windows aux applications et ressources dans le cloud, comme Office 365 et des milliers d'applications métier qui s'appuient sur Azure AD pour l'authentification via [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Les appareils appartenant à l’entreprise qui sont joints à Azure AD bénéficient également de l'authentification unique aux ressources locales lorsque l’appareil est connecté au réseau de l’entreprise et depuis n’importe quel lieu lorsque ces ressources sont exposées le [Proxy d'application Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Itinérance de l’état du système d'exploitation** : les paramètres d'accessibilité, les sites web et les mots de passe Wi-Fi sont synchronisés entre les appareils appartenant à l'entreprise sans nécessiter de compte Microsoft personnel.


* **Windows Store pour les entreprises** : Windows Store prendra en charge l’acquisition d’applications et la gestion des licences associées avec des comptes Azure AD. Les organisations seront en mesure d’obtenir des licences en volume pour les applications et de les rendre disponibles à leurs utilisateurs.

## Comment fonctionnent les différents appareils avec Azure AD Join ?

| Appareil d'entreprise (joint au domaine local) | Appareil d'entreprise (joint au cloud) | Appareil personnel |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Les utilisateurs se connectent à Windows avec les informations d'identification de leur compte professionnel (comme ils le font déjà). | Les utilisateurs peuvent se connecter à Windows avec les informations d'identification de leur compte professionnel gérées dans Azure AD. Cela est pertinent pour les appareils d'entreprise dans trois cas de figure : l'organisation n'a pas d’annuaire AD local (par exemple, une PME), l'organisation ne crée pas tous les comptes d'utilisateurs dans AD (par exemple, étudiants, consultants, travailleurs saisonniers), les appareils ne peuvent pas être joints à un domaine (local), comme les téléphones ou tablettes exécutant une version mobile. Par exemple, appareil secondaire emporté à l’usine ou dans l’atelier. Fonctionne pour les organisations gérées et fédérées. | Les utilisateurs se connectent à Windows avec les informations d'identification MSA personnelles (comme ils le font déjà). |
| Les utilisateurs ont accès à l'itinérance des paramètres et au Windows Store. Ces services fonctionnent avec les comptes professionnels (MSA personnel non requis). Les organisations doivent connecter leur annuaire AD local à Azure AD. | Configuration libre-service : les utilisateurs peuvent accéder à l'expérience OOBE via leur compte professionnel plutôt que de faire appel au service informatique pour l’approvisionnement de leurs appareils (les deux méthodes sont prises en charge). | Ajout facilité d’un compte professionnel géré dans AD ou Azure AD. |
| Authentification unique du Bureau aux sites web/applications/ressources d’entreprise, à la fois localement et dans le cloud. Applications qui utilisent Azure AD pour l'authentification. | Inscription automatique dans l'annuaire d'entreprise (Azure AD) et inscription automatique dans GPM. (fonctionnalité Azure AD Premium) | Fournit l'authentification unique sur les appareils et aux applications/sites web/ressources avec ce compte professionnel. |
| Les utilisateurs peuvent ajouter leur MSA personnel pour accéder à leurs images/fichiers personnels sans incidence sur les données d'entreprise (les paramètres d'itinérance continuent de fonctionner avec un compte professionnel). Le compte MSA permet l'authentification unique et ne fait plus appel à l’itinérance des paramètres. | Réinitialisation de mot de passe libre-service sur winlogon (possibilité de réinitialiser un mot de passe oublié) (Azure AD Premium est requis). | Fournit l'accès au Store de l’entreprise afin que les utilisateurs puissent acquérir et utiliser des applications métier sur leurs appareils personnels. | |


## Informations supplémentaires
* [Windows 10 pour l’entreprise : plusieurs manières d’utiliser des appareils professionnels](active-directory-azureadjoin-windows10-devices-overview.md)
* [Extension des fonctionnalités du cloud aux appareils Windows 10 via Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Authentification des identités sans mot de passe avec Microsoft Passport](active-directory-azureadjoin-passport.md)
* [En savoir plus sur les scénarios d’utilisation pour Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connecter des appareils joints au domaine à Azure AD pour des expériences Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuration d’Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->