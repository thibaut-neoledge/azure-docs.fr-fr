---
title: Outils Azure Data Lake pour Visual Studio avec Hortonworks Sandbox | Microsoft Docs
description: "Apprendre à utiliser les outils Azure Data Lake pour Visual Studio avec le bac à sable Hortonworks (exécuté sur une machine virtuelle locale). Grâce à ces outils, vous pouvez créer et exécuter des tâches Hive et Pig sur le bac à sable et afficher la sortie et l’historique de la tâche."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 599aeb1f38c804c2edf6590140e739c9705ab1ab
ms.lasthandoff: 03/07/2017


---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Utiliser les outils Azure Data Lake pour Visual Studio avec Hortonworks Sandbox

Les outils Azure Data Lake pour Visual Studio incluent des outils pour travailler avec des clusters Hadoop génériques ainsi qu’avec Azure Data Lake et HDInsight. Ce document décrit les étapes nécessaires pour utiliser les outils Azure Data Lake avec Hortonworks Sandbox en cours d’exécution sur une machine virtuelle locale.

Hortonworks Sandbox permet de travailler avec Hadoop localement sur votre environnement de développement. Une fois que vous avez développé une solution et que vous souhaitez la mettre à l’échelle, vous pouvez passer à un cluster HDInsight.

## <a name="prerequisites"></a>Conditions préalables

* Hortonworks Sandbox en cours d’exécution sur une machine virtuelle sur votre environnement de développement. Ce document a été écrit et testé avec le bac à sable en cours d’exécution dans Oracle VirtualBox, qui a été configuré à l’aide des informations contenues dans le document [Prise en main de l’écosystème Hadoop](hdinsight-hadoop-emulator-get-started.md) .

* Visual Studio 2013, 2015 ou 2017, toute édition.

* [SDK Azure pour .NET](https://azure.microsoft.com/downloads/) 2.7.1 ou ultérieure.

* [Outils Azure Data Lake pour Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="configure-passwords-for-the-sandbox"></a>Configuration des mots de passe pour le bac à sable

Vérifiez que Hortonworks Sandbox est en cours d’exécution, puis suivez les étapes du document [Prise en main de l’écosystème Hadoop](hdinsight-hadoop-emulator-get-started.md#set-sandbox-passwords). Ces étapes permettent de configurer le mot de passe pour le compte SSH `root` et le compte Ambari `admin`. Ces mots de passe sont utilisés lors de la connexion au bac à sable à partir de Visual Studio.

## <a name="connect-the-tools-to-the-sandbox"></a>Connexion des outils au bac à sable

1. Ouvrez Visual Studio et sélectionnez **Affichage**, puis **Explorateur de serveurs**.

2. Dans **l’Explorateur de serveurs**, cliquez avec le bouton droit sur l’entrée **HDInsight**, puis sélectionnez **Connexion à l’émulateur HDInsight**.

    ![Connexion à l’émulateur HDInsight](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Dans la boîte de dialogue **Connexion à l’émulateur HDInsight**, entrez le mot de passe que vous avez configuré pour Ambari.

    ![Entrer le mot de passe Ambari](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Sélectionnez **Suivant** pour continuer.

4. Utilisez le champ **Mot de passe** pour entrer le mot de passe que vous avez configuré pour le compte `root`. Conservez la valeur par défaut dans les autres champs.

    ![Entrer le mot de passe racine](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Sélectionnez **Suivant** pour continuer.

5. Attendez la validation des services pour poursuivre. Dans certains cas, la validation peut échouer et vous inviter à mettre à jour la configuration. Dans ce cas, sélectionnez le bouton **Mettre à jour** et attendez la fin de la configuration et de la vérification du service.

    ![Erreurs et bouton Mettre à jour](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > Le processus de mise à jour utilise Ambari pour remplacer la configuration de Hortonworks Sandbox par celle exigée par les outils Azure Data Lake pour Visual Studio.

    Une fois la validation terminée, sélectionnez **Terminer** pour terminer la configuration.

    ![Terminer la connexion](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

    > [!NOTE]
    > Selon la vitesse de votre environnement de développement et la quantité de mémoire allouée à la machine virtuelle, la configuration et la validation des services peuvent prendre quelques minutes.

Une fois ces étapes terminées, vous avez maintenant une entrée « Cluster HDInsight local » dans l’Explorateur de serveurs sous la section HDInsight.

## <a name="write-a-hive-query"></a>Écriture d’une requête Hive

Hive fournit un langage de requête de type SQL (HiveQL) pour le traitement des données structurées. Utilisez les étapes suivantes pour apprendre à exécuter des requêtes ad hoc sur le cluster local.

1. Dans l’**Explorateur de serveurs**, cliquez avec le bouton droit sur l’entrée du cluster local que vous avez ajouté précédemment, puis sélectionnez **Écrire une requête Hive**.

    ![Écriture d’une requête Hive](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Cette opération ouvre une nouvelle fenêtre de requête qui vous permet de saisir et de soumettre rapidement une requête au cluster local.

2. Dans la nouvelle fenêtre de requête, entrez la commande suivante :

        select count(*) from sample_08;

    En haut de la fenêtre de requête, assurez-vous que la configuration du cluster local est sélectionnée, puis sélectionnez **Envoyer**. Laissez les autres valeurs (**lot** et nom du serveur) par défaut.

    ![fenêtre de requête et bouton Envoyer](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Vous pouvez également utiliser le menu déroulant en regard **d’Envoyer** pour sélectionner **Avancé**. Les options avancées vous permettent d’indiquer des options supplémentaires lors de l’envoi de la tâche.

    ![envoi avancé](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Une fois que vous avez envoyé la requête, l’état de la tâche s’affiche. Cet état comprend des informations sur la tâche telle qu’elle est traitée par Hadoop. L’entrée **État de la tâche** indique l’état actuel de la tâche. Cet état est mis à jour régulièrement. Vous pouvez également utiliser l’icône d’actualisation pour rafraîchir l’état manuellement.

    ![État de la tâche](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Une fois que **l’état de la tâche** est **Finished (Terminée)**, un graphe orienté acyclique dirigé s’affiche. Il décrit le chemin d’exécution qui a été déterminé par Tez (le moteur d’exécution par défaut de Hive sur le cluster local).

    > [!NOTE]
    > Tez est également le moteur par défaut pour les clusters HDInsight sous Linux. En revanche, ce n’est pas le moteur par défaut des clusters HDInsight sous Windows. Pour l’utiliser avec ces derniers, vous devez ajouter la ligne `set hive.execution.engine = tez;` au début de votre requête Hive.

    Utilisez le lien **Sortie de la tâche** pour afficher la sortie. Ici, c’est **823**, le nombre de lignes dans la table sample_08. Vous pouvez afficher des informations de diagnostic sur la tâche à l’aide des liens **Journal de la tâche** et **Download YARN Log (Télécharger le journal YARN)**.

4. Vous pouvez également exécuter des tâches Hive en mode interactif en modifiant le champ **Lot** sur **Interactif**, puis sélectionnez **Exécuter**.

    ![Requête interactive](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Une requête interactive affiche le journal de sortie généré lors du traitement dans la fenêtre **HiveServer2 Output** (Sortie HiveServer2).

    > [!NOTE]
    > Ces informations sont identiques à celles qui sont disponibles dans le lien **Journal de la tâche** une fois la tâche terminée.

    ![HiveServer2 Output (Sortie HiveServer2)](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Création d’un projet Hive

Vous pouvez également créer un projet qui contient plusieurs scripts Hive. Un projet est utile quand vous avez plusieurs scripts liés que vous devez conserver ensemble ou gérer à l’aide d’un système de contrôle de versions.

1. Dans Visual Studio, sélectionnez **Fichier**, **Nouveau**, puis__Project__.

2. Dans la liste des projets, développez **Modèles**, **Azure Data Lake**, puis sélectionnez **HIVE (HDInsight)**. Dans la liste des modèles, sélectionnez **Hive Sample (Exemple Hive)**. Entrez un nom et un emplacement, puis sélectionnez **OK**.

    ![Modèle HIVE (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Le projet **Hive Sample (Exemple Hive)** contient deux scripts, **WebLogAnalysis.hql** et **SensorDataAnalysis.hql**. Vous pouvez les envoyer à l’aide du bouton **Envoyer** situé en haut de la fenêtre.

## <a name="create-a-pig-project"></a>Création d’un projet Pig

Contrairement à Hive qui offre un langage de type SQL pour travailler avec des données structurées, Pig effectue des transformations sur les données. Pig fournit un langage (Pig Latin) permettant de développer un pipeline des transformations. Suivez les étapes ci-dessous pour utiliser Pig avec le cluster local :

1. Ouvrez Visual Studio, sélectionnez **Fichier**, **Nouveau**, puis **Projet**. Dans la liste des projets, développez **Modèles**, **Azure Data Lake**, puis sélectionnez **Pig (HDInsight)**. Dans la liste des modèles, sélectionnez **Pig Application (Application Pig)**. Entrez un nom et un emplacement, puis sélectionnez **OK**.

    ![Projet Pig (HDInsight)](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Entrez le texte suivant comme contenu du fichier **script.pig** créé dans le cadre de ce projet.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig utilise un autre langage que Hive,mais les tâches sont exécutées via le bouton **Envoyer** pour les deux langages. La sélection de la liste déroulante sous **Envoyer** affiche une boîte de dialogue Envoi avancé pour Pig.

    ![Envoi avancé Pig](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. L’état de la tâche et la sortie s’affichent de la même façon que pour une requête Hive.

    ![image d’une tâche pig terminée](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Affichage des tâches

Les outils Azure Data Lake vous permettent également d’afficher facilement les informations sur les tâches qui ont été exécutées sur Hadoop. Utilisez les étapes suivantes pour afficher les tâches qui ont été exécutées sur le cluster local.

1. Dans **l’Explorateur de serveurs**, cliquez avec le bouton droit sur le cluster local, puis sélectionnez **Afficher les tâches**. Une liste des tâches qui ont été soumises au cluster s’affiche.

    ![Affichage des tâches](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Dans la liste des tâches, sélectionnez-en une pour en afficher les détails.

    ![sélectionner une tâche](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Les informations affichées sont similaires à celles qui s’affichent après l’exécution d’une requête Hive ou Pig, et sont complétées par des liens permettant d’afficher des informations sur la sortie et le journal.

3. Vous pouvez également modifier et soumettre à nouveau la tâche à partir d’ici.

## <a name="view-hive-databases"></a>Affichage des bases de données Hive

1. Dans **l’Explorateur de serveurs**, développez l’entrée **cluster local HDInsight**, puis développez **Bases de données Hive**. Les bases de données **Par défaut** et **xademo** sur le cluster local sont affichées. Le développement d’une base de données révèle les tables qu’elle contient.

    ![bases de données développées](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Le développement d’une table affiche les colonnes qu’elle contient. Vous pouvez cliquer sur une table avec le bouton droit de la souris et sélectionner **Afficher les 100 premières lignes** pour afficher rapidement les données.

    ![affichage des bases de données Hive](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Propriétés des bases de données et des tables

Vous avez peut-être remarqué que vous pouvez choisir d’afficher les **propriétés** d’une base de données ou d’une table. La sélection de **Propriétés** affiche les détails de l’élément sélectionné dans la fenêtre Propriétés.

![Propriétés](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Création d’une table

Pour créer une table, cliquez avec le bouton droit de la souris sur une base de données, puis sélectionnez **Créer une table**.

![Créer une table](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Vous pouvez ensuite créer la table à l’aide d’un formulaire. Vous pouvez afficher le code HiveQL brut qui sera utilisé pour créer la table en bas de cette page.

![créer un formulaire de table](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Étapes suivantes

* [Se familiariser avec Hortonworks Sandbox](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Didacticiel Hadoop - Prise en main de HDP](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)

