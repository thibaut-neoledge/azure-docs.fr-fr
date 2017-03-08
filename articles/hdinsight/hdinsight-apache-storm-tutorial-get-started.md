---
title: "Didacticiel Apache Storm : prise en main de Storm | Microsoft Docs"
description: "Prise en main de l&quot;analyse des big data avec Apache Storm et les exemples Starter Storm sur HDInsight. Découvrez comment utiliser Storm pour traiter les données en temps réel."
keywords: apache storm,didacticiel apache storm,analyses big data,prise en main storm
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6f93f7ff-0736-4708-80ef-4289dae532a9
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: e505d02895abd011661b3e4f66c7f4f7ea042358
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-hdinsight"></a>Bien démarrer avec des exemples Storm Starter pour l’analyse de données volumineuses (« Big Data ») sur HDInsight

Apache Storm est un système de calcul en temps réel, évolutif, distribué, à tolérance de panne, qui permet de traiter des flux de données. Avec Storm sur Microsoft Azure HDInsight, vous pouvez créer un cluster Storm basé sur le cloud qui effectue l’analyse de Big Data en temps réel. 

> [!IMPORTANT]
> Les étapes décrites dans cet article créent un cluster HDInsight Windows. HDInsight est uniquement disponible sur Windows pour les versions antérieures à HDInsight 3.4. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).
>
> Pour savoir comment créer un cluster Storm sur HDInsight sous Linux, consultez le [Didacticiel Apache Storm : prise en main de l’exemple Storm Starter à l’aide de l’analyse de données sur HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md)

## <a name="prerequisites"></a>Conditions préalables
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vous devez disposer de ce qui suit pour suivre jusqu’au bout ce didacticiel Storm Apache :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Créer un cluster Storm

Utilisez les étapes suivantes pour créer un Storm sur un cluster HDInsight :

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez **+ et Nouveau**, **Intelligence et analyse**, puis **HDInsight**.
   
    ![Création d'un cluster HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/create-hdinsight.png)

2. Dans le panneau **De base**, entrez les informations suivantes :

    * **Nom du cluster** : nom du cluster HDInsight.
    * **Abonnement** : sélectionnez l'abonnement souhaité.
    * **Nom d’utilisateur de connexion du cluster** et **Mot de passe de connexion du cluster** : les informations de connexion lors de l’accès au cluster sur HTTPS. Vous utilisez ces informations d’identification pour accéder aux services tels que l’interface utilisateur Ambari Web ou l’API REST.
    * **Nom d’utilisateur Secure Shell (SSH)** : conservez la valeur par défaut pour ces champs. Ces informations ne sont pas utilisées sur les clusters HDInsight Windows.
    * **Groupe de ressources** : groupe de ressources dans lequel créer le cluster.
    * **Emplacement** : la région Azure dans laquelle créer le cluster.
   
    ![Sélectionnez un abonnement](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-basic-configuration.png)

3. Sélectionnez le **Type de cluster**, puis définissez les valeurs suivantes sur le panneau **Configuration du cluster** :
   
    * **Type de cluster** : Storm

    * **Système d’exploitation** : Windows

    * **Version** : Storm 0.10.0 (HDI 3.3)

        > [!NOTE]
        > HDInsight version 3.4 et versions ultérieures sont uniquement disponibles avec le système d’exploitation Linux.

    * **Niveau cluster** : standard
     
    Enfin, utilisez le bouton **Sélectionner** pour enregistrer les paramètres.
     
    ![Sélectionner un type de cluster](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-cluster-type.png)

4. Après avoir sélectionné le type de cluster, utilisez le bouton __Sélectionner__ pour définir le type de cluster. Ensuite, utilisez le bouton __Suivant__ bouton pour terminer la configuration de base.

5. À partir du panneau **Stockage**, sélectionnez ou créer un compte de stockage. Pour les étapes de ce document, laissez les autres champs de ce panneau sur leurs valeurs par défaut. Utilisez le bouton __Suivant__ pour enregistrer la configuration de stockage.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/hdinsight-apache-storm-tutorial-get-started/set-hdinsight-storage-account.png)

6. Dans le panneau **Résumé**, passez en revue la configuration du cluster. Utilisez les liens __Modifier__ pour modifier les éventuels paramètres incorrects. Pour finir, cliquez sur le bouton __Créer__ pour créer le cluster.
   
    ![Résumé de la configuration du cluster](./media/hdinsight-apache-storm-tutorial-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > La création du cluster peut prendre jusqu’à 20 minutes.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Exécution d’un exemple Starter Storm sur HDInsight
Avec ce didacticiel Apache Storm vous apprendrez à analyser des big data à l’aide d’exemples Starter Storm sur GitHub.

Chaque cluster Storm sur HDInsight est doté d’un tableau de bord Storm, qui peut être utilisé pour télécharger et exécuter les topologies Storm sur le cluster. Chaque cluster est également fourni avec des exemples de topologies pouvant être exécutés directement depuis le tableau de bord Storm.

### <a id="connect"></a>Connexion au tableau de bord
Le tableau de bord se trouve dans **https://&lt;clustername>.azurehdinsight.net//**, où **clustername** est le nom du cluster. Vous trouverez également un lien vers le tableau de bord en sélectionnant le cluster dans le tableau d’accueil, puis en sélectionnant le lien **Tableau de bord** en haut du panneau.

![Portail Azure avec un lien vers le tableau de bord Storm](./media/hdinsight-apache-storm-tutorial-get-started/dashboard.png)

> [!NOTE]
> Quand vous vous connectez au tableau de bord, vous devez entrer un nom d’utilisateur et un mot de passe. Il s’agit du nom de l’administrateur (**admin**) et du mot de passe utilisé lors de la création du cluster.
> 
> 

Une fois le tableau de bord Storm chargé, le formulaire **Envoi de la topologie** s’affichera.

![Envoyer votre topologie Storm Starter avec le tableau de bord Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)

Le formulaire **Envoi de la topologie** peut servir à télécharger et exécuter des fichiers .jar qui contiennent des topologies Storm. Il inclut également plusieurs exemples de base fournis avec le cluster.

### <a id="run"></a>Exécution de l’exemple de statistiques du projet de départ Storm dans GitHub
Les exemples fournis avec le cluster incluent plusieurs variantes d’une topologie de statistique. Ces exemples incluent un **spout** qui émet des phrases aléatoirement, et des **bolts** qui fragmentent chaque phrase en mots, puis comptent le nombre d’occurrences de chaque mot. Ces exemples sont tirés des [exemples Storm Starter](https://github.com/apache/storm/tree/master/examples/storm-starter), qui font partie d’Apache Storm.

Procédez comme suit pour exécuter un exemple Storm Starter :

1. Sélectionnez **StormStarter - WordCount** dans la liste déroulante du **fichier Jar**. Cette opération remplit les champs **Nom de la classe** et **Paramètres supplémentaires** avec les paramètres de cet exemple.
   
    ![Storm Starter avec l'entrée Statistiques sélectionnée sur le tableau de bord Storm.](./media/hdinsight-apache-storm-tutorial-get-started/submit.png)
   
   * **Nom de la classe** : classe dans le fichier .jar qui envoie la topologie.
   * **Paramètres supplémentaires** : tous les paramètres requis par la topologie. Dans cet exemple, le champ sert à fournir un nom convivial pour la topologie envoyée.
2. Cliquez sur **Envoyer**. Après quelques instants, le champ **Résultat** affiche la commande utilisée pour envoyer la tâche, ainsi que les résultats de la commande. Le champ **Erreur** affiche toutes les erreurs qui se produisent au cours de l’envoi de la topologie.
   
    ![Bouton Envoyer et résultats de l'entrée Statistiques Storm Starter.](./media/hdinsight-apache-storm-tutorial-get-started/submit-results.png)
   
   > [!NOTE]
   > Les résultats n’indiquent pas que la topologie est terminée : **une topologie Storm, une fois démarrée, s’exécute jusqu’à ce que vous l’arrêtiez.** La topologie de statistiques génère des phrases aléatoires tout en conservant le décompte du nombre de fois qu’elle rencontre chaque mot, jusqu’à ce que vous l’arrêtiez.
   > 
   > 

### <a id="monitor"></a>Analyse de la topologie
L’interface utilisateur Storm peut servir à analyser la topologie.

1. Dans la partie supérieure du tableau de bord Storm, sélectionnez **Interface utilisateur Storm** . Un résumé des informations sur le cluster et les topologies en cours d’exécution s’affiche.
   
    ![Tableau de bord Storm affichant le résumé de la topologie Statistiques Storm Starter.](./media/hdinsight-apache-storm-tutorial-get-started/stormui.png)
   
    Dans la page ci-dessus, vous pouvez voir la durée pendant laquelle la topologie a été active, ainsi que le nombre de processus de travail, d’exécuteurs et de tâches actuellement utilisés.
   
   > [!NOTE]
   > La colonne **Nom** contient le nom convivial fourni précédemment via le champ**Paramètres supplémentaires**.
   > 
   > 
2. Sous **Résumé de la topologie**, sélectionnez l’entrée **Statistiques** située dans la colonne **Nom**. Vous obtenez plus d’informations sur la topologie.
   
    ![Tableau de bord Storm avec les informations sur la topologie Statistiques Storm Starter.](./media/hdinsight-apache-storm-tutorial-get-started/topology-summary.png)
   
    Cette page fournit les informations suivantes :
   
   * **Statistiques de topologie** : informations de base sur les performances de la topologie, organisées dans des fenêtres de temps.
     
     > [!NOTE]
     > La sélection d’une fenêtre de temps spécifique modifie la fenêtre de temps pour les informations affichées dans d’autres sections de la page.
     > 
     > 
   * **Spouts** : informations de base sur les spouts, y compris la dernière erreur retournée par chaque spout.
   * **Bolts** : informations de base sur les bolts.
   * **Configuration de la topologie** : informations détaillées sur la configuration de la topologie.
     
     Cette page présente également les actions qui peuvent être effectuées sur la topologie :
   * **Activer** : reprend le traitement d’une topologie désactivée.
   * **Désactiver** : suspend une topologie en cours d’exécution.
   * **Rééquilibrer** : ajuste le parallélisme de la topologie. Il convient de rééquilibrer les topologies en cours d’exécution après avoir modifié le nombre de nœuds dans le cluster. Cela permet à la topologie d’ajuster le parallélisme pour compenser l’augmentation/la réduction du nombre de nœuds du cluster. Pour plus d’informations, consultez la rubrique [Présentation du parallélisme d’une topologie Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
   * **Supprimer** : met fin à une topologie Storm après expiration du délai spécifié.
3. À partir de cette page, sélectionnez une entrée dans la section **Spouts** ou **Bolts**. Vous obtenez des informations relatives au composant sélectionné.
   
    ![Tableau de bord Storm avec des informations sur les composants sélectionnés.](./media/hdinsight-apache-storm-tutorial-get-started/component-summary.png)
   
    Cette page affiche les informations suivantes :
   
   * **Statistiques du spout/bolt** : informations de base sur les performances de la topologie, organisées dans des fenêtres de temps.
     
     > [!NOTE]
     > La sélection d’une fenêtre de temps spécifique modifie la fenêtre de temps pour les informations affichées dans d’autres sections de la page.
     > 
     > 
   * **Statistiques d’entrée** (bolt uniquement) : informations sur les composants qui produisent des données consommées par le bolt.
   * **Statistiques de sortie** : informations sur les données émises par ce bolt.
   * **Exécuteurs** : informations sur les instances de ce composant.
   * **Erreurs** : erreurs générées par ce composant.
4. Lorsque vous affichez les détails d’un spout ou d’un bolt, sélectionnez une entrée depuis la colonne **Port** située dans la section **Exécuteurs** pour afficher les détails d’une instance spécifique du composant.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    À partir de ces données, vous pouvez voir qu’il y a 1,493,957 occurrences du mot **seven** . C’est le nombre de fois où il a été détecté depuis le démarrage de cette topologie.

### <a name="stop-the-topology"></a>Arrêt de la topologie
Retournez à la page **Résumé de la topologie**, puis sélectionnez **Supprimer** dans la section**Actions de topologie**. Lorsque vous êtes invité à entrer le nombre de secondes à attendre avant l’arrêt de la topologie, entrez le nombre 10. Après le délai d’expiration, la topologie n’apparaît plus quand vous vous rendez dans la section **Interface utilisateur Storm** du tableau de bord.

## <a name="delete-the-cluster"></a>Suppression du cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="summary"></a>Résumé
Dans ce didacticiel sur Storm Apache, vous avez appris à créer un cluster Storm sur HDInsight à l’aide de Storm Starter et à déployer, surveiller et gérer des topologies Storm à l’aide du tableau de bord Storm.

## <a id="next"></a>Étapes suivantes
* **HDInsight Tools pour Visual Studio** : HDInsight Tools vous permet d’utiliser Visual Studio pour envoyer, surveiller et gérer des topologies Storm semblables à celles du tableau de bord Storm mentionnées plus haut. HDInsight Tools offre la possibilité de créer des topologies Storm C# et inclut des exemples de topologies que vous pouvez déployer et exécuter sur votre cluster.
  
    Pour plus d’informations, consultez la rubrique [Prise en main des outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).
* **Exemples de fichiers** : le cluster Storm HDInsight contient plusieurs exemples dans le répertoire **%STORM_HOME%\contrib**. Chaque exemple doit contenir les éléments suivants :
  
  * Le code source : par exemple, storm-starter-0.9.1.2.1.5.0-2057-sources.jar
  * Les documents Java : par exemple, storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar
  * L'exemple : par exemple, storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar
    
    Utilisez la commande « jar » pour extraire le code source ou les documents Java. Par exemple, « jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar ».
    
    > [!NOTE]
    > Les documents Java sont composés de pages Web. Lorsque vous les avez extraits, utilisez un navigateur pour afficher le fichier **index.html** .
    > 
    > 
    
    Pour accéder à ces exemples, vous devez activer le bureau à distance pour le cluster Storm sur HDInsight, puis copier les fichiers à partir de **%STORM_HOME%\contrib**.
* Le document suivant contient une liste d’autres exemples pouvant être utilisés avec Storm sur HDInsight :
  
  * [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

