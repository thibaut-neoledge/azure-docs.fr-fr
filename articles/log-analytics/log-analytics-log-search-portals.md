---
title: "Portails servant à la création et la modification des requêtes de journal dans Azure Log Analytics | Microsoft Docs"
description: "Cet article décrit les portails que vous pouvez utiliser dans Azure Log Analytics pour créer et modifier les recherches dans les journaux."
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
ms.date: 07/25/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: b415141f152e88e13678c464eddc4f73df2da54e
ms.contentlocale: fr-fr
ms.lasthandoff: 07/28/2017

---
# <a name="portals-for-creating-and-editing-log-queries-in-azure-log-analytics"></a>Portails servant à la création et la modification des requêtes de journal dans Azure Log Analytics

> [!NOTE]
> Cet article décrit les portails d’Azure Log Analytics utilisant le nouveau langage de requête.  Vous pouvez en savoir plus sur ce nouveau langage et obtenir la procédure permettant de mettre à niveau votre espace de travail à [Mise à niveau de votre espace de travail Azure Log Analytics vers la nouvelle recherche dans les journaux](log-analytics-log-search-upgrade.md).  
>
> Si votre espace de travail n’a pas été mis à niveau vers le nouveau langage de requête, référez-vous à la section [Trouver des données à l’aide des recherches dans les journaux de Log Analytics](log-analytics-log-searches.md) pour plus d’informations sur la version actuelle du portail de recherche dans les journaux.

Utilisez les recherches dans les journaux à plusieurs emplacements de Log Analytics pour récupérer des données à partir de l’espace de travail.  Toutefois, pour la création et la modification de requêtes, ainsi que le travail interactif avec des données renvoyées, vous disposez des deux options décrites ci-dessous.  

## <a name="log-search-portal"></a>Portail de recherche dans les journaux
Le portail de recherche dans les journaux est accessible à partir du portail Azure ou du portail OMS.  Il convient pour la création de requêtes de base pouvant être créées sur une seule ligne.  Il est possible d’utiliser le portail de recherche dans les journaux sans lancer un portail externe. Vous pouvez en outre l’utiliser pour exécuter diverses fonctions avec des recherches dans les journaux, y compris la création de règles d’alerte, la création de groupes d’ordinateurs et l’exportation de résultats de la requête.  

Le portail de recherche dans les journaux fournit plusieurs fonctionnalités permettant de modifier la requête sans avoir une connaissance complète du langage de requête.  Vous pouvez obtenir un résumé de ces fonctionnalités dans la section [Créer des recherches dans les journaux dans Azure Log Analytics à l’aide du portail de recherche dans les journaux](log-analytics-log-search-log-search-portal.md).


![Portail de recherche dans les journaux](media/log-analytics-log-search-portals/log-search-portal.png)

## <a name="advanced-analytics-portal"></a>Portail Analytics avancé
Le portail Analytics avancé est un portail dédié qui fournit des fonctionnalités avancées non disponibles dans le portail de recherche dans les journaux.  Ces fonctionnalités incluent la possibilité de modifier une requête sur plusieurs lignes et d’exécuter de façon sélective du code, des données Intellisense contextuelles et Smart Analytics.  Le portail Analytics avancé est plus approprié pour la conception des requêtes complexes qui sont soit enregistrées sous la forme d’une recherche dans les journaux soit copiées et collées dans d’autres éléments de Log Analytics.  Lancez le portail Analytics avancé à partir d’un lien du portail de recherche dans les journaux.

![Portail Analytics avancé](media/log-analytics-log-search-portals/advanced-analytics-portal.png)


En raison de ses fonctionnalités avancées, vous utiliserez généralement le portail Analytics avancé comme votre principal outil de création et modification des requêtes.  Une fois rassuré que la requête fonctionne comme prévu, copiez/collez-la à un autre emplacement, notamment le portail de recherche dans les journaux ou le concepteur de vues.  Étant donné que le portail avancé Analytics avancé prend en charge les requêtes avec plusieurs lignes, vous devez prendre les éléments suivants en considération lors de la copie d’une requête à partir de ce portail.

- Supprimez les commentaires à partir de la requête avant de la copier dans un autre emplacement.  Vous pouvez commenter une ligne en la précédant de deux barres obliques (//).  Lorsque vous collez une requête de lignes multiples dans une seule ligne, les sauts de ligne sont supprimés.  Si les commentaires sont inclus, tous les caractères suivant le premier commentaire sont considérés comme faisant partie du commentaire.


## <a name="next-steps"></a>Étapes suivantes

- Consultez un didacticiel sur l’utilisation du [portail de recherche dans les journaux](log-analytics-log-search-log-search-portal.md) ou du [portail Analytics avancé](https://docs.loganalytics.io/learn/tutorial_getting_started_with_analytics_portal.html) pour créer des requêtes.
- Consultez un [didacticiel sur l’écriture de requêtes](https://docs.loganalytics.io/learn/tutorial_getting_started_with_queries.html) à l’aide du nouveau langage de requête.

