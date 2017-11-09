---
title: Interroger Azure Log Analytics pour surveiller les clusters Azure HDInsight | Microsoft Docs
description: "Découvrez comment exécuter des requêtes sur Azure Log Analytics pour surveiller les travaux en cours d’exécution dans un cluster HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: nitinme
ms.openlocfilehash: 8fe91bed69a1c06367346041d8caba4aaee4c82a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="query-azure-log-analytics-to-monitor-hdinsight-clusters-preview"></a>Interroger Azure Log Analytics pour surveiller les clusters HDInsight (préversion)

Dans cet article, vous examinez certains scénarios d’utilisation d’Azure Log Analytics avec des clusters Azure HDInsight. Les trois scénarios les plus courants sont :

* Analyser les métriques du cluster HDInsight dans OMS
* Rechercher des messages de journaux spécifiques pour des clusters HDInsight
* Créer des alertes basées sur les événements qui se produisent dans les clusters

## <a name="prerequisites"></a>Composants requis

* Vous devez avoir configuré un cluster HDInsight pour utiliser Azure Log Analytics. Pour obtenir des instructions, consultez la page [Utiliser Azure Log Analytics avec des clusters HDInsight](hdinsight-hadoop-oms-log-analytics-tutorial.md).

* Vous devez avoir ajouté à cet espace de travail OMS les solutions de gestion de cluster spécifiques à HDInsight, comme décrit dans [Ajouter des solutions de gestion de clusters HDInsight à Log Analytics](hdinsight-hadoop-oms-log-analytics-management-solutions.md).

## <a name="analyze-hdinsight-cluster-metrics-in-oms"></a>Analyser les métriques du cluster HDInsight dans OMS

Cette section détaille les étapes permettant de rechercher des mesures spécifiques pour votre cluster HDInsight.

1. Ouvrez le tableau de bord OMS. Dans le portail Azure, ouvrez le panneau de cluster HDInsight que vous avez associé à Azure Log Analytics, cliquez sur l’onglet Analyse, puis sur **Ouvrir le tableau de bord OMS**.

    ![Ouvrir le tableau de bord OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Ouvrir le tableau de bord OMS")

2. Dans l’écran d’accueil du tableau de bord OMS, cliquez sur **Recherche dans les journaux**.

    ![Ouvrir Recherche dans les journaux](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Ouvrir Recherche dans les journaux")

3. Dans la zone de texte **Begin search here** (Lancer la recherche ici) de la fenêtre Recherche dans les journaux, tapez `*` afin de rechercher toutes les mesures pour toutes les mesures disponibles dans tous les clusters HDInsight configurés pour utiliser Azure Log Analytics. Appuyez sur ENTRÉE.

    ![Rechercher toutes les mesures](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Rechercher toutes les mesures")

4. Un résultat similaire à ce qui suit s’affiche normalement.

    ![Rechercher toutes les sorties de mesures](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics-output.png "Rechercher toutes les sorties de mesures")

5. Dans le volet gauche, sous la catégorie **Type**, recherchez une mesure que vous souhaitez approfondir. Pour ce didacticiel, choisissons `metrics_resourcemanager_queue_root_default_CL`. Sélectionnez la case à cocher correspondant à la mesure, puis cliquez sur **Appliquer**.

    > [!NOTE]
    > Vous devrez peut-être cliquer sur le bouton **[+]Plus** pour trouver la mesure que vous recherchez. En outre, comme le bouton **Appliquer** figure au bas de la liste, vous devez faire défiler la page vers le bas pour l’afficher.
    > 
    >    
    Notez que la requête dans la zone de texte apparaît désormais dans la zone en surbrillance dans la capture d’écran suivante :

    ![Rechercher des mesures spécifiques](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Rechercher des mesures spécifiques")

6. Vous pouvez maintenant approfondir cette mesure spécifique. Par exemple, vous pouvez maintenant affiner les résultats en fonction de la moyenne des ressources utilisées dans un intervalle de 10 minutes, en les classant par nom de cluster. Saisissez la requête suivante dans la zone de texte de la requête.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure avg(UsedAMResourceMB_d) by ClusterName_s interval 10minute

    ![Rechercher des mesures spécifiques](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics.png "Rechercher des mesures spécifiques")

7. Au lieu d’affiner les résultats en fonction de la moyenne des ressources utilisées, vous pouvez utiliser la requête suivante pour affiner les résultats en fonction du moment où les ressources maximales ont été utilisées (ainsi qu’aux 90e et 95e centiles) dans une fenêtre de 10 minutes.

        * (Type=metrics_resourcemanager_queue_root_default_CL) | measure max(UsedAMResourceMB_d) , pct95(UsedAMResourceMB_d), pct90(UsedAMResourceMB_d)  by ClusterName_s interval 10minute

    ![Rechercher des mesures spécifiques](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-more-specific-metrics-1.png "Rechercher des mesures spécifiques")

## <a name="search-for-specific-log-messages-in-hdinsight-clusters"></a>Rechercher des messages de journaux spécifiques dans des clusters HDInsight

Cette section détaille les étapes permettant de rechercher des messages d’erreur pendant une fenêtre de temps spécifique. Ces étapes ne sont qu’un exemple d’une méthode permettant d’afficher le message d’erreur qui vous intéresse. Vous pouvez utiliser n’importe quelle propriété disponible pour rechercher les erreurs que vous voulez trouver.

1. Ouvrez le tableau de bord OMS. Dans le portail Azure, ouvrez le panneau de cluster HDInsight que vous avez associé à Azure Log Analytics, cliquez sur l’onglet Analyse, puis sur **Ouvrir le tableau de bord OMS**.

    ![Ouvrir le tableau de bord OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Ouvrir le tableau de bord OMS")

2. Dans l’écran d’accueil du tableau de bord OMS, cliquez sur **Recherche dans les journaux**.

    ![Ouvrir Recherche dans les journaux](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Ouvrir Recherche dans les journaux")

3. Dans la zone de texte **Begin search here** (Lancer la recherche ici) de la fenêtre Recherche dans les journaux, tapez `"Error"` (sans les guillemets) afin de rechercher tous les messages d’erreur pour tous les clusters HDInsight configurés pour utiliser Azure Log Analytics. Appuyez sur ENTRÉE.

    ![Rechercher toutes les erreurs](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors.png "Rechercher toutes les erreurs")

4. Un résultat similaire à ce qui suit s’affiche normalement.

    ![Rechercher tous les résultats d’erreur](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-errors-output.png "Rechercher tous les résultats d’erreur")

5. Dans le volet gauche, sous la catégorie **Type**, recherchez un type d’erreur que vous souhaitez approfondir. Pour ce didacticiel, choisissons `log_sparkappsexecutors_CL`. Sélectionnez la case à cocher correspondant à la mesure, puis cliquez sur **Appliquer**.

    ![Rechercher des erreurs spécifiques](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error.png "Rechercher des erreurs spécifiques")

        
6. Notez que la requête dans la zone de texte s’affiche désormais dans la zone en surbrillance ci-dessous et que les résultats sont optimisés pour afficher uniquement le type d’erreur que vous avez sélectionné.

    ![Rechercher un résultat d’erreur spécifique](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-output.png "Rechercher un résultat d’erreur spécifique")

7. Vous pouvez maintenant approfondir cette liste d’erreurs spécifiques en utilisant les options disponibles dans le volet gauche. Par exemple, vous pouvez affiner la requête pour consulter uniquement les messages d’erreur à partir d’un nœud de travail spécifique.

    ![Rechercher un résultat d’erreur spécifique](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-refined.png "Rechercher un résultat d’erreur spécifique")

8. Vous pouvez vous concentrer sur l’heure à laquelle vous pensez que l’erreur s’est produite en sélectionnant la valeur de temps appropriée dans le volet gauche.

    ![Rechercher un résultat d’erreur spécifique](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-time.png "Rechercher un résultat d’erreur spécifique")

9. Vous voilà maintenant à l’erreur spécifique que vous recherchez. Vous pouvez cliquer sur **[+]afficher plus** pour examiner le message d’erreur.

    ![Rechercher un résultat d’erreur spécifique](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-error-arrived.png "Rechercher un résultat d’erreur spécifique")

## <a name="create-alerts-to-track-events"></a>Créer des alertes pour suivre des événements

La première étape pour créer une alerte consiste à définir une requête qui déclenche l’alerte. Par souci de simplicité, nous utiliserons la requête suivante, qui fournit la liste des applications ayant échoué en cours d’exécution sur les clusters HDInsight.

    * (Type=metrics_resourcemanager_queue_root_default_CL) AppsFailed_d>0 

Vous pouvez utiliser la requête de votre choix pour créer une alerte.

1. Ouvrez le tableau de bord OMS. Dans le portail Azure, ouvrez le panneau de cluster HDInsight que vous avez associé à Azure Log Analytics, cliquez sur l’onglet Analyse, puis sur **Ouvrir le tableau de bord OMS**.

    ![Ouvrir le tableau de bord OMS](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-open-oms-dashboard.png "Ouvrir le tableau de bord OMS")

2. Dans l’écran d’accueil du tableau de bord OMS, cliquez sur **Recherche dans les journaux**.

    ![Ouvrir Recherche dans les journaux](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-click-log-search.png "Ouvrir Recherche dans les journaux")

3. Dans la zone de texte **Begin search here** (Lancer la recherche ici) de la fenêtre Recherche dans les journaux, collez la requête sur laquelle vous souhaitez créer une alerte, puis cliquez sur le bouton **Alerte**.

    ![Saisir une requête pour créer une alerte](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Saisir une requête pour créer une alerte")

4. Dans la fenêtre **Ajouter une règle d’alerte**, entrez la requête puis d’autres détails pour créer une alerte, puis cliquez sur **Enregistrer**.

    ![Saisir une requête pour créer une alerte](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Saisir une requête pour créer une alerte")

    Dans cette capture d’écran, nous envoyons uniquement une notification par e-mail si la requête d’alerte génère un résultat.

5. Vous pouvez également modifier ou supprimer une alerte existante. Pour cela, dans n’importe quelle page du portail OMS, cliquez sur l’icône **Paramètres**.

    ![Saisir une requête pour créer une alerte](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png "Saisir une requête pour créer une alerte")

6. À la page **Paramètres**, cliquez sur **Alertes** pour afficher les alertes que vous avez créées. Vous pouvez également activer ou désactiver une alerte, la modifier ou la supprimer. Pour plus d’informations, voir [Utilisation des règles d’alerte dans Log Analytics](../log-analytics/log-analytics-alerts-creating.md).

## <a name="see-also"></a>Voir aussi

* [Utilisation d’OMS Log Analytics](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Création de règles d’alerte dans Log Analytics](../log-analytics/log-analytics-alerts-creating.md)
