---
title: Recherche Azure Security Center | Microsoft Docs
description: "Découvrez comment Azure Security Center utilise la recherche Log Analytics pour récupérer et analyser vos données de sécurité."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Recherche Azure Security Center
Azure Security Center utilise la [recherche Log Analytics](../log-analytics/log-analytics-log-searches.md) pour récupérer et analyser vos données de sécurité. Log Analytics inclut un langage de requête pour rapidement récupérer et consolider les données. Dans Security Center, vous pouvez utiliser la recherche Log Analytics pour construire des requêtes et analyser les données collectées.

Recherche est disponible dans le niveau Gratuit et le niveau Standard de Security Center.  Les données disponibles dans vos recherches dans les journaux dépendent du niveau appliqué à votre espace de travail.  Pour plus d’informations, consultez la [page de tarification](../security-center/security-center-pricing.md) de Security Center.


> [!NOTE]
> Security Center n’enregistre pas les données de sécurité d’un espace de travail avec le niveau Gratuit. Vous pouvez envoyer différents journaux à un espace de travail dans le niveau Gratuit et effectuer des recherches sur ces données, mais les résultats de recherche n’incluent pas de données de Security Center. Security Center enregistre uniquement les données dans un espace de travail appartenant au niveau Standard.
>
>

## <a name="access-search"></a>Accéder à la recherche
1. Dans le menu principal de Security Center, sélectionnez **Recherche**.

  ![Sélectionner Recherche dans les journaux][1]

2. Security Center répertorie tous les espaces de travail dans vos abonnements Azure. Sélectionnez un espace de travail. (Si vous avez un seul espace de travail, ce sélecteur d’espaces de travail n’apparaît pas.)

  ![Sélectionner un espace de travail][2]

3. **Recherche dans les journaux** s’ouvre. Pour interroger davantage de données dans l’espace de travail sélectionné, entrez cet exemple de requête :

  SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

  Le résultat affiche tous les comptes pour lesquels l’ouverture de session a échoué (événement 4625).

  ![Résultats de la recherche][3]

Consultez [Référence de recherche Log Analytics](../log-analytics/log-analytics-search-reference.md) pour plus d’informations sur la façon d’interroger des données dans l’espace de travail sélectionné.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment accéder à la recherche dans Security Center. Security Center utilise la recherche Log Analytics. Pour en savoir plus sur la recherche Log Analytics, consultez :

- [Présentation de Log Analytics](../log-analytics/log-analytics-overview.md) - Présentation de Log Analytics
- [Présentation des recherches dans les journaux dans Log Analytics](../log-analytics/log-analytics-log-search-new.md) - Décrit comment sont utilisées les recherches dans les journaux dans Log Analytics, et présente les concepts que vous devez comprendre avant de créer une recherche dans les journaux
- [Trouver des données avec les recherches de journaux dans Log Analytics](../log-analytics/log-analytics-log-searches.md) - Didacticiel sur l’utilisation de la recherche dans les journaux.
- [Référence de recherche Log Analytics](../log-analytics/log-analytics-search-reference.md) - Décrit le langage de requête dans Log Analytics.

Pour plus d’informations sur Security Center, consultez :

- [Présentation de Security Center](security-center-intro.md) - Décrit les fonctionnalités clés de Security Center.

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
