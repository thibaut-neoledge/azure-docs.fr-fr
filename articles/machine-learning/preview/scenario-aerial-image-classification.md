---
title: "Classification d’images aériennes | Microsoft Docs"
description: "Fournit des instructions pour la classification d’images aériennes dans un scénario réel"
author: mawah
ms.author: mawah
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.topic: article
ms.service: machine-learning
services: machine-learning
ms.date: 09/15/2017
ms.openlocfilehash: e6b673527d77550d4213e6d742156ccc525f6b44
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="aerial-image-classification"></a>Classification d’images aériennes

Cet exemple montre comment utiliser Azure Machine Learning Workbench pour coordonner l’apprentissage distribué et l’opérationnalisation des modèles de classification des images. Nous utilisons le package [Microsoft Machine Learning pour Apache Spark (MMLSpark)](https://github.com/Azure/mmlspark) pour caractériser les images selon des modèles CNTK préformés et pour former des classifieurs à partir des caractéristiques établies. Nous appliquons ensuite les modèles formés en parallèle à de grands ensembles d’images dans le cloud. Ces étapes sont effectuées sur un cluster [Azure HDInsight Spark](https://azure.microsoft.com/en-us/services/hdinsight/apache-spark/), ce qui nous permet de moduler la vitesse de l’apprentissage et de l’opérationnalisation en ajoutant ou en supprimant des nœuds worker.

La méthode d’apprentissage par transfert que nous présentons a plusieurs grands avantages par rapport au réapprentissage ou à l’optimisation d’un réseau neuronal profond : elle ne nécessite pas de calcul GPU, est intrinsèquement rapide et arbitrairement évolutif, et nécessite moins de paramètres. Cette méthode est par conséquent un excellent choix si vous avez peu d’exemples d’apprentissage, comme cela est fréquent dans les cas d’usage personnalisés. Beaucoup d’utilisateurs rapportent que l’apprentissage par transfert produit des modèles très performants, et leur évite d’avoir à effectuer un apprentissage plus long et plus coûteux d’un réseau neuronal profond.

Le cluster Spark utilisé dans cet exemple contient 40 nœuds worker et présente un coût de fonctionnement d’environ 40 $/h. Pour effectuer cette procédure pas à pas, comptez environ deux heures. Quand vous l’aurez terminée, suivez les instructions de nettoyage pour supprimer les ressources créées et arrêter la facturation.

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie

Le dépôt GitHub public correspondant à ce scénario réel contient tous les supports, y compris les exemples de code, nécessaires pour cet exemple :

[https://github.com/Azure/MachineLearningSamples-AerialImageClassification](https://github.com/Azure/MachineLearningSamples-AerialImageClassification)

## <a name="use-case-description"></a>Description du cas d’usage

Dans ce scénario, nous formons des modèles pour classer les types de sol montrés dans des images aériennes prises sur un tracé de 224 mètres sur 224. Les modèles de classification de l’occupation des sols sont utiles pour suivre l’évolution de l’urbanisation, de la déforestation, de la disparition des zones humides et d’autres tendances environnementales majeures à partir des images aériennes régulièrement collectées. Nous avons préparé des ensembles d’images d’apprentissage et de validation sur la base d’images issues du programme américain NAIP (National Agriculture Imagery Program) et d’étiquettes d’occupation des sols publiées dans la base de données NLCD (National Land Cover Database) des États-Unis. Exemples d’images de chaque classe d’occupation des sols :

![Exemples de régions pour chaque étiquette d’occupation des sols](media/scenario-aerial-image-classification/example-labels.PNG)

Après l’apprentissage et la validation du modèle de classifieurs, nous appliquons ce modèle aux images aériennes couvrant le comté du Middlesex au Massachusetts (où se trouve le siège du centre NERD de Microsoft pour la Nouvelle-Angleterre) pour illustrer l’utilisation de ce modèle dans l’étude des tendances du développement urbain.

Pour obtenir un classifieur d’images à l’aide de l’apprentissage par transfert, les chercheurs de données doivent souvent créer plusieurs modèles avec différentes méthodes et choisir ensuite le modèle le plus performant. Azure Machine Learning Workbench peut aider les chercheurs de données à coordonner l’apprentissage entre différents environnements Compute, à suivre et comparer les performances de plusieurs modèles, et à appliquer le modèle choisi à des jeux de données volumineux dans le cloud.

## <a name="scenario-structure"></a>Structure du scénario

Dans cet exemple, les images et les modèles préformés sont hébergés dans un compte de stockage Azure. Un cluster Azure HDInsight Spark lit ces fichiers et construit un modèle de classification des images à l’aide de MMLSpark. Le modèle formé et ses prédictions sont ensuite écrits dans le compte de stockage, où ils peuvent être analysés et visualisés par un notebook Jupyter exécuté localement. Azure Machine Learning Workbench coordonne l’exécution à distance des scripts sur le cluster Spark. Il effectue également le suivi des métriques de précision pour plusieurs modèles formés avec des méthodes différentes, permettant ainsi à l’utilisateur de choisir le modèle le plus performant.

![Schéma d’un scénario réel de classification d’images aériennes](media/scenario-aerial-image-classification/scenario-schematic.PNG)

Ces instructions vous expliquent pas à pas comment créer et préparer un compte de stockage Azure et un cluster Spark, notamment comment transférer des données et installer des dépendances. Ensuite, elles expliquent comment lancer les travaux d’apprentissage et comment comparer les performances des modèles obtenus. Pour finir, elles montrent comment appliquer un modèle choisi à un grand ensemble d’images sur le cluster Spark et comment analyser les résultats de prédiction localement.


## <a name="set-up-the-execution-environment"></a>Configurer l’environnement d’exécution

Les instructions suivantes vous guident pendant le processus de configuration de l’environnement d’exécution pour cet exemple.

### <a name="prerequisites"></a>Composants requis
- Un [compte Azure](https://azure.microsoft.com/en-us/free/) (des comptes d’essai gratuit sont disponibles)
    - Cet exemple crée un cluster HDInsight Spark contenant 40 nœuds worker (sur un total de 168 cœurs). Vérifiez que votre compte a assez de cœurs disponibles, dans l’onglet « Utilisation + quotas » de votre abonnement dans le Portail Azure.
    - Si vous n’avez pas assez de cœurs disponibles, modifiez le modèle de cluster HDInsight pour réduire le nombre de workers provisionnés. Pour obtenir les instructions correspondantes, consultez la section « Créer le cluster HDInsight Spark ».
- [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md)
    - Suivez le [guide de démarrage rapide Installer et créer](quickstart-installation.md) pour installer Azure Machine Learning Workbench et créer des comptes d’expérimentation et de gestion des modèles.
- [AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy), un utilitaire gratuit pour coordonner le transfert des fichiers entre les comptes de stockage Azure
    - Assurez-vous que le dossier contenant le fichier exécutable AzCopy est spécifié dans la variable d’environnement PATH de votre système. (Les instructions pour modifier les variables d’environnement sont disponibles [ici](https://support.microsoft.com/en-us/help/310519/how-to-manage-environment-variables-in-windows-xp)). Cet exemple a été testé sur un PC Windows 10. Vérifiez que vous pouvez l’exécuter sur n’importe quelle machine Windows, y compris Azure Data Science Virtual Machine. Vous devrez peut-être apporter quelques modifications mineures (par exemple, changer les variables filepath) si vous exécutez cet exemple sur macOS.

### <a name="set-up-azure-resources"></a>Configurer les ressources Azure

Cet exemple nécessite un cluster HDInsight Spark et un compte de stockage Azure pour héberger les fichiers appropriés. Suivez les instructions ci-dessous pour créer ces ressources dans un nouveau groupe de ressources Azure :

#### <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Classification d’images aériennes » et sélectionnez le modèle
5.  Cliquez sur **Créer**
 
#### <a name="create-the-resource-group"></a>Créer le groupe de ressources

1. Dans votre projet Azure Machine Learning Workbench, ouvrez une interface de ligne de commande (CLI) en cliquant sur Fichier -> Ouvrir l’invite de commandes.
1. À partir de l’interface de ligne de commande, connectez-vous à votre compte Azure en exécutant la commande suivante :

    ```
    az login
    ```

    Vous êtes invité à visiter une URL et à taper un code temporaire fourni. Le site web demande les informations d’identification de votre compte Azure.
    
1. Une fois la connexion établie, retournez à l’interface CLI et exécutez la commande suivante pour déterminer les abonnements Azure disponibles pour votre compte Azure :

    ```
    az account list
    ```

    Cette commande répertorie tous les abonnements associés à votre compte Azure. Recherchez l’ID de l’abonnement à utiliser. Écrivez l’ID d’abonnement à l’endroit indiqué dans la commande suivante, puis exécutez-la pour définir l’abonnement actif :

    ```
    az account set --subscription [subscription ID]
    ```

1. Les ressources Azure créées dans cet exemple sont stockées dans un groupe de ressources Azure. Choisissez un nom de groupe de ressources unique et écrivez-le à l’endroit indiqué, puis exécutez les deux commandes pour créer le groupe de ressources Azure :

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### <a name="create-the-storage-account"></a>Créer le compte de stockage

Nous allons maintenant créer le compte de stockage qui héberge les fichiers projet auxquels HDInsight Spark doit avoir accès.

1. Choisissez un nom de compte de stockage unique et écrivez-le à l’endroit indiqué dans la commande `set` suivante, puis créez un compte de stockage Azure en exécutant les deux commandes :

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Exécutez la commande suivante pour répertorier les clés de compte de stockage :

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Enregistrez la valeur de `key1` comme clé de stockage dans la commande suivante, puis exécutez-la pour stocker la valeur.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. Dans l’éditeur de texte de votre choix, chargez le fichier `settings.cfg` à partir du sous-répertoire « Code » du projet Azure Machine Learning Workbench, puis insérez le nom et la clé du compte de stockage aux endroits indiqués. Les lignes modifiées dans le fichier doivent se présenter ainsi :
    ```
    [Settings]
        # Credentials for the Azure Storage account
        storage_account_name = storacctname
        storage_account_key = kpI...88 chars total...Q==
    ```
    Enregistrez et fermez le fichier `settings.cfg`.
1. Si vous ne l’avez pas déjà fait, téléchargez et installez l’utilitaire [AzCopy](http://aka.ms/downloadazcopy). Vérifiez que l’exécutable AzCopy se trouve sur votre chemin système. Pour cela, tapez « AzCopy » et appuyez sur Entrée pour afficher sa documentation.
1. Lancez les commandes suivantes pour copier l’ensemble des exemples de données, des modèles préformés et des scripts d’apprentissage de modèle aux emplacements appropriés dans votre compte de stockage :

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Le transfert des fichiers peut prendre jusqu’à 20 minutes. En attendant, passez à la section suivante. Vous devrez peut-être ouvrir une autre interface de ligne de commande dans Workbench pour y redéfinir les variables temporaires.

#### <a name="create-the-hdinsight-spark-cluster"></a>Créer le cluster HDInsight Spark

Pour créer un cluster HDInsight, nous vous recommandons d’utiliser le modèle Resource Manager de cluster HDInsight Spark inclus dans le sous-dossier « Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning » de ce projet.

1. Le modèle de cluster HDInsight Spark est le fichier « template.json » qui se trouve dans le sous-dossier « Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning » de ce projet. Par défaut, le modèle crée un cluster Spark avec 40 nœuds Worker. Si vous devez ajuster ce nombre, ouvrez le modèle dans l’éditeur de texte de votre choix et remplacez toutes les instances de « 40 » par le nombre de nœuds Worker désiré.
    - Si vous choisissez un petit nombre de nœuds Worker, des erreurs de mémoire insuffisante risquent de se produire. Pour éviter ces erreurs, exécutez les scripts d’apprentissage et une d’opérationnalisation sur un sous-ensemble des données disponibles, comme décrit plus loin dans ce document.
2. Choisissez un nom et un mot de passe uniques pour le cluster HDInsight et écrivez-les aux endroits indiqués dans la commande suivante. Ensuite, exécutez la commande pour créer le cluster :

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Le déploiement de votre cluster peut prendre jusqu’à 30 minutes (ce qui comprend le provisionnement et l’exécution des actions de script).

### <a name="prepare-the-azure-machine-learning-workbench-execution-environment"></a>Préparer l’environnement d’exécution Azure Machine Learning Workbench

#### <a name="register-the-hdinsight-cluster-as-an-azure-machine-learning-workbench-compute-target"></a>Inscrire le cluster HDInsight comme cible de calcul Azure Machine Learning Workbench

Une fois la création du cluster HDInsight est terminée, inscrivez le cluster comme cible de calcul pour votre projet comme suit :

1.  Exécutez la commande suivante à partir de l’interface de ligne de commande Azure Machine Learning :

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    Cette commande ajoute deux fichiers, `myhdi.runconfig` et `myhdi.compute`, au dossier `aml_config` de votre projet.

1. Ouvrez le fichier `myhdi.compute` dans l’éditeur de texte de votre choix. Remplacez la ligne `yarnDeployMode: cluster` par `yarnDeployMode: client`, puis enregistrez et fermez le fichier.
1. Exécutez la commande suivante pour préparer votre environnement :
   ```
   az ml experiment prepare -c myhdi
   ```

#### <a name="install-local-dependencies"></a>Installer des dépendances locales

Ouvrez une interface CLI à partir d’Azure Machine Learning Workbench et exécutez la commande suivante pour installer les dépendances nécessaires pour une exécution locale :

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## <a name="data-acquisition-and-understanding"></a>Acquisition de données et compréhension

Ce scénario utilise les données publiques d’imagerie aérienne du programme NAIP ([National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/)) avec une résolution de 1 mètre. Nous avons rogné les données NAIP d’origine pour générer des jeux de fichiers PNG de 224 x 224 pixels, puis nous les avons triés selon les étiquettes d’occupation du sol de la base de données NLCD ([National Land Cover Database](https://www.mrlc.gov/nlcd2011.php)). Voici un exemple d’image avec l’étiquette « Developed » en taille réelle :

![Exemple de vignette d’un terrain développé](media/scenario-aerial-image-classification/sample-tile-developed.png)

Nous utilisons des jeux d’environ 44 000 images et 11 000 images en classes équilibrées pour l’apprentissage et la validation du modèle, respectivement. Pour démontrer le déploiement du modèle, nous utilisons un jeu d’environ 67 000 images couvrant le comté de Middlesex, dans le Massachusetts. C’est là que se trouve le centre de recherche et de développement de Microsoft en Nouvelle-Angleterre (NERD). Pour plus d’informations sur la construction de ces jeux d’images, consultez le [dépôt git Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Localisation du comté de Middlesex, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

Durant la configuration, les jeux d’images aériennes utilisés dans cet exemple ont été transférés au compte de stockage que vous avez créé. Toutes les images d’apprentissage, de validation et d’opérationnalisation sont des fichiers PNG de 224 x 224 pixels avec une résolution d’un pixel par mètre carré. Les images d’apprentissage et de validation ont été organisées dans des sous-dossiers selon leur étiquette d’occupation du sol. (Les étiquettes d’occupation du sol des images d’opérationnalisation sont inconnues et, dans de nombreux cas, ambiguës ; certaines de ces images de contiennent plusieurs types de sols.) Pour plus d’informations sur la construction de ces jeux d’images, consultez le [dépôt git Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

Pour afficher des exemples d’images dans votre compte de stockage Azure (facultatif) :
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Recherchez le nom de votre compte de stockage dans la barre de recherche en haut de l’écran. Cliquez sur votre compte de stockage dans les résultats de la recherche.
2. Cliquez sur le lien « Objets blob » dans le volet principal du compte de stockage.
3. Cliquez sur le conteneur nommé « train ». Vous devriez voir une liste de répertoires nommés en fonction de l’occupation du sol.
4. Cliquez sur l’un de ces répertoires pour charger la liste des images qu’il contient.
5. Cliquez sur n’importe quelle image et téléchargez-la pour l’afficher.
6. Si vous le souhaitez, cliquez sur les conteneurs nommés « test » et « middlesexma2016 » pour afficher leur contenu.

## <a name="modeling"></a>Modélisation

### <a name="training-models-with-mmlspark"></a>Modèles d’apprentissage avec MMLSpark
Le script `run_mmlspark.py` dans le sous-dossier « Code\02_Modeling » du projet Workbench sert à effectuer l’apprentissage d’un modèle [MMLSpark](https://github.com/Azure/mmlspark) pour la classification d’images. Le script commence par caractériser les images du jeu d’apprentissage à l’aide d’un DNN de classification d’images préformé sur le jeu de données ImageNet (AlexNet ou ResNet à 18 couches). Le script utilise ensuite les images caractérisées pour effectuer l’apprentissage d’un modèle MMLSpark (forêt aléatoire ou modèle de régression logistique) pour classer les images. Le jeu d’images de test est ensuite caractérisé et noté avec le modèle formé. La précision des prédictions du modèle sur le jeu de test est calculée et consignée dans la fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench. Enfin, le modèle MMLSpark formé et ses prédictions sur le jeu de test sont enregistrés dans le stockage d’objets blob.

Sélectionnez un nom de modèle de sortie unique pour votre modèle formé, un type de modèle préformé et un type de modèle MMLSpark. Écrivez vos sélections aux endroits indiqués dans le modèle de commande suivant, puis commencez le réapprentissage en exécutant la commande à partir d’une interface de ligne de commande Microsoft Azure Machine Learning :

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

Vous pouvez utiliser un paramètre `--sample_frac` supplémentaire pour effectuer l’apprentissage du modèle et le tester avec un sous-ensemble des données disponibles. L’utilisation d’une fraction des échantillons permet de diminuer les exigences liées au runtime et à la mémoire, mais c’est au détriment de la précision du modèle formé. Pour plus d’informations sur ce paramètre et les autres disponibles, exécutez `python Code\02_Modeling\run_mmlspark.py -h`.

Les utilisateurs sont invités à exécuter ce script plusieurs fois avec différents paramètres d’entrée. Vous pouvez ensuite comparer les performances des modèles résultants dans la fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench.

### <a name="comparing-model-performance-using-the-workbench-run-history-feature"></a>Comparaison des performances des modèles à l’aide de la fonctionnalité d’historique des exécutions de Workbench

Après au moins deux exécutions d’apprentissage d’un type ou l’autre, cliquez sur l’icône d’horloge située à côté de la barre de menus de gauche pour accéder à la fonctionnalité Historique des exécutions dans Workbench. Sélectionnez `run_mmlspark.py` dans la liste des scripts à gauche. Un volet se charge et compare la précision du jeu de test pour toutes les exécutions. Pour plus de détails, faites défiler vers le bas et cliquez sur le nom d’une exécution individuelle.

## <a name="deployment"></a>Déploiement

Pour appliquer l’un de vos modèles formés aux images aériennes couvrant le comté de Middlesex, MA à l’aide de l’exécution distante sur HDInsight, insérez le nom du modèle désiré dans la commande suivante et exécutez-la :

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

Vous pouvez utiliser un paramètre `--sample_frac` supplémentaire pour opérationnaliser le modèle avec un sous-ensemble des données disponibles. L’utilisation d’une fraction des échantillons permet de diminuer les exigences liées au runtime et à la mémoire, mais c’est au détriment de la précision du modèle formé. Pour plus d’informations sur ce paramètre et les autres paramètres disponibles, exécutez `python Code\03_Deployment\batch_score_spark -h`.

Ce script écrit les prédictions du modèle dans votre compte de stockage. Vous pouvez examiner les prédictions selon les indications de la section suivante.

## <a name="visualization"></a>Visualisation

Le notebook Jupyter « Model prediction analysis » dans le sous-dossier « Code\04_Result_Analysis » du projet Workbench visualise les prédictions d’un modèle. Pour charger et exécuter le notebook, effectuez les étapes suivantes :
1. Ouvrez le projet dans Workbench et cliquez sur l’icône de dossier (« Files ») à côté du menu de gauche pour charger la liste de répertoires.
2. Accédez au sous-dossier « Code\04_Result_Analysis » et cliquez sur le notebook nommé « Model prediction analysis ». Un rendu de l’aperçu du notebook doit s’afficher.
3. Cliquez sur « Start Notebook Server » pour charger le notebook.
4. Dans la première cellule, entrez le nom du modèle dont vous souhaitez analyser les résultats à l’endroit indiqué.
5. Cliquez sur « Cell -> Run All » pour exécuter toutes les cellules dans le notebook.
6. Passez en revue le notebook pour découvrir plus en détail les analyses et les visualisations qu’il présente.

## <a name="cleanup"></a>Nettoyage
Une fois l’exemple terminé, nous vous recommandons de supprimer toutes les ressources que vous avez créées en exécutant la commande suivante à partir de l’interface de ligne de commande Azure :

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## <a name="references"></a>Références

- [Dépôt Embarrassingly Parallel Image Classification](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Décrit la construction du jeu de données au moyen d’images disponibles gratuitement et d’étiquettes
- Dépôt GitHub [MMLSpark](https://github.com/Azure/mmlspark)
   - Contient des exemples supplémentaires d’apprentissage et d’évaluation de modèle avec MMLSpark

## <a name="conclusions"></a>Conclusions

Azure Machine Learning Workbench permet aux scientifiques des données de déployer facilement leur code sur des cibles de calcul distantes. Dans cet exemple, le code local a été déployé pour une exécution à distance sur un cluster HDInsight. La fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench nous a permis de faire le suivi des performances de plusieurs modèles et d’identifier le modèle le plus exact. Grâce aux notebooks Jupyter de Workbench, nous avons pu visualiser les prédictions de nos modèles dans un environnement graphique interactif.

## <a name="next-steps"></a>Étapes suivantes
Pour approfondir cet exemple :
- Dans la fonctionnalité d’historique des exécutions d’Azure Machine Learning Workbench, cliquez sur le symbole d’engrenage pour sélectionner les graphes et les métriques à afficher.
- Examinez les instructions qui appellent `run_logger` dans les exemples de scripts. Veillez à bien comprendre comment chaque métrique est enregistrée.
- Examinez les instructions qui appellent `blob_service` dans les exemples de scripts. Veillez à bien comprendre comment les modèles formés et les prédictions sont stockés dans le cloud et récupérés.
- Explorez le contenu des conteneurs créés dans votre compte de stockage d’objets blob. Veillez à bien comprendre le script ou la commande responsable de la création de chaque groupe de fichiers.
- Modifiez le script d’apprentissage pour effectuer l’apprentissage d’un autre type de modèle MMLSpark ou pour changer les hyperparamètres du modèle. Utilisez la fonctionnalité d’historique des exécutions pour déterminer si vos changements ont permis d’améliorer ou non la précision du modèle.
