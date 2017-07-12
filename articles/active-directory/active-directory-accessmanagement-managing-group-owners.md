---
title: "Étapes suivantes de la gestion des accès à l’aide de groupes | Microsoft Docs"
description: "Procédures avancées concernant la gestion des groupes de sécurité et l’utilisation de ces groupes pour gérer l’accès à une ressource."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 44350a3c-8ea1-4da1-aaac-7fc53933dd21
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8c4920107b5d05716021bcef80d3c52c25f6eade
ms.contentlocale: fr-fr
ms.lasthandoff: 12/28/2016


---
<a id="managing-owners-for-a-group" class="xliff"></a>

# Gestion des propriétaires d’un groupe
Une fois que le propriétaire d'une ressource a attribué l'accès à une ressource à un groupe Azure AD, l'appartenance au groupe est gérée par le propriétaire du groupe. Le propriétaire de la ressource délègue effectivement au propriétaire du groupe l’autorisation d’affecter des utilisateurs aux ressources.

<a id="assigning-group-ownership" class="xliff"></a>

## Affectation de l'appartenance de groupe
**Pour ajouter un propriétaire à un groupe**

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis ouvrez le répertoire de votre organisation.
2. Sélectionnez l’onglet **Groupes** , puis ouvrez le groupe auquel vous souhaitez ajouter des propriétaires.
3. Sélectionnez **Ajouter des propriétaires**.
4. Sur la page **Ajouter des propriétaires**, sélectionnez l’utilisateur à ajouter en tant que propriétaire de ce groupe, puis vérifiez que son nom a été ajouté au volet **Sélectionné**.

**Pour supprimer un propriétaire d'un groupe**

1. Dans le [portail Azure Classic](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis ouvrez le répertoire de votre organisation.
2. Sélectionnez l’onglet **Groupes** , puis ouvrez le groupe duquel vous souhaitez supprimer un propriétaire.
3. Sélectionnez l’onglet **Propriétaires** .
4. Sélectionnez le propriétaire que vous souhaitez supprimer de ce groupe, puis choisissez **Supprimer**.

<a id="additional-information" class="xliff"></a>

## Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources avec les groupes Azure Active Directory](active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
* [Intégration des identités locales dans Azure Active Directory](active-directory-aadconnect.md)


