---
title: "Utiliser la vue Tez d’Ambari avec HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser la vue Tez d’Ambari pour déboguer les travaux Tez dans HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: ccd1dffda19718a434fc09bb74a536714799740a
ms.openlocfilehash: 0c288989a2b1662bcbec6fd2c74dffaefedc82b8
ms.lasthandoff: 01/18/2017


---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>Utiliser les vues Ambari pour déboguer les travaux Tez dans HDInsight
L’interface utilisateur Web d’Ambari pour HDInsight contient une vue Tez qui peut servir à comprendre et à déboguer les travaux utilisant Tez comme moteur d’exécution. La vue Tez vous permet de visualiser le travail sous forme de graphique d’éléments connectés, d’explorer chacun d’entre eux, ainsi que d’extraire des statistiques et des informations de journalisation.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Composants requis
* Un cluster HDInsight sous Linux Pour plus d’informations sur la création d’un cluster, consultez l’article [Prise en main de HDInsight sur Linux](hdinsight-hadoop-linux-tutorial-get-started.md).
* Un navigateur web moderne qui prend en charge HTML5.

## <a name="understanding-tez"></a>Présentation de Tez
Tez est une infrastructure extensible pour le traitement des données dans Hadoop plus rapide que le traitement MapReduce traditionnel. Pour les clusters HDInsight sous Linux, Tez est le moteur par défaut pour Hive.

Lorsque Tez reçoit un travail à effectuer, il crée un graphe orienté acyclique (Directed Acyclic Graph - DAG) qui décrit l’ordre d’exécution des actions requises. Les actions individuelles sont appelées des vertex et exécutent une partie du travail global. L’exécution réelle du travail décrit par un vertex est appelée une tâche, et peut être répartie sur plusieurs nœuds du cluster.

### <a name="understanding-the-tez-view"></a>Présentation de la vue Tez
La vue Tez fournit des informations sur les processus en cours d’exécution, ou qui ont été exécutés à l’aide de Tez. Elle vous permet d’afficher le DAG généré par Tez, de connaître la répartition entre les clusters, et d’accéder aux compteurs tels que la mémoire utilisée par les tâches et les vertex, ainsi qu’aux informations d’erreur. Elle peut fournir des informations utiles dans les scénarios suivants :

* Surveiller les processus à long terme, voir l'avancement des tâches de mappage et de réduction.
* Analyser les données historiques des processus ayant réussi ou échoué, afin de savoir comment le traitement peut être amélioré ou pourquoi il a échoué.

## <a name="generate-a-dag"></a>Générer un DAG
La vue Tez contient des données uniquement si une tâche qui utilise le moteur Tez est en cours d’exécution ou a déjà été exécutée. Les requêtes Hive simples peuvent généralement être résolues sans utiliser Tez. Toutefois, Tez est généralement nécessaire pour les requêtes plus complexes destinées à filtrer, regrouper, classer, joindre, etc.

Utilisez les étapes suivantes pour exécuter une requête Hive à l'aide de Tez.

1. Dans un navigateur web, accédez à l’adresse https://CLUSTERNAME.azurehdinsight.net, où **CLUSTERNAME** est le nom de votre cluster HDInsight.
2. Dans le menu situé en haut de la page, sélectionnez l’icône **Vues** . Cette icône représente une série de carrés. Dans la liste déroulante qui s’affiche, sélectionnez **Vue Hive**. 
   
    ![Sélectionner la vue Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)
3. Une fois la vue Hive chargée, collez le code suivant dans l’éditeur de requête, puis cliquez sur **Exécuter**.
   
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
   
    Une fois le travail terminé, le résultat devrait s’afficher dans la section **Résultats du processus de requête** . Le contenu doit ressembler à ce qui suit.
   
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
4. Sélectionnez l’onglet **Journal**. Des informations similaires à ce qui suit doivent s’afficher.
   
        INFO : Session is already open
        INFO :
   
        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)
   
    Enregistrez la valeur **ID d’application** , car elle sera utilisée dans la section suivante.

## <a name="use-the-tez-view"></a>Utiliser la vue Tez
1. Dans le menu situé en haut de la page, sélectionnez l’icône **Vues** . Dans la liste déroulante qui s’affiche, sélectionnez **Vue Tez**.
   
    ![Sélectionner la vue Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)
2. Une fois la vue Tez chargée, vous voyez apparaître la liste des DAG en cours d’exécution ou qui ont été exécutés sur le cluster. La vue par défaut inclut le nom du DAG, l’ID, l’émetteur, l’état, l’heure de début, l’heure de fin, la durée, l’ID d’application et la file d’attente. Il est possible d’ajouter des colonnes à l’aide de l’icône d’engrenage à droite de la page.
   
    ![Tous les DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)
3. Si vous ne voyez qu’une seule entrée, il s’agit de la requête que vous avez exécutée dans la section précédente. Si vous en voyez plusieurs, vous pouvez faire une recherche en entrant ID d’application dans le champ **ID d’Application** , puis appuyez sur Entrée.
4. Sélectionnez le **nom du DAG**. Cette action permet d’afficher les informations sur le DAG, et offre la possibilité de télécharger un fichier compressé des fichiers JSON contenant des informations sur le DAG.
   
    ![Détails du DAG](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)
5. Au-dessus des **Détails du DAG** se trouvent plusieurs liens pouvant être utilisés pour afficher des informations sur le DAG.
   
   * **Compteurs du DAG** permet d’afficher les informations de compteurs pour ce DAG.
   * **Vue graphique** permet d’afficher une représentation graphique de ce DAG.
   * **Tous les vertex** permet d’afficher une liste des vertex dans ce DAG.
   * **Toutes les tâches** permet d’afficher une liste des tâches pour tous les vertex dans ce DAG.
   * **Toutes les tentatives de tâches** permet d’afficher des informations sur les tentatives d’exécution de tâches pour ce DAG.
     
     > [!NOTE]
     > Si vous parcourez les colonnes Vertex, Tâches et Tentatives de tâches, vous voyez des liens permettant d’afficher les **compteurs** et d’**afficher ou télécharger les journaux** pour chaque ligne.
     > 
     > 
     
     Si le travail a échoué, les détails du DAG affichent un état d’ÉCHEC, ainsi que des liens vers des informations sur la tâche ayant échoué. Des informations de diagnostic seront affichées sous les détails du DAG.
     
     ![Un écran Détails du DAG décrivant un échec](./media/hdinsight-debug-ambari-tez-view/faileddag.png)
6. Sélectionnez **Vue graphique**. Cette action permet d’afficher une représentation graphique du DAG. Dans cette vue, vous pouvez placer la souris sur chaque vertex pour afficher les informations le concernant.
   
    ![Vue graphique](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)
7. Cliquez sur un vertex pour charger ses **Détails du vertex**. Cliquez sur le vertex **Map 1** pour afficher les détails de cet élément.
   
    ![Détails](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)
8. Notez qu’en haut de la page, vous disposez maintenant de liens relatifs aux vertex et aux tâches.
   
   > [!NOTE]
   > Vous pouvez également accéder à cette page en retournant aux **Détails du DAG**, en sélectionnant **Détails du vertex**, puis le vertex **Map 1**.
   > 
   > 
   
   * **Compteurs du vertex** permet d’afficher les informations de compteurs pour ce vertex.
   * **Tâches** permet d’afficher les tâches de ce vertex.
   * **Tentatives de tâches** permet d’afficher des informations sur les tentatives d’exécution de tâches pour ce vertex.
   * **Sources et récepteurs** permet d’afficher les sources et les récepteurs de données pour ce vertex.
     
     > [!NOTE]
     > Comme avec le menu précédent, vous pouvez parcourir les colonnes Tâches, Tentatives de tâche et Sources et récepteurs__ afin d’afficher des liens vers d’autres informations pour chaque élément.
     > 
     > 
9. Sélectionnez **Tâches**, puis sélectionnez l’élément nommé **00_000000**. Cette action permet d’afficher les **détails de la tâche**. À partir de cet écran, vous pouvez consulter les **compteurs de tâche** et les **tentatives de tâche**.
   
   ![Détails de la tâche](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à utiliser la vue Tez, obtenez plus d’informations sur l’ [Utilisation de Hive dans HDInsight](hdinsight-use-hive.md).

Pour plus d’informations techniques sur Tez, consultez la [page Tez sur Hortonworks](http://hortonworks.com/hadoop/tez/)(en anglais).

Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md)


