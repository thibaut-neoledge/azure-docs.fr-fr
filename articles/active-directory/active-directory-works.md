<properties
	pageTitle="Fonctionnement d’Azure AD"
	description="Azure AD crée un paysage d'identité qui vous appartient dans le cloud. Il peut être connecté à votre système d'identité locale ou utilisé indépendamment."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="curtand"/>



# Fonctionnement d’Azure Active Directory


###Autres articles sur le sujet
[Présentation d’Azure AD](active-directory-whatis.md)<br> [Comment cela fonctionne-t-il ?](active-directory-works.md)<br> [Prise en main](active-directory-get-started.md)<br> [Étapes suivantes](active-directory-next-steps.md)<br> [En savoir plus](active-directory-learn-map.md)


Azure AD crée un paysage d'identité qui vous appartient dans le cloud. Il peut être connecté à votre système d'identité locale ou utilisé indépendamment.

Vous pouvez considérer un compte dans Azure AD comme votre permis de conduire pour le cloud, c’est-à-dire un ID unique vous permettant d’accéder aux services en ligne. Azure AD fonctionne ainsi de la même manière que votre propre bureau d'enregistrement privé du cloud pour ces permis de conduire. Il permet d’utiliser des identités n'importe où dans le cloud et améliore la mobilité des utilisateurs qui accèdent aux ressources locales.

> [AZURE.NOTE]Pour utiliser Azure Active Directory, vous avez besoin d'un compte Azure. Si vous ne possédez pas encore un compte Azure, vous pouvez [vous inscrire pour en obtenir un gratuitement](http://azure.microsoft.com/pricing/free-trial/).

## Comment Azure AD prend-il en charge Office 365, Microsoft Intune et d’autres services Azure ?
Le portail Azure, le Centre d’administration Office 365, le portail de compte Microsoft Intune et les applets de commande du module Azure AD PowerShell peuvent tous lire et écrire dans une instance partagée unique d’Azure AD associée à votre répertoire. Les portails (ou applets de commande) agissent ainsi comme une interface frontale qui extrait et/ou modifie vos données de répertoire. [En savoir plus sur la prise en charge des autres services](active-directory-administer.md#what-is-an-azure-ad-tenant)

## Comment Azure AD prend-il en charge des applications tierces ?
Azure AD simplifie l'authentification pour les développeurs en fournissant une identité en tant que service, ainsi que des bibliothèques open source pour différentes plateformes qui vous permettent de vous initier rapidement au codage. [En savoir plus sur les scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).


## Comment Azure AD étend-il mon répertoire local ?
Azure AD prend en charge plusieurs des protocoles d'authentification et d'autorisation les plus couramment utilisés. [En savoir plus sur les protocoles d'authentification d'Azure Active Directory](active-directory-authentication-scenarios.md).

## Comment Azure m'aide-t-il à gérer les identités ?
Vous désirez en savoir plus sur la gestion d'Azure AD ? Comment bénéficier d’un répertoire ? Comment supprimer un disque ? Comment gérer les données d’un répertoire ? En savoir plus sur l'administration d’un répertoire Azure AD. [En savoir plus sur l'administration d'Azure AD](active-directory-administer.md).

## Ressources supplémentaires

* [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
* [Identité Azure](fundamentals-identity.md)
 

<!---HONumber=July15_HO4-->