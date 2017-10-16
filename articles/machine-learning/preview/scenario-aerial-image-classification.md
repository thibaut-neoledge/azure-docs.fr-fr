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
ms.openlocfilehash: 43b124fc3eb72adc5d299b218c9e16ec83d1a240
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
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

Les [instructions pas à pas](https://github.com/MicrosoftDocs/azure-docs-pr/tree/release-ignite-aml-v2/articles/machine-learning/) vous guident d’abord dans les étapes de création et de préparation d’un compte de stockage Azure et d’un cluster Spark, y compris le transfert des données et l’installation des dépendances. Ensuite, elles expliquent comment lancer les travaux d’apprentissage et comment comparer les performances des modèles obtenus. Pour finir, elles montrent comment appliquer un modèle choisi à un grand ensemble d’images sur le cluster Spark et comment analyser les résultats de prédiction localement.


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

    ````
    az login
    ```

    You are asked to visit a URL and type in a provided temporary code; the website requests your Azure account credentials.
    
1. When login is complete, return to the CLI and run the following command to determine which Azure subscriptions are available to your Azure account:

    ```
    az account list
    ```

    This command lists all subscriptions associated with your Azure account. Find the ID of the subscription you would like to use. Write the subscription ID where indicated in the following command, then set the active subscription by executing the command:

    ```
    az account set --subscription [subscription ID]
    ```

1. The Azure resources created in this example are stored together in an Azure resource group. Choose a unique resource group name and write it where indicated, then execute both commands to create the Azure resource group:

    ```
    set AZURE_RESOURCE_GROUP=[resource group name]
    az group create --location eastus --name %AZURE_RESOURCE_GROUP%
    ```

#### Create the storage account

We now create the storage account that hosts project files that must be accessed by the HDInsight Spark.

1. Choose a unique storage account name and write it where indicated in the following `set` command, then create an Azure storage account by executing both commands:

    ```
    set STORAGE_ACCOUNT_NAME=[storage account name]
    az storage account create --name %STORAGE_ACCOUNT_NAME% --resource-group %AZURE_RESOURCE_GROUP% --sku Standard_LRS
    ```

1. Issue the following command to list the storage account keys:

    ```
    az storage account keys list --resource-group %AZURE_RESOURCE_GROUP% --account-name %STORAGE_ACCOUNT_NAME%
    ```

    Record the value of `key1` as the storage key in the following command, then run the command to store the value.
    ```
    set STORAGE_ACCOUNT_KEY=[storage account key]
    ```
1. In your favorite text editor, load the `settings.cfg` file from the Azure Machine Learning Workbench project's "Code" subdirectory, and insert the storage account name and key as indicated. Save and close the `settings.cfg` file.
1. If you have not already done so, download and install the [AzCopy](http://aka.ms/downloadazcopy) utility. Ensure that the AzCopy executable is on your system path by typing "AzCopy" and pressing Enter to show its documentation.
1. Issue the following commands to copy all of the sample data, pretrained models, and model training scripts to the appropriate locations in your storage account:

    ```
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/test /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/test /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/train /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/train /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/middlesexma2016 /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/middlesexma2016 /DestKey:%STORAGE_ACCOUNT_KEY% /S
    AzCopy /Source:https://mawahsparktutorial.blob.core.windows.net/pretrainedmodels /SourceSAS:"?sv=2017-04-17&ss=bf&srt=sco&sp=rwl&se=2037-08-25T22:02:55Z&st=2017-08-25T14:02:55Z&spr=https,http&sig=yyO6fyanu9ilAeW7TpkgbAqeTnrPR%2BpP1eh9TcpIXWw%3D" /Dest:https://%STORAGE_ACCOUNT_NAME%.blob.core.windows.net/pretrainedmodels /DestKey:%STORAGE_ACCOUNT_KEY% /S
    ```

    Expect file transfer to take up to 20 minutes. While you wait, you can proceed to the following section. You may need to open another Command Line Interface through Workbench and redefine the temporary variables there.

#### Create the HDInsight Spark cluster

Our recommended method to create an HDInsight cluster uses the HDInsight Spark cluster Resource Manager template included in the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project.

1. The HDInsight Spark cluster template is the "template.json" file under the "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning" subfolder of this project. By default, the template creates a Spark cluster with 40 worker nodes. If you must adjust that number, open the template in your favorite text editor and replace all instances of "40" with the worker node number of your choice.
    - You may encounter out-of-memory errors if the number of worker nodes you choose is small. To combat memory errors, you may run the training and operationalization scripts on a subset of the available data as described later in this document.
2. Choose a unique name and password for the HDInsight cluster and write them where indicated in the following command. Then create the cluster by issuing the following command:

    ```
    set HDINSIGHT_CLUSTER_NAME=[HDInsight cluster name]
    set HDINSIGHT_CLUSTER_PASSWORD=[HDInsight cluster password]
    az group deployment create --resource-group %AZURE_RESOURCE_GROUP% --name hdispark --template-file "Code\01_Data_Acquisition_and_Understanding\01_HDInsight_Spark_Provisioning\template.json" --parameters storageAccountName=%STORAGE_ACCOUNT_NAME%.blob.core.windows.net storageAccountKey=%STORAGE_ACCOUNT_KEY% clusterName=%HDINSIGHT_CLUSTER_NAME% clusterLoginPassword=%HDINSIGHT_CLUSTER_PASSWORD%
    ```

Your cluster's deployment may take up to 30 minutes (including provisioning and script action execution).

### Prepare the Azure Machine Learning Workbench execution environment

#### Register the HDInsight cluster as an Azure Machine Learning Workbench compute target

Once HDInsight cluster creation is complete, register the cluster as a compute target for your project as follows:

1.  Issue the following command from the Azure Machine Learning Command Line Interface:

    ```
    az ml computetarget attach --name myhdi --address %HDINSIGHT_CLUSTER_NAME%-ssh.azurehdinsight.net --username sshuser --password %HDINSIGHT_CLUSTER_PASSWORD% -t cluster
    ```

    This command adds two files, `myhdi.runconfig` and `myhdi.compute`, to your project's `aml_config` folder.

1. Open the `myhdi.compute` file in your favorite text editor. Modify the `yarnDeployMode: cluster` line to read `yarnDeployMode: client`, then save and close the file.
1. Run the following command to prepare your environment for use:
   ```
   az ml experiment prepare -c myhdi
   ```

#### Install local dependencies

Open a CLI from Azure Machine Learning Workbench and install dependencies needed for local execution by issuing the following command:

```
pip install matplotlib azure-storage==0.36.0 pillow scikit-learn
```

## Data acquisition and understanding

This scenario uses publicly available aerial imagery data from the [National Agriculture Imagery Program](https://www.fsa.usda.gov/programs-and-services/aerial-photography/imagery-programs/naip-imagery/) at 1-meter resolution. We have generated sets of 224 pixel x 224 pixel PNG files cropped from the original NAIP data and sorted according to land use labels from the [National Land Cover Database](https://www.mrlc.gov/nlcd2011.php). A sample image with label "Developed" is shown at full size:

![A sample tile of developed land](media/scenario-aerial-image-classification/sample-tile-developed.png)

Class-balanced sets of ~44k and 11k images are used for model training and validation, respectively. We demonstrate model deployment on a ~67k image set tiling Middlesex County, MA -- home of Microsoft's New England Research and Development (NERD) center. For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

![Location of Middlesex County, Massachusetts](media/scenario-aerial-image-classification/middlesex-ma.png)

During setup, the aerial image sets used in this example were transferred to the storage account that you created. The training, validation, and operationalization images are all 224 pixel x 224 pixel PNG files at a resolution of one pixel per square meter. The training and validation images have been organized into subfolders based on their land use label. (The land use labels of the operationalization images are unknown and in many cases ambiguous; some of these images contain multiple land types.) For more information on how these image sets were constructed, see the [Embarrassingly Parallel Image Classification git repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification).

To view example images in your Azure storage account (optional):
1. Log in to the [Azure portal](https://portal.azure.com).
1. Search for the name of your storage account in the search bar along the top of your screen. Click on your storage account in the search results.
2. Click on the "Blobs" link in the storage account's main pane.
3. Click on the container named "train." You should see a list of directories named according to land use.
4. Click on any of these directories to load the list of images it contains.
5. Click on any image and download it to view the image.
6. If desired, click on the containers named "test" and "middlesexma2016" to view their contents as well.

## Modeling

### Training models with MMLSpark
The `run_mmlspark.py` script in the "Code\02_Modeling" subfolder of the Workbench project is used to train an [MMLSpark](https://github.com/Azure/mmlspark) model for image classification. The script first featurizes the training set images using an image classifier DNN pretrained on the ImageNet dataset (either AlexNet or an 18-layer ResNet). The script then uses the featurized images to train an MMLSpark model (either a random forest or a logistic regression model) to classify the images. The test image set is then featurized and scored with the trained model. The accuracy of the model's predictions on the test set is calculated and logged to Azure Machine Learning Workbench's run history feature. Finally, the trained MMLSpark model and its predictions on the test set are saved to blob storage.

Select a unique output model name for your trained model, a pretrained model type, and an MMLSpark model type. Write your selections where indicated in the following command template, then begin retraining by executing the command from an Azure ML Command Line Interface:

```
az ml experiment submit -c myhdi Code\02_Modeling\run_mmlspark.py --config_filename Code/settings.cfg --output_model_name [unique model name, alphanumeric characters only] --pretrained_model_type {alexnet,resnet18} --mmlspark_model_type {randomforest,logisticregression}
```

An additional `--sample_frac` parameter can be used to train and test the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of trained model accuracy. For more information on this and other parameters, run `python Code\02_Modeling\run_mmlspark.py -h`.

Users are encouraged to run this script several times with different input parameters. The performance of the resulting models can then be compared in Azure Machine Learning Workbench's Run History feature.

### Comparing model performance using the Workbench Run History feature

After you have executed two or more training runs of either type, navigate to the Run History feature in Workbench by clicking the clock icon along the left-hand menu bar. Select `run_mmlspark.py` from the list of scripts at left. A pane loads comparing the test set accuracy for all runs. To see more detail, scroll down and click on the name of an individual run.

## Deployment

To apply one of your trained models to aerial images tiling Middlesex County, MA using remote execution on HDInsight, insert your desired model's name into the following command and execute it:

```
az ml experiment submit -c myhdi Code\03_Deployment\batch_score_spark.py --config_filename Code/settings.cfg --output_model_name [trained model name chosen earlier]
```

An additional `--sample_frac` parameter can be used to operationalize the model with a subset of available data. Using a small sample fraction decreases runtime and memory requirements at the expense of prediction completeness. For more information on this and other parameters, run `python Code\03_Deployment\batch_score_spark -h`.

This script writes the model's predictions to your storage account. The predictions can be examined as described in the next section.

## Visualization

The "Model prediction analysis" Jupyter notebook in the "Code\04_Result_Analysis" subfolder of the Workbench project visualizes a model's predictions. Load and run the notebook as follows:
1. Open the project in Workbench and click on the folder ("Files") icon along the left-hand menu to load the directory listing.
2. Navigate to the "Code\04_Result_Analysis" subfolder and click on the notebook named "Model prediction analysis." A preview rendering of the notebook should be displayed.
3. Click "Start Notebook Server" to load the notebook.
4. In the first cell, enter the name of the model whose results you would like to analyze where indicated.
5. Click on "Cell -> Run All" to execute all cells in the notebook.
6. Read along with the notebook to learn more about the analyses and visualizations it presents.

## Cleanup
When you have completed the example, we recommend that you delete all of the resources you have created by executing the following command from the Azure Command Line Interface:

  ```
  az group delete --name %AZURE_RESOURCE_GROUP%
  ```

## References

- [The Embarrassingly Parallel Image Classification repository](https://github.com/Azure/Embarrassingly-Parallel-Image-Classification)
   - Describes dataset construction from freely available imagery and labels
- [MMLSpark](https://github.com/Azure/mmlspark) GitHub repository
   - Contains additional examples of model training and evaluation with MMLSpark

## Conclusions

Azure Machine Learning Workbench helps data scientists easily deploy their code on remote compute targets. In this example, local code was deployed for remote execution on an HDInsight cluster. Azure Machine Learning Workbench's run history feature tracked the performance of multiple models and helped us identify the most accurate model. Workbench's Jupyter notebooks feature helped visualize our models' predictions in an interactive, graphical environment.

## Next steps
To dive deeper into this example:
- In Azure Machine Learning Workbench's Run History feature, click the gear symbols to select which graphs and metrics are displayed.
- Examine the sample scripts for statements calling the `run_logger`. Check that you understand how each metric is being recorded.
- Examine the sample scripts for statements calling the `blob_service`. Check that you understand how trained models and predictions are stored and retrieved from the cloud.
- Explore the contents of the containers created in your blob storage account. Ensure that you understand which script or command is responsible for creating each group of files.
- Modify the training script to train a different MMLSpark model type or to change the model hyperparameters. Use the run history feature to determine whether your changes increased or decreased the model's accuracy.
