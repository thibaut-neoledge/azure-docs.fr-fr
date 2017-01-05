---
title: "Analyser l’utilisation des données dans Log Analytics | Microsoft Docs"
description: "Vous pouvez utiliser le tableau de bord Utilisation dans Log Analytics pour afficher la quantité de données envoyées au service OMS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 668cde23cb717fcad52fd7823561d10735e6df1b


---
# <a name="analyze-data-usage-in-log-analytics"></a>Analyser l’utilisation des données dans Log Analytics
Log Analytics collecte les données et les envoie au service OMS à intervalles réguliers.  Vous pouvez utiliser le tableau de bord **Utilisation dans Log Analytics** pour afficher la quantité de données envoyées au service OMS. Le tableau de bord vous indique également la quantité de données envoyées par les solutions et la fréquence à laquelle vos serveurs envoient des données.

> [!NOTE]
> Si vous disposez d’un compte gratuit, vos envois au service OMS sont limités à 500 Mo de données par jour. Si vous atteignez la limite quotidienne, l’analyse de données s’arrête et reprend le jour suivant. Dans ce cas, vous devrez également renvoyer toute donnée n’ayant pas été acceptée ou traitée par OMS.

Si vous avez dépassé votre limite d’utilisation quotidienne ou si vous en êtes proche, vous pouvez éventuellement supprimer une solution pour réduire la quantité de données que vous envoyez au service OMS. Pour plus d'informations sur la suppression de solutions, consultez [Ajouter des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md).

![tableau de bord utilisation](./media/log-analytics-usage/usage-dashboard01.png)

Le tableau de bord **Utilisation de Log Analytics** affiche les informations suivantes :

- Volume de données
    - Volume de données dans le temps (en fonction de la portée temporelle actuelle)
    - Volume de données par solution
    - Données non associées à un ordinateur
- Ordinateurs
    - Ordinateurs envoyant des données
    - Ordinateurs sans données dans les dernières 24 heures
- Offres
    - Nœuds Insight & Analytics
    - Nœuds d’automatisation et de contrôle
    - Nœuds de sécurité
- Performances
    - Temps nécessaire pour recueillir et indexer les données
- Liste de requêtes

## <a name="to-work-with-usage-data"></a>Utilisation des données d’utilisation
1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement Azure.
2. Dans le menu **Hub**, cliquez sur **Plus de services** et, dans la liste des ressources, tapez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Cliquez sur **Log Analytics**.  
    ![Hub Azure](./media/log-analytics-usage/hub.png)
3. Le tableau de bord **Log Analytics** affiche la liste de vos espaces de travail. Sélectionnez un espace de travail.
4. Dans le tableau de bord *Espace de travail*, cliquez sur **Utilisation de Log Analytics**.
5. Dans le tableau de bord **Utilisation de Log Analytics**, cliquez sur **Heure : 24 dernières heures** pour modifier l’intervalle de temps.  
    ![Intervalle de temps](./media/log-analytics-usage/time.png)
6. Affichez les panneaux de catégorie d’utilisation qui montrent les domaines qui vous intéressent. Choisissez un panneau, puis cliquez dessus pour afficher plus de détails dans [Recherche de journal](log-analytics-log-searches.md).  
    ![Exemple de panneau d’utilisation des données](./media/log-analytics-usage/blade.png)
7. Dans le tableau de bord Recherche de journal, passez en revue les résultats renvoyés par la recherche.  
    ![exemple de recherche de journal d’utilisation](./media/log-analytics-usage/usage-log-search.png)


## <a name="next-steps"></a>Étapes suivantes
* Consultez la section [Recherches de journal dans Log Analytics](log-analytics-log-searches.md) pour afficher les informations détaillées qui sont collectées et envoyées à OMS par les fonctionnalités et les solutions.



<!--HONumber=Dec16_HO2-->


