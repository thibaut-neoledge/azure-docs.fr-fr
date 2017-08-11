---
title: "Affecter des utilisateurs à un domaine personnalisé dans Azure Active Directory | Microsoft Docs"
description: "Comment remplir un domaine personnalisé dans Azure Active Directory avec des comptes d'utilisateur."
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 39cb54a6637088c35c6aef864a804c24803f48ba
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="assign-users-to-a-custom-domain"></a>Affecter des utilisateurs à un domaine personnalisé
Après avoir ajouté votre domaine personnalisé à Azure Active Directory, vous devez ajouter les comptes d'utilisateur pour ce domaine afin de pouvoir les authentifier.

> [!IMPORTANT]
> Microsoft recommande de gérer Azure AD à l’aide du [Centre d’administration Azure AD](https://aad.portal.azure.com) dans le portail Azure au lieu d’utiliser le portail Azure classique référencé dans cet article. Pour savoir comment gérer vos noms de domaine dans le centre d’administration Azure AD, consultez la section [Gestion des noms de domaines personnalisés dans Azure Active Directory](active-directory-domains-manage-azure-portal.md).

## <a name="users-synced-from-a-on-premises-directory"></a>Utilisateurs synchronisés à partir d’un répertoire local
Si vous avez déjà configuré une connexion entre un domaine Active Directory local et Azure Active Directory, la synchronisation peut remplir les comptes. Pour plus d'informations sur la synchronisation d’Azure Active Directory avec votre domaine Active Directory local, consultez [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Utilisateurs ajoutés et gérés dans le cloud
Pour modifier le domaine d'un compte d'utilisateur existant :

1. Ouvrez le portail Azure Classic à l'aide d'un compte administrateur global ou administrateur d'utilisateurs.
2. Ouvrez votre annuaire.
3. Cliquez sur l’onglet **Utilisateurs** .
4. Sélectionnez l'utilisateur dans la liste.
5. Modifiez le domaine de l'utilisateur, puis sélectionnez **Enregistrer**.

Vous pouvez également utiliser [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou [l’API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Sélectionner un domaine personnalisé lors de la création d'un nouvel utilisateur
1. Ouvrez le portail Azure Classic à l'aide d'un compte administrateur global ou administrateur d'utilisateurs.
2. Ouvrez votre annuaire.
3. Cliquez sur l’onglet **Utilisateurs** .
4. Dans la barre de commandes, sélectionnez **Ajouter**.
5. Lorsque vous ajoutez le nom d'utilisateur, choisissez le domaine personnalisé dans la liste des domaines.
6. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes
* [Utilisation de noms de domaine personnalisés pour simplifier l’expérience de connexion pour vos utilisateurs](active-directory-add-domain.md)
* [Gérer les noms de domaine personnalisés](active-directory-add-manage-domain-names.md)
* [En savoir plus sur les concepts de gestion de domaine dans Azure AD](active-directory-add-domain-concepts.md)


