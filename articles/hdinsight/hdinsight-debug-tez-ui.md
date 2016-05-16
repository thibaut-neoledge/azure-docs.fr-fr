<properties
pageTitle="Utiliser l'interface utilisateur Tez avec HDInsight basé sur Windows | Azure"
description="Apprenez à utiliser l'interface utilisateur Tez pour déboguer les travaux Tez dans HDInsight sous Windows."
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
ms.date="05/03/2016"
ms.author="larryfr"/>

# Utiliser l’interface utilisateur Tez pour déboguer les travaux Tez dans HDInsight sous Windows

L’interface utilisateur Tez est une page web qui peut servir à comprendre et à déboguer les travaux utilisant Tez comme moteur d’exécution pour les clusters HDInsight basés sur Windows. L’interface utilisateur Tez vous permet de visualiser le travail sous forme de graphique d’éléments connectés, d’explorer chacun d’entre eux, ainsi que d’extraire des statistiques et des informations de journalisation.

> [AZURE.NOTE] Les informations de ce document sont spécifiques aux clusters HDInsight sous Windows. Pour plus d’informations sur l’affichage et le débogage de Tez dans HDInsight sous Linux, consultez [Utiliser les vues Ambari pour déboguer les travaux Tez dans HDInsight](hdinsight-debug-ambari-tez-view.md).

##Composants requis

* Un cluster HDInsight Windows Pour plus d’informations sur la création d’un cluster, consultez [Prise en main de HDInsight sur Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

    > [AZURE.IMPORTANT] L’interface utilisateur Tez est disponible uniquement pour les clusters HDInsight sur Windows créés après le 8 février 2016.

* Un client Bureau à distance basé sur Windows.

##Présentation de Tez

Tez est une infrastructure extensible pour le traitement des données dans Hadoop plus rapide que le traitement MapReduce traditionnel. Pour les clusters HDInsight basés sur Windows, il s’agit d’un moteur facultatif que vous pouvez activer pour Hive dans le cadre de votre requête Hive à l’aide de la commande suivante :

    set hive.execution.engine=tez;

Lorsque Tez reçoit un travail à effectuer, il crée un graphe orienté acyclique (Directed Acyclic Graph - DAG) qui décrit l’ordre d’exécution des actions requises. Les actions individuelles sont appelées des vertex et exécutent une partie du travail global. L’exécution réelle du travail décrit par un vertex est appelée une tâche, et peut être répartie sur plusieurs nœuds du cluster.

###Présentation de l’interface utilisateur Tez

L’interface utilisateur Tez est une page web qui fournit des informations sur les processus en cours d’exécution, ou qui ont été exécutés à l’aide de Tez. Elle vous permet d'afficher le DAG généré par Tez, de connaître la répartition entre les clusters, et d'accéder aux compteurs tels que la mémoire utilisée par les tâches et les vertex, ainsi qu'aux informations d'erreur. Elle peut fournir des informations utiles dans les scénarios suivants :

* Surveiller les processus à long terme, voir l'avancement des tâches de mappage et de réduction.

* Analyser les données historiques des processus ayant réussi ou échoué, afin de savoir comment le traitement peut être amélioré ou pourquoi il a échoué.

##Générer un DAG

L'interface utilisateur Tez contient des données uniquement si une tâche qui utilise le moteur Tez est en cours d'exécution ou a déjà été exécutée. Les requêtes Hive simples peuvent généralement être résolues sans utiliser Tez. Toutefois, Tez est généralement nécessaire pour les requêtes plus complexes destinées à filtrer, regrouper, classer, joindre, etc.

Utilisez les étapes suivantes pour exécuter une requête Hive à l'aide de Tez.

1. Dans un navigateur web, accédez à https://CLUSTERNAME.azurehdinsight.net, où __CLUSTERNAME__ est le nom de votre cluster HDInsight.

2. Dans le menu situé en haut de la page, sélectionnez __Éditeur Hive__. Cela affiche une page avec l'exemple de requête suivant.

        Select * from hivesampletable

    Effacez l'exemple de requête et remplacez-le par ce qui suit.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

3. Cliquez sur le bouton __Envoyer__. La section __Session de travail__ en bas de la page affiche l'état de la requête. Une fois que l'état passe à __Terminé__, sélectionnez le lien __Afficher les détails__ pour afficher les résultats. La __sortie de la tâche__ doit ressembler à ce qui suit :
        
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

##Utiliser l'interface utilisateur Tez

> [AZURE.NOTE] L'interface utilisateur Tez est seulement disponible à partir du bureau des nœuds principaux du cluster, donc vous devez utiliser le Bureau à distance pour vous connecter aux nœuds principaux.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre cluster HDInsight. En haut du panneau HDInsight, sélectionnez l’icône __Bureau à distance__. Le panneau du Bureau à distance s’affiche

    ![Icône Bureau à distance](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)

2. Dans le panneau du Bureau à distance, sélectionnez __Connexion__ pour vous connecter au nœud principal du cluster. Lorsque vous y êtes invité, utilisez le nom d'utilisateur et le mot de passe du Bureau à distance du cluster pour authentifier la connexion.

    ![Icône Connexion Bureau à distance](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

    > [AZURE.NOTE] Si vous n'avez pas activé la connexion Bureau à distance, fournissez un nom d'utilisateur, un mot de passe et la date d'expiration, puis sélectionnez __Activer__ pour activer le Bureau à distance. Une fois celui-ci activé, utilisez les étapes précédentes pour vous connecter.

3. Une fois connecté, ouvrez Internet Explorer sur le Bureau à distance, sélectionnez l'icône d'engrenage dans l’angle supérieur droit du navigateur, puis __Paramètres d’affichage de compatibilité__.

4. En bas de la section __Paramètres d’affichage de compatibilité__, désactivez la case __Afficher les sites intranet dans Affichage de compatibilité__ et __Utiliser les listes de compatibilité Microsoft__, puis sélectionnez __Fermer__.

5. Dans Internet Explorer, accédez à http://headnodehost:8188/tezui/#/. L’interface utilisateur de Tez s’affiche

    ![Interface utilisateur Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Une fois l’interface utilisateur Tez chargée, vous voyez apparaître la liste des DAG en cours d’exécution ou qui ont été exécutés sur le cluster. La vue par défaut inclut le nom du DAG, l’ID, l’émetteur, l’état, l’heure de début, l’heure de fin, la durée, l’ID d’application et la file d’attente. Il est possible d’ajouter des colonnes à l’aide de l’icône d’engrenage à droite de la page.

    Si vous ne voyez qu’une seule entrée, il s’agit de la requête que vous avez exécutée dans la section précédente. Si vous voyez plusieurs entrées, vous pouvez effectuer une recherche en entrant des critères dans les champs au-dessus du DAG, puis appuyez sur __Entrée__.

4. Sélectionnez le __Nom du Dag__ pour l’entrée DAG la plus récente. Cette action permet d’afficher les informations sur le DAG, et offre la possibilité de télécharger un fichier compressé des fichiers JSON contenant des informations sur le DAG.

    ![Détails du DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)

5. Au-dessus des __Détails du DAG__ se trouvent plusieurs liens pouvant être utilisés pour afficher des informations sur le DAG.

    * __Compteurs du DAG__ permet d’afficher les informations de compteurs pour ce DAG.
    
    * __Vue graphique__ permet d’afficher une représentation graphique de ce DAG.
    
    * __Tous les vertex__ permet d’afficher une liste des vertex dans ce DAG.
    
    * __Toutes les tâches__ permet d’afficher une liste des tâches pour tous les vertex dans ce DAG.
    
    * __Toutes les tentatives de tâches__ permet d’afficher des informations sur les tentatives d’exécution de tâches pour ce DAG.
    
    > [AZURE.NOTE] Si vous parcourez les colonnes Vertex, Tâches et Tentatives de tâches, vous voyez des liens permettant d’afficher les __compteurs__ et d’__afficher ou télécharger les journaux__ pour chaque ligne.

    Si le travail a échoué, les détails du DAG affichent un état d’ÉCHEC, ainsi que des liens vers des informations sur la tâche ayant échoué. Des informations de diagnostic seront affichées sous les détails du DAG.

7. Sélectionnez __Vue graphique__. Cette action permet d’afficher une représentation graphique du DAG. Dans cette vue, vous pouvez placer la souris sur chaque vertex pour afficher les informations le concernant.

    ![Vue graphique](./media/hdinsight-debug-tez-ui/dagdiagram.png)

8. Cliquez sur un vertex pour charger ses __Détails__. Cliquez sur le vertex __Map 1__ pour afficher les détails de cet élément. Sélectionnez __Confirmer__ pour confirmer la navigation.

    ![Détails du vertex](./media/hdinsight-debug-tez-ui/vertexdetails.png)

9. Notez qu’en haut de la page, vous disposez maintenant de liens relatifs aux vertex et aux tâches.

    > [AZURE.NOTE] Vous pouvez également accéder à cette page en retournant aux __Détails du DAG__, en sélectionnant __Détails du vertex__, puis le vertex __Map 1__.

    * __Compteurs du vertex__ permet d’afficher les informations de compteurs pour ce vertex.
    
    * __Tâches__ permet d’afficher les tâches de ce vertex.
    
    * __Tentatives de tâches__ permet d’afficher des informations sur les tentatives d’exécution de tâches pour ce vertex.
    
    * __Sources et récepteurs__ permet d’afficher les sources et les récepteurs de données pour ce vertex.

    > [AZURE.NOTE] Comme avec le menu précédent, vous pouvez parcourir les colonnes Tâches, Tentatives de tâche et Sources et récepteurs\_\_ afin d’afficher des liens vers d’autres informations pour chaque élément.

10. Sélectionnez __Tâches__, puis sélectionnez l’élément nommé __00\_000000__. Cette action permet d’afficher les __Détails de la tâche__. À partir de cet écran, vous pouvez consulter les __Compteurs de la tâche__ et les __Tentatives de tâche__.

    ![Détails de la tâche](./media/hdinsight-debug-tez-ui/taskdetails.png)

##Étapes suivantes

Maintenant que vous avez appris à utiliser la vue Tez, obtenez plus d’informations sur l’[Utilisation de Hive dans HDInsight](hdinsight-use-hive.md).

Pour plus d’informations techniques sur Tez, consultez la [page Tez sur Hortonworks](http://hortonworks.com/hadoop/tez/) (en anglais).

<!---HONumber=AcomDC_0504_2016-->