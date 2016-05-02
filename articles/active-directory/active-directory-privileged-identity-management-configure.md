<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Une rubrique qui explique ce qu’est Azure AD Privileged Identity Management et comment utiliser PIM pour améliorer votre sécurité dans le cloud."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/15/2016"
	ms.author="kgremban"/>

# Azure AD Privileged Identity Management

Le service Azure Active Directory (AD) Privileged Identity Management vous permet de gérer, de contrôler et de surveiller vos identités privilégiées et l’accès aux ressources dans Azure AD et dans d’autres services en ligne Microsoft tels qu’Office 365 ou Microsoft Intune.

Les utilisateurs doivent parfois effectuer des opérations privilégiées dans des ressources Azure ou Office 365, ou dans d'autres applications SaaS. Cela signifie souvent que les entreprises doivent leur donner un accès privilégié permanent à Azure AD. C’est un risque de sécurité croissant pour les ressources hébergées dans le cloud, car les entreprises ne peuvent pas suffisamment surveiller ce que ces utilisateurs font avec leurs privilèges d'administrateur. En outre, si un compte d’utilisateur disposant d’un accès privilégié est compromis, cette seule faille peut affecter sa sécurité globale sur le cloud. Azure AD Privileged Identity Management contribue à minimiser ce risque.

Grâce à Azure AD Privileged Identity Management, vous pouvez :

- Identifier les utilisateurs qui ont un rôle d’administrateur dans Azure AD
- Activer à la demande un accès administrateur « juste à temps » aux services Microsoft Online Services comme Office 365 et Intune
- Obtenir des rapports sur l'historique des accès administrateur et sur les modifications apportées aux affectations de l'administrateur
- Recevoir des alertes sur l'accès à un rôle privilégié

Azure AD Privileged Identity Management peut gérer les rôles d'organisation intégrés dans Azure AD, notamment :

- Administrateur général
- Administrateur de facturation
- Administrateur de services  
- Administrateur d'utilisateurs
- Administrateur de mots de passe

## Administrateur des accès immédiats

Jusqu'ici, vous pouviez affecter un utilisateur à un rôle d'administrateur via le portail de gestion Azure précédent ou Windows PowerShell. Cela permet à cet utilisateur de devenir **administrateur permanent** pour ce rôle, toujours actif dans le rôle qui lui a été affecté. Azure AD Privileged Identity Management introduit le concept d'un **administrateur temporaire** pour un rôle, qui est un utilisateur devant terminer un processus d'activation pour ce rôle. Le processus d'activation permet d’activer l'affectation de l'utilisateur à un rôle dans Azure AD pendant une période spécifiée, par exemple 8 heures.

## Activer Privileged Identity Management pour votre répertoire

Vous pouvez commencer à utiliser Azure AD Privileged Identity Management en accédant au [portail Azure](https://portal.azure.com/). Azure AD Privileged Identity Management n'apparaît pas dans le portail classique antérieur.

>[AZURE.NOTE] Vous devez être un administrateur général avec un compte d’organisation, et non un compte Microsoft, pour activer Azure AD Privileged Identity Management pour un répertoire.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre répertoire.
2. Si votre organisation possède plusieurs répertoires, cliquez sur votre nom d'utilisateur dans le coin supérieur droit du portail Azure, puis sélectionnez le répertoire où vous allez utiliser Azure AD Privileged Identity Management.
3. Cliquez sur l’icône **Nouveau** dans le volet de navigation à gauche.
4. Sélectionnez **Sécurité + Identité**.
5. Sélectionnez **Azure AD Privileged Identity Management**.
6. Cochez la case **Épingler au tableau de bord**, puis cliquez sur le bouton **Créer**. Le tableau de bord Privileged Identity Management s’ouvre.

Si vous êtes la première personne à utiliser Azure AD Privileged Identity Management dans votre répertoire, l’[Assistant sécurité](active-directory-privileged-identity-management-security-wizard.md) vous guidera à travers l'expérience de l'attribution initiale. Vous deviendrez alors automatiquement le premier **Administrateur de la sécurité** du répertoire.

Seul un administrateur de la sécurité peut utiliser l’application PIM pour gérer l'accès des autres administrateurs. Vous pouvez [donner aux autres utilisateurs la possibilité de gérer dans PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Tableau de bord de gestion des identités privilégiées

Azure AD Privileged Identity Manager fournit un tableau de bord qui contient des informations importantes telles que :

- Le nombre d'utilisateurs affectés à chaque rôle privilégié  
- Le nombre d'administrateurs temporaires et permanents
- L’historique des accès de chaque administrateur

![Capture d’écran du tableau de bord PIM][2]

## Gestion des rôles privilégiés

Avec Azure AD Privileged Identity Management, vous pouvez gérer les administrateurs en ajoutant ou en supprimant des administrateurs permanents ou temporaires pour chaque rôle.

![Capture d’écran d’ajout et de suppression d’administrateurs dans PIM][3]

## Configurer les paramètres d'activation de rôle

À l'aide des paramètres d'activation de rôle, vous pouvez configurer les propriétés d'activation d’un rôle temporaire, notamment :

- La durée de la période d’activation d’un rôle
- La notification d'activation d’un rôle
- Les informations qu'un utilisateur doit fournir au cours du processus d'activation du rôle  

![Capture d’écran de l’activation d’administrateur dans les paramètres PIM][4]

## Activation d’un rôle  

Pour activer un rôle, un administrateur temporaire doit demander une « activation » limitée dans le temps pour le rôle. L'activation peut être demandée à l'aide de l’option **Activate my role** dans Azure AD Privileged Identity Management.

Un administrateur qui souhaite activer un rôle doit initialiser Azure AD Privileged Identity Management sur le portail Azure.

Toutes les catégories d'administrateur peuvent utiliser Azure AD Privileged Identity Management pour activer leur rôle.

L'activation du rôle est limitée dans le temps. Dans les paramètres d’activation de rôle, vous pouvez définir la durée de l’activation, ainsi que les informations obligatoires que l’administrateur doit fournir pour activer le rôle.

![Capture d’écran de demande d’activation de rôle d’administrateur dans PIM][5]

## Historique d’activation des rôles

Azure AD Privileged Identity Management vous permet également d’effectuer le suivi des modifications dans les attributions de rôles privilégiés et dans l’historique d’activation des rôles. Cette opération peut être effectuée à l’aide des options de journal d'audit :

![Capture d’écran de l’historique d’activation dans PIM][6]

## Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0420_2016-->