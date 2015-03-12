<properties 
	pageTitle="Qu'est-ce qu'Azure Active Directory ?" 
	description="Utilisez Azure Active Directory pour étendre vos identités locales existantes dans le cloud en vue d'une meilleure expérience administrateur et utilisateur final pendant que Microsoft assure la poursuite de l'exécution d'Active Directory dans le cloud avec une grande échelle, une haute disponibilité et la récupération d'urgence intégrée. Ou bien développez des applications Azure AD intégrées pour votre organisation ou d'autres organisations." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="terrylan" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="justinha"/>





<h1 id="whatisaad">Qu'est-ce qu'Azure Active Directory ?</h1>

Azure Active Directory est un service qui fournit des fonctionnalités de gestion de l'accès et de l'identité dans le cloud. Tout comme Active Directory est un service mis à disposition des clients via le système d'exploitation Windows Server pour la gestion locale des identités, Azure Active Directory (Azure AD) est un service disponible via Azure pour la gestion des identités dans le cloud.  [En savoir plus](http://msdn.microsoft.com/library/hh967611.aspx)

Comme il s'agit de l'annuaire cloud de votre organisation, vous décidez quels sont vos utilisateurs, quelles informations sont conservées dans le cloud, quels utilisateurs sont autorisés à utiliser et à gérer les informations et quels services ou applications sont autorisés à accéder à celles-ci. 

Lorsque vous utilisez Azure AD, il est de la responsabilité de Microsoft de garantir l'exécution d'Active Directory dans le cloud avec une récupération d'urgence à grande échelle, à haute disponibilité et intégrée, tout en respectant les exigences en termes de confidentialité et de sécurité des informations de votre organisation.

<h3>Intégration à un annuaire Active Directory local</h3>

Azure AD peut servir d'annuaire cloud autonome pour votre organisation, mais également intégrer un annuaire Active Directory local existant à Azure AD. Certaines fonctionnalités d'intégration incluent la synchronisation d'annuaires et l'authentification unique, qui prolongent davantage la portée de vos identités locales existantes dans le cloud pour une administration et une expérience utilisateur optimales. 
 [En savoir plus](http://msdn.microsoft.com/library/jj573653)

<h3>Intégration à vos applications</h3>

Les développeurs d'applications peuvent intégrer leurs applications à Azure AD pour fournir des fonctionnalités d'authentification unique pour leurs utilisateurs. Cela permet d'héberger les applications d'entreprise dans le cloud et d'authentifier facilement les utilisateurs à l'aide d'informations d'identification d'entreprise. Cela permet également aux fournisseurs Saas (software as a service) de faciliter l'authentification des utilisateurs dans les organisations Azure AD lors de l'authentification auprès de leurs services. Les développeurs peuvent également utiliser l'API Graph pour interroger les données d'annuaire dans le cadre de la gestion des entités, telles que les utilisateurs ou groupes. [En savoir plus](http://go.microsoft.com/fwlink/?LinkID=290817&clcid=0x409)

**Ressources supplémentaires**

* [Inscription à Azure en tant qu'organisation](/fr-fr/manage/services/identity/organizational-account/)
* [Identité Azure](/fr-fr/manage/windows/fundamentals/identity/)
* [Bibliothèque Azure AD sur MSDN](http://go.microsoft.com/fwlink/?LinkId=293425)

<!--HONumber=46--> 
