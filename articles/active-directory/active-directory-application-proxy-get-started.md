<properties
	pageTitle="Offrir un accès à distance sécurisé aux applications locales"
	description="Explique comment utiliser le Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/07/2015"
	ms.author="rkarlin"/>

# Offrir un accès à distance sécurisé aux applications locales

Vous voulez offrir un accès à distance aux utilisateurs de toutes sortes d’appareils : gérés ou non gérés, tablettes, smartphones et ordinateurs portables. Offrir un accès sécurisé à une multitude de ressources peut être complexe. Ces dernières années, les proxys inverses ont été largement utilisés pour fournir un accès à distance sécurisé, mais ils doivent se trouver derrière des pare-feu difficiles à sécuriser et difficiles à rendre hautement disponibles.

## Accès à distance sécurisé dans le cloud
Dans notre environnement de cloud moderne, l’accès à distance franchit une étape avec le Proxy d’application dans Microsoft Azure Active Directory. Le Proxy d’application est une fonctionnalité d’Azure AD fournie en tant que service, donc simple à déployer et à utiliser. Il s’intègre avec Azure Active Directory, la plateforme d’identité utilisée par Office 365.

## Présentation du Proxy d’application Azure Active Directory
Le Proxy d’application offre une authentification unique et un accès à distance sécurisé pour les applications web hébergées en local, par exemple les sites SharePoint et Outlook Web Access. Vos applications web locales sont maintenant accessibles de la même manière que vos applications SaaS dans Azure Active Directory, sans devoir recourir à un VPN ou à une modification de l’infrastructure réseau. Le Proxy d’application vous permet de publier des applications. Les employés peuvent se connecter à vos applications depuis leur domicile, sur leurs propres appareils et s’authentifier via ce proxy cloud.

## Comment cela fonctionne-t-il ?
### Activation de l’accès
Le Proxy d’application fonctionne avec un service Windows Server léger appelé connecteur, à l’intérieur de votre réseau. Le connecteur n’a pas besoin que les ports entrants soient ouverts et vous n’avez rien à placer dans la zone DMZ. Si le volume de trafic vers vos applications est important, vous pouvez ajouter d’autres connecteurs. Le service se chargera de l’équilibrage de la charge. Les connecteurs sont sans état et extraient tout ce dont ils ont besoin sur le cloud. Lorsqu’un utilisateur accède aux applications à distance, à partir de n’importe quel appareil, il est authentifié par Azure Active Directory et obtient un accès à l’application.

### Authentification unique
Le Proxy d’application Azure AD fournit l’authentification unique (SSO) pour les applications qui utilisent l’authentification Windows intégrée ou les applications compatibles avec les revendications. Si votre application utilise l’authentification Windows intégrée, le Proxy d’application emprunte l’identité de l’utilisateur à l’aide de la délégation Kerberos contrainte pour fournir l’authentification unique. L’authentification unique pour les applications prenant en charge les revendications est réussie, car l’utilisateur a déjà été authentifié par Azure Active Directory.

## Pour commencer
Assurez-vous que vous disposez d’un abonnement Azure AD Basic ou Premium et un annuaire Azure AD sur lequel vous êtes administrateur global. Vous devez également disposer de licences d’Azure AD Premium ou Basic pour l’administrateur de l’annuaire et les utilisateurs qui accèdent aux applications. Découvrez plus d’informations ici.

### Accès à distance sécurisé aux applications locales
La configuration du Proxy d’application s’effectue en deux étapes :

1. [Activer le Proxy d’application et configurer le connecteur](active-directory-application-proxy-enable.md)<br>
2. [Publier des applications](active-directory-application-proxy-publish.md) : utilisez l’Assistant pour publier vos applications locales et les rendre accessibles à distance.

## Et ensuite ?
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :


- [Publier des applications avec votre propre nom de domaine](https://msdn.microsoft.com/library/azure/mt210927.aspx)
- [Activer l’authentification unique](https://msdn.microsoft.com/library/azure/dn879065.aspx)
- [Utiliser des applications utilisant les revendications](https://msdn.microsoft.com/library/azure/mt210926.aspx)
- [Activer l’accès conditionnel](https://msdn.microsoft.com/library/azure/dn931796.aspx)


### En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](https://msdn.microsoft.com/library/azure/dn768219.aspx)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires
* [Inscription à Azure en tant qu’organisation](../sign-up-organization.md)
* [Identité Azure](../fundamentals-identity.md)

<!---HONumber=July15_HO4-->