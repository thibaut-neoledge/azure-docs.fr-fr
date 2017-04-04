---
title: "Prise en main de R Server sur HDInsight | Microsoft Docs"
description: "Apprenez à créer un Apache Spark sur un cluster HDInsight incluant R Server, puis à envoyer un script R sur le cluster."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f816a6972c0e80c6a7063705917ecf18debc75f6
ms.lasthandoff: 03/29/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>Commencer à utiliser R Server sur HDInsight
HDInsight inclut une option R Server à intégrer dans votre cluster HDInsight. Cela permet aux scripts R d’utiliser Spark et MapReduce pour exécuter des calculs distribués. Dans ce document, vous allez apprendre à créer un R Server sur cluster HDInsight, puis à exécuter un script R illustrant l’utilisation de Spark pour effectuer des calculs R distribués.

## <a name="prerequisites"></a>Composants requis
* **Abonnement Azure** : avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations, consultez [Obtenir la version d’évaluation gratuite d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) .
* **Client Secure Shell (SSH)** : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

  * **Clés SSH (facultatives)** : vous pouvez sécuriser le compte SSH utilisé pour la connexion au cluster à l’aide d’un mot de passe ou d’une clé publique. Le recours à un mot de passe est plus facile et vous permet de commencer sans avoir à créer une paire de clés publique/privée. Toutefois, il est plus sûr d’utiliser une clé.

      Les étapes décrites dans ce document supposent que vous utilisez un mot de passe.

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>Création du cluster
> [!NOTE]
> Les étapes décrites dans ce document expliquent comment créer un R Server sur cluster HDInsight à l’aide des informations de configuration de base. Pour d’autres paramètres de configuration du cluster (par exemple, l’ajout de comptes de stockage à l’aide d’Azure Virtual Network ou la création d’un metastore pour Hive), voir [Créer des clusters HDInsight sous Linux](hdinsight-hadoop-provision-linux-clusters.md). Pour créer un R Server à l’aide d’un modèle Azure Resource Management, consultez la rubrique [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Déployer un cluster HDInsight R Server).
>
>

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **NOUVEAU**, **Intelligence et analyse**, puis **HDInsight**.

    ![Image de la création d’un cluster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Dans le champ **Nom du cluster** de l’expérience **Création rapide**, entrez un nom pour le cluster. Si vous avez plusieurs abonnements Azure, utilisez l’entrée **Abonnement** pour sélectionner celui que vous voulez utiliser.

    ![Sélections de nom et d’abonnement de cluster](./media/hdinsight-getting-started-with-r/clustername.png)

4. Sélectionnez **Type de cluster** pour ouvrir le panneau **Configuration de cluster**. Dans le panneau **Configuration de cluster** , sélectionnez les options suivantes :

   * **Type de cluster** : R Server
   * **Version** : sélectionnez la version de R Server à installer sur le cluster. Sélectionnez la version la plus récente pour bénéficier des dernières fonctionnalités. D’autres versions sont disponibles si nécessaire pour assurer la compatibilité. Les notes de publication pour chacune des versions disponibles se trouvent [ici](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes).
   * **R Studio Community Edition pour R Server** : cet IDE basé sur navigateur est installé par défaut sur le nœud de périmètre.  Si vous préférez ne pas l’installer, décochez la case. Si vous choisissez de l’installer, vous trouverez l’URL d’accès à la connexion à RStudio Server sur le panneau d’une application du portail de votre cluster une fois qu’il a été créé.

   Conservez les valeurs par défaut des autres options et utilisez le bouton **Sélectionner** pour enregistrer le type de cluster.

   ![Capture d’écran du panneau Type de cluster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. Saisissez un **nom de connexion au cluster** et un **mot de passe de connexion au cluster**.

   Spécifiez un **nom d’utilisateur SSH**.  Le protocole SSH est utilisé pour se connecter à distance au cluster à l’aide d’un client **Secure Shell (SSH)** . Vous pouvez spécifier l’utilisateur SSH dans cette boîte de dialogue ou après la création du cluster (onglet Configuration du cluster). R Server est configuré pour attendre un **nom d’utilisateur SSH** de l’« utilisateur distant ».  **Si vous utilisez un autre nom d’utilisateur, vous devrez effectuer une étape supplémentaire après la création du cluster.**

   Laissez la case cochée pour **Utiliser le même mot de passe que pour la connexion au cluster** pour utiliser **MOT DE PASSE** comme type d’authentification, sauf si vous préférez utiliser une clé publique.  Vous aurez besoin d’une paire de clés publique/privée si vous souhaitez accéder au R Server sur le cluster via un client distant (par exemple, RTV, RStudio ou un autre IDE de bureau). Vous devez choisir un mot de passe SSH si vous installez RStudio Server Community Edition.     

   Pour créer et utiliser une paire de clés publique/privée, décochez **Utiliser le même mot de passe que pour la connexion au cluster**, puis sélectionnez **CLÉ PUBLIQUE** et procédez comme suit.  Ces instructions supposent que vous avez Cygwin avec ssh-keygen ou équivalent installé.

   * Générez une paire de clés publique/privée à partir de l’invite de commandes sur votre ordinateur portable :

   `ssh-keygen -t rsa -b 2048`

   * Suivez les instructions pour nommer un fichier de clé, puis entrez un mot de passe pour renforcer la sécurité. Votre écran doit ressembler à ceci :

   ![Ligne de commande SSH dans Windows](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * Cela crée un fichier de clé privée et un fichier de clé publique sous le nom <nom de fichier de la clé privée>.pub, par exemple, furiosa et furiosa.pub.

   ![SSH dir](./media/hdinsight-getting-started-with-r/dir.png)

   * Spécifiez ensuite le fichier de clé publique (*.pub) lors de l’attribution des informations d’identification du cluster HDI, puis confirmez votre groupe de ressources et votre région et sélectionnez **Suivant**

   ![Panneau Informations d’identification](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Modifier les autorisations sur le fichier de clé privée sur votre ordinateur portable

   `chmod 600 <private-key-filename>`

   * Utilisez le fichier de clé privée avec SSH pour la connexion à distance

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   ou dans le cadre de la définition de votre contexte de calcul Hadoop Spark pour R Server sur le client : voir Using Microsoft R Server as a Hadoop Client (Utilisation de Microsoft R Server en tant que client Hadoop) dans la section [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Création d’un contexte de calcul pour Spark) du document en ligne [Get started with ScaleR on Apache Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) (Guide pratique de ScaleR sur Apache Spark).

6. La création rapide vous fait passer au panneau **Stockage** pour sélectionner les paramètres de compte de stockage à utiliser pour l’emplacement principal du système de fichiers HDFS utilisé par le cluster. Sélectionnez un compte de stockage Azure nouveau ou existant ou un compte de stockage Data Lake existant.

   1. Si vous sélectionnez un compte de stockage Azure, vous pouvez opter pour un compte de stockage existant en choisissant **Sélectionner un compte de stockage**, puis en spécifiant le compte. Ou créez un nouveau compte à l’aide du lien **Nouveau** dans la section **Sélectionner un compte de stockage**.

      > [!NOTE]
      > Si vous sélectionnez **Nouveau**, vous devez entrer un nom pour le nouveau compte de stockage. Une coche verte s’affiche si le nom est accepté.

      Le **Conteneur par défaut** correspond au nom du cluster. Conservez cette valeur.

      Si l’option Nouveau compte de stockage a été sélectionnée, une invite permettant de choisir un **Emplacement** s’affiche pour sélectionner la région dans laquelle le compte de stockage sera créé.  

         ![Panneau Source de données](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > La sélection de l’emplacement de la source de données par défaut définira également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même région.

   2. Si vous choisissez d’utiliser un Data Lake Store existant, sélectionnez le compte de stockage ADLS à utiliser et ajoutez l’identité ADD de cluster à votre cluster afin d’autoriser l’accès au magasin. Pour plus d’informations sur ce processus, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

   Pour enregistrer la configuration de la source de données, utilisez le bouton **Sélectionner** .


7. Le panneau **Résumé** s’affiche pour valider tous les paramètres. Ici, vous pouvez modifier votre **Taille de cluster** pour changer le nombre de serveurs de votre cluster et également spécifier les **Actions de script** vous souhaitez exécuter. Sauf si vous savez que vous avez besoin d’un cluster plus grand, conservez le nombre de nœuds Worker par défaut `4`. Le coût estimé du cluster s'affiche dans le panneau.

   ![Résumé du cluster](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > Si nécessaire, vous pouvez redimensionner votre cluster ultérieurement via le portail (Cluster -> Paramètres -> Cluster de mise à l’échelle) pour augmenter ou diminuer le nombre de nœuds de travail.  Cela peut être utile pour faire fonctionner le cluster au ralenti lorsqu’il n’est pas utilisé, ou pour ajouter de la capacité afin de répondre aux besoins des tâches plus intensives.
   >
   >

    Certains facteurs à prendre en compte lors du dimensionnement de votre cluster, les nœuds de données et le nœud de périmètre sont les suivants :  

   * Les performances des analyses du R Server distribué sur Spark sont proportionnelles au nombre de nœuds Worker lorsque les données sont volumineuses.  

   * Les performances des analyses du R Server sont linéaires quant à la taille des données analysées. Par exemple :  

     * Pour les volumes de données petits à moyens, les performances sont idéales lorsqu’elles sont analysées dans un contexte de calcul local à la périphérie du nœud.  Pour plus d’informations sur les scénarios de fonctionnement des contextes de calcul locaux et Spark, consultez la rubrique Options de contexte de calcul pour R Server sur HDInsight.<br>
     * Si vous vous connectez au nœud de périmètre et exécutez votre script R, toutes les fonctions à l’exception des fonctions rx ScaleR sont exécutées <strong>localement</strong> sur le nœud de périmètre. La mémoire et le nombre de cœurs du nœud de périmètre doivent donc être dimensionnés en conséquence. Ces conditions s’appliquent si vous utilisez R Server sur HDI comme contexte de calcul à distance à partir de votre ordinateur portable.

     ![Panneau Niveaux de tarification du nœud](./media/hdinsight-getting-started-with-r/pricingtier.png)

     Pour enregistrer la configuration de la tarification du nœud, utilisez le bouton **Sélectionner** .

   Vous remarquez qu’il existe également un lien vers **Télécharger le modèle et les paramètres**. Cliquer sur ce lien permet d’afficher les scripts qui peuvent être utilisés pour automatiser la création d’un cluster avec la configuration sélectionnée. Ces scripts sont également disponibles dans l’entrée du portail Azure de votre cluster, une fois celui-ci créé.

   > [!NOTE]
   > La création du cluster prend un certain temps (en règle générale, environ 20 minutes). Utilisez la vignette du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour vérifier le processus de création.
   >
   >

## <a name="connect-to-rstudio-server"></a>Se connecter à RStudio Server

Si vous avez choisi d’inclure RStudio Server Community Edition dans votre installation, vous pouvez accéder à la connexion RStudio via deux méthodes différentes.

1. Soit partir de l’URL suivante (où **CLUSTERNAME** est le nom du cluster que vous avez créé) :

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Soit en ouvrant l’entrée pour votre cluster dans le portail Azure, en sélectionnant le lien rapide de Tableaux de bord R Server, puis en sélectionnant le tableau de bord RStudio :

     ![Accès au tableau de bord RStudio](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Accès au tableau de bord RStudio](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Peu importe la méthode, vous devrez vous authentifier la première fois que vous vous connecterez.  À la première authentification, fournissez le nom d’utilisateur et le mot de passe administrateur pour le cluster. À la seconde invite, indiquez le nom d’utilisateur et le mot de passe SSH. Les connexions suivantes ne nécessiteront que le nom d’utilisateur et le mot de passe SSH.

## <a name="connect-to-the-r-server-edge-node"></a>Se connecter au nœud de périmètre R Server
Connectez-vous au nœud de périmètre R Server du cluster HDInsight à l’aide de SSH :

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Vous pouvez également trouver l’adresse `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` dans le portail Azure en sélectionnant votre cluster, puis **Tous les paramètres**, **Applications** et **RServer**. Cela a pour effet d’afficher les informations relatives au point de terminaison SSH pour le nœud de périmètre.
>
> ![Image du point de terminaison SSH pour le nœud de périmètre](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple, `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois connecté, une invite semblable à la suivante s’affiche.

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>Utiliser la console R

1. Dans la session SSH, utilisez la commande suivante pour démarrer la console R.  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. Vous pouvez entrer un code R dans l’invite `>` . R Server inclut des packages qui vous permettent d’interagir facilement avec Hadoop et d’exécuter des calculs distribués. Par exemple, utilisez la commande suivante pour afficher la racine du système de fichiers par défaut du cluster HDInsight.

`rxHadoopListFiles("/")`

Vous pouvez également utiliser l’adressage de style WASB.

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>À l’aide de R Server sur HDI à partir d’une instance distante de Microsoft R serveur ou Microsoft R Client
Conformément à la section ci-dessus concernant l’utilisation de paires de clés publiques/privées pour accéder au cluster, il est possible de configurer l’accès au contexte de calcul HDI Hadoop Spark à partir d’une instance distante de Microsoft R Server ou Microsoft R Client exécutée sur un ordinateur de bureau ou un ordinateur portable (voir la section Using Microsoft R Server as a Hadoop Client in the [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Utilisation de Microsoft R Server en tant que client Hadoop pour la création d’un contexte de calcul pour Spark) du [guide en ligne de mise en route RevoScaleR Hadoop Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)).  Pour cela, vous devez spécifier les options suivantes lorsque vous définissez le contexte de calcul RxSpark sur votre ordinateur portable : hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches et sshProfileScript. Par exemple :

```
    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )
```


## <a name="use-a-compute-context"></a>Utiliser un contexte de calcul
Un contexte de calcul vous permet de contrôler si calcul sera effectué localement sur le nœud de périmètre, ou si elle sera distribuée entre les nœuds du cluster HDInsight.

1. Dans RStudio Server ou la console R (dans une session SSH), utilisez ce qui suit pour charger les exemples de données dans le stockage par défaut pour HDInsight.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Ensuite, créez des données et définissez deux sources de données exploitables.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Exécutez une régression logistique sur les données à l’aide du contexte de calcul local.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Vous devez voir la sortie qui se termine par des lignes similaires à ce qui suit.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Ensuite, exécutez la même régression logistique en utilisant le contexte Spark. Le contexte Spark répartit le traitement sur tous les nœuds de travail dans le cluster HDInsight.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > Vous pouvez également utiliser MapReduce pour répartir le calcul sur des nœuds de cluster. Pour plus d’informations sur le contexte de calcul, consultez [Options de contexte de calcul pour R Server sur HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuer le code R à plusieurs nœuds
Avec R Server, vous pouvez facilement utiliser du code R existant pour l’exécuter sur plusieurs nœuds du cluster à l’aide de `rxExec`. Cela est utile lors d’un balayage paramétrique ou de simulations. L’exemple suivant montre comment utiliser `rxExec`.

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

Si vous utilisez encore le contexte Spark ou MapReduce, la valeur du nom de nœud (nodename) est renvoyée pour les nœuds Worker sur lesquels le code `(Sys.info()["nodename"])` est exécuté. Par exemple, sur un cluster à quatre nœuds, vous pouvez recevoir une sortie similaire à ce qui suit.


    ```
    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Accès aux données dans Hive et Parquet
Une nouvelle fonctionnalité disponible dans R Server 9.0 et versions ultérieures permet un accès direct aux données de Hive et Parquet pour une utilisation par les fonctions de ScaleR dans le contexte de calcul Spark. Ces fonctionnalités sont disponibles via les nouvelles fonctions de source de données ScaleR appelées RxHiveData et RxParquetData qui fonctionnent à l’aide de Spark SQL pour charger les données directement dans un tableau de données Spark pour analyse par ScaleR.  

Vous trouverez ci-dessous un exemple de code relatif à l’utilisation des nouvelles fonctions :



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

Pour des informations supplémentaires sur l’utilisation de ces nouvelles fonctions, consultez l’aide en ligne dans R Server via les commandes ?RxHivedata et ?RxParquetData.  


## <a name="install-r-packages"></a>Installer des packages R
Si vous souhaitez installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser `install.packages()` directement à partir de la console R quand vous êtes connecté au nœud de périphérie par le biais de SSH. Toutefois, si vous avez besoin installer des packages R sur les nœuds de travail du cluster, vous devez utiliser une action de script.

Les actions de script sont des scripts Bash permettant de modifier la configuration du cluster HDInsight ou d’installer des logiciels supplémentaires. Dans ce cas, il s’agit d’installer des packages R supplémentaires. Pour installer des packages supplémentaires à l’aide d’une action de Script, procédez comme suit.

> [!IMPORTANT]
> L’utilisation d’actions de script pour installer des packages R supplémentaires n’est possible qu’après la création du cluster. Il ne faut pas y recourir lors de la création du cluster, car le script a besoin que R Server soit entièrement installé et configuré.
>
>

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre R Server sur le cluster HDInsight.

2. Dans le panneau **Paramètres**, sélectionnez **Actions de script** puis **Envoyer** pour soumettre une nouvelle action de script.

   ![Image du panneau Actions de script](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. Dans le panneau **Envoyer une action de script**, entrez les informations suivantes.

   * **Nom** : un nom convivial pour identifier ce script

   * **URI de script bash** : `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **En-tête** : cette valeur doit être **désactivée**

   * **Worker** : cette valeur doit être **activée**

   * **Nœuds de périmètre** : cette valeur doit être **désactivée**.

   * **Zookeeper** : cette valeur doit être **désactivée**

   * **Paramètres** : les packages R à installer. Par exemple, `bitops stringr arules`

   * **Conservez cette action de script...** : cette option doit être **activée**  

   > [!NOTE]
   > 1. Par défaut, tous les packages R sont installés à partir d’un instantané du référentiel Microsoft MRAN cohérent avec la version du serveur R qui a été installée.  Si vous souhaitez installer des versions plus récentes des packages, il y a des risques d’incompatibilité. Cependant, cela est possible en spécifiant `useCRAN` en tant que premier élément de la liste de packages (par exemple, `useCRAN bitops, stringr, arules`).  
   > 2. Certains packages R nécessiteront des bibliothèques de système Linux supplémentaires. Pour plus de commodité, nous avons préinstallé les dépendances requises par les 100 premiers packages R les plus populaires. Toutefois, si les packages R que vous installez nécessitent des bibliothèques supplémentaires, vous devez télécharger le script de base utilisé ici et ajouter des étapes pour installer les bibliothèques système. Vous devez ensuite charger le script modifié dans un conteneur d’objets blob publics dans Azure Storage, puis utiliser le script modifié pour installer les packages.
   >    Pour plus d’informations sur le développement d’actions de script, consultez la section [Développer des actions de script](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Ajout d’une action de script](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Sélectionnez **Créer** pour exécuter le script. Une fois le script exécuté, les packages R sont disponibles sur tous les nœuds de travail.

## <a name="using-microsoft-r-server-operationalization"></a>Utilisation de l’opérationnalisation de Microsoft R Server
Lorsque votre modélisation des données est terminée, vous pouvez opérationnaliser le modèle pour effectuer des prévisions. Pour configurer l’opérationnalisation de Microsoft R Server, suivez les étapes ci-dessous.

Tout d’abord, utilisez SSH dans le nœud de périphérie. Par exemple, ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```.

Après l’utilisation de SSH, remplacez le répertoire par le suivant et appliquez la méthode sudo au dll dotnet, comme indiqué ci-dessous.

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

Pour configurer l’opérationnalisation de Microsoft R Server avec une configuration complète, procédez comme suit :

* Sélectionnez « 1. Configure R Server for Operationalization » (Configurer l’opérationnalisation de R Server).
* Sélectionnez « A. One-box (web + compute nodes) » (Solution complète + nœuds de calcul).
* Entrez un mot de passe pour l’utilisateur **admin**.

![opérationnalisation complète](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Vous pouvez éventuellement effectuer les vérifications de diagnostic en exécutant un test de diagnostic, comme indiqué ci-dessous.

* Sélectionnez « 6. Exécuter des tests de diagnostic ».
* Sélectionnez « A. Configuration de test ».
* Entrez le nom d’utilisateur = « admin » et le mot de passe de l’étape de configuration ci-dessus.
* Confirmez l’intégrité globale = pass.
* Quittez l’utilitaire d’administration.
* Quittez SSH.

![Diagnostics pour l’opérationnalisation](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

À ce stade, la configuration de l’opérationnalisation est terminée. Vous pouvez désormais utiliser le package « mrsdeploy » sur votre RClient pour vous connecter à l’opérationnalisation sur le nœud de périphérie et commencer à utiliser ses fonctionnalités, telles que [l’exécution à distance](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) et les [services web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Selon que votre cluster est configuré sur un réseau virtuel ou non, vous devrez peut-être configurer le tunneling de réacheminement du port via une connexion SSH, comme expliqué ci-dessous :

### <a name="rserver-cluster-on-virtual-network"></a>Cluster RServer sur un réseau virtuel

Assurez-vous que vous autorisez le trafic via le port 12800 vers le nœud de périphérie. De cette façon, vous pouvez utiliser le nœud de périphérie pour vous connecter à la fonctionnalité d’opérationnalisation.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Si remoteLogin() ne peut pas se connecter au nœud de périphérie, mais si vous pouvez exécuter SSH sur ce dernier, vous devez vérifier si la règle pour autoriser le trafic sur le port 12800 a été définie correctement ou non. Si vous continuez à rencontrer le problème, vous pouvez utiliser une solution de contournement en configurant le tunneling de réacheminement du port via SSH.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Cluster RServer non configuré sur un réseau virtuel

Si votre cluster n’est pas configuré sur un réseau virtuel OU si vous rencontrez des problèmes de connectivité via un réseau virtuel, vous pouvez utiliser le tunneling de réacheminement du port SSH comme indiqué ci-dessous :

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

Vous pouvez également le configurer sur PuTTY.

![connexion SSH PuTTY](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Une fois votre session SSH active, le trafic du port 12800 de votre ordinateur sera transféré vers le port du nœud de périphérie 12800 via une session SSH. Vérifiez que vous utilisez `127.0.0.1:12800` dans votre méthode remoteLogin(). Cela vous permettra de vous connecter à l’opérationnalisation du nœud de périphérie via le réacheminement de port.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Comment mettre à l’échelle des nœuds de calcul de l’opérationnalisation Microsoft R Server sur des nœuds Worker HDInsight ?


### <a name="decommission-the-worker-nodes"></a>Désactiver le(s) nœud(s) Worker
Microsoft R Server n’est actuellement pas géré par YARN. Si les nœuds Worker ne sont pas désactivés, le gestionnaire de ressources YARN ne fonctionnera pas comme prévu, car il n’aura pas connaissance des ressources prises en charge par le serveur. Afin d’éviter ce problème, nous vous recommandons de désactiver les nœuds Worker par rapport auxquels vous souhaitez mettre à l’échelle les nœuds de calcul.

Étapes de désactivation des nœuds Worker :

* Connectez-vous à la console Ambari du cluster HDI et cliquez sur l’onglet « Hôtes »
* Sélectionnez les nœuds Worker (à désactiver), cliquez sur « Actions » > « Hôtes sélectionnés » > « Hôtes » > puis sur « Activer le mode de maintenance ». Par exemple, dans l’image ci-dessous, nous avons sélectionné wn3 et wn4 pour la désactivation.  

   ![Désactiver les nœuds Worker](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Sélectionnez « Actions » > « Hôtes sélectionnés » > « DataNodes » > cliquez sur « Désactiver ».
* Sélectionnez « Actions » > « Hôtes sélectionnés » > « NodeManagers » > cliquez sur « Désactiver ».
* Sélectionnez « Actions » > « Hôtes sélectionnés » > « DataNodes » > cliquez sur « Arrêter ».
* Sélectionnez « Actions » > « Hôtes sélectionnés » > « NodeManagers » > cliquez sur « Arrêter ».
* Sélectionnez « Actions » > « Hôtes sélectionnés » > « Hôtes » > cliquez sur « Stop All Components » (Arrêter tous les composants).
* Désélectionner les nœuds Worker et sélectionner les nœuds principaux
* Sélectionnez « Actions » > « Hôtes sélectionnés » > « Hôtes » > « Restart All Components » (Redémarrer tous les composants).


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Configurer les nœuds de calcul sur chaque nœud Worker désactivé

* Utilisez SSH dans chaque nœud Worker désactivé.
* Exécutez l’utilitaire d’administration à l’aide de `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`
* Entrez « 1 » pour sélectionner l’option « 1. Configure R Server for Operationalization » (Configurer R Server pour l’opérationnalisation).
* Entrez « c » pour sélectionner l’option « C. Nœud de calcul ». Cette opération permet de configurer un nœud de calcul sur le nœud Worker.
* Quittez l’utilitaire d’administration.

### <a name="add-compute-nodes-details-on-web-node"></a>Ajouter des détails sur les nœuds de calcul sur le nœud web
Une fois que tous les nœuds Worker désactivés ont été configurés pour exécuter le nœud de calcul, revenez au nœud de périphérie et ajoutez les adresses IP des nœuds Worker désactivés dans la configuration du nœud web de Microsoft R Server :

* Utiliser SSH dans le nœud de périphérie
* Exécutez `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
* Recherchez la section « URI » et ajouter l’adresse IP du nœud Worker ainsi que les détails du port.

![Ligne de commande Désactiver les nœuds Worker](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment créer un cluster HDInsight incluant R Server et connaissez les rudiments de l’utilisation de la console R à partir d’une session SSH, utilisez les informations suivantes pour découvrir d’autres manière d’utiliser R Server sur HDInsight.

* [Ajouter RStudio Server à HDInsight (s’il n’est pas installé lors de la création du cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)](hdinsight-hadoop-r-server-compute-contexts.md)
* [Options d’Azure Storage pour R Server sur HDInsight](hdinsight-hadoop-r-server-storage.md)

