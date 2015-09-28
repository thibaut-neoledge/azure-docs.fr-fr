<properties
	pageTitle="Utiliser des applications prenant en charge les revendications dans le proxy d’application"
	description="Explique comment devenir opérationnel avec le proxy d’application Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# Utiliser des applications prenant en charge les revendications dans le proxy d’application


Les applications prenant en charge les revendications effectuent une redirection vers STS, qui à son tour demande les informations d’identification de l’utilisateur en échange d’un jeton avant de rediriger l’utilisateur vers l’application. Pour permettre au proxy d’application de travailler avec ces redirections, les étapes suivantes sont nécessaires pour permettre au proxy d’application de travailler avec des applications prenant en charge les revendications.

> [AZURE.IMPORTANT]Le Proxy d’application est une fonctionnalité qui n’est disponible que si vous effectuez une mise à niveau vers l’édition Premium ou Basic d’Azure Active Directory. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).


## Configuration requise

Avant d’effectuer cette procédure, assurez-vous que le service STS vers lequel l’application prenant en charge les revendications effectue la redirection est disponible en dehors de votre réseau local.

### Configuration du portail Azure

1. Publiez votre application en suivant les instructions décrites dans [Publier des applications avec le proxy d’application](active-directory-application-proxy-publish.md).
2. Dans la liste des applications, sélectionnez l’application prenant en charge les revendications, puis cliquez sur **Configurer**.
3. Si vous avez choisi **Direct** comme **méthode de préauthentification**, assurez-vous de sélectionner **HTTPS** comme schéma d’**URL externe**.
4. Si vous choisissez Azure Active Directory comme **méthode de préauthentification**, assurez-vous de sélectionner **Aucune** comme **Méthode d’authentification interne**.

### Configuration d’AD FS

1. Ouvrez **Gestion AD FS**.
2. Accédez à **Approbations de la partie de confiance** et cliquez avec le bouton droit sur l’application que vous publiez avec le proxy d’application, puis choisissez **Propriétés**.

![Approbations de partie de confiance, clic droit sur le nom de l’application – capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. Sous l’onglet **Points de terminaison**, sous **Type de point de terminaison**, sélectionnez **WS-Federation**.
4. Sous **URL approuvée**, entrez l’URL que vous avez entrée dans le proxy d’application sous **URL externe**, puis cliquez sur **OK**.

![Ajouter un point de terminaison, définition de la valeur de l’URL approuvée – capture d’écran](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)

<!---HONumber=Sept15_HO3-->