---
title: Azure HDInsight Tools - Utiliser Visual Studio Code pour Hive, LLAP ou pySpark | Microsoft Docs
description: "Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts."
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: jejiang
ms.openlocfilehash: fdf1911f4f5b515592b6197ea738d808104cb43e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Utiliser Azure HDInsight Tools pour Visual Studio Code

Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code (VSCode) pour créer et envoyer des travaux Hive de traitement par lots, des requêtes Hive interactives et des scripts pySpark. Azure HDInsight Tools peut être installé sur les plateformes prises en charge par VSCode, notamment sur Windows, Linux et MacOS. Renseignez-vous sur les prérequis pour les différentes plateformes.


## <a name="prerequisites"></a>Composants requis

Avant de poursuivre cet article, vérifiez que vous avez les éléments nécessaires suivants :

- Un cluster HDInsight.  Pour créer un cluster, consultez [Prise en main de HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono est nécessaire uniquement pour les plateformes Linux et MacOS.

## <a name="install-the-hdinsight-tools"></a>Installer HDInsight Tools
   
Une fois que vous avez tous les éléments prérequis, installez Azure HDInsight Tools pour VSCode. 

**Pour installer Azure HDInsight tools**

1. Ouvrez **Visual Studio Code**.
2. Cliquez sur **Extensions** dans le volet gauche. Saisissez **HDInsight** dans la zone de recherche.
3. Cliquez sur **Installer** à côté de **Azure HDInsight tools**. Après quelques secondes, le bouton **Installer** change en **Recharger**.
4. Cliquez sur **Recharger** pour activer l’extension **Azure HDInsight tools**.
5. Cliquez sur **Recharger la fenêtre** pour confirmer. **Azure HDInsight tools** est maintenant affiché dans le volet Extensions.

   ![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

Pour les utilisateurs de Spark, l’extension Python est recommandée pour avoir une meilleure expérience du service de langage. L’extension Python n’est pas nécessaire pour les utilisateurs de Hive. 

**Pour installer Python**

1. Ouvrez **Visual Studio Code**.
2. Cliquez sur **Extensions** dans le volet gauche. Entrez **python** dans la zone de recherche. Une liste des extensions python s’affiche, parmi lesquelles l’extension **Python**.
3. Cliquez sur **Installer** à côté de **Python**. Après quelques secondes, le bouton **Installer** change en **Recharger**.
4. Cliquez sur **Recharger** pour activer l’extension **Python**.
5. Cliquez sur **Recharger la fenêtre** pour confirmer. L’extension **Python** est maintenant affichée dans le volet Extensions.

     ![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

## <a name="open-hdinsight-workspace"></a>Ouvrir un espace de travail HDInsight

Vous devez créer un espace de travail dans VSCode avant de pouvoir vous connecter à Azure.

**Pour ouvrir un espace de travail**

1. Dans le menu **Fichier**, cliquez sur **Ouvrir le dossier**, spécifiez un dossier existant ou créez un autre dossier comme dossier de travail. Le dossier s’affiche dans le volet gauche.
2. Dans le volet gauche, cliquez sur l’icône **Nouveau fichier** à côté du dossier de travail.

   ![nouveau fichier](./media/hdinsight-for-vscode/new-file.png)
3. Nommez le nouveau fichier avec l’extension de fichier .hql (requêtes Hive) ou .py (script Spark). Un fichier de configuration **XXXX_hdi_settings.json** est automatiquement ajouté au dossier de travail.
4. Ouvrez le fichier **XXXX_hdi_settings.json** à partir de la fenêtre **EXPLORER**, ou cliquez avec le bouton droit sur l’éditeur de script pour sélectionner **Set Configuration**. Vous pouvez configurer les paramètres d’entrée de connexion, de cluster par défaut et d’envoi des travaux, comme indiqué dans l’exemple du fichier. Vous pouvez aussi laisser les autres paramètres vides.

## <a name="connect-to-azure"></a>Connexion à Azure

Pour pouvoir envoyer des scripts aux clusters HDInsight à partir de VSCode, vous devez vous connecter à votre compte Azure.

**Pour vous connecter à Azure**

1. Créez un dossier de travail et un fichier de script si vous n’en avez pas.
2. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **HDInsight: Login** dans le menu contextuel. Vous pouvez également appuyer sur **Ctrl+Maj+P** et entrer **HDInsight: Login**.

    ![connexion HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Connectez-vous en suivant les instructions de connexion affichées dans le volet **OUTPUT**.

    **Azure :** ![informations de connexion HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Une fois connecté, le nom de votre compte Azure s’affiche dans la barre d’état dans l’angle inférieur gauche de la fenêtre VSCode. 

    > [!NOTE] 
    > Si l’authentification à deux facteurs est activée pour votre compte Azure, nous vous recommandons d’utiliser l’authentification par téléphone au lieu du code PIN.
    > Il y a un problème connu avec la connexion Azure à l’aide de Google Chrome.

4. Cliquez avec le bouton droit sur l’éditeur de script pour ouvrir le menu contextuel :

    ![menu contextuel de l’éditeur de script HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-context-menu.png)

    À partir du menu contextuel, vous pouvez effectuer les travaux suivants :

    - logout
    - List clusters
    - Set default cluster
    - Submit interactive Hive queries
    - Submit Hive batch script
    - Set configuration

## <a name="list-hdinsight-clusters"></a>Afficher la liste des clusters HDInsight

Pour tester la connexion, vous pouvez afficher la liste de vos clusters HDInsight :

**Pour afficher la liste des clusters HDInsight dans votre abonnement Azure**
1. Ouvrez un espace de travail et connectez-vous à Azure. Consultez [Ouvrir un espace de travail HDInsight](#open-hdinsight-workspace) et [Connexion à Azure](#connect-to-azure).
2. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **HDInsight: List Cluster** dans le menu contextuel. 
3. Les clusters Hive et Spark s’affichent dans le volet **Output**.

    ![définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Définir le cluster par défaut
1. Ouvrez un espace de travail et connectez-vous à Azure. Consultez [Ouvrir un espace de travail HDInsight](#open-hdinsight-workspace) et [Connexion à Azure](#connect-to-azure).
2. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: Set Default Cluster**. 
3. Sélectionnez un cluster à utiliser comme cluster par défaut pour le fichier de script actuel. Le fichier de configuration **XXXX_hdi_settings.json** est automatiquement mis à jour. 

   ![définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Définir l’environnement Azure 
1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**.
2. Entrez **HDInsight: Set Azure Environment**.
3. Sélectionnez Azure ou AzureChina comme entrée de connexion par défaut.
4. L’entrée de connexion par défaut que vous avez sélectionnée est automatiquement ajoutée au fichier **XXXX_hdi_settings.json**. Elle est également directement mise à jour dans ce fichier de configuration. 

   ![définir la configuration de l’entrée de connexion par défaut](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Envoyer des requêtes Hive interactives

HDInsight Tools pour VSCode vous permet d’envoyer des requêtes Hive interactives aux clusters Interactive Query HDInsight.

1. Créez un dossier de travail et un fichier de script Hive si vous n’en avez pas.
2. Connectez-vous à votre compte Azure, puis configurez le cluster par défaut si vous ne l’avez pas encore fait.
3. Copiez et collez le code suivant dans votre fichier Hive, puis enregistrez-le.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: Interactive Hive** pour envoyer la requête. HDInsight Tools vous permet également d’envoyer un bloc de code au lieu du fichier de script entier à partir du menu contextuel. Peu après, le résultat de la requête s’affiche dans un nouvel onglet :

   ![résultat de la requête Hive interactive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Volet **RESULTS** : vous pouvez enregistrer le résultat complet dans un fichier CSV, JSON ou EXCEL dans un chemin d’accès local, ou enregistrer seulement certaines lignes du résultat.
    - Volet **MESSAGES** : cliquez sur un numéro de **ligne** pour accéder à la première ligne du script en cours d’exécution.

Une requête interactive est beaucoup plus rapide que [l’exécution d’un travail Hive de traitement par lots](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Envoyer des scripts de commandes par lot Hive

1. Créez un dossier de travail et un fichier de script Hive si vous n’en avez pas.
2. Connectez-vous à votre compte Azure, puis configurez le cluster par défaut si vous ne l’avez pas encore fait.
3. Copiez et collez le code suivant dans votre fichier Hive, puis enregistrez-le.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: Submit Hive Batch Script** pour envoyer un travail Hive. 
4. Sélectionnez le cluster auquel envoyer le travail.  

    Après l’envoi du travail Hive, les informations sur la réussite de l’envoi et l’ID du travail s’affichent dans le panneau **OUTPUT**. Le **NAVIGATEUR WEB** s’ouvre également. Il affiche l’état et les journaux des travaux en temps réel.

   ![résultat de l’envoi du travail Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

Un travail de traitement par lots est beaucoup plus long que [l’envoi de requêtes Hive interactives](#submit-interactive-hive-queries).


## <a name="submit-pyspark-job"></a>Envoi d’un travail PySpark

1. Créez un dossier de travail et un fichier de script avec l’extension .py si vous n’en avez pas.
2. Connectez-vous à votre compte Azure, puis configurez le cluster par défaut si vous ne l’avez pas encore fait.
3. Copiez et collez le code suivant dans le fichier de script :

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: Submit PySpark Job**. 
5. Sélectionnez un cluster auquel envoyer le travail PySpark. 

   ![résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Après l’envoi d’un travail Python, les journaux d’envoi s’affichent dans la fenêtre **OUTPUT** dans VSCode. **L’URL de l’interface utilisateur Spark** et **l’URL de l’interface utilisateur Yarn** s’affichent également. Vous pouvez ouvrir l’URL dans un navigateur web pour suivre l’état du travail.


## <a name="additional-features"></a>Fonctionnalités supplémentaires

HDInsight Tools pour VSCode prend en charge les fonctionnalités suivantes :

- **Saisie semi-automatique IntelliSense**. Les suggestions sont issues d’un mot clé, d’une méthode, des variables, etc. Des icônes différentes représentent des types d’objets différents :

    ![types d’objets IntelliSense dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marqueur d’erreurs IntelliSense**. Le service de langage souligne les erreurs de saisie dans le script Hive.     
- **Coloration syntaxique**. Le service de langage utilise plusieurs couleurs pour différencier les variables, les mots clés, le type des données, les fonctions, etc. 

    ![coloration syntaxique dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Étapes suivantes

### <a name="demo"></a>Démonstration
* HDInsight pour VScode : [vidéo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](./hdinsight-connect-hive-zeppelin.md).

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](hdinsight-apache-spark-job-debugging.md)



