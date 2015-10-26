<properties
	pageTitle="Activation de l'accès hybride avec le proxy d'application | Microsoft Azure"
	description="Activez l'accès aux applications qui s'exécutent à l'intérieur de votre réseau privé à partir de votre réseau via Azure Active Directory."
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
	ms.date="10/06/2015"
	ms.author="femila"/>

#Activation de l'accès hybride avec le proxy d'application
Le proxy d’application Microsoft Azure AD vous permet de publier des applications qui s'exécutent à l'intérieur de votre réseau privé afin d’être accessibles de façon sécurisée, sur n'importe quel appareil, depuis l’extérieur de votre réseau. Le proxy d'application Azure AD permet de publier des applications à l'intérieur de votre réseau privé local ou votre réseau cloud. Il peut être configuré comme service prêt à l’emploi intégré à Azure AD, qui nécessite uniquement l’installation d’un connecteur de proxy d'application sur un serveur au sein de votre environnement.

##Fonctionnement

Les étapes suivantes expliquent comment les applications peuvent être publiées dans un environnement hybride.
 
1.	Les connecteurs sont déployés sur le réseau d'entreprise. (Plusieurs connecteurs peuvent être déployés pour la redondance et la mise à l’échelle.)
2.	Le connecteur se connecte automatiquement au service cloud.
3.	L’utilisateur se connecte au service cloud qui achemine le trafic aux ressources via les connecteurs.

Le diagramme suivant fournit plus de détails sur le fonctionnement du proxy d'application :

 ![Fonctionnement du proxy d’application AzureAD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

 
1.	L’utilisateur accède à l'application via le proxy d'application et sera dirigé vers la page de connexion Azure AD pour s’identifier.
2.	Une fois la session ouverte, un jeton est généré et envoyé à l'utilisateur.
3.	L'utilisateur envoie maintenant le jeton au proxy d'application qui récupérera le nom d'utilisateur principal (UPN) et le nom de sécurité principal (SPN) du jeton et dirigera la requête vers le connecteur.
4.	Le connecteur emprunte l'identité de l'utilisateur pour demander un ticket Kerberos qui peut être utilisé pour l'authentification (Windows) interne. (Délégation Kerberos contrainte)
5.	 Un ticket Kerberos est extrait d'Active Directory.
6.	Le ticket récupéré est envoyé au serveur d'applications où il est en cours de vérification.
7.	 La réponse sera envoyée à l’utilisateur via le proxy d'application. Il existe une seule condition pour la publication d'une application web : l'application web dans votre réseau privé doit être accessible par le serveur où le connecteur est installé. En d'autres termes, vous pouvez installer le connecteur sur les serveurs d'applications eux-mêmes ou sur tout autre serveur de votre environnement. Cela ne pose pas de problème tant le connecteur est capable d'accéder à l'application web.

##Intégration d'applications locales via le proxy d'application Azure AD
Le panneau d'accès d’Azure AD vous permet de publier vos applications locales sur Azure AD. Les étapes suivantes doivent être effectuées :

1. Activez le proxy d'application dans Azure AD, puis installez et inscrivez le connecteur. Pour obtenir des instructions détaillées, consultez [Activation du proxy d’application Azure AD](active-directory-application-proxy-enable/#step-1-enable-application-proxy-in-azure-ad.md).
2. Publication d’applications à l'aide du proxy d’application Azure AD - Pour obtenir des instructions détaillées, consultez [Publication d’applications via le proxy d'application Azure AD](active-directory-application-proxy-publish.md).

<!---HONumber=Oct15_HO3-->