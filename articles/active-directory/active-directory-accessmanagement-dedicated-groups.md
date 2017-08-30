---
title: "Groupes dédiés dans Azure Active Directory | Microsoft Docs"
description: "Vue d’ensemble du fonctionnement et de la création des groupes dédiés dans Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 86158909-083a-41fe-8090-955e96ad1865
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: rodejo
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: a4a78b92c8bb9e8aff25fd724ed78283de8f2fd8
ms.openlocfilehash: 92b9c88ec49424c96c3bd21bc5c4ce390352c17b
ms.contentlocale: fr-fr
ms.lasthandoff: 02/13/2017

---
# <a name="dedicated-groups-in-azure-active-directory"></a>Groupes dédiés dans Azure Active Directory
Dans Azure Active Directory (Azure AD), la fonctionnalité des groupes dédiés crée et remplit automatiquement l’appartenance aux groupes Azure AD prédéfinis. Les membres des groupes dédiés ne peuvent pas être ajoutés, ni supprimés via le portail Azure Classic, les applets Windows PowerShell ou via un programme.

> [!NOTE]
> Les groupes dédiés nécessitent qu’une licence Azure AD Premium soit affectée à
>
> * l’administrateur qui gère la règle sur un groupe
> * tous les utilisateurs qui sont sélectionnés par la règle pour être membres du groupe
>
>

**Pour activer les groupes dédiés**

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis ouvrez le répertoire de votre organisation.
2. Sélectionnez l’onglet **Groupes** , puis ouvrez le groupe que vous souhaitez modifier.
3. Sélectionnez l’onglet **Configurer**, puis définissez **Activer les groupes dédiés** sur **Oui**.

Une fois que l’option Activer les groupes dédiés est définie sur **Oui**, vous pouvez configurer l’annuaire pour qu’il crée automatiquement le groupe dédié Tous les utilisateurs. Pour ce faire, définissez l’option **Activer le groupe Tous les utilisateurs** sur **Oui**. Vous pouvez ensuite également modifier le nom de ce groupe dédié, en effectuant votre saisie dans le champ **Nom d’affichage du groupe Tous les utilisateurs**.

Le groupe Tous les utilisateurs peut vous permettre d’attribuer les mêmes autorisations à l’ensemble des utilisateurs de votre répertoire. Par exemple, vous pouvez autoriser l’ensemble des utilisateurs de votre répertoire à accéder à l’application SaaS, en attribuant l’accès à cette application au groupe dédié Tous les utilisateurs.

Le groupe « Tous les utilisateurs » dédié inclut tous les utilisateurs du répertoire, dont les invités et les utilisateurs externes. Si vous avez besoin d’un groupe qui exclut les utilisateurs externes, vous pouvez créer un groupe avec une règle dynamique basée sur attribut telle que la suivante :

                (user.userPrincipalName -notContains "#EXT#@")

Pour un groupe qui exclut tous les invités, utilisez une règle telle que la suivante :

                (user.userType -ne "Guest")

Pour en savoir plus sur la création de règles *avancées* (règles pouvant contenir plusieurs comparaisons) pour l’appartenance dynamique à un groupe, consultez la page [Utilisation d’attributs pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md).

### <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)

