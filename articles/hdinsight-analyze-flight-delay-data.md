<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyse des donn&eacute;es sur les retards de vol avec Hadoop dans HDInsight | Azure" metaKeywords="" description="D&eacute;couvrez comment t&eacute;l&eacute;charger des donn&eacute;es dans HDInsight, les traiter au moyen de Hive et exporter les r&eacute;sultats dans une base de donn&eacute;es SQL au moyen de Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyse des donn&eacute;es sur les retards de vol avec Hadoop dans HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Analyse des données sur les retards de vol avec Hadoop dans HDInsight

Hive permet d'exécuter des tâches Hadoop MapReduce via un langage de création de scripts semblable à SQL, nommé *[HiveQL][HiveQL]*, qui peut être appliqué à la synthèse, à l'envoi de requêtes et à l'analyse d'importants volumes de données. Ce didacticiel vous indique comment utiliser Hive pour calculer les retards moyens enregistrés dans les aéroports et comment utiliser Sqoop pour exporter les résultats dans une base de données SQL Azure.

L'un des principaux avantages de HDInsight est la séparation du calcul et du stockage des données. HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Un processus HDInsight habituel peut être scindé en trois parties :

1.  Le stockage des données dans un stockage d'objets blob. Il peut s'agir d'un processus continu. Par exemple, les données météorologiques, les données de capteur, les journaux web, et en l'occurrence, les données liées aux retards de vol, sont enregistrées dans un stockage d'objets blob.
2.  Exécution des tâches. Au moment de traiter les données, vous exécutez un script PowerShell (ou une application cliente) pour approvisionner un cluster HDInsight, exécuter des tâches et supprimer le cluster. Les tâches enregistrent les données de sortie dans le stockage d'objets blob Azure. Les données de sortie sont conservées même après la suppression du cluster. De cette façon, vous ne devez payer que ce que vous avez consommé.
3.  Extrayez la sortie à partir du stockage d'objets blob, ou en l'occurrence, exportez les données vers une base de données SQL Azure.

Le schéma suivant illustre le scénario et la structure de cet article :

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

Remarque : les nombres indiqués dans le schéma correspondent aux titres des sections.

**Durée de réalisation estimée :** 30 minutes

## Dans ce didacticiel

-   [Configuration requise][Configuration requise]
-   [Exécution d'une tâche Hive dans un cluster HDInsight nouveau ou existant (M1)][Exécution d'une tâche Hive dans un cluster HDInsight nouveau ou existant (M1)]
-   [Utilisation de Sqoop pour exporter la sortie vers une base de données SQL Azure (M2)][Utilisation de Sqoop pour exporter la sortie vers une base de données SQL Azure (M2)]
-   [Étapes suivantes][Étapes suivantes]
-   [Annexe A : Téléchargement des données de retard de vol dans un stockage d'objets blob Azure (A1)][Annexe A : Téléchargement des données de retard de vol dans un stockage d'objets blob Azure (A1)]
-   [Annexe B : Création et téléchargement d'un script HiveQL (A2)][Annexe B : Création et téléchargement d'un script HiveQL (A2)]

## <span id="prerequisite"></span></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Un poste de travail sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][Installation et configuration d'Azure PowerShell].
-   Un abonnement Azure. \*\*\*

**Présentation du stockage HDInsight**

Les clusters Hadoop dans HDInsight utilisent le stockage d'objets blob Azure pour stocker des données. Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du *HDFS* sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Lorsque vous approvisionnez un cluster HDInsight, un conteneur de stockage d'objets blob d'un compte de stockage Azure est désigné comme système de fichiers par défaut, comme dans HDFS. Le compte de stockage est connu sous le nom de *compte de stockage par défaut*, et le conteneur d'objets blob sous le nom de *conteneur d'objets blob par défaut* ou *conteneur par défaut*. Le compte de stockage par défaut doit se situer dans le même centre de données que le cluster HDInsight. La suppression d'un cluster HDInsight ne permet pas de supprimer le conteneur par défaut ou le compte de stockage par défaut.

Outre le compte de stockage par défaut, d'autres comptes de stockage Azure peuvent être associés à un cluster HDInsight pendant l'approvisionnement. L'association consiste à ajouter le compte de stockage et la clé du compte de stockage au fichier de configuration. Cela permet au cluster d'accéder à ces comptes de stockage à l'exécution. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight][Approvisionnement de clusters Hadoop dans HDInsight].

La syntaxe WASB est :

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Le chemin d'accès WASB est un chemin d'accès virtuel. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][Utilisation du stockage d'objets blob Azure avec HDInsight].

Vous pouvez accéder à un fichier stocké dans le conteneur par défaut à partir de HDInsight en utilisant l'un des URI suivants (flightdelays.hql est utilisé comme exemple) :

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

    tutorials/flightdelays/flightdelays.hql

Remarquez l'absence de « / » devant le nom d'objet blob.

**Fichiers utilisés dans ce didacticiel**

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

| Fichiers                                                                  | Description                                                                                                      |
|---------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | Fichier script HiveQL requis par la tâche Hive que vous exécuterez.                                              |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Données d'entrée pour les tâches Hive.                                                                           |
| \\tutorials\\flightdelays\\output                                         | Chemin de sortie pour la tâche Hive. Le conteneur par défaut est utilisé pour le stockage des données de sortie. |
| \\tutorials\\flightdelays\\jobstatus                                      | Dossier des statuts des tâches Hive                                                                              |

Ce didacticiel utilise les données de ponctualité des vols des compagnies aériennes de la [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA). Les données ont été téléchargées dans un conteneur de stockage d'objets blob Azure avec l'autorisation d'accès aux objets blob publics. Étant donné qu'il s'agit d'un conteneur d'objets blob publics, il n'est pas nécessaire d'associer ce compte de stockage au cluster HDInsight. Le script HiveQL est également téléchargé sur le même conteneur d'objets blob. Pour apprendre à obtenir/télécharger les données sur votre propre compte de stockage, et à créer/télécharger le fichier de script HiveQL, consultez l'[annexe][annexe].

**Présentation des tables interne et externe Hive**

Voici quelques éléments à connaître sur les tables interne et externe Hive :

-   La commande CREATE TABLE permet de créer une table interne. Le fichier de données doit se trouver dans le conteneur par défaut.
-   La commande CREATE TABLE transfère le fichier de données vers le dossier /hive/warehouse/<tablename>.
-   La commande CREATE EXTERNAL TABLE permet de créer une table externe. Le fichier de données peut se trouver à l'extérieur du conteneur par défaut.
-   La commande CREATE EXTERNAL TABLE ne déplace pas le fichier de données.
-   La commande CREATE EXTERNAL TABLE n'autorise aucun dossier à l'EMPLACEMENT. C'est la raison pour laquelle le didacticiel réalise une copie du fichier sample.log.

Pour plus d'informations, consultez la rubrique [HDInsight : introduction aux tables interne et externe Hive][HDInsight : introduction aux tables interne et externe Hive].

> [WACOM.NOTE] Une des instructions HiveQL crée une table externe Hive. La table externe Hive conserve le fichier de données à son emplacement d'origine. La table interne Hive transfère le fichier de données vers hive\\warehouse. Pour les besoins de la table interne Hive, le fichier de données doit être situé dans le conteneur par défaut. Pour les données stockées en dehors du conteneur d'objets blob par défaut, vous devez utiliser des tables externes Hive.

## <span id="runjob"></span></a>Exécution de la tâche Hive dans un cluster HDInsight nouveau ou existant

Hadoop correspond au traitement par lots. La façon la plus économique d'exécuter une tâche Hive consiste à approvisionner un cluster pour la tâche, et à supprimer cette dernière une fois qu'elle est terminée. Le script suivant couvre le processus dans son intégralité. Pour plus d'informations sur l'approvisionnement d'un cluster HDInsight et l'exécution de tâches Hive, consultez les rubriques [Approvisionnement de clusters Hadoop dans HDInsight][Approvisionnement de clusters Hadoop dans HDInsight] et [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight].

**Pour exécuter des requêtes Hive à l'aide de PowerShell**

1.  Ouvrez PowerShell ISE.
2.  Copiez-collez le script suivant dans le volet du script :

        *** add script here

3.  Appuyez sur **F5** pour exécuter le script. Le résultat doit ressembler à ceci :

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

    Entre chaque ville et chaque retard se trouve un délimiteur non visible dans la fenêtre des résultats PowerShell. Il s'agit de « \\001 ». Vous utiliserez ce délimiteur lors de l'exécution de l'export Sqoop.

## <span id="exportdata"></span></a>Exportation de la sortie de la tâche Hive vers Base de données SQL Azure

Les dernières étapes consistent à exécuter l'export Sqoop pour exporter les données vers la base de données SQL. Un peu plus tôt dans ce didacticiel, vous avez créé la base de données SQL ainsi que la table AvgDelays.

**Pour exporter les données vers la base de données SQL**

1.  Ouvrez Azure PowerShell.
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

3.  Définissez les cinq premières variables, puis exécutez les commandes :

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    Vous trouverez ci-dessous les variables et leur description :

    <table border="1">

    <tr>
    <td>
    **Nom de la variable**

    </td>
    <td>
    **Remarque**

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Nom du cluster HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Serveur de base de données SQL vers lequel Sqoop exporte les données.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Nom de l'utilisateur de la base de données SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Mot de passe de l'utilisateur de la base de données SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Base de données SQL vers laquelle Sqoop exporte les données. Le nom par défaut correspond à \*HDISqoop".

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    Base de données SQL vers laquelle Sqoop exporte les données. Le nom par défaut correspond à AvgDelays. Il s'agit de la table que vous avez créée plus tôt dans le didacticiel.

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Emplacement du fichier de résultat Hive. Sqoop exporte les fichiers de cet emplacement vers la base de données SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Exécutez la commande suivante pour exporter les données :

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  Connectez-vous à votre base de données SQL et consultez les retards de vol moyens par ville dans la table *AvgDelays* :

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>Étapes suivantes

Vous savez à présent télécharger un fichier vers le stockage d'objets blob, renseigner une table Hive à l'aide des données du stockage d'objets blob, exécuter des requêtes Hive et utiliser Sqoop pour exporter des données entre HDFS et la base de données SQL Azure. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main de HDInsight][Prise en main de HDInsight]
-   [Utilisation de Hive avec HDInsight][Utilisation de Hive avec HDInsight]
-   [Utilisation d'Oozie avec HDInsight][Utilisation d'Oozie avec HDInsight]
-   [Utilisation de Sqoop avec HDInsight][Utilisation de Sqoop avec HDInsight]
-   [Utilisation de Pig avec HDInsight][Utilisation de Pig avec HDInsight]
-   [Développement de programmes MapReduce en Java pour HDInsight][Développement de programmes MapReduce en Java pour HDInsight]
-   [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight]

## <span id="appendix-a"></span></a>Annexe A - Téléchargement de données de retard de vol vers le stockage d'objets blob Azure

**Pour télécharger les données de vol**

1.  Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics][Research and Innovative Technology Administration, Bureau of Transportation Statistics] (RITA).
2.  Sur la page, sélectionnez les valeurs suivantes :

    | Nom            | Valeur                                                                                                                                                                                                                                                                                                                                                                                           |
    |----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filtre année   | 2013                                                                                                                                                                                                                                                                                                                                                                                             |
    | Filtre période | Janvier                                                                                                                                                                                                                                                                                                                                                                                          |
    | Champs :       | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (effacez tous les autres champs) |

3.  Cliquez sur **Télécharger**.
4.  Décompressez le fichier dans le dossier **C:\\Tutorials\\FlightDelays\\Data**. Chaque fichier correspond à un fichier CSV d'environ 60 Go.
5.  Donnez au fichier le nom du mois dont il contient les données. Par exemple, renommez le fichier contenant les données du mois de janvier *Janvier.csv*.
6.  Répétez les étapes 2 et 5 afin de télécharger un fichier pour chacun des 12 mois de l'année 2013. Vous aurez besoin d'au moins un fichier pour exécuter le didacticiel.

**Pour télécharger les données de retard de vol vers le stockage d'objets blob Azure**

1.  Ouvrez PowerShell ISE.
2.  Collez le script suivant dans le volet du script :

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }

        # Validate the container
        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion

        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"

                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green

                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  Appuyez sur **F5** pour exécuter le script.

## <span id="appendix-b"></span></a>Annexe B - Création et téléchargement d'un script HiveQL

À l'aide d'Azure PowerShell, vous pouvez exécuter plusieurs instructions HiveQL, une par une, ou empaqueter l'instruction HiveQL dans un fichier de script. Cette section explique comment créer un script HiveQL et télécharger celui-ci vers le stockage d'objets blob Azure en utilisant Azure PowerShell. Hive requiert le stockage de scripts HiveQL sur WASB.

Le script HiveQL exécutera les opérations suivantes :

1.  **Il supprime la table delays\_raw**, le cas échéant.
2.  **Il crée la table externe Hive delays\_raw** pointant vers l'emplacement WASB où se trouvent les fichiers de retard de vol. Cette requête spécifie les champs délimités par « , » et les lignes se terminant par « \\n ». Cela pose un problème lorsque les valeurs des champs *contiennent* des virgules, car Hive n'est pas en mesure de différencier une virgule utilisée en tant que délimiteur de champ d'une virgule faisant partie d'une valeur de champ (ce qui est le cas pour les valeurs des champs ORIGIN\_CITY\_NAME et DEST\_CITY\_NAME). Pour y remédier, la requête crée des colonnes TEMP afin de contenir les données incorrectement réparties dans les colonnes.
3.  **Il supprime la table des retards**, le cas échéant.
4.  **Il crée la table des retards**. Il est conseillé de nettoyer les données avant tout traitement plus approfondi. Cette requête crée une nouvelle table, *retards*, à partir de la table *delays\_raw*. Notez que les colonnes TEMP (comme indiqué précédemment) ne sont pas copiées et que la fonction *substring* est utilisée pour supprimer les guillemets présents dans les données.
5.  **Il calcule les retards moyens liés aux conditions météo et regroupe les résultats par nom de ville.** Il transmet également les résultats à WASB. Notez que la requête supprime les apostrophes des données et exclut les lignes dans lesquelles la valeur correspondant à *weather\_delay* est *null*, ce qui est nécessaire car Sqoop, utilisé plus tard dans ce didacticiel, ne gère pas ces valeurs par défaut.

Pour obtenir la liste complète des commandes HiveQL, consultez la rubrique [Langage de définition des données Hive][HiveQL]. Chaque commande HiveQL doit se terminer par un point virgule.

**Pour créer un fichier de script HiveQL**

1.  Ouvrez Azure PowerShell ISE.
2.  Copiez-collez le script suivant dans le volet du script :

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Voici les constantes utilisées dans le script :

    -   **$hqlLocalFileName** : le script enregistre le fichier script HiveQL en local avant de le télécharger sur WASB. Il s'agit du nom de fichier. La valeur par défaut est <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u>.
    -   **$hqlBlobName** : il s'agit du nom d'objet blob du fichier de script HiveQL utilisé dans le stockage d'objets blob Azure. La valeur par défaut est <u>tutorials/flightdelays/flightdelays.hql</u>. Étant donné que le fichier sera écrit directement dans le stockage d'objets blob Azure, il n'y a PAS de « / » au début du nom d'objet blob. Si vous voulez accéder au fichier à partir de WASB, il vous faudra ajouter « / » au début du nom de fichier.
    -   **$srcDataFolder** et **$dstDataFolder** : = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

4.  Définissez les deux premières variables, puis exécutez les commandes.

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    Vous trouverez ci-dessous les variables et leur description :

    <table border="1">

    <tr>
    <td>
    **Nom de la variable**

    </td>
    <td>
    **Description**

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Compte Azure Storage utilisé pour stocker le fichier de script HiveQL. Les scripts PowerShell fournis dans ce didacticiel requièrent les fichiers de données de vol et le fichier de script situés dans le même compte Azure Storage et le même conteneur de stockage d'objets blob.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Conteneur de stockage d'objets blob Azure utilisé pour stocker le fichier de script HiveQL. Les scripts PowerShell fournis dans ce didacticiel requièrent les fichiers de données de vol et le fichier de script situés dans le même compte Azure Storage et le même conteneur de stockage d'objets blob.

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    Nom des fichiers locaux du script HiveQL avant son téléchargement vers WASB. Pour simplifier le script PowerShell, vous devez écrire le fichier en local, puis vous utilisez le cmdlet Set-AzureStorageBlobContent pour envoyer le fichier de script à HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    Nom du fichier de script avec son chemin WASB.

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    Dossier WASB depuis lequel le script HiveQL extrait les données.

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    Dossier WASB vers lequel le script HiveQL envoie la sortie. Plus loin dans ce didacticiel, vous utiliserez Sqoop pour exporter les données de ce dossier vers la base de données SQL Azure.

    </td>
    </tr>

    </table>
    </p>
5.  Exécutez les commandes suivantes pour définir les instructions HiveQL :

        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"

        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

        $hqlDropDelays = "DROP TABLE delays;"

        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;" 

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

6.  Exécutez les commandes suivantes pour écrire le fichier de script Hive sur le poste de travail et l'envoyer à WASB :

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    Le résultat doit ressembler à ceci :

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## Préparation de la base de données SQL

**Pour préparer la base de données SQL (fusionnez ceci avec le script Sqoop)**

1.  Ouvrez PowerShell ISE.
2.  Copiez-collez le script suivant dans le volet du script :

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"

        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                (
                    [origin_city_name] ASC
                )
                )"

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
            $sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan

            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress   
            New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
        }
        else
        {
            $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
            if (! $dbServer)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green

            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 

            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
        }
        else
        {
            $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
            if (! $db)
            {
                throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
            }
            else
            {
                Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
            }
        }

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    > WACOM.NOTE Le script utilise un service REST, http://bot.whatismyipaddress.com, pour extraire votre adresse IP externe. L'adresse IP permet de créer une règle de pare-feu pour votre serveur de base de données SQL.

    Voici quelques-unes des constantes utilisées dans le script :

    -   **$ipAddressRestService** : la valeur par défaut est <u>http://bot.whatismyipaddress.com</u>. Il s'agit d'un service REST d'adresse IP publique permettant d'obtenir votre adresse IP externe. Vous pouvez utiliser d'autres services si vous voulez. Les adresses IP externes extraites au moyen du service serviront à créer une règle de pare-feu pour votre serveur de base de données SQL Azure, ce qui vous permet d'accéder à la base de données à partir de votre poste de travail (au moyen d'un script PowerShell).
    -   **$fireWallRuleName** : il s'agit du nom de règle de pare-feu du serveur de base de données SQL Azure. Le nom par défaut est <u>FlightDelay</u>. Vous pouvez le renommer si vous voulez.
    -   **$sqlDatabaseMaxSizeGB** : cette valeur est uniquement utilisée lors de la création d'un serveur de base de données SQL Azure. La valeur par défaut est <u>10GB</u>. Une capacité de 10 Go est suffisante pour ce didacticiel.
    -   **$sqlDatabaseName** : cette valeur est uniquement utilisée lors de la création d'une base de données SQL Azure. La valeur par défaut est <u>HDISqoop</u>. Si vous la renommez, vous devez mettre à jour le script Sqoop PowerShell en conséquence.

3.  Appuyez sur **F5** pour exécuter le script. Vous devez entrer les informations d'identification de votre abonnement Azure, ainsi que les valeurs suivantes :

    -   **sqlDatabaseServer** : entrez le nom du serveur de base de données SQL Azure vers lequel vous voulez exporter la sortie Hive. N'entrez rien pour en créer un.
    -   **sqlDatabaseUsername** : entrez la connexion de base de données. Vous devez le spécifier si vous voulez créer un serveur de base de données ou en utiliser un qui existe déjà.
    -   **sqlDatabasePassword** : entrez le mot de passe de la connexion de base de données. Vous devez le spécifier si vous voulez créer un serveur de base de données ou en utiliser un qui existe déjà.
    -   **sqlDatabaseLocation** : entrez la région. Cette valeur est uniquement utilisée lorsque vous voulez créer un serveur de base de données.
    -   **sqlDatabaseName** : entrez le nom de la base de données SQL Azure. N'entrez rien pour en créer une. Le nom de base de données par défaut est **HDISqoop**.

4.  Validez la sortie du script. Vérifiez que le script s'est correctement exécuté.

Si vous décidez d'utiliser une autre méthode pour télécharger les fichiers, vérifiez que le chemin d'accès au fichier est *tutorials/flightdelays/data*. La syntaxe permettant d'accéder aux fichiers est la suivante :

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* correspond au dossier virtuel que vous avez créé lors du téléchargement des fichiers. Assurez-vous qu'il existe 12 fichiers, un par mois.

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [Configuration requise]: #prerequisite
  [Exécution d'une tâche Hive dans un cluster HDInsight nouveau ou existant (M1)]: #runjob
  [Utilisation de Sqoop pour exporter la sortie vers une base de données SQL Azure (M2)]: #exportdata
  [Étapes suivantes]: #nextsteps
  [Annexe A : Téléchargement des données de retard de vol dans un stockage d'objets blob Azure (A1)]: #appdendix-a
  [Annexe B : Création et téléchargement d'un script HiveQL (A2)]: #appendix-b
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Approvisionnement de clusters Hadoop dans HDInsight]: ../hdinsight-provision-clusters/
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [annexe]: #appendix
  [HDInsight : introduction aux tables interne et externe Hive]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Utilisation d'Oozie avec HDInsight]: ../hdinsight-use-oozie/
  [Utilisation de Sqoop avec HDInsight]: ../hdinsight-use-sqoop/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
  [Développement de programmes MapReduce en Java pour HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
