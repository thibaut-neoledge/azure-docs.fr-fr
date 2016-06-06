<properties
   pageTitle="Comment gérer les paramètres d'activation de rôle | Microsoft Azure"
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Comment gérer les paramètres d'activation de rôle dans Azure AD Privileged Identity Management

Un administrateur de rôle privilégié peut personnaliser Azure AD Privileged Identity Management (PIM) dans son organisation, notamment modifier l’expérience d’un utilisateur qui active une attribution de rôle temporaire.

## Gérer les paramètres d'activation de rôle

1. Accédez à la [portail Azure](https://portal.azure.com) et sélectionnez l’application **Azure AD Privileged Identity Management** à partir du tableau de bord.
2. Sélectionnez le rôle que vous souhaitez gérer à partir de la table des rôles.
3. Cliquez sur **Paramètres**.
4. Définissez le nombre d’heure d’activation par défaut en déplaçant le curseur ou en entrant ce nombre dans le champ de texte. Le maximum est de 72 heures.
5. Cliquez sur **Activer** ou sur **Désactiver** pour envoyer ou non des notifications relatives à l’activation aux administrateurs. (L'activation des notifications peut aider à détecter une activité non autorisée de l’administrateur.)
6. Cliquez sur **Activer** pour permettre aux administrateurs d’entrer des informations de billetterie dans leur demande d’activation. (Ces informations peuvent être utiles lors d’un audit d’accès au rôle ultérieur.)
7. Cliquez sur **Activer** ou **Désactiver** pour exiger ou non une authentification multifacteur pour une demande d’activation.
8. Cliquez sur **Enregistrer**.

Vous ne pouvez pas désactiver l’authentification multifacteur pour les rôles à privilèges élevés pour Azure AD et Office 365, notamment :
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

<!---HONumber=AcomDC_0525_2016-->