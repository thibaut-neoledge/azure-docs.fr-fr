<properties
	pageTitle="Supprimer un domaine personnalisé dans Azure Active Directory | Microsoft Azure"
	description="Suppression d’un domaine personnalisé dans Azure Active Directory."
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
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="curtand;jeffsta"/>


# Suppression d’un nom de domaine personnalisé

Vous pouvez supprimer un nom de domaine personnalisé qui n’est plus nécessaire à votre annuaire Azure AD ; par exemple, si vous utilisez un nouveau nom d'entreprise ou si vous utilisez un autre annuaire Azure AD, pour citer deux scénarios. Vous pouvez également supprimer un domaine non vérifié, par exemple si vous constatez, après l'avoir ajouté, que vous avez mal orthographié son nom, ou si vous n'avez pas défini correctement la valeur pour fédérer le domaine.

## Avant de commencer

Avant de supprimer un nom de domaine, nous vous recommandons de lire les informations suivantes :

- Le nom de domaine contoso.onmicrosoft.com d’origine qui a été fourni pour votre annuaire au moment de l’inscription ne peut pas être supprimé.
- Vous ne pouvez pas supprimer un domaine de niveau supérieur ayant des sous-domaines associés tant que les sous-domaines n’ont pas été supprimés. Par exemple, vous ne pouvez pas supprimer adatum.com si corp.adatum.com ou un autre sous-domaine utilise le nom de domaine de niveau supérieur. Pour plus d'informations, consultez l'article de support concernant l’erreur[« Le domaine a des sous-domaines associés » ou « Vous ne pouvez pas supprimer un domaine avec des sous-domaines » lorsque vous essayez de supprimer un domaine d'Office 365](https://support.microsoft.com/kb/2787792/).
- Avez-vous activé la synchronisation d’annuaires ? Si oui, un domaine a été automatiquement ajouté à votre compte et ressemble à ceci : contoso.mail.onmicrosoft.com. Ce nom de domaine ne peut pas être supprimé.
- Avant de pouvoir supprimer un nom de domaine, vous devez d’abord supprimer le nom de domaine de tous les utilisateurs ou les comptes de messagerie électronique associés à ce domaine. Vous pouvez supprimer tous les comptes ou réaliser des modifications en bloc sur les comptes d’utilisateurs pour changer leurs informations de nom de domaine et leurs adresses électroniques. Pour plus d’informations, consultez la rubrique [Création ou modification d’utilisateurs dans Azure AD](active-directory-create-users.md). Pensez à supprimer :

	-   Tout utilisateur qui possède le domaine dans son nom d’utilisateur ou son adresse électronique

	-   Un groupe à extension messagerie qui possède le domaine dans son adresse de messagerie

	-   Toute application qui possède le domaine dans son URL de réponse

- Si vous hébergez un site SharePoint Online sur un nom de domaine qui est utilisé pour une collection de sites SharePoint Online, vous devez supprimer la collection de sites avant de pouvoir supprimer le nom de domaine.

## Pour supprimer un domaine

1.  Connectez-vous au portail Azure Classic à l'aide d'un compte disposant de privilèges d'administrateur global pour ce répertoire.

2.  Ouvrez votre répertoire, puis sélectionnez **Domaines**.

3.  Sélectionnez le domaine, puis cliquez sur Supprimer.

## Étapes suivantes

- [Utilisation de noms de domaine personnalisés pour simplifier l’expérience de connexion pour vos utilisateurs](active-directory-add-domain.md)
- [Ajout d’une marque de société aux pages de connexion et du volet d’accès](active-directory-add-company-branding.md)
- [Ajouter et vérifier un nom de domaine personnalisé dans Azure Active Directory](active-directory-add-domain-add-verify-general.md)
- [Affecter des utilisateurs à un domaine personnalisé](active-directory-add-domain-add-users.md)
- [Modifier le bureau d’enregistrement DNS pour votre nom de domaine personnalisé](active-directory-add-domain-change-registrar.md)

<!---HONumber=AcomDC_0211_2016-->