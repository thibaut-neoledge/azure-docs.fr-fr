---
title: "Appeler des programmes Spark à partir d’Azure Data Factory | Microsoft Docs"
description: "Apprenez à appeler des programmes Spark à partir d&quot;une fabrique de données Azure avec l&quot;activité MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 124f04eb39296899af501d506476ad966442fc3d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Appeler des programmes Spark à partir des pipelines Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Activité Hive](data-factory-hive-activity.md)
> * [Activité pig](data-factory-pig-activity.md)
> * [Activité MapReduce](data-factory-map-reduce.md)
> * [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Activité Spark](data-factory-spark.md)
> * [Activité d’exécution par lot Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Activité des ressources de mise à jour de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Activité de procédure stockée](data-factory-stored-proc-activity.md)
> * [Activité U-SQL Data Lake Analytics](data-factory-usql-activity.md)
> * [Activité personnalisée .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduction
L’activité Spark est l’une des [activités de transformation des données](data-factory-data-transformation-activities.md) prises en charge par Azure Data Factory. Cette activité exécute le programme Spark spécifié sur votre cluster Apache Spark dans Azure HDInsight.    

> [!IMPORTANT]
> - L’activité Spark ne prend pas en charge les clusters Spark HDInsight qui utilisent Azure Data Lake Store en tant que stockage principal.
> - L’activité Spark prend en charge uniquement les clusters Spark HDInsight existants (c’est-à-dire vos propres clusters). Elle ne prend pas en charge les services liés HDInsight à la demande.

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>Procédure pas à pas : création d’un pipeline avec l’activité Spark
Voici les étapes classiques pour créer un pipeline Data Factory avec une activité Spark.  

1. Créer une fabrique de données.
2. Créez un service lié Azure Storage pour lier à la fabrique de données le stockage Azure qui est associé à votre cluster Spark HDInsight.     
2. Créez un service lié Azure HDInsight qui relie votre cluster Apache Spark dans Azure HDInsight à la fabrique de données.
3. Créez un jeu de données faisant référence au service lié Stockage Azure. Actuellement, vous devez spécifier un jeu de données de sortie d’une activité même si aucune sortie n’est produite.  
4. Créez un pipeline avec une activité Spark faisant référence au service lié HDInsight créé au point 2. L’activité est configurée avec le jeu de données que vous avez créé à l’étape précédente comme un jeu de données de sortie. Le jeu de données de sortie pilote la planification (horaire, quotidienne, etc.). Par conséquent, vous devez spécifier le jeu de données de sortie même si l’activité ne produit pas vraiment de sortie.

### <a name="prerequisites"></a>Composants requis
1. Créez un **compte de stockage Azure général** en suivant les instructions fournies dans la procédure pas à pas : [Create a storage account](../storage/storage-create-storage-account.md#create-a-storage-account) (Création d’un compte de stockage).  
2. Créez un **cluster Apache Spark dans Azure HDInsight** en suivant les instructions contenues dans le didacticiel [Create Apache Spark cluster in Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) (Création d’un cluster Apache Spark dans Azure HDInsight). Associez le compte de stockage Azure créé à l’étape 1 à ce cluster.  
3. Téléchargez et consultez le fichier de script Python **test.py** situé à l’adresse : [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).  
3.  Téléchargez le fichier **test.py** dans le dossier **pyFiles** du conteneur **adfspark** figurant dans votre stockage Blob Azure. Créez le conteneur et le dossier s’ils n’existent pas.

### <a name="create-data-factory"></a>Créer une fabrique de données
Commençons par la création de la fabrique de données dans cette étape.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **NOUVEAU** dans le menu de gauche, puis sur **Données et analyse** et sur **Data Factory**.
3. Dans le panneau **Nouvelle fabrique de données**, entrez **SparkDF** dans le champ Nom.

   > [!IMPORTANT]
   > Le nom de la fabrique de données Azure doit être un nom **global unique**. Si vous recevez l’erreur : **Le nom de fabrique de données « SparkDF » n’est pas disponible**. Modifiez le nom de la fabrique de données (par exemple, votrenomSparkDFdate) et réessayez. Consultez la rubrique [Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.   
4. Sélectionnez l’ **abonnement Azure** où vous voulez que la fabrique de données soit créée.
5. Sélectionnez un **groupe de ressources** Azure existant ou créez-en un.
6. Sélectionnez l’option **Épingler au tableau de bord**.  
6. Cliquez sur **Créer** dans le panneau **Nouvelle fabrique de données**.

   > [!IMPORTANT]
   > Pour créer des instances Data Factory, vous devez avoir un rôle de [collaborateur de fabrique de données](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) au niveau de l’abonnement/du groupe de ressources.
7. La fabrique de données apparaît comme étant en cours de création dans le **tableau de bord** du portail Azure, comme suit :   
8. Une fois la fabrique de données créée, la page correspondante s’affiche et indique son contenu. Si vous ne voyez pas cette page, cliquez sur la vignette de votre fabrique de données sur le tableau de bord.

    ![Panneau Data Factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>créer des services liés
Dans cette étape, vous allez créer deux services liés, l’un pour lier votre cluster Spark à votre fabrique de données, l’autre pour lier votre stockage Azure à votre fabrique de données.  

#### <a name="create-azure-storage-linked-service"></a>Créer le service lié Azure Storage
Dans cette étape, vous liez votre compte Stockage Azure à votre fabrique de données. Un jeu de données que vous allez créer plus loin dans cette procédure fait référence à ce service lié. Le service lié HDInsight que vous définissez dans l’étape suivante fait également référence à ce service lié.  

1. Cliquez sur **Créer et déployer** dans le panneau **Fabrique de données** de votre fabrique de données. Vous devriez voir l’éditeur Data Factory Editor.
2. Cliquez sur **Nouveau magasin de données** et choisissez **Stockage Azure**.

   ![Nouveau magasin de données - Stockage Azure - menu](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Le **script JSON** de création d’un service lié Microsoft Azure Storage doit apparaître dans l’éditeur.

   ![Service lié Azure Storage](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Remplacez **account name** et **account key** par le nom et la clé de votre compte de stockage Azure. Pour savoir comment obtenir votre clé d’accès de stockage, reportez-vous aux informations sur l’affichage, la copie et la régénération de clés d’accès de stockage dans [Gérer votre compte de stockage](../storage/storage-create-storage-account.md#manage-your-storage-account).
5. Pour déployer le service lié, cliquez sur **Déployer** dans la barre de commandes. Une fois que le service lié est déployé, la fenêtre**Draft-1** doit disparaître tandis que **AzureStorageLinkedService** s’affiche dans l’arborescence sur la gauche.

#### <a name="create-hdinsight-linked-service"></a>Créer un service lié Azure HDInsight
Dans cette étape, vous allez créer un service lié Azure HDInsight qui relie votre cluster Spark HDInsight à la fabrique de données. Le service lié HDInsight est utilisé pour exécuter le programme Spark spécifié dans l’activité Spark du pipeline de cet exemple.  

1. Si ce bouton n'est pas affiché dans la barre d'outils, cliquez sur **... Plus** dans la barre d’outils, cliquez sur **Nouveau calcul**, puis cliquez sur **Cluster HDInsight**.

    ![Créer un service lié Azure HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)
2. Copiez et collez l’extrait ci-dessous dans la fenêtre **Draft-1** . Dans l’éditeur JSON, procédez comme suit :
    1. Spécifier **l’URI** du cluster Spark HDInsight. Par exemple : `https://<sparkclustername>.azurehdinsight.net/`.
    2. Spécifiez le nom de **l’utilisateur** qui a accès au cluster Spark.
    3. Spécifiez le **mot de passe** de l’utilisateur.
    4. Spécifiez le **service lié du stockage Azure** associé au cluster Spark HDInsight. Dans cet exemple, il s’agit de **AzureStorageLinkedService**.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - L’activité Spark ne prend pas en charge les clusters Spark HDInsight qui utilisent Azure Data Lake Store en tant que stockage principal.
    > - L’activité Spark prend en charge uniquement le cluster Spark HDInsight existant (c’est-à-dire votre propre cluster). Elle ne prend pas en charge les services liés HDInsight à la demande.

    Consultez la page consacrée au [service lié HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) pour plus d’informations sur le service lié HDInsight.
3.  Pour déployer le service lié, cliquez sur **Déployer** dans la barre de commandes.  

### <a name="create-output-dataset"></a>Créer un jeu de données de sortie
Le jeu de données de sortie pilote la planification (horaire, quotidienne, etc.). Par conséquent, vous devez spécifier un jeu de données de sortie pour l’activité Spark du pipeline, même si l’activité ne produit pas vraiment de sortie. Vous n’êtes pas obligé de spécifier un jeu de données d’entrée pour l’activité.

1. Dans **Data Factory Editor**, cliquez sur **... Plus** dans la barre de commandes, cliquez sur **Nouveau jeu de données** et sélectionnez **Stockage d’objets Blob Azure**.  
2. Copiez et collez l’extrait ci-dessous dans la fenêtre Draft-1. L’extrait de code JSON définit un jeu de données appelé **OutputDataset**. En outre, vous indiquez que les résultats sont stockés dans le conteneur d’objets blob nommé **adfspark** et dans le dossier nommé **pyFiles/output**. Comme mentionné précédemment, ce jeu de données est un jeu de données factice. Le programme Spark, dans cet exemple, ne produit pas de sortie. La section **availability** spécifie que le jeu de données de sortie est produit tous les jours.  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. Pour déployer le jeu de données, cliquez sur **Déployer** dans la barre de commandes.


### <a name="create-pipeline"></a>Création d’un pipeline
Dans cette étape, vous allez créer un pipeline avec une activité **HDInsightSpark**. À ce stade, c'est le jeu de données de sortie qui pilote la planification : vous devez donc créer un jeu de données de sortie même si l’activité ne génère aucune sortie. Si l’activité ne prend aucune entrée, vous pouvez ignorer la création du jeu de données d’entrée. Par conséquent, aucun jeu de données d’entrée n’est spécifié dans cet exemple.

1. Dans **Data Factory Editor**, cliquez sur **… Plus** dans la barre de commandes, puis cliquez sur **Nouveau pipeline**.
2. Remplacez le script de la fenêtre Draft-1 par le script suivant :

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Notez les points suivants :
    - La propriété **type** est définie sur **HDInsightSpark**.
    - **rootPath** est définie sur **adfspark\\pyFiles**, où adfspark est le conteneur d’objets Blob Azure contenant le dossier pyFiles. Dans cet exemple, le stockage Blob Azure est celui qui est associé au cluster Spark. Vous pouvez charger le fichier vers un autre stockage Azure. Si vous procédez ainsi, créez un service lié de stockage Azure pour lier ce compte de stockage à la fabrique de données. Ensuite, spécifiez le nom du service lié en tant que valeur pour la propriété **sparkJobLinkedService**. Consultez les [propriétés de l’activité Spark](#spark-activity-properties) pour plus d’informations sur cette propriété et d’autres propriétés prises en charge par l’activité Spark.  
    - La propriété **entryFilePath** est définie sur **test.py**, c’est-à-dire le fichier python.
    - La propriété **getDebugInfo** est définie sur **Always**, ce qui signifie que les fichiers journaux sont toujours générés (succès ou échec).

        > [!IMPORTANT]
        > Nous vous recommandons de ne pas définir cette propriété sur `Always` dans un environnement de production, sauf si vous dépannez un problème.
    - La section **outputs** contient un jeu de données de sortie. Vous devez spécifier un jeu de données de sortie même si le programme Spark ne génère aucune sortie. Le jeu de données de sortie pilote la planification du pipeline (horaire, quotidienne, etc.).  

        Pour plus d’informations sur les propriétés prises en charge par l’activité Spark, consultez la section [Propriétés de l’activité Spark](#spark-activity-properties).
3. Cliquez sur **Déployer** dans la barre de commandes pour déployer le pipeline.

### <a name="monitor-pipeline"></a>Surveillance d’un pipeline
1. Cliquez sur **X** pour fermer les panneaux Data Factory Editor et revenir à la page d’accueil Data Factory. Cliquez sur **Surveiller et gérer** pour lancer l’application d’analyse dans un autre onglet.

    ![Mosaïque Surveiller et gérer](media/data-factory-spark/monitor-and-manage-tile.png)
2. Modifiez le filtre de **début** en haut de la page sur **2/1/2017**, puis cliquez sur **Appliquer**.
3. Vous ne devriez voir qu’une seule fenêtre d’activité puisqu’il n’y a qu’un seul jour entre le début (2017-02-01) et la fin (2017-02-02) du pipeline. Vérifiez que la tranche de données est à l’état **Prêt**.

    ![Surveiller le pipeline](media/data-factory-spark/monitor-and-manage-app.png)    
4. Sélectionnez la **fenêtre activité** pour afficher les détails concernant l’exécution de l’activité. S’il y a une erreur, vous pouvez en afficher les détails dans le volet droit.

### <a name="verify-the-results"></a>Vérifier les résultats

1. Lancez le **bloc-notes Jupyter** pour votre cluster Spark HDInsight via l’URL suivante : https://NOMDUCLUSTER.azurehdinsight.net/jupyter. Vous pouvez également lancer le tableau de bord de votre cluster Spark HDInsight, puis ouvrir le **bloc-notes Jupyter**.
2. Cliquez sur **Nouveau** -> **PySpark** pour ouvrir un nouveau bloc-notes.

    ![Nouveau bloc-notes Jupyter](media/data-factory-spark/jupyter-new-book.png)
3. Exécutez la commande suivante en copiant/collant le texte et en appuyant sur les touches **MAJ + ENTRÉE** à la fin de la deuxième instruction.  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Vérifiez que les données figurent dans la table hvac :  

    ![Résultats de la requête Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

Consultez la section relative à [l’exécution d’une requête SQL Spark](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md#run-an-interactive-spark-sql-query) pour obtenir des instructions détaillées. 

### <a name="troubleshooting"></a>Résolution de problèmes
Comme vous définissez **getDebugInfo** sur **Toujours**, un sous-dossier **log** apparaît dans le dossier **pyFiles** de votre conteneur d’objets Blob Azure. Le fichier journal figurant dans ce dossier fournit des détails supplémentaires. Ce fichier journal est particulièrement utile en cas d’erreur. Dans un environnement de production, vous souhaiterez peut-être définir cette propriété sur **Échec**.

Pour résoudre des problèmes, procédez comme suit :


1. Accédez à `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Application d’interface utilisateur YARN](media/data-factory-spark/yarnui-application.png)  
2. Cliquez sur **Journaux** pour l’une des tentatives d’exécution.

    ![Page d’application](media/data-factory-spark/yarn-applications.png)
3. Vous devriez voir d’autres informations d’erreur sur la page du journal.

    ![Erreur du journal](media/data-factory-spark/yarnui-application-error.png)

Les sections suivantes fournissent des informations sur les entités Data Factory pour utiliser le cluster Apache Spark et l’activité Spark dans votre fabrique de données.

## <a name="spark-activity-properties"></a>Propriétés de l'activité Spark
Voici la définition d’exemple JSON d’un pipeline avec activité Spark :    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Le tableau suivant décrit les propriétés JSON utilisées dans la définition JSON :

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| name | Nom de l'activité dans le pipeline. | Oui |
| Description | Texte décrivant l’activité. | Non |
| type | Cette propriété doit être définie sur HDInsightSpark. | Oui |
| linkedServiceName | Nom d’un service lié HDInsight sur lequel s’exécute le programme Spark. | Oui |
| rootPath | Conteneur d’objets blob Azure et dossier contenant le fichier Spark. Le nom de fichier respecte la casse. | Oui |
| entryFilePath | Chemin d’accès relatif au dossier racine du code/package Spark. | Oui |
| className | Classe principale Java/Spark de l’application. | Non |
| arguments | Liste d’arguments de ligne de commande du programme Spark. | Non |
| proxyUser | Identité du compte d’utilisateur à emprunter pour exécuter le programme Spark. | Non |
| sparkConfig | Propriétés de configuration Spark. | Non |
| getDebugInfo | Spécifie quand les fichiers journaux de Spark sont copiés vers le stockage Azure utilisé par le cluster HDInsight (ou) spécifié par sparkJobLinkedService. Valeurs autorisées : Aucun, Toujours ou Échec. Valeur par défaut : Aucun. | Non |
| sparkJobLinkedService | Service lié de stockage Azure qui contient le fichier de travail, les dépendances et les journaux Spark.  Si vous ne spécifiez pas de valeur pour cette propriété, le stockage associé au cluster HDInsight est utilisé. | Non |

## <a name="folder-structure"></a>Structure de dossiers
L’activité Spark ne prend pas en charge un script en ligne, contrairement aux activités Pig et Hive. Les travaux Spark sont également plus extensibles que les travaux Pig/Hive. Pour les travaux Spark, vous pouvez fournir plusieurs dépendances, telles que des packages jar (placés dans le CLASSPATH Java), des fichiers Python (placés dans le PYTHONPATH) et tout autre fichier.

Créez la structure de dossiers suivante dans le stockage Blob Azure référencé par le service lié HDInsight. Ensuite, téléchargez les fichiers dépendants dans les sous-dossiers appropriés dans le dossier racine représenté par **entryFilePath**. Par exemple, téléchargez les fichiers Python dans le sous-dossier pyFiles et les fichiers jar dans le sous-dossier jars du dossier racine. Lors de l’exécution, le service Data Factory attend la structure de dossiers suivante dans le stockage Blob Azure :     

| Chemin | Description | Requis | Type |
| ---- | ----------- | -------- | ---- |
| .    | Chemin d’accès racine du travail Spark dans le service lié de stockage    | Oui | Dossier |
| &lt;défini par l’utilisateur &gt; | Chemin d’accès pointant vers le fichier d’entrée du travail Spark | Oui | Fichier |
| ./jars | Tous les fichiers dans ce dossier sont téléchargés et placés dans le classpath Java du cluster | Non | Dossier |
| ./pyFiles | Tous les fichiers dans ce dossier sont téléchargés et placés dans le PYTHONPATH du cluster | Non | Dossier |
| ./files | Tous les fichiers dans ce dossier sont téléchargés et placés dans le répertoire de travail de l’exécuteur | Non | Dossier |
| ./archives | Tous les fichiers dans ce dossier ne sont pas compressés | Non | Dossier |
| ./logs | Dossier dans lequel sont stockés les journaux du cluster Spark| Non | Dossier |

Voici un exemple de stockage qui contient deux fichiers de travail Spark dans le stockage Blob Azure référencé par le service lié HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```

