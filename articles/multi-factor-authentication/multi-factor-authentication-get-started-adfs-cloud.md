<properties 
	pageTitle="Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS" 
	description="Voici la page d'authentification multifacteur Azure qui explique la prise en main de l'authentification multifacteur Azure et d’AD FS 2.0 dans le cloud." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Sécurisation des ressources de cloud avec le serveur Azure Multi-Factor Authentication et AD FS

Si votre organisation est fédérée avec Azure Active Directory et que vous disposez de ressources accessibles à Azure AD, vous pouvez utiliser l'authentification multifacteur d’Azure ou les services de fédération d’Active Directory pour sécuriser ces ressources. Utilisez les procédures ci-dessous pour sécuriser les ressources Azure Active Directory avec l’authentification multifacteur d’Azure ou les services de fédération d’Active Directory

## Pour sécuriser les ressources Azure AD à l'aide d'AD FS, procédez comme suit : 



1. Utilisez la procédure décrite dans [Activation de l'authentification multifacteur](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) pour permettre aux utilisateurs d’activer un compte.
2. Utilisez la procédure suivante pour configurer une règle de revendication :

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	Démarrez la console de gestion AD FS.
- 	Accédez aux Parties de confiance et avec le bouton droit de la souris cliquez sur Partie de confiance. Sélectionnez Modifier les règles de revendication...
- 	Cliquez sur Ajouter une règle...
- 	Dans la liste déroulante, sélectionnez Envoyer les revendications à l’aide d’une règle personnalisée, puis cliquez sur Suivant.
- 	Donnez un nom à cette règle.
- 	Sous règle personnalisée : ajoutez le code suivant :


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Revendication correspondante :

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Cliquez sur OK. Cliquez sur Finish. Fermez la console de gestion AD FS.

Les utilisateurs peuvent ensuite procéder à la connexion à l'aide de la méthode locale (par exemple, carte à puce).


 

<!---HONumber=July15_HO2-->