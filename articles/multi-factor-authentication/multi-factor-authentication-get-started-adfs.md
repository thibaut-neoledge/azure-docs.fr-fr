<properties 
	pageTitle="Prise en main d’Azure Multi-Factor Authentication et des services de fédération Active Directory (AD FS)" 
	description="Ceci est la page d'Azure Multi-Factor Authentication qui explique la prise en main d’Azure MFA et des services AD FS." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>

# Prise en main d’Azure Multi-Factor Authentication et des services de fédération Active Directory (AD FS)



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si votre organisation a fédéré votre Active Directory local avec Azure Active Directory à l'aide d'AD FS, vous disposez des deux options suivantes pour l'utilisation d’Azure Multi-Factor Authentication.

- Sécuriser les ressources de cloud à l’aide de l’authentification multifacteur Azure ou des services de fédération Active Directory (AD FS) 
- Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication 

Le tableau suivant résume le processus d'authentification avec la sécurisation des ressources à l’aide d’Azure Multi-Factor Authentication et AD FS

|Processus d'authentification : applications web|Processus d'authentification : applications hors navigateur
:------------- | :------------- | :------------- |
Sécurisation des ressources Azure AD à l'aide d’Azure Multi-Factor Authentication |<li>Le premier facteur d'authentification est effectué à l'aide d'AD FS local.</li> <li>Le second facteur est une méthode par téléphone effectuée à l'aide de l'authentification cloud.</li>|Les utilisateurs peuvent utiliser des mots de passe d'application pour se connecter.
Sécurisation des ressources Azure AD à l'aide des services ADFS |<li>Le premier facteur d'authentification est effectué localement à l'aide d'AD FS.</li><li>Le second facteur est effectué localement en répondant à la demande.</li>|Les utilisateurs peuvent utiliser des mots de passe d'application pour se connecter.

Mises en garde relatives aux mots de passe d'application pour les utilisateurs fédérés :

- Le mot de passe d’application est vérifié à l'aide de l'authentification cloud et contourne donc les fédérations. La fédération n'est utilisée activement que lorsque vous configurez le mot de passe d’application.
- Les paramètres de contrôle d'accès client locaux ne sont pas honorés par Mot de passe d’application
- Vous perdez la fonctionnalité de journalisation d'authentification locale pour le mot de passe d’application.
- La désactivation/suppression de compte peut mettre jusqu'à 3 heures pour synchroniser les annuaires, ce qui peut retarder la désactivation/suppression du mot de passe dans l’identité de cloud.

Pour plus d'informations sur la configuration d’Azure Multi-Factor Authentication ou du serveur Azure Multi-Factor Authentication avec AD FS, consultez les rubriques suivantes :

- [Sécuriser les ressources cloud à l’aide d’Azure Multi-Factor Authentication et AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Sécuriser les ressources de cloud et locales à l'aide du serveur Azure Multi-Factor Authentication avec AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)







 

<!---HONumber=AcomDC_0224_2016-->