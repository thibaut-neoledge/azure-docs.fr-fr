---
title: "Gestion des noms de domaine personnalisés dans Azure Active Directory | Microsoft Docs"
description: "Concepts de gestion et procédures pour gérer un domaine personnalisé dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cf3523bd-9ee0-439e-963d-ccea038867b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 06e2dd05bdf21074ed060057cbc61e93f304399a
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Gestion des noms de domaine personnalisés dans Azure Active Directory
Un nom de domaine peut être un identificateur important pour de nombreuses ressources de répertoire, lorsqu’il est compris dans :

* Un nom d’utilisateur ou une adresse e-mail d’un utilisateur
* L’adresse d’un groupe
* L’URI ID d’application pour une application

Une ressource dans Azure Active Directory (Azure AD) peut inclure un nom de domaine déjà vérifié comme appartenant au répertoire qui contient la ressource. Seul un administrateur général peut effectuer des tâches de gestion de domaine dans Azure AD.

> [!IMPORTANT]
> Microsoft recommande de gérer Azure AD à l’aide du [Centre d’administration Azure AD](https://aad.portal.azure.com) dans le portail Azure au lieu d’utiliser le portail Azure classique référencé dans cet article. Pour savoir comment gérer vos noms de domaine dans le centre d’administration Azure AD, consultez la section [Gestion des noms de domaines personnalisés dans Azure Active Directory](active-directory-domains-manage-azure-portal.md).

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Définir le nom de domaine principal pour votre répertoire Azure AD
Lors de la création du répertoire, le nom de domaine initial, par exemple « contoso.onmicrosoft.com », est également le nom de domaine principal de votre répertoire. Le domaine principal est le nom de domaine par défaut pour un nouvel utilisateur lorsque vous créez un utilisateur dans le [portail Azure Classic](https://manage.windowsazure.com/), ou dans d’autres portails, tels que le portail d’administration d’Office 365. Cela simplifie le processus permettant à un administrateur de créer des utilisateurs dans le portail.

Pour modifier le nom de domaine principal dans votre répertoire, procédez comme suit :

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) avec un compte d’administrateur général pour votre répertoire Azure AD.
2. Sélectionnez **Active Directory** dans le volet de navigation gauche.
3. Ouvrez votre annuaire.
4. Sélectionnez l’onglet **Domaines** .
5. Sélectionnez le bouton **Modifier le principal** sur la barre de commandes.
6. Sélectionnez le domaine que vous souhaitez utiliser comme nouveau domaine principal pour votre répertoire.

Vous pouvez modifier le nom de domaine principal de votre répertoire en n’importe quel domaine personnalisé vérifié qui n’est pas fédéré. La modification du domaine principal de votre répertoire ne changera pas les noms des utilisateurs existants.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Ajouter des noms de domaine personnalisés à Azure AD
Vous pouvez ajouter jusqu’à 900 noms de domaine personnalisés à chaque répertoire Azure AD. Le processus permettant d’ [ajouter un nom de domaine personnalisé supplémentaire](active-directory-add-domain.md) est le même pour le premier nom de domaine personnalisé.

## <a name="add-subdomains-of-a-custom-domain"></a>Ajouter des sous-domaines d’un domaine personnalisé
Si vous souhaitez ajouter un nom de domaine de troisième niveau, tel que « europe.contoso.com » à votre répertoire, vous devez tout d’abord ajouter et vérifier le domaine de second niveau, tel que contoso.com. Le sous-domaine est automatiquement vérifié par Azure AD. Pour voir que le sous-domaine que vous venez d’ajouter a été vérifié, actualisez la page dans le navigateur qui répertorie les domaines de votre répertoire.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Que faire en cas de modification du bureau d’enregistrement DNS pour votre nom de domaine personnalisé ?
Si vous modifiez le bureau d’enregistrement DNS de votre nom de domaine personnalisé, vous pouvez continuer à utiliser votre nom de domaine personnalisé sans interruption et sans tâches de configuration supplémentaires dans Azure AD. Si vous utilisez votre nom de domaine personnalisé avec Office 365, Intune ou autres services s’appuyant sur des noms de domaine personnalisés dans Azure AD, reportez-vous à la documentation de ces services.

## <a name="delete-a-custom-domain-name"></a>Supprimer un nom de domaine personnalisé
Vous pouvez supprimer un nom de domaine personnalisé de votre répertoire Azure AD si votre organisation n’utilise plus ce nom de domaine ou si vous souhaitez l’utiliser avec un autre répertoire Azure AD.

Pour supprimer un nom de domaine personnalisé, vous devez d’abord vous assurer qu’aucune des ressources de votre répertoire ne s’appuie sur le nom de domaine. Vous ne pouvez pas supprimer un nom de domaine de votre répertoire si :

* L’utilisateur dispose d’un nom d’utilisateur, d’une adresse de messagerie ou d’une adresse de proxy qui incluent le nom de domaine.
* Le groupe dispose d’une adresse de messagerie ou d’une adresse de proxy qui incluent le nom de domaine.
* Une application dans Azure AD dispose d’une URI ID d’application qui inclut le nom de domaine.

Vous devez modifier ou supprimer n’importe quelle ressource de ce type dans votre répertoire Azure AD pour pouvoir supprimer le nom de domaine personnalisé.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utiliser PowerShell ou API Graph pour gérer les noms de domaine
La plupart des tâches de gestion des noms de domaine dans Azure Active Directory peuvent également être accomplies à l’aide de Microsoft PowerShell, ou encore par programmation à l’aide d’API Graph d’Azure AD.

* [Utilisation de PowerShell pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Utilisation d’API Graph pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les noms de domaine dans Azure AD](active-directory-add-domain-concepts.md)
* [Gérer les noms de domaine personnalisés](active-directory-add-manage-domain-names.md)

