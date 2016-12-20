---
title: "Créer un rapport d’historique des modifications d’accès | Microsoft Docs"
description: "Générez un rapport qui répertorie toutes les modifications d’accès à vos abonnements Azure avec contrôle d’accès basé sur les rôles au cours des 90 derniers jours."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 44295ff647cbfd2d63ffe08d101da66b83a924f6


---
# <a name="create-an-access-change-history-report"></a>Créer un rapport d’historique des modifications d’accès
Chaque fois qu’un utilisateur autorise ou interdit l’accès dans vos abonnements, les modifications sont consignées dans les événements Azure. Vous pouvez créer des rapports d’historique de modification d’accès pour voir toutes les modifications apportées au cours des 90 derniers jours.

## <a name="create-a-report-with-azure-powershell"></a>Créer un rapport avec Azure PowerShell
Pour créer un rapport d’historique des modifications d’accès dans PowerShell, utilisez la commande `Get-AzureRMAuthorizationChangeLog` . Vous trouverez plus d'informations sur cette applet de commande dans la [Galerie PowerShell](https://www.powershellgallery.com/packages/AzureRM.Storage/1.0.6/Content/ResourceManagerStartup.ps1).

Lorsque vous appelez cette commande, vous pouvez spécifier quelle propriété des affectations répertorier, y compris les suivantes :

| Propriété | Description |
| --- | --- |
| **Action** |Si l’accès a été autorisé ou interdit |
| **Appelant** |Le propriétaire responsable de la modification d’accès |
| **Date** |La date et l’heure de la modification d’accès |
| **DirectoryName** |Le répertoire Azure Active Directory |
| **PrincipalName** |Le nom de l’utilisateur, du groupe ou de l’application |
| **PrincipalType** |Si l’affectation était pour un utilisateur, un groupe ou une application |
| **RoleId** |Le GUID du rôle qui a été accordé ou refusé |
| **RoleName** |Le rôle qui a été accordé ou refusé |
| **ScopeName** |Le nom de l’abonnement, du groupe de ressources ou de la ressource |
| **ScopeType** |Si l’étendue de l’affectation était au niveau de l’abonnement, du groupe de ressources ou de la ressource |
| **SubscriptionId** |Le GUID de l’abonnement Azure |
| **SubscriptionName** |Le nom de l’abonnement Azure |

Cet exemple de commande répertorie toutes les modifications d’accès de l’abonnement au cours des sept derniers jours :

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - capture d’écran](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Créer un rapport avec l’interface de ligne de commande Azure
Pour créer un rapport d’historique des modifications d’accès dans l’interface de ligne de commande Azure, utilisez la commande `azure role assignment changelog list` .

## <a name="export-to-a-spreadsheet"></a>Exporter vers une feuille de calcul
Pour enregistrer le rapport ou manipuler les données, exportez les modifications d’accès vers un fichier .csv. Vous pouvez ensuite afficher le rapport dans une feuille de calcul pour révision.

![ChangeLog affiché en tant que feuille de calcul - capture d’écran](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="see-also"></a>Voir aussi
* Prise en main du [contrôle d’accès basé sur les rôles Azure](role-based-access-control-configure.md)
* Utilisation des [rôles personnalisés dans le contrôle d’accès en fonction du rôle (RBAC) Azure](role-based-access-control-custom-roles.md)




<!--HONumber=Nov16_HO3-->


