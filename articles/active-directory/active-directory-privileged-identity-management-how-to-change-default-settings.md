<properties
   pageTitle="Modifier ou afficher les paramètres par défaut d’un rôle | Microsoft Azure"
   description="Découvrez comment modifier les paramètres par défaut d’identités privilégiées avec l’extension Azure Active Directory Privileged Identity Management."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management : comment modifier ou afficher les paramètres d’activation par défaut d’un rôle

## Modification et affichage de l’activation de rôle par défaut
1. Dans le tableau de bord, cliquez sur le rôle à configurer dans la table de rôles.
2. Cliquez sur **Paramètres**.
3. Définissez la durée d’activation par défaut (en heures) en déplaçant le curseur ou en entrant le nombre d’heures dans le champ de texte.
4. Cliquez sur **Activer** ou sur **Désactiver** selon que vous souhaitez ou non que des notifications relatives à l’activation soient envoyées aux administrateurs.
5. Cliquez sur **Activer** ou **Désactiver** selon que vous souhaitez ou non autoriser les administrateurs à entrer des informations de billetterie dans leur demande d’activation.
6. Cliquez sur **Activer** ou **Désactiver** selon que vous souhaitez ou non exiger l’application de la solution Multi-Factor Authentication (MFA) pour une demande d’activation.

    Vous ne pouvez pas désactiver l’authentification multifacteur pour les rôles à privilèges élevés pour Azure AD et Office 365, notamment :

    - Administrateur général
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

7. Cliquez sur **Activer** ou **Désactiver** selon que vous voulez autoriser ou non que les administrateurs généraux soient temporaires.
8. Cliquez sur **Enregistrer**.

Pour plus d’informations sur l’utilisation de la solution MFA avec Privileged Identity Management, voir [Exigence de l’application de la solution MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->