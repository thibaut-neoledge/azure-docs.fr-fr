<properties
	pageTitle="Azure AD et applications : un guide pour les développeurs | Microsoft Azure"
	description="Destiné aux professionnels de l’informatique, cet article fournit des instructions pour l’intégration d’applications Azure à Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Azure AD et applications : un guide pour les développeurs

## Vue d’ensemble

Ce guide fournit une vue d’ensemble du développement d’applications métier pour Azure Active Directory et s’adresse particulièrement aux administrateurs généraux de systèmes Active Directory/Office 365.

La création d’applications intégrées à Azure AD permet aux utilisateurs de votre organisation de bénéficier de l’authentification unique avec Office 365. En disposant de l’application dans Azure AD, vous pouvez contrôler la stratégie d’authentification définie pour celle-ci. Pour en savoir plus sur l’accès conditionnel et la façon de protéger les applications avec l’authentification multifacteur, consultez le document suivant : [Configuration des règles d’accès](active-directory-conditional-access-azuread-connected-apps.md).

Votre application doit être inscrite pour utiliser Azure Active Directory. L’inscription de l’application permet aux développeurs de votre organisation d’authentifier les membres de cette dernière à l’aide de Microsoft Azure AD et de demander l’accès à leurs ressources utilisateur (courrier électronique, calendrier ou documents, par exemple).

Tout membre de votre annuaire (pas les invités) peut inscrire une application, procédé également appelé *création d’un objet d’application*.

En inscrivant une application, tout utilisateur peut effectuer les opérations suivantes :

- Obtenir pour son application une identité reconnue par Azure AD.
- Obtenir un ou plusieurs secrets/clés que l’application peut utiliser pour s’authentifier auprès d’Active Directory.
- Personnaliser l’application avec un nom, un logo, etc., dans le portail Azure.
- Tirer parti des fonctionnalités d’autorisation Azure AD pour son application.
  - Contrôle d’accès en fonction du rôle (RBAC) pour les applications
  - Azure Active Directory en tant que serveur d’autorisation oAuth (sécuriser une API exposée par l’application)

- Déclarer les autorisations requises nécessaires au bon fonctionnement de l’application. À savoir :
	  - Autorisations de l’application (administrateurs généraux uniquement). Par exemple :
	    - Appartenance au rôle dans une autre application Azure AD ou appartenance au rôle par rapport à une ressource, un groupe de ressources ou un abonnement Azure
	  - Autorisations déléguées (tout utilisateur). Par exemple :
	    - (Azure AD) Connexion et lecture de profil
	    - (Exchange) Lecture de courrier électronique, envoi de courrier électronique
	    - (SharePoint) Lecture

> [AZURE.NOTE]Par défaut, tout membre peut inscrire une application. Pour savoir comment limiter à des membres spécifiques les autorisations d’inscription d’applications, reportez-vous au document [Comment les applications sont ajoutées à Azure AD](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

Voici les opérations que vous devez effectuer en tant qu’administrateur général pour aider les développeurs à préparer leurs applications pour la production :

- Configurer des règles d’accès (stratégie d’accès/MFA)
- Configurer l’application pour qu’elle demande l’affectation de l’utilisateur et affecter des utilisateurs
- Supprimer l’expérience de consentement d’utilisateur par défaut

## Configuration des règles d’accès

Comme nous l’avons mentionné plus haut, consultez l’article suivant pour en savoir plus sur la configuration des règles d’accès pour n’importe quelle application.

[Configuration des règles d’accès](active-directory-conditional-access-azuread-connected-apps.md)

## Configurer l’application pour qu’elle demande l’affectation de l’utilisateur et affecter des utilisateurs

Par défaut, l’affectation d’utilisateurs n’est pas nécessaire pour que ces derniers accèdent à une application. Toutefois, si l’application expose des rôles ou que vous souhaitez qu’elle s’affiche sur le panneau d’accès d’un utilisateur, vous devez demander l’affectation de l’utilisateur et affecter des utilisateurs ou des groupes.

[Demande de l’affectation de l’utilisateur](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Si vous êtes abonné à Azure AD Premium ou Enterprise Mobility Suite (EMS), nous vous recommandons fortement de tirer parti des groupes. L’affectation de groupes à l’application vous permet de déléguer la gestion d’accès en continu au propriétaire du groupe. Vous pouvez créer un groupe ou demander à la personne responsable au sein de votre organisation de créer un groupe à l’aide de votre dispositif de gestion de groupe.

[Affectation d’utilisateurs à une application](active-directory-applications-guiding-developers-assigning-users.md) et [Affectation de groupes à une application](active-directory-applications-guiding-developers-assigning-groups.md)

## Suppression du consentement de l’utilisateur

Par défaut, l’utilisateur doit consentir à l’autorisation nécessaire pour la connexion. L’expérience de la demande d’un consentement à l’octroi d’autorisations à une application peut être déconcertante pour les utilisateurs qui ne sont pas familiarisés avec la prise de décision de ce type.

Pour les applications de confiance, vous pouvez donner votre consentement à l’application pour le compte de tous les utilisateurs de votre organisation.

Pour en savoir plus sur le consentement de l’utilisateur et sur l’expérience du consentement dans Azure, consultez [Intégration d’applications dans Azure Active Directory](active-directory-integrating-applications.md).

##Articles connexes

- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->