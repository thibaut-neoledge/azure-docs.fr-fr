---
title: "Affecter des utilisateurs à un domaine personnalisé dans Azure Active Directory | Microsoft Docs"
description: "Comment remplir un domaine personnalisé dans Azure Active Directory avec des comptes d&quot;utilisateur."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 717b5a7c-7bc3-4ab1-98b5-4740b53338fe
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2016
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ccfa8d72b51c43e04d924d2b096e3cbc407af043
ms.contentlocale: fr-fr
ms.lasthandoff: 12/28/2016


---
# <a name="assign-users-to-a-custom-domain"></a>Affecter des utilisateurs à un domaine personnalisé
Après avoir ajouté votre domaine personnalisé à Azure Active Directory, vous devez ajouter les comptes d'utilisateur pour ce domaine afin de pouvoir les authentifier.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Utilisateurs synchronisés à partir d’un répertoire de votre réseau d'entreprise
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


