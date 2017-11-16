---
title: "Rapports d’accès et d’utilisation pour Azure MFA | Microsoft Docs"
description: "Cette section décrit comment utiliser la fonctionnalité de rapports d’Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: a0ac1711b6bfb8f461cd775ed1f3409925643615
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Rapports dans Azure Multi-Factor Authentication

Azure Multi-Factor Authentication fournit plusieurs rapports qui peuvent être utilisés par vous et votre organisation. Ces rapports sont accessibles via le portail de gestion Multi-Factor Authentication. Le tableau ci-après répertorie les rapports disponibles :

| Rapport | Description |
|:--- |:--- |
| Usage |Les rapports d'utilisation affichent des informations sur l'utilisation générale, des récapitulatifs par utilisateurs et des détails sur les utilisateurs. |
| État du serveur |Ce rapport affiche l'état des serveurs Multi-Factor Authentication associés à votre compte. |
| Historique de l'utilisateur bloqué |Ces rapports présentent l'historique des demandes de blocage ou déblocage des utilisateurs. |
| Historique de l'utilisateur contourné |Affiche l'historique des demandes de contournement de Multi-Factor Authentication pour le numéro de téléphone d'un utilisateur. |
| Alerte de fraude |Affiche un historique des alertes de fraude soumises au cours de la plage de dates spécifiée. |
| Mis en file d'attente. |Répertorie les rapports en file d'attente de traitement et leur état. Un lien pour télécharger ou afficher le rapport est fourni lorsque ce dernier est terminé. |

## <a name="view-reports"></a>Afficher des rapports

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez à gauche Active Directory.
3. Suivez l’une de ces deux options, selon que vous utilisez ou non des fournisseurs d’authentification :
   * **Option 1**: cliquez sur l’onglet Fournisseurs d’authentification multifacteur. Sélectionnez votre fournisseur MFA et cliquez sur le bouton **Gérer** dans la partie inférieure.
   * **Option 2**: sélectionnez votre annuaire et accédez à l’onglet **Configurer**. Dans la section Authentification multifacteur, sélectionnez **Gérer les paramètres du service**. En bas de la page Paramètres du service MFA, cliquez sur le lien Accéder au portail.
4. Dans le portail de gestion Azure Multi-Factor Authentication, sélectionnez le type de rapport désiré dans la section **Afficher un rapport** dans le volet de navigation gauche.

<center>![Cloud](./media/multi-factor-authentication-manage-reports/report.png)</center>

## <a name="powershell-reporting"></a>Génération de rapports Powershell

Identifiez les utilisateurs qui se sont inscrits pour l’authentification multifacteur à l’aide du code Powershell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifiez les utilisateurs qui ne se sont pas inscrits pour l’authentification multifacteur à l’aide du code Powershell qui suit.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

**Ressources supplémentaires**

* [Pour les utilisateurs](end-user/multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authentication sur MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
