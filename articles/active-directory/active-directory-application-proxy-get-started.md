<properties
	pageTitle="Offrir un accès à distance sécurisé aux applications locales"
	description="Explique comment utiliser le Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales."
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
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# Offrir un accès à distance sécurisé aux applications locales

> [AZURE.NOTE] Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Vous voulez fournir un accès à distance aux utilisateurs de toutes sortes d’appareils : gérés ou non gérés, tablettes, smartphones et ordinateurs portables. Or, fournir un accès sécurisé à une multitude de ressources peut être complexe. Ces dernières années, les proxys inverses ont été largement utilisés pour fournir un accès à distance sécurisé, mais ils doivent se trouver derrière des pare-feu difficiles à sécuriser et difficiles à rendre hautement disponibles.

## Accès à distance sécurisé dans le cloud
Dans notre environnement de cloud moderne, l’accès à distance franchit une étape avec le Proxy d’application dans Azure Active Directory (AD). Le Proxy d’application est une fonctionnalité d’Azure AD fournie en tant que service, donc simple à déployer et à utiliser. Il s’intègre à Azure AD, la plateforme d’identité utilisée par Office 365.

## Présentation du Proxy d’application Azure Active Directory
Le Proxy d’application offre une authentification unique (SSO) et un accès à distance sécurisé pour les applications web hébergées en local, par exemple les sites SharePoint et Outlook Web Access. Vos applications web locales sont maintenant accessibles de la même manière que vos applications SaaS dans Azure Active Directory, sans devoir recourir à un VPN ou à une modification de l’infrastructure réseau. Le Proxy d’application vous permet de publier des applications. Les employés peuvent alors s’y connecter de leur domicile, sur leurs propres appareils et s’authentifier via ce proxy cloud.

## Comment cela fonctionne-t-il ?

Le Proxy d’application fonctionne selon trois étapes de base. Tout d’abord, les connecteurs sont déployés sur le réseau local. Le connecteur se connecte ensuite au service cloud. Enfin, le connecteur et le service cloud acheminent le trafic utilisateur vers les applications.

 ![Diagramme du proxy d’application AzureAD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. L’utilisateur accède à l'application via le proxy d'application et sera dirigé vers la page de connexion Azure AD pour s’identifier.
2. Une fois la session ouverte, un jeton est généré et envoyé à l'utilisateur.
3. L'utilisateur envoie le jeton au proxy d'application qui récupère le nom d'utilisateur principal (UPN) et le nom de sécurité principal (SPN) du jeton puis dirige la requête vers le connecteur.
4. Le connecteur emprunte l'identité de l'utilisateur pour demander un ticket Kerberos qui peut être utilisé pour l'authentification (Windows) interne. Ce processus est appelé délégation Kerberos contrainte
5. Un ticket Kerberos est extrait d'Active Directory.
6. Le ticket est envoyé au serveur d’applications puis vérifié.
7. La réponse est envoyée à l’utilisateur via le proxy d'application.

### Activation de l’accès
Le Proxy d’application fonctionne avec un service Windows Server léger appelé connecteur, à l’intérieur de votre réseau. Le connecteur n’a pas besoin que les ports entrants soient ouverts et vous n’avez rien à placer dans la zone DMZ. Si le volume de trafic vers vos applications est important, vous pouvez ajouter d’autres connecteurs. Le service se chargera de l’équilibrage de la charge. Les connecteurs sont sans état et extraient tout ce dont ils ont besoin sur le cloud.

Lorsqu’un utilisateur accède aux applications à distance, à partir de n’importe quel appareil, il est authentifié par Azure Active Directory et obtient un accès à l’application.

### Authentification unique
Le Proxy d’application Azure AD fournit l’authentification unique aux applications qui utilisent l’authentification Windows intégrée (IWA) ou applications prenant en charge les revendications. Si votre application utilise l’authentification IWA, le Proxy d’application emprunte l’identité de l’utilisateur à l’aide de la délégation Kerberos contrainte pour fournir l’authentification unique (SSO). Si vous avez une application prenant en charge les revendications qui fait confiance à Azure Active Directory, l’authentification unique est réussie, car l’utilisateur a déjà été authentifié par Azure AD.

## Pour commencer
Assurez-vous que vous disposez d’un abonnement Azure AD Basic ou Premium et un annuaire Azure AD sur lequel vous êtes administrateur global. Vous devez également disposer de licences d’Azure AD Premium ou Basic pour l’administrateur de l’annuaire et les utilisateurs qui accèdent aux applications. Pour plus d’informations, consultez [Éditions d’Azure Active Directory](active-directory-editions.md).

### Accès à distance sécurisé aux applications locales
La configuration du Proxy d’application s’effectue en deux étapes :

1. [Activer le Proxy d’application et configurer le connecteur](active-directory-application-proxy-enable.md)  
2. [Publier des applications](active-directory-application-proxy-publish.md) : utilisez l’Assistant simple et rapide pour publier vos applications locales et les rendre accessibles à distance.

## Et ensuite ?
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :

- [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Utiliser des applications utilisant les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)


### En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires
- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
- [Identité Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0518_2016-->