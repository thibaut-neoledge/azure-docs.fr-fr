---
title: "Restaurer un groupe Office 365 supprimé dans la préversion d’Azure Active Directory | Microsoft Docs"
description: "Comment restaurer un groupe supprimé, afficher les groupes pouvant être restaurés et supprimer de façon permanente un groupe dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 4e2fe22953ff74214c0c20fdf2fabceeda63be35
ms.lasthandoff: 04/27/2017


---

# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Restaurer un groupe Office 365 supprimé dans Azure Active Directory

Lorsque vous supprimez un groupe Office 365 dans la préversion d’Azure Active Directory (Azure AD), celui-ci est conservé, mais il n’est pas visible pendant 30 jours à partir de la date de suppression. De cette façon, le groupe et son contenu peuvent être restaurés au besoin. Cette fonctionnalité est limitée exclusivement aux groupes Office 365 dans Azure AD. Elle n’est pas disponible pour les groupes de sécurité et les groupes de distribution.

Les autorisations requises pour restaurer un groupe peuvent être les suivantes :

Rôle  | Autorisations 
--------- | ---------
Administrateur de la société, Prise en charge du partenaire de niveau 2 et Administrateurs de services InTune | Peut restaurer n’importe quel groupe Office 365 supprimé 
Administrateur de compte d’utilisateur et Prise en charge du partenaire de niveau 2 | Peut restaurer n’importe quel groupe Office 365 supprimé, à l’exception de ceux affectés au rôle Administrateur de la société 
Utilisateur | Peut restaurer n’importe quel groupe Office 365 supprimé dont il est propriétaire 


## <a name="how-to-view-deleted-office-365-groups-that-are-available-to-restore"></a>Affichage des groupes Office 365 supprimés disponibles pour la restauration
Les applets de commande suivantes peuvent servir à afficher les groupes supprimés pour vérifier que ceux qui vous intéressent n’ont pas encore été supprimés de façon définitive. Ces applets de commande font partie du [module Azure Active Directory PowerShell V2 Preview](https://www.powershellgallery.com/packages/AzureADPreview). Pour plus d’informations sur ce module, consultez l’article [Azure Active Directory PowerShell Version 2](/powershell/azure/install-adv2?view=azureadps-2.0).
Notez que les applets de commande pour la gestion de la suppression réversible et de la récupération sont en préversion publique et que nous devons parfois apporter des modifications majeures aux applets de commande en préversion. Pour cette raison, l’utilisation de ces applets de commande dans un environnement de production est déconseillée.

1.    Exécutez l’applet de commande suivante pour afficher tous les groupes Office 365 supprimés dans votre locataire et dont la restauration est toujours possible.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.    Ou, si vous connaissez l’ID d’objet d’un groupe spécifique (que vous pouvez obtenir à partir de l’applet de commande indiquée à l’étape 1), exécutez l’applet de commande suivante pour vérifier que le groupe supprimé spécifique n’a pas encore été supprimé de façon définitive.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-an-office-365-group"></a>Restauration d’un groupe Office 365
Une fois que vous avez vérifié que le groupe est toujours disponible pour la restauration, restaurez-le en effectuant l’une des étapes suivantes. Si le groupe contient des documents, des sites SP ou d’autres objets persistants, la restauration du groupe et de son contenu peut nécessiter jusqu’à 24 heures.

1.    Exécutez l’applet de commande suivante pour restaurer le groupe et son contenu.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

Vous pouvez également exécuter l’applet de commande suivante pour supprimer définitivement le groupe supprimé.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>Comment savez-vous si l’opération a fonctionné ?
Pour vérifier que vous avez correctement restauré un groupe Office 365, exécutez l’applet de commande `Get-AzureADGroup –ObjectId <objectId>` pour afficher des informations sur le groupe. Une fois que vous avez effectué la demande de restauration :
- Le groupe s’affiche dans la barre de navigation de gauche dans Exchange.
- Le plan associé au groupe apparaît dans le planificateur.
- Les sites Sharepoint et tout leur contenu sont disponibles.
- Le groupe est accessible à partir des points de terminaison Exchange et des autres charges de travail Office 365 qui prennent en charge les groupes Office 365.


## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur les groupes Azure Active Directory.

* [Consulter les groupes existants](active-directory-groups-view-azure-portal.md)
* [Gérer les paramètres d’un groupe](active-directory-groups-settings-azure-portal.md)
* [Gérer les membres d’un groupe](active-directory-groups-members-azure-portal.md)
* [Gérer l’appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
* [Gérer les règles dynamiques pour les utilisateurs dans un groupe](active-directory-groups-dynamic-membership-azure-portal.md)

