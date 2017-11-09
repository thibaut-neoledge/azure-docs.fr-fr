---
title: "Expiration des groupes Office 365 en préversion dans Azure Active Directory | Microsoft Docs"
description: "Comment configurer l’expiration des groupes Office 365 dans Azure Active Directory (préversion)"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8a43df84fd050d7b4bd8d937b8c55e744cb805d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-office-365-groups-expiration-preview"></a>Configurer l’expiration des groupes Office 365 (préversion)

Vous pouvez désormais gérer le cycle de vie des groupes Office 365 en définissant l’expiration pour les groupes Office 365 que vous sélectionnez. Une fois cette expiration définie, les propriétaires de ces groupes sont invités à renouveler leurs groupes s’ils en ont toujours besoin. Les groupes Office 365 qui ne sont pas renouvelés seront supprimés. Les groupes Office 365 qui ont été supprimés peuvent être restaurés dans les 30 jours qui suivent par les propriétaires des groupes ou l’administrateur.  


> [!NOTE]
> Vous pouvez définir l’expiration pour les groupes Office 365 uniquement.
>
> La définition de l’expiration pour les groupes O365 nécessite qu’une licence Azure AD Premium soit affectée à
>   - l’administrateur qui configure les paramètres d’expiration pour le locataire ;
>   - tous les membres des groupes sélectionnés pour ce paramètre.

## <a name="set-office-365-groups-expiration"></a>Définir l’expiration des groupes Office 365

1. Ouvrez le [centre d’administration Azure AD](https://aad.portal.azure.com) avec un compte qui est un administrateur général dans votre locataire Azure AD.

2. Ouvrez Azure AD et sélectionnez **Utilisateurs et groupes**.

3. Sélectionnez **Paramètres de groupe**, puis **Expiration** pour ouvrir les paramètres d’expiration.
  
  ![Panneau Expiration](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Dans le panneau **Expiration**, vous pouvez :

  * Définir la durée de vie du groupe en jours. Vous pouvez sélectionner l’une des valeurs prédéfinies ou une valeur personnalisée (31 jours ou plus). 
  * Spécifier une adresse de messagerie à laquelle les notifications de renouvellement et d’expiration doivent être envoyées lorsqu’un groupe n’a pas de propriétaire. 
  * Sélectionnez les groupes Office 365 qui expirent. Vous pouvez activer l’expiration pour **tous** les groupes Office 365, vous pouvez sélectionner certains groupes Office 365, ou vous pouvez sélectionner **Aucun** pour désactiver l’expiration de tous les groupes.
  * Enregistrer vos paramètres lorsque vous avez terminé en sélectionnant **Enregistrer**.

Pour obtenir des instructions sur la façon de télécharger et d’installer le module Microsoft PowerShell pour configurer l’expiration des groupes Office 365 via PowerShell, consultez [Azure Active Directory V2 PowerShell Module - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Module Azure Active Directory V2 PowerShell - Préversion publique 2.0.0.137).

Les notifications par e-mail comme celle-ci sont envoyées aux propriétaires de groupes Office 365 30 jours, 15 jours et 1 jour avant l’expiration du groupe.

![Notification par e-mail de l’expiration](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Le propriétaire du groupe peut ensuite sélectionner **Renew group** (Renouveler le groupe) pour renouveler le groupe Office 365, ou vous pouvez sélectionner **Accéder au groupe** pour voir les membres et d’autres détails sur le groupe.

Quand un groupe expire, il est supprimé un jour après la date d’expiration. Une notification par e-mail comme celle-ci est envoyée aux propriétaires de groupes Office 365 pour les informer de l’expiration et de la suppression qui en découle de leur groupe Office 365.

![Notification par e-mail de la suppression du groupe](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Le groupe peut être restauré en sélectionnant **Restore group** (Restaurer le groupe) ou à l’aide des cmdlets PowerShell, comme décrit dans [Restaurer un groupe Office 365 supprimé dans Azure Active Directory] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Si le groupe que vous restaurez contient des documents, des sites SharePoint ou d’autres objets persistants, la restauration du groupe et de son contenu peut nécessiter jusqu’à 24 heures.

> [!NOTE]
> * Lorsque vous déployez les paramètres d’expiration, il peut y avoir certains groupes plus anciens que la fenêtre d’expiration. Ces groupes ne sont pas supprimés immédiatement, mais leur suppression est définie sur 30 jours jusqu’à l’expiration. La première notification par e-mail est envoyée dans la journée qui suit. Par exemple, le groupe A a été créé il y a 400 jours et l’intervalle d’expiration est défini sur 180 jours. Lorsque vous appliquez les paramètres d’expiration, il reste 30 jours au groupe A avant qu’il ne soit supprimé, sauf si le propriétaire le renouvelle.
> * Lorsqu’un groupe dynamique est supprimé et restauré, il est considéré comme un groupe nouveau, complété conformément à la règle. Ce processus peut prendre jusqu’à 24 heures.

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur les groupes Azure AD.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)
