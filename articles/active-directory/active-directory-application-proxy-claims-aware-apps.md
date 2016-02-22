<properties
	pageTitle="Utiliser des applications prenant en charge les revendications dans le proxy d’application"
	description="Explique comment devenir opérationnel avec le proxy d’application Azure AD."
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



# Utiliser des applications prenant en charge les revendications dans le proxy d’application

> [AZURE.IMPORTANT] Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

Les applications prenant en charge les revendications effectuent une redirection vers le service d’émission de jeton de sécurité (STS), qui à son tour demande les informations d’identification de l’utilisateur en échange d’un jeton avant de rediriger l’utilisateur vers l’application. Pour permettre au proxy d’application de travailler avec ces redirections, les étapes suivantes sont nécessaires.

## Configuration requise

Avant d’effectuer cette procédure, vérifiez que le service STS vers lequel l’application prenant en charge les revendications effectue la redirection est disponible en dehors de votre réseau local.


### Configuration du portail Azure Classic

1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md).
2. Dans la liste des applications, sélectionnez l’application prenant en charge les revendications, puis cliquez sur **Configurer**.
3. Si vous avez choisi **Direct** comme **méthode de préauthentification**, assurez-vous de sélectionner **HTTPS** comme schéma d’**URL externe**.
4. Si vous choisissez **Azure Active Directory** comme **méthode de préauthentification**, sélectionnez **Aucune** comme **méthode d’authentification interne**.


### Configuration d’AD FS

1. Ouvrez Gestion AD FS.
2. Accédez à **Approbations de la partie de confiance**, cliquez avec le bouton droit sur l’application que vous publiez avec le proxy d’application, puis choisissez **Propriétés**. ![Approbations de partie de confiance, clic droit sur le nom de l’application – capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Sous l’onglet **Points de terminaison**, sous **Type de point de terminaison**, sélectionnez **WS-Federation**.
4. Sous **URL approuvée**, entrez l’URL que vous avez entrée dans le proxy d’application sous **URL externe**, puis cliquez sur **OK**. ![Ajouter un point de terminaison, définition de la valeur de l’URL approuvée – capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## Voir aussi
Vous pouvez faire bien d’autres choses encore avec le Proxy d’application :

- [Publiez des applications avec le proxy d’application](active-directory-application-proxy-publish.md)
- [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

## En savoir plus sur le Proxy d’application
- [Consultez notre aide en ligne](active-directory-application-proxy-enable.md)
- [Consultez le blog sur le Proxy d’application](http://blogs.technet.com/b/applicationproxyblog/)
- [Regardez nos vidéos sur Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Ressources supplémentaires

- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
- [Offrir un accès distant sécurisé à des applications locales](active-directory-application-proxy-get-started.md)
- [Activation d'applications clientes natives de manière à ce qu'elles interagissent avec des applications proxy](active-directory-application-proxy-native-client.md)
- [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
- [Identité Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->