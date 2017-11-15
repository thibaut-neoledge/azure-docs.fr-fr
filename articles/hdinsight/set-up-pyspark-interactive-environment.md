---
title: "Azure HDInsight Tools - Définir l’environnement interactif de PySpark pour Visual Studio Code | Microsoft Docs"
description: "Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts."
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: 
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 24839aadaee07b98ac5a6e6cfd14e44de54e7e7e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="set-up-pyspark-interactive-environment-for-visual-studio-code"></a>Définir l’environnement interactif de PySpark pour Visual Studio Code

Les étapes suivantes montrent comment installer des packages python pendant l’exécution de **HDInsight: PySpark Interactive**.


## <a name="set-up-pyspark-interactive-environment-on-macos-and-linux"></a>Définir l’environnement interactif de PySpark sur MacOS et Linux
Vous devez utiliser la commande **pip3** pour les étapes suivantes, s’il s’agit de **python 3.x**.
1. Assurez-vous de l’installation de **Python** et de **pip**.
 
    ![Version de python et de pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Installer Jupyter
    ```
    sudo pip install jupyter
    ```
    +  Le message d’erreur suivant peut s’afficher sur Linux et MacOS :

        ![Erreur1](./media/set-up-pyspark-interactive-environment/error1.png)
        ```Resolve:
        sudo pip uninstall asyncio
        sudo pip install trollies
        ```

    + Le message d’erreur suivant peut s’afficher à l’installation de libkrb5-dev (pour Linux uniquement) :

        ![Erreur2](./media/set-up-pyspark-interactive-environment/error2.png)
        ```Resolve:
        sudo apt-get install libkrb5-dev 
        ```

3. Installer sparkmagic
   ```
   sudo pip install sparkmagic
   ```

4. Vérifiez qu’ipywidgets est correctement installé en exécutant :
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Installer les noyaux wrapper](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Installez les noyaux wrapper. Exécutez la commande **pip show sparkmagic** pour voir le chemin de l’installation de sparkmagic. 

    ![Emplacement de sparkmagic](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Accédez à l’emplacement et exécutez :

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![Installation de jupyter kernelspec](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Vérifiez l’état de l’installation : 

    ```
    jupyter-kernelspec list
    ```
    ![liste de jupyter kernelspec](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Pour les noyaux disponibles : **python2** et **pysparkkernel** correspondent à **python 2.x** ; **python3** et  **pyspark3kernel** correspondent à **python 3.x**. 

8. Redémarrez VScode et revenez à l’éditeur de script qui exécute **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Étapes suivantes

### <a name="demo"></a>Démonstration
* HDInsight pour VScode : [vidéo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](hdinsight-for-vscode.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](hdinsight-connect-hive-zeppelin.md)
