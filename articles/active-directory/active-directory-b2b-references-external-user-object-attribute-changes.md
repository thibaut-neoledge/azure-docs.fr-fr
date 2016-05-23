<properties
   pageTitle="Changement d’attribut d’objet d’utilisateur externe pour la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Azure"
   description="Azure Active Directory B2B prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
   services="active-directory"
   documentationCenter=""
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
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Version préliminaire d’Azure AD B2B Collaboration : changement d’attribut d’objet utilisateur externe

Chaque utilisateur d’un annuaire Azure AD est représenté par un objet utilisateur. L’objet utilisateur dans Azure AD subit des modifications d’attribut aux différentes étapes du flux d’échange d’invités de la collaboration B2B. L’objet utilisateur qui représente l’utilisateur partenaire dans l’annuaire possède des attributs qui changent au moment de l’échange lorsque le partenaire utilisateur clique sur le lien dans le message électronique d’invitation. Plus précisément :

- Les attributs **SignInName** et **AltSecId** sont remplis.
- L’attribut **DisplayName** remplace le nom d’utilisateur principal (user\_fabrikam.com#EXT#@contoso.com) par le nom de connexion (user@fabrikam.com).

Le suivi de ces attributs dans Azure AD peut vous aider à déterminer si un utilisateur partenaire a échangé son invitation de collaboration B2B.

## Articles connexes
Consultez les autres articles sur la collaboration B2B d'Azure AD :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
- [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)
- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->