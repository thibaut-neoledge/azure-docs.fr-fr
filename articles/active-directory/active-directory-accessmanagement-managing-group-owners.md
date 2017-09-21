---
title: "Gestion des propriétaires de groupe dans Azure Active Directory | Microsoft Docs"
description: "Gestion des propriétaires de groupe et utilisation de ces groupes pour gérer l’accès à une ressource."
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
ms.date: 09/13/2017
ms.author: curtand
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 70be82fdd673c4f245e306b1e1cfdcd94d4dac53
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="managing-owners-for-a-group"></a>Gestion des propriétaires d’un groupe
Une fois que le propriétaire d’une ressource a attribué l’accès à une ressource à un groupe Azure AD, l’appartenance au groupe est gérée par le propriétaire du groupe. Le propriétaire de la ressource délègue effectivement au propriétaire du groupe l’autorisation d’affecter des utilisateurs aux ressources.

## <a name="add-an-owner-to-a-group"></a>Ajouter un propriétaire à un groupe

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Utilisateurs et groupes**.
2. Sélectionnez **Tous les groupes**, puis ouvrez le groupe auquel vous voulez ajouter des propriétaires.
3. Sélectionnez **Propriétaires**, puis sélectionnez **Ajouter des propriétaires**.
4. Dans la page **Ajouter des propriétaires**, sélectionnez l’utilisateur à ajouter en tant que propriétaire de ce groupe, puis cliquez ou appuyez sur **Sélectionner**. 

## <a name="remove-an-owner-from-a-group"></a>Supprimer un propriétaire d’un groupe

1. Dans le [centre d’administration Azure AD](https://aad.portal.azure.com), sélectionnez **Utilisateurs et groupes**.
2. Sélectionnez **Tous les groupes**, puis ouvrez le groupe duquel vous voulez supprimer des propriétaires.
3. Sélectionnez **Propriétaires**, sélectionnez le propriétaire à supprimer de ce groupe, puis cliquez ou appuyez sur **Sélectionner**.
4. Dans le volet ouvert du propriétaire sélectionné, sélectionnez **Supprimer**.

## <a name="additional-information"></a>Informations supplémentaires
Ces articles fournissent des informations supplémentaires sur les groupes Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Création d’un nouveau groupe et ajout de membres](active-directory-groups-create-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)

