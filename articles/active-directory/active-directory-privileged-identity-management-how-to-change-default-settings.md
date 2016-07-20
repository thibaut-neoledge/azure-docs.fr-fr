<properties
   pageTitle="Comment gérer les paramètres d'activation de rôle | Microsoft Azure"
   description="Découvrez comment modifier les paramètres par défaut d’identités privilégiées avec l’extension Azure Active Directory Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# Comment gérer les paramètres d'activation de rôle dans Azure AD Privileged Identity Management

Un administrateur de rôle privilégié peut personnaliser Azure AD Privileged Identity Management (PIM) dans son organisation, notamment modifier l’expérience d’un utilisateur qui active une attribution de rôle éligible.

## Gérer les paramètres d'activation de rôle

1. Accédez à la [portail Azure](https://portal.azure.com) et sélectionnez l’application **Azure AD Privileged Identity Management** à partir du tableau de bord.
2. Sélectionnez le rôle que vous souhaitez gérer à partir de la table des rôles.
3. Cliquez sur **Paramètres**.

Sur la page des paramètres de chaque rôle, vous pouvez configurer plusieurs paramètres. Ces paramètres affectent uniquement les utilisateurs qui sont des administrateurs éligibles et non des administrateurs permanents.

**Activations** : durée, en heures, pendant laquelle un rôle reste actif avant d’expirer. Cette durée peut être comprise entre 1 et 72 heures.

**Notifications** : vous pouvez choisir si le système envoie ou non des messages électroniques aux administrateurs pour leur confirmer qu’ils ont activé un rôle. Cette option peut être utile pour détecter les activations non autorisées ou illégitimes.

**Ticket d’incident/demande** : vous pouvez choisir si les administrateurs admissibles doivent ou non inclure un numéro de ticket lorsqu’ils activent leur rôle. Cela peut être utile lorsque vous effectuez des audits d’accès à un rôle.

**Authentification multifacteur** : vous pouvez choisir si les utilisateurs doivent ou non vérifier leur identité via l’authentification multifacteur avant de pouvoir activer leurs rôles. Une seule vérification est nécessaire par session, et non chaque fois qu’ils ont activé un rôle. Il existe deux conseils à garder à l’esprit lorsque vous activez l’authentification multifacteur :

- Les utilisateurs qui disposent de comptes Microsoft pour leurs adresses de messagerie (généralement @outlook.com, mais pas toujours) ne peuvent pas s’inscrire pour l’authentification multifacteur Azure. Si vous souhaitez attribuer des rôles aux utilisateurs disposant de comptes Microsoft, vous devez les rendre administrateurs permanents ou désactiver l’authentification multifacteur pour ce rôle.

- Vous ne pouvez pas désactiver l’authentification multifacteur pour les rôles à privilèges élevés pour Azure AD et Office 365. Il s’agit d’une fonctionnalité de sécurité car ces rôles doivent être soigneusement protégés :

    - Administrateur général
    - Administrateur de rôle privilégié
    - Administrateur de compte d’utilisateur
    - Enregistreur de répertoire
    - Prise en charge de niveau 1 de partenaire
    - Prise en charge de niveau 2 de partenaire
    - Administrateur de facturation
    - Administrateur de sécurité
    - Administrateur Exchange
    - Administrateur de boîte aux lettres
    - Administrateur Skype Entreprise
    - Administrateur SharePoint
    - Administrateur de conformité

Pour plus d’informations sur l’utilisation de la solution MFA avec Privileged Identity Management, voir [Exigence de l’application de la solution MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0706_2016-->