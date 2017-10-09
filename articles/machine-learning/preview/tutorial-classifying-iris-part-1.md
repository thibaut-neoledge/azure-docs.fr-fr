---
title: "Préparer les données pour le didacticiel de classification d’Iris dans les services de Machine Learning (préversion) | Microsoft Docs"
description: "Ce didacticiel complet montre comment utiliser les services Azure Machine Learning (préversion) de bout en bout. Cette première partie est consacrée à la préparation des données."
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e88b6bf74b7492353e5d5d004bde12fa4787e405
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="classifying-iris-part-1-prepare-data"></a>Classification d’Iris, première partie : préparer les données
Les services Azure Machine Learning (préversion) forment une solution d’analyse avancée et de science des données intégrée de bout en bout qui permet aux scientifiques professionnels de préparer des données, développer des expériences et déployer des modèles à l’échelle du cloud.

Ce didacticiel est la première partie d’une série qui en compte trois. Dans ce didacticiel, nous allons étudier les principes de base des services d’Azure Machine Learning (préversion). Ce didacticiel vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un projet dans Azure Machine Learning Workbench
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler le package de préparation de données

Pour simplifier les choses, ce didacticiel utilise le [jeu de données Iris de Fisher](https://en.wikipedia.org/wiki/Iris_flower_data_set) intemporel. Les captures d’écran sont spécifiques à Windows, mais l’expérience macOS est presque identique.

## <a name="launch-azure-machine-learning-workbench"></a>Lancer Azure Machine Learning Workbench
Consultez le [guide de démarrage rapide d’installation et de création](quickstart-installation.md) pour installer l’application Azure Machine Learning Workbench. Celle-ci inclut l’interface de ligne de commande (CLI). Lancez l’application Azure Machine Learning Workbench et connectez-vous si nécessaire.

## <a name="create-a-new-project"></a>Création d'un projet
1. Dans le volet **PROJETS**, cliquez sur l’icône **+** pour créer un **Nouveau projet**.

   ![nouvel espace de travail](media/tutorial-classifying-iris/new_ws.png)

2. Sous **Créer un projet**, indiquez les détails du projet. 

   ![Nouveau projet](media/tutorial-classifying-iris/new_project.png)

   - Dans le champ **Nom du projet**, nommez le projet. Par exemple, utilisez la valeur **myIris**.
   - Choisissez le **Répertoire du projet** dans lequel le projet est créé. Par exemple, utilisez la valeur **C:\Temp**. 
   - Entrez la **Description du projet**. 
   - Le champ **Dépôt Git** est facultatif et peut être vide. Vous pouvez fournir un dépôt Git vide existant (dépôt sans branche maître) sur VSTS. Vous pourrez ainsi activer des scénarios d’itinérance et de partage plus tard. Pour plus d’informations, consultez l’article [Utilisation du dépôt Git](using-git-ml-project.md). 
   - Choisissez un **Espace de travail**. Par exemple, ce didacticiel utilise **IrisGarden**. 
   - Sélectionnez le modèle **Classification d’Iris** dans la liste des modèles de projet. 

3. Cliquez sur le bouton **Créer** pour créer le projet. Un projet est créé et ouvert pour vous.

## <a name="create-a-data-preparation-package"></a>Créer un package de préparation de données
1. Ouvrez le fichier `iris.csv` à partir de l’**affichage des fichiers**. Le fichier se compose d’une table simple de 5 colonnes et de 150 lignes. Elle comprend quatre colonnes numériques et une colonne cible de chaînes. Il n’y a pas d’en-têtes de colonnes.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   >Remarque : Il est déconseillé d’inclure des fichiers de données dans votre dossier de projet, en particulier s’ils sont volumineux. Nous incluons le fichier `iris.csv` dans ce modèle à des fins de démonstration, car il est très petit. Pour plus d’informations, consultez l’article [Guide pratique pour lire et écrire des fichiers de données volumineux](how-to-read-write-files.md).

2. Dans la **Vue de données**, cliquez sur l’icône **+** pour ajouter une nouvelle source de données. L’Assistant **Ajouter une source de données** démarre. 

   ![vue de données](media/tutorial-classifying-iris/data_view.png)

3. Sélectionnez l’option **Fichier(s)/Répertoire**, puis choisissez le fichier local `iris.csv`. Acceptez les paramètres par défaut pour chaque écran, puis cliquez sur **Terminer**. 

   ![sélectionner iris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Veillez à sélectionner le fichier `iris.csv` dans le répertoire de projet actif pour cet exercice ; sinon, les étapes suivantes risquent d’échouer. 

4. Un nouveau fichier `iris-1.dsource` est créé. Le fichier est nommé de façon unique à l’aide d’un tiret et du chiffre un, car l’exemple de projet est déjà fourni avec un fichier non numéroté (`iris.dsource`).  Le fichier s’ouvre et les données s’affichent. Une série d’en-têtes de colonne, de `Column1` à `Column5`, est automatiquement ajoutée à ce jeu de données. Si vous faites défiler les données vers le bas, vous noterez que la dernière ligne du jeu de données est vide. Cela est dû à un saut de ligne supplémentaire dans le fichier csv.

   ![affichage de données iris](media/tutorial-classifying-iris/iris_data_view.png)

5. Cliquez maintenant sur le bouton **Métriques**. Observez les histogrammes et le jeu complet de statistiques pour chaque colonne. Vous pouvez également cliquer sur le bouton **Données** pour afficher à nouveau les données. 

   ![affichage de données iris](media/tutorial-classifying-iris/iris_metrics_view.png)

6. Cliquez sur le bouton **Préparer** à côté du bouton **Métriques** (ou le bouton **Données** si vous êtes dans l’affichage des métriques). La boîte de dialogue **Préparer** s’affiche. L’exemple de projet étant déjà fourni avec un fichier `iris.dprep`, vous êtes invité par défaut à créer un flux de données dans le package de préparation de données **iris.dprep** existant. Remplacez la valeur de liste déroulante par **+Nouveau package de préparation de données**, entrez une nouvelle valeur « iris-1 », puis cliquez sur **OK**.

   ![affichage de données iris](media/tutorial-classifying-iris/new_dprep.png)

Un package de préparation de données nommé `iris-1.dprep` est créé et ouvert dans l’éditeur de préparation des données.

Effectuons à présent quelques tâches simples de préparation de données. Renommez les noms des colonnes. Il vous suffit de cliquer sur chaque en-tête de colonne pour modifier le texte de l’en-tête. Entrez les noms de colonnes suivants : `Sepal Length`, `Sepal Width`, `Petal Length`, `Petal Width` et `Species`.

![renommer des colonnes](media/tutorial-classifying-iris/rename_column.png)

Sélectionnez la colonne `Species`, puis cliquez dessus avec le bouton droit. Choisissez **Nombres de valeurs**. 

![nombre de valeurs](media/tutorial-classifying-iris/value_count.png)

Cette action crée un histogramme avec quatre barres. Notez que la colonne cible comprend trois valeurs distinctes : `Iris_virginica`, `Iris_versicolor` et `Iris-setosa`. Il y a également une ligne avec une valeur `(null)`. Supprimez cette ligne. Pour cela, sélectionnez la barre représentant la valeur null et cliquez sur le bouton de filtre **-**. 

![nombre de valeurs](media/tutorial-classifying-iris/filter_out.png)

Chaque action que vous effectuez, qu’il s’agisse de renommer des colonnes ou d’exclure une ligne en filtrant sur la valeur null, est enregistrée en tant qu’étape de préparation des données dans le volet **ÉTAPES**. Vous pouvez les modifier (pour régler leurs paramètres), les réorganiser ou même les supprimer.

![steps](media/tutorial-classifying-iris/steps.png)

## <a name="generate-pythonpyspark-code-to-invoke-data-prep-package"></a>Générer du code Python/PySpark pour appeler le package de préparation de données

Maintenant, fermez l’éditeur de préparation de données. (Ne vous inquiétez pas, les données sont enregistrées automatiquement.) Cliquez avec le bouton droit sur le fichier **iris-1.dprep** pour afficher le menu contextuel, puis choisissez **Générer un fichier de code d’accès aux données**. 

![générer le code](media/tutorial-classifying-iris/generate_code.png)

Un fichier **iris-1.py** est créé avec deux lignes de code préremplies (et des commentaires) :

```python
# This code snippet will load the referenced package and return a DataFrame.
# If the code is run in a PySpark environment, the code will return a
# Spark DataFrame. If not, the code will return a Pandas DataFrame.

from azureml.dataprep.package import run
df = run('iris.dprep', dataflow_idx=0)
```
Cet extrait de code appelle la logique que vous avez créée sous la forme d’un package de préparation de données. Selon le contexte dans lequel ce code est exécuté, `df` peut être un [DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) s’il est exécuté dans le runtime Python ou un [DataFrame Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) s’il est exécuté dans un contexte Spark. Pour plus d’informations sur la préparation des données dans Azure Machine Learning Workbench, consultez le guide [Bien démarrer avec la préparation des données](data-prep-getting-started.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce premier didacticiel de notre série qui en compte trois, vous avez utilisé Azure Machine Learning Workbench pour :
> [!div class="checklist"]
> * Création d'un projet 
> * Créer un package de préparation de données
> * Générer du code Python/PySpark pour appeler le package de préparation de données

Vous pouvez à présent passer à la partie suivante de la série pour générer un modèle d’apprentissage automatique.
> [!div class="nextstepaction"]
> [Générer un modèle](tutorial-classifying-iris-part-2.md)

