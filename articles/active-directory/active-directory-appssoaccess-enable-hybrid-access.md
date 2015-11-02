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
	ms.date="10/16/2015"
	ms.author="femila"/>

# Activation de l'accès hybride avec le proxy d'application
Le proxy d’application Microsoft Azure Active Directory (AD) vous permet d’accéder de façon sécurisée aux applications situées à l’intérieur de votre réseau privé, en tout lieu et sur n’importe quel appareil. Après avoir installé un connecteur de proxy d'application dans votre environnement, il peut être facilement configuré avec Azure AD.

Il existe une seule condition pour permettre l’accès à une application web : l'application web doit être accessible au serveur sur lequel le connecteur est installé. En d'autres termes, vous pouvez installer le connecteur sur les serveurs d'applications eux-mêmes ou sur tout autre serveur de votre environnement à condition que le connecteur puisse accéder à l’application web.

##Fonctionnement
### Rapide présentation
1. Les connecteurs sont déployés sur le réseau local. (Plusieurs connecteurs peuvent être déployés pour la redondance et la mise à l’échelle.)
2. Le connecteur se connecte au service cloud.
3. Le connecteur et le service cloud acheminent le trafic utilisateur vers les applications.

 ![Diagramme du proxy d’application AzureAD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### Examen approfondi
1. L’utilisateur accède à l'application via le proxy d'application et sera dirigé vers la page de connexion Azure AD pour s’identifier.
2. Une fois la session ouverte, un jeton est généré et envoyé à l'utilisateur.
3. L'utilisateur envoie le jeton au proxy d'application qui récupère le nom d'utilisateur principal (UPN) et le nom de sécurité principal (SPN) du jeton puis dirige la requête vers le connecteur.
4. Le connecteur emprunte l'identité de l'utilisateur pour demander un ticket Kerberos qui peut être utilisé pour l'authentification (Windows) interne. Ce processus est appelé délégation Kerberos contrainte
5. Un ticket Kerberos est extrait d'Active Directory.
6. Le ticket est envoyé au serveur d’applications puis vérifié.
7. La réponse est envoyée à l’utilisateur via le proxy d'application.

## Articles connexes
- [Activation du proxy d’application Azure AD](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [Publication d’applications via le proxy d'application Azure AD](active-directory-application-proxy-publish.md)

<!---HONumber=Oct15_HO4-->