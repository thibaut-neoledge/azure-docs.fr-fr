---
title: "Générer un modèle pour les services Azure Machine Learning (préversion) | Microsoft Docs"
description: "Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (préversion) de bout en bout. Il s’agit de la partie 2 sur l’expérimentation."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: hero-article
ms.date: 09/25/2017
ms.openlocfilehash: 273b94e9a53e36d7e8c699d3b7fa4305b881a001
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="classifying-iris-part-2-build-a-model"></a>Classification d’Iris, partie 2 : générer un modèle
Les services Azure Machine Learning (préversion) forment une solution d’analytique avancée et de science des données intégrée de bout en bout qui permet aux scientifiques des données professionnels de préparer des données, développer des expérimentations et déployer des modèles à l’échelle du cloud.

Ce didacticiel est le deuxième d’une série de trois. Dans cette partie du didacticiel, vous utilisez les services Azure Machine Learning (préversion) pour apprendre à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Travailler dans Azure Machine Learning Workbench
> * Ouvrir des scripts et passer le code en revue
> * Exécuter des scripts dans un environnement local
> * Examiner l’historique des exécutions
> * Exécuter des scripts dans un environnement Docker local
> * Exécuter des scripts dans une fenêtre Azure CLI locale
> * Exécuter des scripts dans un environnement Docker distant
> * Exécuter des scripts dans un environnement HDInsight cloud

Ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel pour simplifier les choses. Les captures d’écran sont spécifiques à Windows, mais l’expérience macOS est presque identique.

## <a name="prerequisites"></a>Composants requis
Vous devez effectuer la première partie de cette série de didacticiels. Suivez le [didacticiel Préparer les données](tutorial-classifying-iris-part-1.md) pour créer des ressources Azure Machine Learning et installer l’application Azure Machine Learning Workbench avant de commencer les étapes de ce didacticiel.

Vous pouvez essayer d’exécuter des scripts dans un conteneur Docker local (facultatif). Pour ce faire, il vous faut un moteur Docker (l’édition Community est suffisante) installé et démarré en local sur votre machine Windows ou macOS. En savoir plus sur les [instructions d’installation Docker](https://docs.docker.com/engine/installation/).

Si vous voulez essayer de répartir les scripts à exécuter dans un conteneur Docker d’une machine virtuelle Azure distante ou un cluster HDInsight Spark, vous pouvez suivre les [instructions pour créer une machine virtuelle de science des données Azure basée sur Ubuntu ou un cluster HDI](how-to-create-dsvm-hdi.md).

## <a name="review-irissklearnpy-and-configuration-files"></a>Examiner les fichiers de configuration et iris_sklearn.py
1. Lancez l’application **Azure Machine Learning Workbench**, puis ouvrez le projet **myIris** que vous avez créé dans la partie précédente de la série de didacticiels.

2. Une fois le projet ouvert, cliquez sur le bouton **Fichiers** (icône de dossier) dans la barre d’outils de gauche dans Azure Machine Learning Workbench pour ouvrir la liste des fichiers dans votre dossier de projet.

3. Sélectionnez le fichier **iris_sklearn.py** ; le code Python s’ouvre alors sous un nouvel onglet de l’éditeur de texte dans Workbench.

   ![Ouvrir le fichier](media/tutorial-classifying-iris/open_iris_sklearn.png)

   >[!NOTE]
   >Le code que vous voyez peut différer légèrement du code précédent, car cet exemple de projet est fréquemment mis à jour.

4. Passez en revue le code de script Python pour vous familiariser avec le style de codage. Notez que le script effectue les tâches suivantes :

   - Il charge le package de préparation des données **iris.dprep** pour créer un [cadre de données pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). 

        >[!NOTE]
        >Nous utilisons le package de préparation des données `iris.dprep` qui est fourni avec l’exemple de projet, qui doit être le même que le fichier `iris-1.dprep` que vous avez créé dans la partie 1 de ce didacticiel.

   - Il ajoute des caractéristiques aléatoires pour rendre le problème plus difficile à résoudre. (Le caractère aléatoire est nécessaire, car Iris est un petit jeu de données qui peut être facilement classé avec près de 100 % de précision.)

   - Il utilise la bibliothèque de machine learning [scikit-learn](http://scikit-learn.org/stable/index.html) pour générer un modèle de régression logistique simple. 

   - Il sérialise le modèle à l’aide de la bibliothèque [pickle](https://docs.python.org/2/library/pickle.html) en un fichier dans le dossier `outputs`, puis il le charge et le désérialise en mémoire.

   - Il utilise le modèle désérialisé pour faire des prédictions sur un nouvel enregistrement. 

   - Il trace deux graphiques (matrice de confusion et courbe ROC multiclasses) à l’aide de la bibliothèque [matplotlib](https://matplotlib.org/), puis les enregistre dans le dossier `outputs`.

   - L’objet `run_logger` est utilisé de bout en bout pour enregistrer les taux de régularisation et modéliser la précision dans des journaux, qui sont automatiquement tracés dans l’historique des exécutions.


## <a name="execute-irissklearnpy-script-in-local-environment"></a>Exécuter le script iris_sklearn.py dans un environnement local

Nous allons préparer l’exécution du script **iris_sklearn.py** pour la première fois. Ce script nécessite les packages scikit-learn et matplotlib. **scikit-learn** est déjà installé par Azure Machine Learning Workbench. Toutefois, nous devons installer **matplotlib**. 

1. Dans Azure Machine Learning Workbench, cliquez sur le menu **Fichier** et choisissez **Ouvrir l’invite de commandes** pour lancer l’invite de commandes. Nous nous référons à cette fenêtre de l’interface de ligne de commande sous l’appellation « fenêtre CLI Azure Machine Learning Workbench », ou « fenêtre CLI » dans sa forme abrégée.

2. Dans la fenêtre CLI, tapez la commande suivante pour installer le package Python **matplotlib**. L’opération doit prendre moins d’une minute.

   ```azurecli
   pip install matplotlib
   ```

   >[!NOTE]
   >Si vous ignorez la commande `pip install` ci-dessus, le code dans `iris_sklearn.py` s’exécute correctement, mais il ne produit pas la sortie de la matrice de confusion et les tracés de courbe ROC multiclasses comme indiqué dans les visualisations de l’historique.

3. Revenez à la fenêtre d’application Workbench. 

4. Dans l’angle supérieur gauche de l’onglet **iris_sklearn.py**, en regard de l’icône d’enregistrement, cliquez sur la liste déroulante et choisissez **Configuration de l’exécution**.  Choisissez **local** comme environnement d’exécution, et `iris_sklearn.py` en comme script à exécuter.

5. Ensuite, vers la droite du même onglet, renseignez le champ **Arguments** avec la valeur `0.01`. 

   ![img](media/tutorial-classifying-iris/run_control.png)

6. Cliquez sur le bouton **Exécuter**. Un travail est planifié immédiatement. Le travail est répertorié dans le panneau **Travaux** sur le côté droit de la fenêtre Workbench. 

7. Après quelques instants, l’état du travail passe de **Soumission** à **Exécution**, puis à **Terminé** peu de temps après.

   ![Exécuter sklearn](media/tutorial-classifying-iris/run_sklearn.png)

8. Cliquez sur le mot **Terminé** dans le texte d’état du travail dans le volet Travaux. Une fenêtre indépendante s’ouvre, puis affiche le texte de sortie standard (stdout) du script en cours d’exécution. Pour fermer le texte stdout, cliquez sur le bouton **X** dans le coin supérieur droit de la fenêtre indépendante.

9. Dans le même état du travail dans le volet Travaux, cliquez sur le texte en bleu **iris_sklearn.py [n]** (_n_ est le nombre d’exécution) juste au-dessus de l’état **Terminé** et de l’heure de début. La page **Propriétés de l’exécution** s’ouvre et affiche les informations relatives aux propriétés de l’exécution, les fichiers de **Sorties**, les **Visualisations** éventuelles, et les **Journaux** issus de cette exécution. 

   Quand l’exécution est terminée, la fenêtre indépendante affiche les résultats suivants :

   >[!NOTE]
   >En raison du degré de randomisation introduit dans la formation définie précédemment, les résultats que vous obtenez diffèrent légèrement.

   ```text
   Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]
   
   Iris dataset shape: (150, 5)
   Regularization rate is 0.01
   LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
          intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
          penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
          verbose=0, warm_start=False)
   Accuracy is 0.6792452830188679
   
   ==========================================
   Serialize and deserialize using the outputs folder.
   
   Export the model to model.pkl
   Import the model from model.pkl
   New sample: [[3.0, 3.6, 1.3, 0.25]]
   Predicted class is ['Iris-setosa']
   Plotting confusion matrix...
   Confusion matrix in text:
   [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
   Confusion matrix plotted.
   Plotting ROC curve....
   ROC curve plotted.
   Confusion matrix and ROC curve plotted. See them in Run History details page.
   ```

10. Fermez l’onglet **Propriétés de l’exécution** et revenez à l’onglet **iris_sklearn.py**. 

11. Effectuez d’autres exécutions. 

    Dans le champ **Arguments**, entrez une série de valeurs numériques différentes comprises entre `0.001` et `10`. Cliquez sur **Exécuter** pour exécuter le code plusieurs fois encore. La valeur d’argument que vous changez chaque fois est transmise à l’algorithme de régression logistique dans le code, ce qui entraîne des résultats différents systématiquement.

## <a name="review-run-history-in-detail"></a>Examiner l’historique des exécutions
Dans Azure Machine Learning Workbench, chaque exécution du script est capturée dans un enregistrement de l’historique des exécutions. Vous pouvez afficher l’historique des exécutions d’un script particulier en ouvrant la vue **Exécutions**.

1. Cliquez sur le bouton **Exécutions** (icône en forme d’horloge) dans la barre d’outils de gauche pour ouvrir la liste des **exécutions**. Cliquez ensuite sur **iris_sklearn.py** pour afficher le **Tableau de bord des exécutions** de `iris_sklearn.py`.

   ![img](media/tutorial-classifying-iris/run_view.png)

2. L’onglet **Tableau de bord des exécutions** s’ouvre. Passez en revue les statistiques capturées sur plusieurs exécutions. Les graphiques apparaissent en haut de l’onglet, tandis que chaque exécution numérotée avec les détails correspondants est répertoriée dans le tableau en bas de la page.

   ![img](media/tutorial-classifying-iris/run_dashboard.png)

3. Filtrez la table, puis cliquez sur les graphiques interactivement pour afficher l’état, la durée, la précision et le taux de régularisation de chaque exécution. 

4. Sélectionnez deux ou trois exécutions dans le tableau **Exécutions**, puis cliquez sur le bouton **Comparer** pour ouvrir une page de comparaison détaillée. Passez en revue la comparaison côte-à-côte. Cliquez sur le bouton précédent **Liste d’exécution** dans le coin supérieur gauche de la page de comparaison pour revenir au **Tableau de bord des exécutions**.

5. Cliquez sur une exécution pour en afficher les détails. Notez que les statistiques de l’exécution sélectionnée sont répertoriées dans la section _Propriétés de l’exécution_. Les fichiers écrits dans le dossier de sortie sont répertoriés dans la section **Sortie** et peuvent être téléchargés.

   ![img](media/tutorial-classifying-iris/run_details.png)

   Les deux tracés (la matrice de confusion et la courbe ROC multiclasses) apparaissent dans la section **Visualisations**. Tous les fichiers journaux se trouvent également dans la section **Journaux**.

## <a name="execute-scripts-in-the-local-docker-environment"></a>Exécuter des scripts dans l’environnement Docker local

Azure Machine Learning vous permet de configurer facilement des environnements d’exécution supplémentaires tels que Docker afin d’y exécuter votre script. 

>[!IMPORTANT]
>Pour que vous puissiez effectuer cette étape, le moteur Docker doit être installé localement et démarré. Consultez le guide d’installation pour plus de détails.

1. Dans la barre d’outils de gauche, sélectionnez l’icône de dossier pour ouvrir la liste **Fichiers** associée à votre projet. Développez le dossier `aml_config`. 

2. Plusieurs environnements sont préconfigurés, tels que **docker-python**, **docker-spark** et **local**. 

   Chaque environnement a deux fichiers, tels que `docker-python.compute` et `docker-python.runconfig`. Ouvrez chaque type de fichier ; vous pouvez alors constater que certaines options sont configurables dans l’éditeur de texte.  

   Fermez (X) les onglets des éditeurs de texte ouverts pour faire de la place.

3. Exécutez le script **iris_sklearn.py** à l’aide de l’environnement **docker-python**. 

   - Dans la barre d’outils de gauche, cliquez sur l’icône en forme d’horloge pour ouvrir le panneau **Exécutions**. Cliquez sur **Toutes les exécutions**. 
   - En haut de l’onglet **Toutes les exécutions**, choisissez **docker-python** comme environnement ciblé à la place de l’environnement par défaut **local**. 
   - Ensuite, vers la droite, choisissez **iris_sklearn.py** comme script à exécuter. 
   - Laissez le champ **Arguments** vide, car le script spécifie une valeur par défaut. 
   - Cliquez sur le bouton **Exécuter**.

4. Comme indiqué dans le panneau **Travaux** sur la droite de la fenêtre Workbench, un nouveau travail démarre.

   La toute première exécution dans un environnement Docker prend quelques minutes supplémentaires. 

   En coulisse, Azure Machine Learning Workbench génère un nouveau fichier Docker faisant référence à l’image Docker de base spécifiée dans le fichier `docker.compute` et aux packages Python de dépendances spécifiés dans le fichier `conda_dependencies.yml`. Ensuite, le moteur Docker télécharge l’image de base à partir d’Azure, installe les packages Python spécifiés dans le fichier `conda_dependencies.yml`, puis démarre un conteneur Docker. Ensuite, il copie (ou référence, selon la configuration d’exécution) la copie locale du dossier du projet, puis exécute le script `iris_sklearn.py`. Le résultat final doit être exactement le même que celui obtenu en ciblant **local**.

5. À présent, essayons Spark. L’image de base Docker contient une instance de Spark préalablement installée et configurée. Vous pouvez donc y exécuter un script PySpark. Il s’agit d’un moyen simple de développer et tester votre programme Spark sans avoir à consacrer de temps à l’installation et à la configuration de Spark. 

   Ouvrez le fichier `iris_pyspark.py` . Ce script charge le fichier de données `iris.csv` et utilise l’algorithme de régression logistique à partir de la bibliothèque de machine learning de Spark pour classer le jeu de données Iris. À présent, définissez l’environnement d’exécution sur **docker-spark** et le script sur **iris_pyspark.py**, puis effectuez une nouvelle exécution. Cette opération prend un peu plus de temps, car une session Spark doit être créée et démarrée à l’intérieur du conteneur Docker. Vous pouvez également voir que la sortie stdout est différente de celle de `iris_pyspark.py`.

6. Effectuez plusieurs exécutions supplémentaires en expérimentant différents arguments. 

7. Ouvrez le fichier `iris_pyspark.py` pour voir le modèle de régression logistique simple créé à l’aide de la bibliothèque de machine learning de Spark. 

8. Interagissez avec le panneau **Travaux**, la vue de liste de l’historique des exécutions et la vue des détails des exécutions entre les différents environnements d’exécution.

## <a name="execute-scripts-in-the-azure-ml-cli-window"></a>Exécuter des scripts dans la fenêtre CLI Azure Machine Learning

1. À l’aide d’Azure Machine Learning Workbench, lancez la fenêtre de ligne de commande en cliquant sur le menu **Fichier**, puis sur **Ouvrir l’invite de commandes**. Votre invite de commandes démarre dans le dossier du projet avec l’invite `C:\Temp\myIris\>`.

   >[!Important]
   >Vous devez utiliser la fenêtre de ligne de commande (lancée à partir de Workbench) pour effectuer les étapes suivantes :

2. Utilisez l’invite de commande (CLI) pour vous connecter à Azure. 

   L’application Workbench et l’interface CLI utilisent des caches d’informations d’identification indépendants pendant l’authentification auprès des ressources Azure. Vous ne devez effectuer cette opération qu’une seule fois, jusqu’à ce que le jeton mis en cache expire. La commande **az account list** retourne la liste des abonnements disponibles pour votre connexion. S’il en existe plusieurs, utilisez la valeur de l’ID de l’abonnement souhaité en la définissant comme compte par défaut dans la commande **az set account -s**. Ensuite, vérifiez le paramétrage à l’aide de la commande account show.

   ```azurecli
   REM login using aka.ms/devicelogin site.
   az login
   
   REM list all Azure subscriptions you have access to. 
   az account list -o table
   
   REM set the current Azure subscription to the one you want to use.
   az set account -s <subscriptionId>
   
   REM verify your current subscription is set correctly
   az account show
   ```

3. Une fois que vous êtes authentifié et que le contexte de l’abonnement Azure actuel est défini, tapez les commandes suivantes dans la fenêtre CLI pour installer matplotlib et envoyer le script Python comme expérience à exécuter.

   ```azurecli
   REM You don't need to do this if you have installed matplotlib locally from the previous steps.
   pip install matplotlib
   
   REM Kick off an execution of the iris_sklearn.py file against local compute context
   az ml experiment submit -c local .\iris_sklearn.py
   ```

4. Passez en revue la sortie. Notez que la sortie et le résultat sont les mêmes que ceux du script que vous avez exécuté dans ce didacticiel à l’aide de Workbench. 

5. Exécutez le même script à l’aide de l’environnement d’exécution Docker si Docker est installé sur votre machine.

   ```azurecli
   REM Execute iris_sklearn.py in local Docker container Python environment.
   az ml experiment submit -c docker-python .\iris_sklearn.py 0.01
   
   REM Execute iris_pyspark.py in local Docker container Spark environment.
   az ml experiment submit -c docker-spark .\iris_pyspark.py 0.1
   ```
6. Dans Azure Machine Learning Workbench, cliquez sur l’icône Dossier dans la barre d’outils de gauche pour répertorier les fichiers projet, puis ouvrez le script Python nommé **run.py**. 

   Ce script est utile pour répéter l’expérience plusieurs fois avec un taux de régularisation spécifique à chaque fois. Ce script démarre un travail `iris_sklearn.py` avec un taux de régularisation de `10.0` (exagérément élevé), puis diminue le taux de moitié dans l’exécution suivante, et ainsi de suite jusqu’à ce que le taux ne soit pas inférieur à `0.005`. 

   ```python
   # run.py
   import os
   
   reg = 10
   while reg > 0.005:
       os.system('az ml experiment submit -c local ./iris_sklearn.py {}'.format(reg))
       reg = reg / 2
   ```

   Pour lancer le script **run.py** à partir de la ligne de commande, exécutez les commandes suivantes :

   ```cmd
   REM Submit iris_sklearn.py multiple times with different regularization rates
   python run.py
   ```

   Quand `run.py` se termine, un graphique apparaît dans la vue de liste de l’historique des exécutions dans Azure Machine Learning Workbench.

## <a name="execute-in-a-docker-container-on-a-remote-machine"></a>Exécuter des scripts dans un conteneur Docker sur une machine distante
Pour exécuter votre script dans un conteneur Docker sur une machine Linux distante, vous devez disposer d’un accès SSH (nom d’utilisateur et mot de passe) à cette machine. En outre, le moteur Docker doit être installé et en cours d’exécution sur cette machine distante. Pour obtenir une machine Linux de ce type, le plus simple consiste à créer une [machine virtuelle DSVM (Data Science Virtual Machine) basée sur Ubuntu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) sur Azure. (Notez que la machine virtuelle DSVM basée sur CentOS n’est pas prise en charge.) 

1. Une fois la machine virtuelle créée, vous pouvez l’attacher en tant qu’environnement d’exécution en générant une paire de fichiers `.runconfig` et `.compute` à l’aide de la commande ci-dessous. Nous allons nommer le nouvel environnement `myvm`.
 
   ```azurecli
   REM create myvm compute target
   az ml computetarget attach --name myvm --address <IP address> --username <username> --password <password> --type remotedocker
   ```
   
   >[!NOTE]
   >La zone d’adresse IP peut également être adressable publiquement par le biais d’un FQDN (nom de domaine complet), tel que `vm-name.southcentralus.cloudapp.azure.com`. Nous vous conseillons d’ajouter le nom de domaine complet à votre machine virtuelle DSVM et de l’utiliser ici au lieu de l’adresse IP, car vous pourriez éteindre la machine virtuelle à un moment donné pour réaliser des économies. De plus, la prochaine fois que vous démarrez la machine virtuelle, l’adresse IP peut avoir changé.

   Ensuite, exécutez la commande suivante pour construire l’image Docker sur la machine virtuelle afin de la préparer pour l’exécution des scripts.
   
   ```azurecli
   REM prepare the myvm compute target
   az ml experiment prepare -c myvm
   ```
   >[!NOTE]
   >Vous pouvez également changer la valeur de `PrepareEnvironment` dans `myvm.runconfig` en remplaçant la valeur par défaut `false` par `true`. Cette opération prépare automatiquement le conteneur Docker à la première exécution.

2. Modifiez le fichier `myvm.runconfig` généré sous `aml_config` et changez le Framework en remplaçant la valeur par défaut `PySpark` par `Python` :

   ```yaml
   "Framework": "Python"
   ```
   >[!NOTE]
   >Vous pouvez également conserver la valeur PySpark pour le paramètre framework. Toutefois, ce paramétrage est quelque peu inefficace si vous n’avez pas vraiment besoin d’une session Spark pour exécuter votre script Python.

3. Exécutez la même commande que celle que vous avez exécutée auparavant dans la fenêtre CLI, mais cette fois, en ciblant _myvm_ :
   ```azurecli
   REM execute iris_sklearn.py in remote Docker container
   az ml experiment submit -c myvm .\iris_sklearn.py
   ```
   La commande est exécutée comme si vous utilisiez un environnement `docker-python`, à la différence que l’exécution se produit sur la machine virtuelle Linux distante. La fenêtre CLI affiche les mêmes informations de sortie.

4. Essayons Spark dans le conteneur. Ouvrez l’Explorateur de fichiers (vous pouvez également effectuer l’opération à partir de la fenêtre CLI si vous êtes familiarisé avec les commandes de manipulation de fichiers de base). Effectuez une copie du fichier `myvm.runconfig` et nommez-la `myvm-spark.runconfig`. Modifiez le nouveau fichier afin de remplacer la valeur `Python` du paramètre `Framework` par `PySpark` :
   ```yaml
   "Framework": "PySpark"
   ```
   N’apportez aucune modification au fichier `myvm.compute`. La même image Docker sur la même machine virtuelle est utilisée pour l’exécution Spark. Dans le nouveau fichier `myvy-spark.runconfig`, le champ `target` pointe vers le même fichier `myvm.compute` par le biais de son nom `myvm`.

5. Tapez la commande suivante pour l’exécuter dans l’instance Spark dans le conteneur Docker distant :
   ```azureli
   REM execute iris_pyspark.py in Spark instance on remote Docker container
   az ml experiment submit -c myvm-spark .\iris_pyspark.py
   ```

## <a name="execute-script-in-an-hdinsight-cluster"></a>Exécuter le script dans un cluster HDInsight
Vous pouvez également exécuter ce script dans un cluster Spark. 

1. Si vous avez accès à un cluster Spark pour Azure HDInsight, générez une commande de configuration d’exécution HDI, comme indiqué ci-après. Indiquez comme paramètres le nom du cluster HDInsight, ainsi que vos nom d’utilisateur et mot de passe HDInsight. Utilisez la commande suivante :

   ```azurecli
   REM create a compute target that points to a HDI cluster
   az ml computetarget attach --name myhdi --address <cluster head node FQDN> --username <username> --password <password> --type cluster

   REM prepare the HDI cluster
   az ml experiment prepare -c myhdi
   ```

   Le nom de domaine complet (FQDN) du nœud principal du cluster est généralement `<cluster_name>-ssh.azurehdinsight.net`.

   >[!NOTE]
   >`username` est le nom d’utilisateur SSH pour le cluster. La valeur par défaut est `sshuser` si vous ne la changez pas pendant l’approvisionnement HDI. Ce n’est pas `admin`, qui correspond à l’autre utilisateur créé pendant l’approvisionnement pour activer l’accès au site web d’administration du cluster. 

2. Exécutez la commande suivante afin d’exécuter le script dans le cluster HDInsight :

   ```azurecli
   REM execute iris_pyspark on the HDI cluster
   az ml experiment submit -c myhdi .\iris_pyspark.py
   ```

   >[!NOTE]
   >Quand vous effectuez une exécution sur un cluster HDI distant, vous pouvez également afficher les détails de l’exécution du travail YARN à l’adresse `https://<cluster_name>.azurehdinsight.net/yarnui` à l’aide du compte d’utilisateur `admin`.


## <a name="next-steps"></a>Étapes suivantes
Dans cette deuxième partie de la série de trois didacticiels, vous avez appris à utiliser les services Azure Machine Learning pour effectuer les opérations suivantes :
> [!div class="checklist"]
> * Travailler dans Azure Machine Learning Workbench
> * Ouvrir des scripts et passer le code en revue
> * Exécuter des scripts dans un environnement local
> * Examiner l’historique des exécutions
> * Exécuter des scripts dans un environnement Docker local
> * Exécuter des scripts dans une fenêtre Azure CLI locale
> * Exécuter des scripts dans un environnement Docker distant
> * Exécuter des scripts dans un environnement HDInsight cloud

Vous êtes prêt à passer à la troisième partie de la série. Le modèle de régression logistique étant créé, nous allons le déployer en tant que service web en temps réel.

> [!div class="nextstepaction"]
> [Déployer un modèle](tutorial-classifying-iris-part-3.md)
