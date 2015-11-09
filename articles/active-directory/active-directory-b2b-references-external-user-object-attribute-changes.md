<properties
   pageTitle="Changement d’attribut d’objet d’utilisateur externe pour la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Azure"
   description="Azure Active Directory B2B prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Changement d’attribut d’objet d’utilisateur externe pour la version préliminaire d’Azure Active Directory (Azure AD) B2B Collaboration
Chaque utilisateur d’un annuaire Azure AD est représenté par un objet utilisateur. L’objet utilisateur dans Azure AD subit des modifications d’attribut aux différentes étapes du flux d’échange d’invités de la collaboration B2B. L’objet utilisateur qui représente l’utilisateur partenaire dans l’annuaire possède des attributs qui changent au moment de l’échange lorsque le partenaire utilisateur clique sur le lien dans le message électronique d’invitation. Plus précisément :

- Les attributs **SignInName** et **AltSecId** sont remplis.
- L’attribut **DisplayName** remplace le nom d’utilisateur principal (user\_fabrikam.com#EXT#@contoso.com) par le nom de connexion (user@fabrikam.com).

Le suivi de ces attributs dans Azure AD peut vous aider à déterminer si un utilisateur partenaire a échangé son invitation de collaboration B2B.

## Articles connexes
Consultez les autres articles sur Azure B2B Collaboration :

- [Qu’est-ce qu’Azure AD B2B Collaboration ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
- [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=Nov15_HO1-->