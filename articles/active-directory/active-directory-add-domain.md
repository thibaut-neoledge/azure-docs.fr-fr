<properties
	pageTitle="Utilisation de noms de domaine personnalisés pour simplifier l’expérience de connexion pour vos utilisateurs | Microsoft Azure"
	description="Explique comment ajouter votre propre nom de domaine à Azure Active Directory et d’autres informations connexes."
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# Utilisation de noms de domaine personnalisés pour simplifier l’expérience de connexion pour vos utilisateurs

Vous pouvez utiliser vos propres noms de domaine pour améliorer et simplifier l’expérience de connexion et d’autres expériences utilisateur dans Azure Active Directory. Par exemple, si votre organisation possède le nom de domaine « contoso.com », vos utilisateurs peuvent se connecter à l’aide de noms d’utilisateur auxquels ils sont habitués, tels que « joe@contoso.com ».

Dans Azure Active Directory, chaque répertoire est fourni avec un nom de domaine intégré se présentant sous la forme « contoso.onmicrosoft.com » qui vous permet de commencer à utiliser Azure ou d’autres services Microsoft. [En savoir plus sur les domaines intégrés](#built-in-domain-names-for-azure-active-directory).

## Utiliser votre nom de domaine personnalisé avec Azure AD

Si votre organisation possède un nom de domaine personnalisé auquel vos utilisateurs sont habitués, il est recommandé de l’utiliser avec Azure Active Directory. Pour utiliser un nom de domaine personnalisé avec Azure Active Directory, vous devez :

-   [Ajouter et vérifier votre nom de votre domaine personnalisé](active-directory-add-domain-add-verify-general.md)

-   [Affecter des utilisateurs au domaine personnalisé](active-directory-add-domain-add-users.md)

## Utiliser votre nom de domaine personnalisé avec Office 365 et d’autres services

À l’image des autres ressources de votre répertoire Azure AD, vous pouvez utiliser les noms de domaine personnalisés que vous avez ajoutés et vérifiés avec Office 365, Intune et d’autres applications faisant appel à Azure AD. Par exemple, l’utilisation d’un nom de domaine personnalisé avec Exchange Online permet aux utilisateurs d’envoyer et de recevoir du courrier électronique à des adresses de messagerie familières telles que joe@contoso.com. Afin d’activer l’utilisation de domaines personnalisés pour ces applications, vous devez ajouter des entrées DNS supplémentaires dans le bureau d’enregistrement DNS en suivant la procédure décrite dans la documentation de ces dernières.

-   [Utilisation de domaines personnalisés avec Office 365](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=fr-FR&rs=fr-FR&ad=US)

-   [Utilisation de domaines personnalisés avec Intune](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Gérer les noms de domaine dans Azure AD

Les noms de domaine ne nécessitent généralement aucune gestion ou administration continue dans Azure Active Directory.

-   [Afficher la liste des noms de domaine de votre répertoire Azure Active Directory](active-directory-add-domain-add-users.md)

-   [What to do if you change the DNS registrar for your custom domain name](active-directory-add-domain-change-registrar.md) (Que faire en cas de modification du bureau d’enregistrement DNS pour votre nom de domaine personnalisé ?)

## Supprimer un nom de domaine personnalisé

Vous pouvez supprimer un nom de domaine personnalisé de votre répertoire Azure AD si votre organisation n’utilise plus ce nom de domaine ou si vous souhaitez l’utiliser avec un autre répertoire Azure AD.

-   [Supprimer un nom de domaine personnalisé](#_Deleting_a_custom)

## Noms de domaine intégrés pour Azure Active Directory

Faites la différence entre les domaines intégrés dans Azure Active Directory (Azure AD) et les domaines personnalisés que vous ajoutez.

-   Intégré : domaine fourni avec votre répertoire Azure AD se présentant sous la forme contoso.onmicrosoft.com

-   Personnalisé : nom de domaine appartenant déjà à votre organisation, tel que contoso.com

## Utiliser PowerShell ou API Graph pour gérer les noms de domaine

La plupart des tâches de gestion des noms de domaine dans Azure Active Directory peuvent également être accomplies à l’aide de Microsoft PowerShell, ou encore par programmation à l’aide d’API Graph.

-   [Utilisation de PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Utilisation d’API Graph pour gérer les noms de domaine dans Azure AD (version préliminaire)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## Étapes suivantes

Si vous avez besoin de ressources supplémentaires pour comprendre l’utilisation des noms de domaine dans Azure Active Directory, consultez :

- [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)
- [Add and verify a custom domain name in Azure Active Directory](active-directory-add-domain-add-verify-general.md) (Ajouter et vérifier un nom de domaine personnalisé dans Azure Active Directory)
- [Assign users to a custom domain](active-directory-add-domain-add-users.md) (Affecter des utilisateurs à un domaine personnalisé)
- [Change the DNS registrar for your custom domain name](active-directory-add-domain-change-registrar.md) (Modifier le bureau d’enregistrement DNS pour votre nom de domaine personnalisé)
- [Delete a custom domain in Azure Active Directory](active-directory-add-domain-delete-domain.md) (Supprimer un nom de domaine personnalisé dans Azure Active Directory)

<!---HONumber=AcomDC_0211_2016-->