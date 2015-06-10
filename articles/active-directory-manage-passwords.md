<properties 
	pageTitle="Gestion des mots de passe dans Azure AD" 
	description="Une rubrique qui explique comment gérer les mots de passe dans Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="Justinha"/>

# Gestion des mots de passe dans Azure AD

Si vous êtes un administrateur, vous pouvez réinitialiser le mot de passe d’un utilisateur dans Azure dans le Portail Azure Classic. Cliquez sur le nom de votre répertoire et sur la page Utilisateurs, cliquez sur le nom de l’utilisateur et, en bas du portail, cliquez sur **Réinitialiser le mot de passe**.

Le reste de cette rubrique couvre l'ensemble complet des fonctionnalités de gestion de mot de passe pris en charge par Azure Active Directory, notamment :

- La modification du **mot de passe libre-service** permet aux utilisateurs finaux ou aux administrateurs de modifier leurs mots de passe expirés ou non expirés sans appeler un administrateur ou le support technique pour obtenir un support.
- La réinitialisation du **mot de passe libre-service** permet aux utilisateurs finaux ou aux administrateurs de modifier leurs mots de passe expirés ou non expirés sans appeler un administrateur ou le support technique pour obtenir un support. La réinitialisation du mot de passe libre-service nécessite Azure AD Premium ou de base. Pour plus d’informations, consultez les[ éditions d’Azure Active Directory](active-directory-editions.md).
- La **réinitialisation du mot de passe initiée par l’administrateur** permet à un administrateur de réinitialiser le mot de passe d’un utilisateur final ou d'un autre administrateur depuis le Portail de gestion Azure.
- Les **rapports d'activité de gestion de mot de passe** donnent aux administrateurs un aperçu de l’activité de réinitialisation et d’enregistrement des mots de passe dans leur organisation. 
- **L'écriture différée de mot de passe** permet la gestion des mots de passe locaux depuis le cloud afin que tous les scénarios mentionnés ci-dessus puissent être exécutés par ou pour des utilisateurs fédérés ou avec un mot de passe synchronisé. L'écriture différée de mot de passe nécessite Azure AD Premium. Pour plus d’informations, consultez [Prise en main d'Azure Active Directory Premium](active-directory-get-started-premium.md).

> [AZURE.NOTE]Azure AD Premium est disponible pour les clients chinois à l'aide de l'instance mondiale d'Azure AD. Actuellement, Azure AD Premium n’est pas pris en charge dans le service Microsoft Azure fonctionnant avec 21Vianet en Chine. Pour plus d’informations, contactez-nous sur le [forum Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

Utilisez les liens suivants pour accéder à la documentation qui vous intéresse le plus :

- [Présentation : gestion de mot de passe dans Azure AD](https://msdn.microsoft.com/library/azure/dn683880.aspx)
- [Réinitialisation de mot de passe libre-service dans Azure AD : activation, configuration et test de la réinitialisation de mot de passe libre-service](https://msdn.microsoft.com/library/azure/dn683881.aspx)
- [Réinitialisation de mot de passe libre-service dans Azure AD : personnalisation de la réinitialisation de mot de passe pour répondre à vos besoins](https://msdn.microsoft.com/library/azure/dn688249.aspx)
- [Réinitialisation de mot de passe libre-service dans Azure AD : déploiement et gestion des meilleures pratiques](https://msdn.microsoft.com/library/azure/dn903643.aspx)
- [Rapports de gestion des mots de passe dans Azure AD : affichage de l'activité de gestion de mot de passe dans votre client](https://msdn.microsoft.com/library/azure/dn903641.aspx)
- [Écriture différée de mot de passe :configuration d’Azure AD pour gérer les mots de passe locaux](https://msdn.microsoft.com/library/azure/dn903642.aspx)
- [FAQ/résolution de problèmes pour la gestion des mots de passe Azure AD](https://msdn.microsoft.com/library/azure/dn683878.aspx)

## Étapes suivantes

- [Administration d’Azure AD](active-directory-administer.md)
- [Création ou modification des utilisateurs dans Azure AD](active-directory-create-users.md)
- [Gestion des groupes dans Azure AD](active-directory-manage-groups.md)

<!---HONumber=58-->