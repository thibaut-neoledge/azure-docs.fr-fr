---
title: "Explication de la préversion d’Azure Active Directory | Microsoft Docs"
description: "Une rubrique qui explique les différences entre Azure Active Directory dans le portail classique et la version préliminaire d’Azure Active Directory dans le portail Azure."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e717c11c-e7c9-4565-8e47-1950905e5b3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 3b31bd036d9c3ff8036b314b93cbddd94874ff63
ms.openlocfilehash: 8f7a679dc5b5726107503a9f7363ab162b1770d0
ms.lasthandoff: 02/15/2017


---
# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Aperçu de l’expérience de gestion d’Azure Active Directory dans le portail Azure
L’expérience de gestion d’Azure Active Directory (Azure AD) est en version préliminaire dans le portail Azure. Vous pouvez l’essayer en vous connectant au [portail Azure](https://portal.azure.com) en tant qu’administrateur général de votre répertoire. Ensuite, sélectionnez Azure Active Directory dans la liste des services, s’il est visible, ou sélectionnez **Plus de services** pour afficher la liste de tous les services. Vous n’avez pas besoin d’un abonnement Azure pour utiliser l’expérience de gestion d’Active Directory dans le portail Azure.

## <a name="learn-about-what-you-can-do-in-the-preview-experience"></a>Découvrez les possibilités offertes par l’expérience de préversion
L’expérience de préversion vous permet de gérer les nombreuses ressources de répertoire telles que les utilisateurs, les groupes, les applications et les paramètres de répertoire dans le portail Azure. Nous améliorons cette expérience pour inclure toutes les fonctionnalités qui existent dans l’expérience de gestion Azure AD du [portail classique Azure](https://manage.windowsazure.com). En attendant, vous devrez continuer à effectuer certaines tâches dans le portail classique.

## <a name="manage-the-same-azure-ad-tenants"></a>Gestion des mêmes locataires Azure AD
L’expérience de préversion lit et écrit dans le même locataire Azure Active Directory que le portail classique et le centre d’administration Office 365. Les modifications apportées dans l’un de ces portails sont répercutées dans tous les autres.

## <a name="use-the-same-authorization-logic"></a>Utiliser la même logique d’autorisation
L’expérience de la version préliminaire utilise la même logique d’autorisation que les clients Active Directory existants. Les utilisateurs sont autorisés à apporter des modifications aux ressources de répertoire en fonction de leur rôle, par exemple administrateur global, administrateur de l’utilisateur ou administrateur de mot de passe. Avoir un rôle sur des ressources Azure ou un abonnement Azure n’autorise pas un utilisateur à gérer les ressources Active Directory. Pour plus d’informations sur les rôles de gestion Azure AD, voir [Attribution de rôles d’administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md).

L’expérience de la version préliminaire est optimisée pour les administrateurs généraux. Si vous utilisez l’expérience de préversion lors d’une connexion en tant qu’utilisateur qui n’est pas un administrateur global, l’expérience peut s’en trouver dégradée. Par exemple, vous pourriez être en mesure de sélectionner un bouton qui vous permet de commencer une tâche que vous ne pouvez pas effectuer dans le répertoire. Nous travaillons sur l’amélioration de cette expérience.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez nous envoyer vos commentaires sur l’expérience de la version préliminaire dans la section portail d’administration du [forum de commentaires Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).

