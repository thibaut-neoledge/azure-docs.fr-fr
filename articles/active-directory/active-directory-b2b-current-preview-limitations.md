<properties
   pageTitle="Limites actuelles de la version préliminaire d’Azure Active Directory B2B Collaboration | Microsoft Azure"
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
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>

# Limites actuelles de la version préliminaire d’Azure Active Directory (Azure AD) B2B Collaboration

- L’authentification multifacteur (MFA) n’est pas prise en charge sur les utilisateurs externes. Par exemple, si Contoso dispose de l’authentification MFA, mais que ce n’est pas le cas de l’organisation partenaire, les utilisateurs de celle-ci ne peuvent pas bénéficier de l’authentification multifacteur par le biais de B2B Collaboration.
- Les invitations ne sont possibles que par l’intermédiaire de la fonctionnalité CSV ; les invitations individuelles et l’accès aux API ne sont pas pris en charge.
- Seuls les administrateurs généraux Azure AD peuvent télécharger des fichiers .csv.
- Les invitations envoyées aux adresses e-mail grand public (telles que hotmail.com, Gmail.com ou comcast.net) ne sont pas prises en charge pour le moment.
- L’accès des utilisateurs externes aux applications locales n’a pas été testé.
- Les utilisateurs externes ne sont pas automatiquement nettoyés quand l’utilisateur réel est supprimé de leur annuaire.
- L’envoi d’invitations à des listes de distribution n’est pas pris en charge.
- 2 000 enregistrements au maximum peuvent être téléchargés par le biais de la fonctionnalité CSV.

## Articles connexes
Consultez les autres articles sur Azure B2B Collaboration :

- [Présentation d’Azure AD B2B Collaboration](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Fonctionnement](active-directory-b2b-how-it-works.md)
- [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
- [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
- [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
- [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)

<!---HONumber=Nov15_HO1-->