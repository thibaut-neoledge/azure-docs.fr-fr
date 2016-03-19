<properties
pageTitle="Utiliser la vue Tez d’Ambari avec HDInsight | Azure"
description="Découvrez comment utiliser la vue Tez d’Ambari pour déboguer les travaux Tez dans HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/16/2016"
ms.author="larryfr"/>

# Utiliser les vues Ambari pour déboguer les travaux Tez dans HDInsight

L’interface utilisateur Web d’Ambari pour HDInsight contient une vue Tez qui peut servir à comprendre et à déboguer les travaux utilisant Tez comme moteur d’exécution. La vue Tez vous permet de visualiser le travail sous forme de graphique d’éléments connectés, d’explorer chacun d’entre eux, ainsi que d’extraire des statistiques et des informations de journalisation.

> [AZURE.NOTE] Les informations présentes sur le document sont spécifiques aux clusters HDInsight sous Linux. Pour plus d’informations sur le débogage des travaux Tez à l’aide de HDInsight sous Windows, consultez l’article [Use the Tez UI to debug Tez jobs on Windows-based HDInsight](hdinsight-debug-tez-ui.md) (Utiliser l’interface utilisateur Tez pour déboguer les travaux Tez dans HDInsight sous Windows).

##Configuration requise

* Un cluster HDInsight sous Linux Pour plus d’informations sur la création d’un cluster, consultez l’article [Prise en main de HDInsight sur Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

* Un navigateur web moderne qui prend en charge HTML5.

##Présentation de Tez

Tez est une infrastructure extensible pour le traitement des données dans Hadoop plus rapide que le traitement MapReduce traditionnel. Pour les clusters HDInsight sous Linux, Tez est le moteur par défaut pour Hive.

Lorsque Tez reçoit un travail à effectuer, il crée un graphe orienté acyclique (Directed Acyclic Graph - DAG) qui décrit l’ordre d’exécution des actions requises. Les actions individuelles sont appelées des vertex et exécutent une partie du travail global. L’exécution réelle du travail décrit par un vertex est appelée une tâche, et peut être répartie sur plusieurs nœuds du cluster.

###Présentation de la vue Tez

La vue Tez fournit des informations sur les processus en cours d’exécution, ou qui ont été exécutés à l’aide de Tez. Elle vous permet d’afficher le DAG généré par Tez, de connaître la répartition entre les clusters, et d’accéder aux compteurs tels que la mémoire utilisée par les tâches et les vertex, ainsi qu’aux informations d’erreur. Elle peut fournir des informations utiles dans les scénarios suivants :

* Surveiller les processus à long terme, voir l’avancement des tâches de mappage et de réduction.

* Analyser les données historiques des processus ayant réussi ou échoué, afin de savoir comment le traitement peut être amélioré ou pourquoi il a échoué.

##Générer un DAG

La vue Tez contient des données uniquement si une tâche qui utilise le moteur Tez est en cours d’exécution ou a déjà été exécutée. Les requêtes Hive simples peuvent généralement être résolues sans utiliser Tez. Toutefois, Tez est généralement nécessaire pour les requêtes plus complexes destinées à filtrer, regrouper, classer, joindre, etc.

Utilisez les étapes suivantes pour exécuter une requête Hive à l’aide de Tez.

1. Dans un navigateur web, accédez à https://CLUSTERNAME.azurehdinsight.net, où __CLUSTERNAME__ est le nom de votre cluster HDInsight.

2. Dans le menu situé en haut de la page, sélectionnez l’icône __Vues__. Cette icône représente une série de carrés. Dans la liste déroulante qui s’affiche, sélectionnez __Vue Hive__.

    ![Sélectionner la vue Hive](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Une fois la vue Hive chargée, collez le code suivant dans l’éditeur de requête, puis cliquez sur __Exécuter__.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    Une fois le travail terminé, le résultat devrait s’afficher dans la section __Résultats du processus de requête__. Le contenu doit ressembler à ce qui suit.
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. Sélectionnez l’onglet __Journal__. Des informations similaires à ce qui suit doivent s’afficher.
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Enregistrez la valeur __ID d’application__, car elle sera utilisée dans la section suivante.

##Utiliser la vue Tez

1. Dans le menu situé en haut de la page, sélectionnez l’icône __Vues__. Dans la liste déroulante qui s’affiche, sélectionnez __Vue Tez__.

    ![Sélectionner la vue Tez](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Une fois la vue Tez chargée, vous voyez apparaître la liste des DAG en cours d’exécution ou qui ont été exécutés sur le cluster. La vue par défaut inclut le nom du DAG, l’ID, l’émetteur, l’état, l’heure de début, l’heure de fin, la durée, l’ID d’application et la file d’attente. Il est possible d’ajouter des colonnes à l’aide de l’icône d’engrenage à droite de la page.

    ![Tous les DAG](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Si vous ne voyez qu’une seule entrée, il s’agit de la requête que vous avez exécutée dans la section précédente. Si vous en voyez plusieurs, vous pouvez faire une recherche en entrant ID d’application dans le champ __ID d’Application__, puis appuyez sur Entrée.

4. Sélectionnez le __nom du DAG__. Cette action permet d’afficher les informations sur le DAG, et offre la possibilité de télécharger un fichier compressé des fichiers JSON contenant des informations sur le DAG.

    ![Détails du DAG](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Au-dessus des __Détails du DAG__ se trouvent plusieurs liens pouvant être utilisés pour afficher des informations sur le DAG.

    * __Compteurs du DAG__ permet d’afficher les informations de compteurs pour ce DAG.
    
    * __Vue graphique__ permet d’afficher une représentation graphique de ce DAG.
    
    * __Tous les vertex__ permet d’afficher une liste des vertex dans ce DAG.
    
    * __Toutes les tâches__ permet d’afficher une liste des tâches pour tous les vertex dans ce DAG.
    
    * __Toutes les tentatives de tâches__ permet d’afficher des informations sur les tentatives d’exécution de tâches pour ce DAG.
    
    > [AZURE.NOTE] Si vous parcourez les colonnes Vertex, Tâches et Tentatives de tâches, vous voyez des liens permettant d’afficher les __compteurs__ et d’__afficher ou télécharger les journaux__ pour chaque ligne.

    Si le travail a échoué, les détails du DAG affichent un état d’ÉCHEC, ainsi que des liens vers des informations sur la tâche ayant échoué. Des informations de diagnostic seront affichées sous les détails du DAG.
    
    ![Un écran Détails du DAG décrivant un échec](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. Sélectionnez __Vue graphique__. Cette action permet d’afficher une représentation graphique du DAG. Dans cette vue, vous pouvez placer la souris sur chaque vertex pour afficher les informations le concernant.

    ![Vue graphique](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. Cliquez sur un vertex pour charger ses __Détails__. Cliquez sur le vertex __Map 1__ pour afficher les détails de cet élément.

    ![Détails du vertex](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. Notez qu’en haut de la page, vous disposez maintenant de liens relatifs aux vertex et aux tâches.

    > [AZURE.NOTE] Vous pouvez également accéder à cette page en retournant aux __Détails du DAG__, en sélectionnant __Détails du vertex__, puis le vertex __Map 1__.

    * __Compteurs du vertex__ permet d’afficher les informations de compteurs pour ce vertex.
    
    * __Tâches__ permet d’afficher les tâches de ce vertex.
    
    * __Tentatives de tâches__ permet d’afficher des informations sur les tentatives d’exécution de tâches pour ce vertex.
    
    * __Sources et récepteurs__ permet d’afficher les sources et les récepteurs de données pour ce vertex.

    > [AZURE.NOTE] Comme avec le menu précédent, vous pouvez parcourir les colonnes Tâches, Tentatives de tâche et Sources et récepteurs\_\_ afin d’afficher des liens vers d’autres informations pour chaque élément.

10. Sélectionnez __Tâches__, puis sélectionnez l’élément nommé __00\_000000__. Cette action permet d’afficher les __Détails de la tâche__. À partir de cet écran, vous pouvez consulter les __Compteurs de la tâche__ et les __Tentatives de tâche__.

    ![Détails de la tâche](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##Étapes suivantes

Maintenant que vous avez appris à utiliser la vue Tez, obtenez plus d’informations sur l’[Utilisation de Hive dans HDInsight](hdinsight-use-hive.md).

Pour plus d’informations techniques sur Tez, consultez la [page Tez sur Hortonworks](http://hortonworks.com/hadoop/tez/) (en anglais).

Pour plus d’informations sur l’utilisation d’Ambari avec HDInsight, consultez la page [Gérer des clusters HDInsight à l’aide de l’interface utilisateur Web d’Ambari](hdinsight-hadoop-manage-ambari.md).

<!---HONumber=AcomDC_0218_2016-->