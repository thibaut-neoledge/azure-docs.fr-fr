<properties 
	pageTitle="Terminologie Azure AD" 
	description="Termes et définitions relatifs à Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Terminologie Azure AD

Microsoft Azure Active Directory (Azure AD) dispose d’un ensemble unique de termes liés aux scénarios hybrides et locaux du cloud. Le tableau suivant définit ces termes pour vous permettre de comprendre leur utilisation.

 Terme | Définition
------------- | -------------
Vérification de sécurité supplémentaire | Un paramètre de sécurité qu’un administrateur général peut définir sur un compte d'utilisateur dans le répertoire d'une organisation pour que l’utilisateur fournisse un mot de passe et une réponse depuis son téléphone afin de vérifier cette identité pour le système d'authentification Azure Active Directory.
Azure Active Directory | Le service d'identité d’Azure qui fournit des fonctionnalités de contrôle d’accès et de gestion d’identités via une API basée sur REST.
Contrôle d’accès d’Azure Active Directory | Le service Azure qui fournit une authentification fédérée ainsi qu’une autorisation assujettie à des règles, basée sur les revendications pour les services Web REST.
Systèmes d'authentification Azure Active Directory | Service d'identité de Microsoft utilisé dans le cloud pour authentifier et autoriser les comptes professionnels ou scolaires.
Azure Active Directory Graph | Une fonctionnalité d'Azure Active Directory qui accède aux objets utilisateur, groupe et rôle dans un graphique d'entreprise sociale pour fournir des informations relatives à l’utilisateur et aux relations.
Module Azure Active Directory pour Windows PowerShell | Groupe d’applets de commande utilisés pour administrer Azure Active Directory. Vous pouvez utiliser ces applets de commande pour gérer des utilisateurs, des groupes, des domaines, des abonnements au service cloud, des licences, la synchronisation de répertoires, l’authentification unique, etc.
Azure Active Directory Connect | L'assistant Azure Active Directory Connect constitue un outil et une expérience guidée uniques permettant de connecter vos annuaires locaux à Azure Active Directory. L'assistant déploie et configure tous les composants requis pour démarrer l'intégration et le bon fonctionnement de votre annuaire. Cela inclut les services de synchronisation, la synchronisation des mots de passe ou les services AD FS (Active Directory Federation Services), ainsi que les conditions préalables tel que le module Azure AD PowerShell.
Outil de synchronisation Azure Active Directory | L'application qui fournit une synchronisation unidirectionnelle des objets répertoire d’un service local Active Directory de l’entreprise vers Azure Active Directory.
Intégration de répertoires | Une fonctionnalité d'Azure Active Directory que vous pouvez définir pour améliorer l'expérience d'administration associée à la gestion d’identités dans votre répertoire local et votre répertoire du cloud. Les scénarios d'intégration de répertoires incluent la synchronisation de répertoires et la synchronisation de répertoires avec authentification unique.
Synchronisation de répertoires | Utilisée pour synchroniser des objets répertoire locaux (utilisateurs, groupes, contacts) sur le cloud pour contribuer à réduire la surcharge administrative. La synchronisation de répertoires est également appelée synchronisation d’annuaires dans le portail Azure AD et le portail de gestion Azure. Une fois la synchronisation de répertoires configurée, les administrateurs peuvent configurer des objets répertoires dans une application Active Directory locale vers une instance Azure AD.
Assistant de connexion Microsoft Online Services | L'Assistant de connexion est une application installée sur un ordinateur client qui permet à un utilisateur de se connecter une fois à cet ordinateur et d’accéder à des services autant de fois que nécessaire pendant la session de connexion. Sans l'Assistant de connexion, les utilisateurs finaux doivent fournir un nom et un mot de passe chaque fois qu'ils essaient d'accéder à un service. L’Assistant de connexion ne doit pas être confondu avec l’authentification unique qui est une fonctionnalité d’intégration de répertoires Azure Active Directory pouvant être déployée pour tirer parti des informations d’identification d’entreprise locales existantes d’un utilisateur permettant d’accéder de façon transparente à des services cloud de Microsoft.
Authentification multi-facteur (également appelée authentification à deux facteurs ou 2FA) | L'authentification multi-facteur ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. Lorsque vous activez l'authentification multi-facteur pour un compte d'utilisateur dans Azure AD, cet utilisateur doit alors utiliser son téléphone, ainsi que ses informations d'identification par mot de passe standard, telles que la méthode de vérification de sécurité supplémentaire, chaque fois qu'il a besoin de se connecter et d’utiliser les services cloud de Microsoft auxquels votre organisation s’est abonnée.
Authentification unique. | Utilisée pour fournir aux utilisateurs une expérience d'authentification plus transparente lorsqu'ils accèdent aux services cloud de Microsoft en étant connectés au réseau de l'entreprise. Pour configurer l'authentification unique, les organisations doivent déployer un service local d'émission de jeton de sécurité. Une fois l'authentification unique configurée, les utilisateurs peuvent utiliser leurs informations d’identification d'entreprise Active Directory (nom d'utilisateur et mot de passe) pour accéder aux services du cloud ainsi qu’à leurs ressources locales.
ID d'utilisateur | Un ID d'utilisateur est un identificateur unique qu’un utilisateur saisit sur la page de connexion pour accéder aux services cloud Microsoft auxquels votre organisation s’est abonnée.
Comptes professionnels ou scolaires | Un compte d'utilisateur attribué par une organisation (professionnelle, scolaire ou à but non lucratif) à l'un de ses membres constitutifs (un employé, un étudiant ou un client) qui fournit l’accès de connexion à un ou plusieurs abonnements de services cloud Microsoft de l'organisation, tels que Office 365 ou Azure. Ces comptes sont stockés dans un répertoire Azure AD de l’organisation et sont généralement supprimés lorsque l'utilisateur quitte l'organisation. Les comptes professionnels ou scolaires diffèrent des comptes Microsoft puisqu’ils sont créés et gérés par les administrateurs de l'organisation, et non par l'utilisateur. 

## Étapes suivantes
- [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
- [Association des abonnements Azure avec Azure AD](active-directory-how-subscriptions-associated-directory.md)
- [Restrictions et limites du service Azure Active Directory](active-directory-service-limits-restrictions.md)

<!---HONumber=58--> 