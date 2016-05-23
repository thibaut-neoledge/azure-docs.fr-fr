<properties
   pageTitle="Limites actuelles de la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Version préliminaire d’Azure AD B2B Collaboration : Limites actuelles de la version préliminaire

- L’authentification multifacteur (MFA) n’est pas prise en charge sur les utilisateurs externes. Par exemple, si Contoso dispose de l’authentification MFA, mais que ce n’est pas le cas de l’organisation partenaire, les utilisateurs de celle-ci ne peuvent pas bénéficier de l’authentification multifacteur par le biais de B2B Collaboration.
- Les invitations ne sont possibles que par l’intermédiaire de la fonctionnalité CSV ; les invitations individuelles et l’accès aux API ne sont pas pris en charge.
- Seuls les administrateurs généraux Azure AD peuvent télécharger des fichiers .csv.
- Les invitations envoyées aux adresses e-mail grand public (telles que hotmail.com, Gmail.com ou comcast.net) ne sont pas prises en charge pour le moment.
- L’accès des utilisateurs externes aux applications locales n’a pas été testé.
- Les utilisateurs externes ne sont pas automatiquement nettoyés quand l’utilisateur réel est supprimé de leur annuaire.
- L’envoi d’invitations à des listes de distribution n’est pas pris en charge.
- 2 000 enregistrements au maximum peuvent être téléchargés par le biais de la fonctionnalité CSV.

## Articles connexes
Consultez les autres articles sur la collaboration B2B d'Azure AD :

- [Qu'est-ce que la collaboration B2B d'Azure AD ?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
- [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->