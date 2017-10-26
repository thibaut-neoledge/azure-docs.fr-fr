---
title: "Résolution des problèmes : Données manquantes dans les journaux d’activité Azure Active Directory téléchargés | Microsoft Docs"
description: "Fournit une résolution au problème de données manquantes dans les journaux d’activité Azure Active Directory téléchargés."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 28bf4eb46f18bf0a9b2cd8b1e7058eccfa12a88f
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2017
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-have-downloaded"></a>Aucune donnée n’apparaît dans les journaux d’activité Azure Active Directory que j’ai téléchargés


## <a name="symptoms"></a>Symptômes

J’ai téléchargé les journaux d’activité (d’audit ou de connexion) et tous les enregistrements correspondant à la période choisie n’apparaissent pas. Pourquoi ? 

 ![Reporting](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Cause :

Lorsque vous téléchargez des journaux d’activité dans le portail Azure, nous limitons l’échelle à des enregistrements de 120 Ko, triés du plus récent au moins récent. 

## <a name="resolution"></a>Résolution :

Vous pouvez tirer parti des [API de création de rapports Azure AD](active-directory-reporting-api-getting-started.md) pour extraire jusqu’à un million d’enregistrements pour un point donné. Nous vous recommandons d’exécuter un script de façon planifiée qui appelle les API de création de rapports pour extraire des enregistrements de manière incrémentielle sur une période donnée (par exemple, quotidienne ou hebdomadaire).

## <a name="next-steps"></a>Étapes suivantes
Consultez le [FAQ sur les rapports Azure Active Directory](active-directory-reporting-faq.md).

