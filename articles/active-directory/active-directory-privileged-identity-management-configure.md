<properties
	pageTitle="Azure AD Privileged Identity Management | Microsoft Azure"
	description="Une rubrique qui explique ce qu’est Azure AD Privileged Identity Management et comment utiliser PIM pour améliorer votre sécurité dans le cloud."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="kgremban"/>

# Azure AD Privileged Identity Management

Le service Azure Active Directory (AD) Privileged Identity Management vous permet de gérer, de contrôler et de surveiller l’accès aux ressources dans Azure AD et dans d’autres services en ligne Microsoft tels qu’Office 365 ou Microsoft Intune.

Les organisations veulent limiter le nombre de personnes qui ont accès aux informations ou aux ressources sécurisées afin de réduire le risque qu’un utilisateur malveillant accède à ces données. Mais utilisateurs doivent pouvoir toujours effectuer des opérations privilégiées dans les applications Azure, Office 365 ou SaaS. En fin de compte, le travail doit être fait et les organisations doivent offrir un accès privilégié aux utilisateurs dans Azure AD sans avoir à surveiller ce que font les utilisateurs avec leurs privilèges d’administrateur. Azure AD Privileged Identity Management contribue à minimiser ce risque.

Azure AD Privileged Identity Management vous aide à :

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

Jusqu'ici, vous pouviez affecter un utilisateur à un rôle d'administrateur via le portail de gestion Azure ou Windows PowerShell. Cela permet à cet utilisateur de devenir **administrateur permanent**, toujours actif dans le rôle qui lui a été affecté. Azure AD Privileged Identity Management introduit le concept d**’administrateur éligible**. Les administrateurs éligibles doivent être des utilisateurs qui nécessitent un accès privilégié de temps à autres, mais pas tous les jours. Ce rôle reste inactif jusqu’à ce l’utilisateur ait besoin d’un tel accès ; dans ce cas, il complète un processus d’activation et devient administrateur actif pour une durée prédéterminée.

## Activer Privileged Identity Management pour votre répertoire

Vous pouvez commencer à utiliser Azure AD Privileged Identity Management dans le [portail Azure](https://portal.azure.com/).

>[AZURE.NOTE] Vous devez être un administrateur général avec un compte d’organisation (par exemple, @votredomaine.com), et non un compte Microsoft (par exemple, @outlook.com), pour activer Azure AD Privileged Identity Management pour un répertoire.

1. Connectez-vous au [portail Azure](https://portal.azure.com/) en tant qu’administrateur général de votre répertoire.
2. Si votre organisation possède plusieurs répertoires, cliquez sur votre nom d'utilisateur dans le coin supérieur droit du portail Azure, puis sélectionnez le répertoire où vous allez utiliser Azure AD Privileged Identity Management.
3. Sélectionnez **Nouveau** > **Sécurité + Identité** > **Azure AD Privileged Identity Management**.

	![Activer PIM dans le portail][1]

4. Cochez la case **Épingler au tableau de bord**, puis cliquez sur le bouton **Créer**. Le tableau de bord Privileged Identity Management s’ouvre.

Si vous êtes la première personne à utiliser Azure AD Privileged Identity Management dans votre répertoire, l’[Assistant sécurité](active-directory-privileged-identity-management-security-wizard.md) vous guidera à travers l'expérience de l'attribution initiale. Vous deviendrez alors automatiquement le premier **Administrateur de la sécurité** et **administrateur de rôle privilégié** du répertoire.

Seul un administrateur de rôle privilégié peut gérer l’accès des autres administrateurs. Vous pouvez [donner aux autres utilisateurs la capacité à gérer dans PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Tableau de bord de gestion des identités privilégiées

Azure AD Privileged Identity Manager fournit un tableau de bord qui contient des informations importantes telles que :

- Alertes indiquant des possibilités d’amélioration de la sécurité
- Le nombre d'utilisateurs affectés à chaque rôle privilégié
- Le nombre d'administrateurs éligibles et permanents
- Révisions en continu de l’accès

![Capture d’écran du tableau de bord PIM][2]

## Gestion des rôles privilégiés

Avec Azure AD Privileged Identity Management, vous pouvez gérer les administrateurs en ajoutant ou en supprimant des administrateurs permanents ou éligibles pour chaque rôle.

![Capture d’écran d’ajout et de suppression d’administrateurs dans PIM][3]

## Configurer les paramètres d'activation de rôle

À l'aide des paramètres d'activation de rôle, vous pouvez configurer les propriétés d'activation d’un rôle éligible, notamment :

- La durée de la période d’activation d’un rôle
- La notification d'activation d’un rôle
- Les informations qu'un utilisateur doit fournir au cours du processus d'activation du rôle

![Capture d’écran de l’activation d’administrateur dans les paramètres PIM][4]

Notez que dans l’image, les boutons de **l’authentification multifacteur** sont désactivés. Avec certains rôles avec des privilèges élevés, nous avons besoin de l’authentification multifacteur pour garantir une protection renforcée.

## Activation d’un rôle  

Pour activer un rôle, un administrateur éligible doit demander une « activation » limitée dans le temps pour le rôle. L'activation peut être demandée à l'aide de l’option **Activate my role** dans Azure AD Privileged Identity Management.

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

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ./media/active-directory-privileged-identity-management-configure/PIM_AddRemove.png
[4]: ./media/active-directory-privileged-identity-management-configure/PIM_RoleActivationSettings.png
[5]: ./media/active-directory-privileged-identity-management-configure/PIM_RequestActivation.png
[6]: ./media/active-directory-privileged-identity-management-configure/PIM_ActivationHistory.png

<!---HONumber=AcomDC_0706_2016-->