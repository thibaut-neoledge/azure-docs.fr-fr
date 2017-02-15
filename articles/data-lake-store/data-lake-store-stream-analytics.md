---
title: "Diffusion des données de Stream Analytics vers Data Lake Store | Microsoft Docs"
description: "Utiliser Azure Stream Analytics pour diffuser les données dans Azure Data Lake Store"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/07/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 2864fbf1fc4f070cb4d88d1bb3efbaaf408c68ce


---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Diffuser des données à partir d’un objet blob Azure Storage dans Data Lake Store à l’aide d’Azure Stream Analytics
Dans cet article, vous allez apprendre à utiliser Azure Data Lake Store comme sortie d’une tâche Azure Stream Analytics. Cet article présente un scénario simple qui lit des données à partir d’un objet blob Azure Storage (entrée) et écrit les données dans Data Lake Store (sortie).

> [!NOTE]
> À l’heure actuelle, la création et la configuration des sorties Data Lake Store de Stream Analytics sont prises en charge uniquement dans le [portail Azure Classic](https://manage.windowsazure.com). Par conséquent, certaines parties de ce didacticiel utilisent le portail Azure Classic.
>
>

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Activez votre abonnement Azure** pour la version d’évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md).
* **Compte Stockage Azure**. Vous allez utiliser un conteneur d’objets blob à partir de ce compte pour entrer des données pour une tâche Stream Analytics. Ce didacticiel part du principe que vous créez un compte de stockage nommé **datalakestoreasa** et un conteneur dans ce compte nommé **datalakestoreasacontainer**. Une fois que vous avez créé le conteneur, chargez-y un fichier d’exemples de données. Vous pouvez obtenir un fichier d’exemples de données à partir du [référentiel Git d’Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Vous pouvez utiliser différents clients, comme [Explorateur de stockage Azure](http://storageexplorer.com/), pour charger des données dans un conteneur d’objets blob.

  > [!NOTE]
  > Si vous créez le compte à partir du portail Azure, assurez-vous que vous utilisez pour cela le modèle de déploiement **classique** . Cela garantit que le compte de stockage est accessible depuis le portail Azure Classic, car c’est le modèle utilisé pour créer une tâche Stream Analytics. Pour obtenir des instructions sur la création d’un compte de stockage à partir du portail Azure à l’aide d’un déploiement classique, consultez [Création d’un compte Azure Storage](../storage/storage-create-storage-account.md#create-a-storage-account).
  >
  > Vous pouvez aussi créer un compte de stockage à partir du portail Azure Classic.
  >
  >
* **Compte Azure Data Lake Store**. Suivez les instructions de [Prise en main d'Azure Data Lake Store avec le portail Azure](data-lake-store-get-started-portal.md).  

## <a name="create-a-stream-analytics-job"></a>Créer un objet blob Stream Analytics
Pour commencer, vous allez créez une tâche Stream Analytics qui inclut une source d’entrée et une destination de sortie. Pour ce didacticiel, la source est un conteneur d’objets blob Azure et la destination est Lake Data Store.

1. Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com).
2. En bas à gauche de l’écran, cliquez sur **Nouveau**, **Services de données**, **Stream Analytics**, **Création rapide**. Fournissez les valeurs indiquées ci-dessous, puis cliquez sur **Créer une tâche Stream Analytics**.

    ![Créer un objet blob Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Create a Stream Analytics job")

## <a name="create-a-blob-input-for-the-job"></a>Créer une entrée d’objets Blob pour la tâche
1. Ouvrez la page du travail Stream Analytics, cliquez sur l’onglet **Entrées**, puis sur **Ajouter une entrée** pour lancer un assistant.
2. Sur la page **Ajouter une entrée à votre tâche**, sélectionnez **Flux de données**, puis cliquez sur la flèche Suivant.

    ![Ajouter une entrée à votre tâche](./media/data-lake-store-stream-analytics/create.input.1.png "Add an input to your job")
3. Sur la page **Ajouter un flux de données à votre travail**, sélectionnez **Stockage d’objets blob**, puis cliquez sur la flèche Suivant.

    ![Ajouter un flux de données à votre tâche](./media/data-lake-store-stream-analytics/create.input.2.png "Add a data stream to the job")
4. Dans la page **Paramètres de l’objet blob de stockage** , fournissez des détails pour le stockage d’objets blob que vous allez utiliser comme source de données d’entrée.

    ![Fournir les paramètres de stockage de l’objet blob](./media/data-lake-store-stream-analytics/create.input.3.png "Provide the blob storage settings")

   * **Entrez un alias d’entrée**. Il s’agit d’un nom unique que vous fournissez pour l’entrée de la tâche.
   * **Sélectionnez un compte de stockage**. Vérifiez que le compte de stockage se trouve dans la même région que la tâche Stream Analytics ou le transfert des données entre les régions génèrera des coûts supplémentaires.
   * **Fournissez un conteneur de stockage**. Vous pouvez choisir de créer un conteneur, ou sélectionner un conteneur existant.

     Cliquez sur la flèche Suivant.
5. Dans la page **Paramètres de sérialisation**, définissez le format de sérialisation sur **CSV**, le délimiteur sur **tabulation**, l’encodage sur **UTF8**, puis cliquez sur la coche.

    ![Fournir les paramètres de sérialisation](./media/data-lake-store-stream-analytics/create.input.4.png "Provide the serialization settings")
6. Lorsque vous avez fini d’utiliser l’assistant, l’entrée de l’objet blob est ajoutée sous l’onglet **Entrées** et la colonne **Diagnostic** indique **OK**. Vous pouvez également explicitement tester la connexion à l’entrée en utilisant le bouton **Test de la connexion** en bas.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Créer une sortie Data Lake Store pour la tâche
1. Ouvrez la page du travail Stream Analytics, cliquez sur l’onglet **Sorties**, puis sur **Ajouter une sortie** pour lancer un assistant.
2. Dans la page **Ajouter une sortie à votre tâche**, sélectionnez **Data Lake Store**, puis cliquez sur la flèche Suivant.

    ![Ajouter une sortie à votre tâche](./media/data-lake-store-stream-analytics/create.output.1.png "Add an output to your job")
3. Dans la page **Autoriser la connexion**, si vous avez déjà créé un compte Data Lake Store, cliquez sur **Autoriser maintenant**. Sinon, cliquez sur **Inscrivez-vous maintenant** pour créer un compte. Ce didacticiel suppose que vous avez déjà créé un compte Data Lake Store (comme indiqué dans les conditions préalables). Vous êtes automatiquement autorisé en utilisant les informations d’identification avec lesquelles vous vous êtes connecté au portail Azure Classic.

    ![Autoriser Data Lake Store](./media/data-lake-store-stream-analytics/create.output.2.png "Authorize Data Lake Store")
4. Dans la page **Paramètres Data Lake Store** , entrez les informations comme illustré dans la capture d’écran ci-dessous.

    ![Spécifier les paramètres Data Lake Store](./media/data-lake-store-stream-analytics/create.output.3.png "Specify Data Lake Store settings")

   * **Entrez un alias de sortie**. Il s’agit d’un nom unique que vous fournissez pour la sortie de la tâche.
   * **Spécifiez un compte Data Lake Store**. Il devrait déjà exister, comme indiqué dans les conditions préalables.
   * **Spécifiez un modèle de préfixe de chemin d’accès**. Cela est nécessaire pour identifier les fichiers de sortie qui sont écrits dans Data Lake Store par la tâche Stream Analytics. Étant donné que les titres des sorties écrits par la tâche sont dans un format GUID, un préfixe suffit à identifier la sortie écrite. Si vous souhaitez inclure un horodatage dans le cadre du préfixe veillez à inclure `{date}/{time}` dans le modèle de préfixe. Si vous incluez cette valeur, les champs **Format de date** et **Format de l’heure** sont activés et vous pouvez sélectionner le format de votre choix.

     Cliquez sur la flèche Suivant.
5. Dans la page **Paramètres de sérialisation**, définissez le format de sérialisation sur **CSV**, le délimiteur sur **tabulation**, l’encodage sur **UTF8**, puis cliquez sur la coche.

    ![Spécifier le format de sortie](./media/data-lake-store-stream-analytics/create.output.4.png "Specify the output format")
6. Lorsque vous avez fini d’utiliser l’assistant, la sortie Data Lake Store est ajoutée sous l’onglet **Sorties** et la colonne **Diagnostic** indique **OK**. Vous pouvez également explicitement tester la connexion à la sortie en utilisant le bouton **Test de la connexion** en bas.

## <a name="run-the-stream-analytics-job"></a>Exécuter la tâche Stream Analytics
Pour exécuter une tâche Stream Analytics, vous devez exécuter une requête à partir de l’onglet Requête. Pour ce didacticiel, vous pouvez exécuter l’exemple de requête en remplaçant les espaces réservés par les alias d’entrée et de sortie de la tâche, comme illustré dans la capture d’écran ci-dessous.

![Exécuter une requête](./media/data-lake-store-stream-analytics/run.query.png "Run query")

Cliquez sur **Enregistrer** à partir du bas de l’écran, puis cliquez sur **Démarrer**. Dans la boîte de dialogue, sélectionnez **Heure personnalisée**, puis sélectionnez une date dans le passé, par exemple **1/1/2016**. Cliquez sur la coche pour démarrer la tâche. Le démarrage de la tâche peut prendre quelques minutes.

![Définir l’heure de la tâche](./media/data-lake-store-stream-analytics/run.query.2.png "Set job time")

Après le démarrage de la tâche, cliquez sur l’onglet **Analyse** pour voir comment les données ont été traitées.

![Surveiller la tâche](./media/data-lake-store-stream-analytics/run.query.3.png "Monitor job")

Enfin, vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour ouvrir votre compte Data Lake Store et vérifier si les données ont été écrites correctement pour le compte.

![Vérifier la sortie](./media/data-lake-store-stream-analytics/run.query.4.png "Verify output")

Dans le volet Explorateur de données, notez que la sortie est écrite dans un dossier spécifié dans les paramètres de sortie Data Lake Store (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Voir aussi
* [Créer un cluster HDInsight pour utiliser Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Nov16_HO3-->


