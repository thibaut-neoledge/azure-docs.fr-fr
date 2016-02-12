<properties
   pageTitle="Version préliminaire d’Azure AD B2B Collaboration : fonctionnement | Microsoft Azure"
   description="Décrit comment Azure Active Directory B2B Collaboration prend en charge les relations interentreprises en permettant aux partenaires commerciaux d’accéder de façon sélective à vos applications d’entreprise"
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
   ms.date="02/03/2016"
   ms.author="viviali"/>

# Version préliminaire d’Azure AD B2B Collaboration : Fonctionnement
Azure AD B2B Collaboration repose sur un modèle d’invitation et d’échange. Vous fournissez les adresses de messagerie des parties avec lesquelles vous souhaitez travailler, ainsi que les applications qu’elles doivent utiliser. Azure AD leur envoie une invitation par courrier électronique avec un lien. L’utilisateur partenaire suit le lien et est invité à se connecter en utilisant son compte Azure AD ou à s’inscrire pour obtenir un nouveau compte Azure AD.

1. Votre administrateur invite les utilisateurs partenaires en téléchargeant [un fichier .csv structuré](active-directory-b2b-references-csv-file-format.md) à l’aide du portail Azure.
2. Le portail envoie des courriers électroniques d’invitation à ces utilisateurs partenaires.
3. Les utilisateurs partenaires cliquent sur le lien dans le message électronique et sont invités à se connecter à l’aide de leurs informations d’identification professionnelles (si elles se trouvent déjà dans Azure AD) ou à s’inscrire en tant qu’utilisateur d’Azure AD B2B Collaboration.
4. Les utilisateurs partenaires sont redirigés vers l’application à laquelle ils ont été invités, et à laquelle ils ont désormais accès.

## Opérations d’annuaire
Les utilisateurs partenaires existent dans votre annuaire Azure AD en tant qu’utilisateurs externes. Cela signifie que votre administrateur peut approvisionner des licences, affecter une appartenance au groupe et accorder l’accès aux applications d’entreprise par le biais du portail Azure ou à l’aide d’Azure PowerShell, comme pour les utilisateurs de votre société.

Bien qu’un abonnement Azure AD payant (De base ou Premium) ne soit pas nécessaire pour utiliser Azure AD B2B, les clients qui en possèdent un bénéficient des avantages supplémentaires suivants :

 - Les administrateurs peuvent affecter des groupes aux applications, permettant une gestion simplifiée de l’accès de l’utilisateur invité.
 - La personnalisation des clients d’administration facilite la personnalisation des messages d’invitation et de l’expérience de l’échange, en offrant plus de contexte aux utilisateurs partenaires invités.

## Articles connexes
 Consultez les autres articles sur Azure B2B Collaboration

 - [Présentation d’Azure AD B2B Collaboration](active-directory-b2b-what-is-azure-ad-b2b.md)
 - [Procédure pas à pas](active-directory-b2b-detailed-walkthrough.md)
 - [Référence du format de fichier CSV](active-directory-b2b-references-csv-file-format.md)
 - [Format du jeton utilisateur externe](active-directory-b2b-references-external-user-token-format.md)
 - [Modifications de l’attribut d’objet utilisateur externe](active-directory-b2b-references-external-user-object-attribute-changes.md)
 - [Limites actuelles de la version préliminaire](active-directory-b2b-current-preview-limitations.md)

<!---HONumber=AcomDC_0204_2016-->