---
title: Forum aux questions pour la nouvelle recherche dans les journaux Log Analytics | Documents Microsoft
description: "Cet article fournit le forum aux questions relatives à la mise à niveau de Log Analytics au nouveau langage de requête."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Forum aux questions pour la nouvelle recherche dans les journaux Log Analytics

Cet article fournit le forum aux questions relatives à la mise à niveau de [Log Analytics au nouveau langage de requête](log-analytics-log-search-upgrade.md).



## <a name="upgrade-process"></a>Mise à niveau

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Je dispose de plusieurs espaces de travail. Puis-je mettre à niveau tous mes espaces de travail simultanément ?  
R : Non.  La mise à niveau s’applique à un seul espace de travail à la fois. Actuellement, il n’existe aucun moyen de la mise à niveau de nombreux espaces de travail à la fois. Notez que les autres utilisateurs de l’espace de travail mis à niveau seront également affectés.  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Les données de journal collectées dans mon espace de travail seront-elles modifiées lors de la mise à niveau ?  
Non. Les données de journal disponibles pour vos recherches d’espace de travail ne sont pas affectées par la mise à niveau. Les recherches enregistrées, les alertes et les vues seront converties vers le nouveau langage de recherche automatiquement.  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>Que se passe-t-il si je ne mets à niveau mon espace de travail ?  
La recherche de journal héritée sera déconseillée dans les prochains mois. Les espaces de travail qui ne sont pas mis à niveau à ce moment-là seront automatiquement mis à niveau.

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>Je n’ai pas choisi la mise à niveau, mais mon espace de travail a quand même été mis à niveau ! Que s’est-il passé ?  
Un autre administrateur de cet espace de travail peut l’avoir mis à niveau. Veuillez noter que tous les espaces de travail sont mis à niveau automatiquement lorsque la nouvelle langue est rendue publique.  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>J’ai effectué la mise à niveau par erreur et à présent, je dois l’annuler et tout restaurer. Que dois-je faire ?  
Pas de problème.  Nous créons une capture instantanée de votre espace de travail avant la mise à niveau, afin de pouvoir le restaurer. Toutefois, gardez à l’esprit que les recherches, alertes ou images que vous avez enregistrées après la mise à niveau seront perdues.  Pour restaurer l’environnement de votre espace de travail, suivez la procédure de la section [Puis-je revenir en arrière après la mise à niveau ?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade).



## <a name="log-searches"></a>Recherches dans les journaux

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>J’ai enregistré des recherches en dehors de mon espace de travail mis à niveau. Puis-je les convertir automatiquement en nouveau langage de recherche ?
Vous pouvez utiliser l’outil de conversion de langage dans la page de recherche de journal pour convertir chacune d’elles.  Il n’existe aucune méthode pour la conversion automatique de plusieurs recherches sans mise à niveau de l’espace de travail.


## <a name="alerts"></a>Alertes

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>J’ai un grand nombre de règles d’alerte. Dois-je les recréer dans la nouvelle langue après la mise à niveau ?  
Non, vos règles d’alerte sont automatiquement converties en nouveau langage de recherche pendant la mise à niveau.  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>Existe-t-il des changements suite à l’intégration de Power BI ?
Oui.  Une fois votre espace de travail mis à niveau, puis le processus d’exportation des données de Log Analytics à Power BI ne fonctionne plus.  Toutes les planifications existantes que vous avez créées avant la mise à niveau sont alors désactivées.  Après la mise à niveau, Azure Log Analytics utilise la même plateforme qu’Application Insights et vous devez utiliser le même processus vous permettant d’exporter des requêtes de Log Analytics vers Power BI comme [processus d’exportation des requêtes d’Application Insights vers Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

## <a name="dashboards"></a>Tableaux de bord

### <a name="can-i-still-use-dashboards"></a>Puis-je continuer à utiliser les tableaux de bord ?
Vous pouvez continuer à utiliser les tableaux de bord que vous avez créés avant la mise à niveau de votre espace de travail. Toutefois, vous ne pouvez pas les modifier ni en créer de nouveaux.  Vous pouvez continuer à créer et à modifier des vues à l’aide du [Concepteur de vues](log-analytics-view-designer.md). 

## <a name="next-steps"></a>Étapes suivantes

- Plus d’informations sur la [mise à niveau de votre espace de travail vers le nouveau langage de requête de Log Analytics](log-analytics-log-search-upgrade.md).

