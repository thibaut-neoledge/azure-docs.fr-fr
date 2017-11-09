---
title: "Surveillance et traitement des événements de sécurité dans Azure Security Center | Microsoft Docs"
description: "Découvrez comment vous pouvez utiliser le tableau de bord des événements de Security Center pour voir les événements de sécurité de vos machines virtuelles Azure et ordinateurs autres qu’Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: terrylan
ms.openlocfilehash: 367067874b167268bd690a9e0b55412e92e08122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Surveillance et traitement des événements de sécurité dans Azure Security Center
Le tableau de bord Événements fournit une vue d’ensemble sur le nombre d’événements de sécurité collectés au fil du temps et une liste d’événements notables qui peuvent nécessiter votre attention.  

> [!NOTE]
> Pour utiliser cette fonctionnalité, votre espace de travail doit exécuter Log Analytics version 2 et se trouver dans le niveau Standard de Security Center. Pour plus d’informations sur le niveau Standard, consultez la [page de tarification](security-center-pricing.md) de Security Center.
>
>

## <a name="what-is-a-security-event"></a>Qu’est-ce qu’un événement de sécurité ?
Security Center utilise Microsoft Monitoring Agent pour collecter différents événements et configurations liées à la sécurité à partir de vos machines et stocke ces événements dans vos espaces de travail. Ces données peuvent être : des journaux de système d’exploitation (journaux d’événements Windows), des processus d’exécution et des événements de solutions de sécurité intégrées à Security Center. Microsoft Monitoring Agent copie également les fichiers de vidage sur incident dans vos espaces de travail.

## <a name="events-processed-dashboard"></a>Tableau de bord Événements traités
Vous accédez au tableau de bord **Événements** à partir du menu principal de Security Center ou du panneau **Vue d’ensemble** de Security Center.  

![Tableau de bord Événements traités][1]

La mosaïque **Événements** dans **Security Center** affiche le nombre d’événements transférés à Security Center à partir de vos machines virtuelles Azure et ordinateurs autres qu’Azure.

Le **tableau de bord Événements** fournit une vue d’ensemble sur le nombre d’événements traités au fil du temps et une liste d’événements.

 ![tableau de bord][2]

 La partie supérieure du tableau de bord présente tous les événements traités la semaine passée. Quant à la partie inférieure du tableau de bord, elle répertorie les événements notables et tous les événements par type :

 - **Notable events** (Événements notables) inclut des requêtes d’événement fournies par Security Center, ainsi que des requêtes d’événement que vous créez et ajoutez. Le tableau de bord fournit également un aperçu rapide sur la quantité de chaque événement notable.
 - **All events by type** (Tous les événements par type) affiche les types d’événements reçus et la quantité de chaque type. SecurityEvent, CommonSecurityLog, WindowsFirewall et W3CIISLog sont des exemples de type d’événement.

> [!NOTE]
> Les événements notables incluent [l’évaluation de la base de référence web](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). L’objectif d’une évaluation de la base de référence web consiste à identifier les paramètres de serveur web potentiellement vulnérables.

## <a name="view-processed-event-details"></a>Afficher les détails d’un événement traité
1. Dans le menu principal de **Security Center**, sélectionnez **Événements**.
2. Le sélecteur d’espaces de travail du **tableau de bord Événements** peut s’ouvrir. Si vous avez un seul espace de travail, ce sélecteur d’espaces de travail n’apparaît pas. Si vous avez plusieurs espaces de travail, vous devez sélectionner un espace de travail pour afficher les détails de son événement traité. Sélectionnez un espace de travail dans la liste, si vous avez plusieurs espaces de travail.

  ![Liste d’espaces de travail][3]

3. Le **tableau de bord Événements** s’ouvre pour afficher les détails de l’événement de l’espace de travail sélectionné. Vous pouvez afficher les événements notables et tous les événements par type.  Dans cet exemple, nous avons sélectionné **Notable events** (Événements notables).

  ![Événement notable][4]

4. Vous pouvez rechercher davantage de données dans l’espace de travail en sélectionnant un type d’événement. Dans cet exemple, nous avons sélectionné **SecurityEvent**.

  ![Sélection d’un type d’événement][5]

5. **Recherche dans les journaux** s’ouvre avec des détails supplémentaires sur le type d’événement.

  ![Recherche dans les journaux][6]

## <a name="add-a-notable-event"></a>Ajouter un événement notable
Security Center fournit des événements notables prêts à l’emploi. Vous pouvez ajouter des événements notables en fonction de votre propre requête à l’aide du [langage de requête Log Analytics](../log-analytics/log-analytics-search-reference.md). Revenons au **tableau de bord Événements** pour ajouter un événement notable.

1. Sélectionnez **Add Notable Event** (Ajouter un événement notable).

  ![Ajouter un événement notable][7]

2. **Add custom notable event** (Ajouter un événement notable personnalisé) s’ouvre.  Dans **Nom d’affichage**, entrez un nom pour votre événement notable. Dans **Requête de recherche**, entrez votre requête pour l’événement.

  ![Entrer votre requête][8]

4. Sélectionnez **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Mettre à jour votre espace de travail pour le traitement des événements
Votre espace de travail doit exécuter Log Analytics version 2 et se trouver dans le niveau Standard de Security Center pour utiliser le traitement des événements dans Security Center. Le sélecteur d’espaces de travail du **tableau de bord Événements** identifie les espaces de travail qui ne respectent pas ces exigences.

![Espace de travail ne répondant pas aux exigences][9]

Si la ligne de l’espace de travail :

- Contient **REQUIRES UPDATE** (MISE À JOUR NÉCESSAIRE), vous devez mettre à jour votre espace de travail vers Log Analytics version 2.
- Contient **METTRE À NIVEAU LE PLAN**, vous devez mettre à niveau votre espace de travail vers le niveau Standard de Security Center.
- Est vide, cela signifie que votre espace de travail répond aux exigences. Si vous cliquez sur un espace de travail, vous être redirigé vers le tableau de bord.

> [!NOTE]
> Dans le **tableau de bord Événements**, la colonne **ÉVÉNEMENTS** indique le nombre d’événements dans chaque espace de travail.  Cette colonne est vide pour certains espaces de travail, car le niveau Gratuit de Security Center est appliqué à cet espace de travail. Dans le niveau Gratuit, Security Center collecte les événements, mais ceux-ci ne sont pas enregistrés dans Log Analytics et ne sont pas disponibles dans le tableau de bord.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Mettre à jour l’espace de travail vers Log Analytics version 2
1. Sélectionnez un espace de travail avec le statut **REQUIRES UPDATE** (MISE À JOUR NÉCESSAIRE).
2. **Rechercher une mise à niveau** s’ouvre. Sélectionnez **Upgrade Now** (Mettre à niveau maintenant).

  ![Mettre à niveau maintenant][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Mettre à niveau vers le niveau Standard de Security Center
1. Sélectionnez un espace de travail avec le statut **METTRE À NIVEAU LE PLAN**.
2. **Le tableau de bord Événements** s’ouvre. Sélectionnez le **tableau de bord Try the Events** (Essayer les événements).

  ![Tableau de bord Essayer][11]

3. Dans **Intégration de la sécurité avancée**, sélectionnez l’espace de travail que vous mettez à niveau.
4. Dans **Tarification**, sélectionnez **Standard**.
5. Sélectionnez **Enregistrer**.

  ![Mettre à niveau vers le niveau Standard][12]

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez découvert comment utiliser le tableau de bord Événements de Security Center. Pour en savoir plus sur le fonctionnement du tableau de bord et écrire vos propres requêtes d’événements, consultez :

- [Présentation de Log Analytics](../log-analytics/log-analytics-overview.md) - Présentation de Log Analytics
- [Présentation des recherches dans les journaux dans Log Analytics](../log-analytics/log-analytics-log-search-new.md) - Décrit comment sont utilisées les recherches dans les journaux dans Log Analytics, et présente les concepts que vous devez comprendre avant de créer une recherche dans les journaux
- [Référence de recherche Log Analytics](../log-analytics/log-analytics-search-reference.md) - Découvrez comment écrire vos propres requêtes d’événements à l’aide du langage de requête dans Log Analytics.

Pour plus d’informations sur Security Center, consultez :

- [Présentation de Security Center](security-center-intro.md) - Décrit les fonctionnalités clés de Security Center.

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
