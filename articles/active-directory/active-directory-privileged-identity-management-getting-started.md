<properties
   pageTitle="Prise en main d’Azure AD Privileged Identity Management | Microsoft Azure"
   description="Découvrez comment gérer des identités privilégiées avec l’application Azure Active Directory Privileged Identity Management dans le portail Azure."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Prise en main d’Azure AD Privileged Identity Management


Le service Azure Active Directory (AD) Privileged Identity Management vous permet de gérer, de contrôler et de surveiller vos identités privilégiées et l’accès aux ressources dans Azure AD et dans d’autres services en ligne Microsoft tels qu’Office 365 ou Microsoft Intune.

Cet article vous indique comment ajouter l'application Azure AD PIM à votre tableau de bord du portail Azure.

## Ajout de l’application Privileged Identity Management

Avant d'utiliser Azure AD Privileged Identity Management, vous devez ajouter l'application à votre tableau de bord du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre répertoire.
2. Si votre organisation possède plusieurs répertoires, cliquez sur votre nom d'utilisateur dans le coin supérieur droit du portail Azure, puis sélectionnez le répertoire où vous allez utiliser PIM.
3. Sélectionnez l’icône **Nouveau** dans le volet de navigation à gauche.
4. Sélectionnez **Sécurité + Identité**.
5. Sélectionnez **Azure AD Privileged Identity Management**.
6. Cochez **Épingler au tableau de bord**, puis cliquez sur le bouton **Créer**. L’application Privileged Identity Management s’ouvre.


Si vous êtes la première personne à utiliser Azure AD Privileged Identity Management dans votre répertoire, l’[Assistant sécurité](active-directory-privileged-identity-management-security-wizard.md) vous guidera à travers l'expérience de l'attribution initiale. Vous deviendrez alors automatiquement le premier **Administrateur de la sécurité** du répertoire. Seul un administrateur de la sécurité peut accéder à cette application pour gérer l'accès des autres administrateurs.

Sinon, si vous avez été affecté à un ou plusieurs rôles par un autre administrateur de la sécurité, vous aurez le choix du rôle à activer. Si vous êtes vous-même dans un rôle d'administrateur de la sécurité, vous verrez également un choix de **Gestion des identités**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

La [vue d’ensemble Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) inclut plus de détails sur la manière de gérer un accès administrateur dans votre organisation.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->