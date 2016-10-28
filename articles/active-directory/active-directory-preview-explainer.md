<properties
	pageTitle="Explication de la version préliminaire d’Azure Active Directory | Microsoft Azure"
	description="Une rubrique qui explique les différences entre Azure Active Directory dans le portail classique et la version préliminaire d’Azure Active Directory dans le portail Azure."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/12/2016"
	ms.author="curtand"/>


# Aperçu de l’expérience de gestion d’Azure Active Directory dans le portail Azure

L’expérience de gestion d’Azure Active Directory (Azure AD) est en version préliminaire dans le portail Azure. Vous pouvez l’essayer en vous connectant au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre répertoire. Ensuite, sélectionnez Azure Active Directory dans la liste des services, s’il est visible, ou sélectionnez **Plus de services** pour afficher la liste de tous les services. Vous n’avez pas besoin d’un abonnement Azure pour utiliser l’expérience de gestion d’Active Directory dans le portail Azure.


## Fonctionnalités de l’expérience de la version préliminaire

L’expérience de la version préliminaire vous permet de gérer les nombreuses ressources de répertoire telles que les utilisateurs, les groupes et les applications, ainsi que les paramètres de répertoire, le tout dans le portail Azure. Nous améliorons cette expérience pour inclure toutes les fonctionnalités qui existent dans l’expérience de gestion Azure AD du [portail classique Azure](https://manage.windowsazure.com). En attendant, vous devrez continuer à effectuer certaines tâches dans le portail classique.

## Gestion des mêmes locataires Azure AD

L’expérience de la version préliminaire lit et écrit dans le même locataire Azure Active Directory que le portail classique, et le centre d’administration Office 365. Les modifications apportées dans un de ces portails sont répercutées dans tous les autres.

## Utiliser la même logique d’autorisation

L’expérience de la version préliminaire utilise la même logique d’autorisation que les clients Active Directory existants. Les utilisateurs sont autorisés à apporter des modifications aux ressources de répertoire en fonction de leur rôle, par exemple administrateur global, administrateur de l’utilisateur ou administrateur de mot de passe. Avoir un rôle sur des ressources Azure ou un abonnement Azure n’autorise pas un utilisateur à gérer les ressources Active Directory. Pour plus d’informations sur les rôles de gestion Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

L’expérience de la version préliminaire est optimisée pour les administrateurs généraux. Si vous utilisez l’expérience préliminaire lors d’une connexion en tant qu’utilisateur qui n’est pas un administrateur global, l’expérience pourrait s’en trouver dégradée. Par exemple, vous pourriez être en mesure de sélectionner un bouton qui vous permet de commencer une tâche que vous ne pouvez pas effectuer dans le répertoire. Nous travaillons sur l’amélioration de cette expérience.
 
## Partagez votre opinion

Vous pouvez nous envoyer vos commentaires sur l’expérience de la version préliminaire dans la section portail d’administration du [forum de commentaires Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).

<!---HONumber=AcomDC_0914_2016-->