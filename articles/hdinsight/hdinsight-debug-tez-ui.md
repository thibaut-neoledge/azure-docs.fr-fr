---
title: "Utiliser l&quot;interface utilisateur Tez avec HDInsight basé sur Windows | Microsoft Docs"
description: "Apprenez à utiliser l&quot;interface utilisateur Tez pour déboguer les travaux Tez dans HDInsight sous Windows."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 3934d26e119c7064328671816943b636b6f90d76
ms.lasthandoff: 04/11/2017


---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Utiliser l’interface utilisateur Tez pour déboguer les travaux Tez dans HDInsight sous Windows
L’interface utilisateur Tez est une page web qui peut servir à comprendre et à déboguer les travaux utilisant Tez comme moteur d’exécution pour les clusters HDInsight basés sur Windows. L’interface utilisateur Tez vous permet de visualiser le travail sous forme de graphique d’éléments connectés, d’explorer chacun d’entre eux, ainsi que d’extraire des statistiques et des informations de journalisation.

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Windows. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Composants requis
* Un cluster HDInsight Windows Pour plus d’informations sur la création d’un cluster, consultez [Prise en main de HDInsight sur Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > L’interface utilisateur Tez est disponible uniquement pour les clusters HDInsight sur Windows créés après le 8 février 2016.
  >
  >
* Un client Bureau à distance basé sur Windows.

## <a name="understanding-tez"></a>Présentation de Tez
Tez est une infrastructure extensible pour le traitement des données dans Hadoop plus rapide que le traitement MapReduce traditionnel. Pour les clusters HDInsight basés sur Windows, il s’agit d’un moteur facultatif que vous pouvez activer pour Hive dans le cadre de votre requête Hive à l’aide de la commande suivante :

    set hive.execution.engine=tez;

Lorsque Tez reçoit un travail à effectuer, il crée un graphe orienté acyclique (Directed Acyclic Graph - DAG) qui décrit l’ordre d’exécution des actions requises. Les actions individuelles sont appelées des vertex et exécutent une partie du travail global. L’exécution réelle du travail décrit par un vertex est appelée une tâche, et peut être répartie sur plusieurs nœuds du cluster.

### <a name="understanding-the-tez-ui"></a>Présentation de l’interface utilisateur Tez
L’interface utilisateur Tez est une page web qui fournit des informations sur les processus en cours d’exécution, ou qui ont été exécutés à l’aide de Tez. Elle vous permet d'afficher le DAG généré par Tez, de connaître la répartition entre les clusters, et d'accéder aux compteurs tels que la mémoire utilisée par les tâches et les vertex, ainsi qu'aux informations d'erreur. Elle peut fournir des informations utiles dans les scénarios suivants :

* Surveiller les processus à long terme, voir l'avancement des tâches de mappage et de réduction.
* Analyser les données historiques des processus ayant réussi ou échoué, afin de savoir comment le traitement peut être amélioré ou pourquoi il a échoué.

## <a name="generate-a-dag"></a>Générer un DAG
L'interface utilisateur Tez contient des données uniquement si une tâche qui utilise le moteur Tez est en cours d'exécution ou a déjà été exécutée. Les requêtes Hive simples peuvent généralement être résolues sans utiliser Tez. Toutefois, Tez est généralement nécessaire pour les requêtes plus complexes destinées à filtrer, regrouper, classer, joindre, etc.

Utilisez les étapes suivantes pour exécuter une requête Hive à l'aide de Tez.

1. Dans un navigateur web, accédez à l’adresse https://CLUSTERNAME.azurehdinsight.net, où **CLUSTERNAME** est le nom de votre cluster HDInsight.
2. Dans le menu situé en haut de la page, sélectionnez **Éditeur Hive**. Cela affiche une page avec l'exemple de requête suivant.

        Select * from hivesampletable

    Effacez l'exemple de requête et remplacez-le par ce qui suit.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Cliquez sur le bouton **Envoyer** . La section **Session de travail** en bas de la page affiche l'état de la requête. Une fois que l'état passe à **Terminé**, sélectionnez le lien **Afficher les détails** pour afficher les résultats. La **sortie de la tâche** doit ressembler à ce qui suit :

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Utiliser l'interface utilisateur Tez
> [!NOTE]
> L'interface utilisateur Tez est seulement disponible à partir du bureau des nœuds principaux du cluster, donc vous devez utiliser le Bureau à distance pour vous connecter aux nœuds principaux.
>
>

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre cluster HDInsight. En haut du panneau HDInsight, sélectionnez l’icône **Bureau à distance**. Le panneau du Bureau à distance s’affiche

    ![Icône Bureau à distance](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. Dans le panneau du Bureau à distance, sélectionnez **Connexion** pour vous connecter au nœud principal du cluster. Lorsque vous y êtes invité, utilisez le nom d'utilisateur et le mot de passe du Bureau à distance du cluster pour authentifier la connexion.

    ![Icône Connexion Bureau à distance](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Si vous n'avez pas activé la connexion Bureau à distance, fournissez un nom d'utilisateur, un mot de passe et la date d'expiration, puis sélectionnez **Activer** pour activer le Bureau à distance. Une fois celui-ci activé, utilisez les étapes précédentes pour vous connecter.
   >
   >
3. Une fois connecté, ouvrez Internet Explorer sur le Bureau à distance, sélectionnez l'icône d'engrenage dans l’angle supérieur droit du navigateur, puis **Paramètres d’affichage de compatibilité**.
4. En bas de la section **Paramètres d’affichage de compatibilité**, désactivez la case **Afficher les sites intranet dans Affichage de compatibilité** et **Utiliser les listes de compatibilité Microsoft**, puis sélectionnez **Fermer**.
5. Dans Internet Explorer, accédez à http://headnodehost:8188/tezui/#/. L’interface utilisateur de Tez s’affiche

    ![Interface utilisateur Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Une fois l’interface utilisateur Tez chargée, vous voyez apparaître la liste des DAG en cours d’exécution ou qui ont été exécutés sur le cluster. La vue par défaut inclut le nom du DAG, l’ID, l’émetteur, l’état, l’heure de début, l’heure de fin, la durée, l’ID d’application et la file d’attente. Il est possible d’ajouter des colonnes à l’aide de l’icône d’engrenage à droite de la page.

    Si vous ne voyez qu’une seule entrée, il s’agit de la requête que vous avez exécutée dans la section précédente. Si vous voyez plusieurs entrées, vous pouvez effectuer une recherche en entrant des critères dans les champs au-dessus du DAG, puis appuyez sur **Entrée**.
6. Sélectionnez le **Nom du Dag** pour l’entrée DAG la plus récente. Cette action permet d’afficher les informations sur le DAG, et offre la possibilité de télécharger un fichier compressé des fichiers JSON contenant des informations sur le DAG.

    ![Détails du DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Au-dessus des **Détails du DAG** se trouvent plusieurs liens pouvant être utilisés pour afficher des informations sur le DAG.

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
8. Sélectionnez **Vue graphique**. Cette action permet d’afficher une représentation graphique du DAG. Dans cette vue, vous pouvez placer la souris sur chaque vertex pour afficher les informations le concernant.

    ![Vue graphique](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Cliquez sur un vertex pour charger ses **Détails du vertex**. Cliquez sur le vertex **Map 1** pour afficher les détails de cet élément. Sélectionnez **Confirmer** pour confirmer la navigation.

    ![Détails](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Notez qu’en haut de la page, vous disposez maintenant de liens relatifs aux vertex et aux tâches.

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
11. Sélectionnez **Tâches**, puis sélectionnez l’élément nommé **00_000000**. Cette action permet d’afficher les **détails de la tâche**. À partir de cet écran, vous pouvez consulter les **compteurs de tâche** et les **tentatives de tâche**.

    ![Détails de la tâche](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris à utiliser la vue Tez, obtenez plus d’informations sur l’ [Utilisation de Hive dans HDInsight](hdinsight-use-hive.md).

Pour plus d’informations techniques sur Tez, consultez la [page Tez sur Hortonworks](http://hortonworks.com/hadoop/tez/)(en anglais).

