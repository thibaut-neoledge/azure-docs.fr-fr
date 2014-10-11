<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyze flight delay data using Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Analyse des données sur les retards de vol avec Hadoop dans HDInsight

Hive permet d'exécuter des tâches Hadoop MapReduce via un langage de création de scripts semblable à SQL, nommé *[HiveQL][]*, qui peut être appliqué au résumé, à l'envoi de requêtes et à l'analyse d'importants volumes de données. Ce didacticiel vous indique comment utiliser Hive pour calculer les retards moyens enregistrés dans les aéroports et comment utiliser Sqoop pour exporter les résultats dans la base de données SQL.

**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Un cluster Azure HDInsight. Pour plus d'informations sur l'approvisionnement d'un cluster HDInsight, consultez la rubrique [Prise en main de HDInsight][] ou [Mise en place de clusters HDInsight][].
-   Un poste de travail sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][].

**Durée de réalisation estimée :** 30 minutes

## Dans ce didacticiel

-   [Préparation du didacticiel][]
-   [Création et téléchargement d'un script HiveQL][]
-   [Exécution d'un script HiveQL][]
-   [Exportation du résultat vers la base de données Azure SQL][]
-   [Étapes suivantes][]

## <span id="prepare"></span></a>Préparation du didacticiel

Ce didacticiel utilise les données de ponctualité des vols des compagnies aériennes de la [Research and Innovative Technology Administration, Bureau of Transportation Statistics][] (RITA) sur votre poste de travail. Vous allez effectuer les opérations suivantes :

1.  Téléchargement des données de données de performances de ponctualité de RITA sur votre poste de travail à l'aide d'un navigateur Web
2.  Envoi des données à HDInsight à l'aide d'Azure PowerShell
3.  Préparation de la base de données SQL à des fins d'exportation des données à l'aide d'Azure PowerShell

**Présentation du stockage HDInsight**

HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Il s'intitule *WASB* ou *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft du HDFS sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][].

Lorsque vous approvisionnez un cluster HDInsight, un conteneur de stockage d'objets blob est désigné comme système de fichiers par défaut, comme dans HDFS. Outre ce conteneur, vous pouvez ajouter des conteneurs supplémentaires à partir du même compte de stockage Azure ou de différents comptes de stockage Azure au cours du processus d'approvisionnement. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters HDInsight][Mise en place de clusters HDInsight].

Pour simplifier le script utilisé dans ce didacticiel, tous les fichiers sont stockés dans le conteneur du système de fichiers par défaut situé sous */tutorials/flightdelays*. Par défaut, ce conteneur porte le même nom que le cluster HDInsight.

La syntaxe WASB est :

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] Seule la syntaxe *wasb://* est prise en charge dans le cluster HDInsight version 3.0. L'ancienne syntaxe *asv://* est prise en charge dans les clusters HDInsight 2.1 et 1.6, mais ne l'est pas dans les clusters HDInsight 3.0 et ne le sera pas dans les versions ultérieures.

> Le chemin d'accès WASB est le chemin d'accès virtuel. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][].

Un fichier stocké dans le conteneur du système de fichiers par défaut est accessible depuis HDInsight à l'aide de l'une des URI suivantes (utilisez flightdelays.hql à titre d'exemple) :

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

    tutorials/flightdelays/flightdelays.hql

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

<table border="1">
<tr><td><strong>Fichiers</strong></td><td><strong>Description</strong></td></tr>
<tr><td>\\tutorials\\flightdelays\\data             </td><td>Données d'entrée des vols pour Hive </td></tr>
<tr><td>\\tutorials\\flightdelays\\output           </td><td>Données de sortie pour Hive         </td></tr>
<tr><td>\\tutorials\\flightdelays\\flightdelays.hql </td><td>Fichier de script HiveQL            </td></tr>
<tr><td>\\tutorials\\flightdelays\\jobstatus        </td><td>État de la tâche Hadoop             </td></tr>
</table>

**Présentation des tables interne et externe Hive**

Voici quelques éléments à connaître sur les tables interne et externe Hive :

-   La commande CREATE TABLE permet de créer une table interne. Le fichier de données doit se trouver dans le conteneur par défaut.
-   La commande CREATE TABLE transfère le fichier de données vers le dossier /hive/warehouse/<tablename>.
-   La commande CREATE EXTERNAL TABLE permet de créer une table externe. Le fichier de données peut se trouver à l'extérieur du conteneur par défaut.
-   La commande CREATE EXTERNAL TABLE ne déplace pas le fichier de données.
-   La commande CREATE EXTERNAL TABLE n'autorise aucun dossier à l'EMPLACEMENT. C'est la raison pour laquelle le didacticiel réalise une copie du fichier sample.log.

Pour plus d'informations, consultez la rubrique [HDInsight : introduction aux tables interne et externe Hive][cindygross-hive-tables].

> [WACOM.NOTE] Une des instructions HiveQL crée une table externe Hive. La table externe Hive conserve le fichier de données à son emplacement d'origine. La table interne Hive transfère le fichier de données vers hive\\warehouse. Pour les besoins de la table externe Hive, le fichier de données doit être situé dans le conteneur WASB du système de fichiers par défaut. Si vous choisissez de stocker les fichiers de données de vol dans un autre conteneur que le conteneur d'objets blob par défaut, vous devez utiliser les tables internes Hive.

**Pour télécharger les données de vol**

1.  Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics][] (RITA).
2.  Sur la page, sélectionnez les valeurs suivantes :

    | Nom            | Valeur                                                                                                                                                                                                                                                                                                                                                                                           |
    |----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filtre année   | 2012                                                                                                                                                                                                                                                                                                                                                                                             |
    | Filtre période | Janvier                                                                                                                                                                                                                                                                                                                                                                                          |
    | Champs :       | \*Year\*, \*FlightDate\*, \*UniqueCarrier\*, \*Carrier\*, \*FlightNum\*, \*OriginAirportID\*, \*Origin\*, \*OriginCityName\*, \*OriginState\*, \*DestAirportID\*, \*Dest\*, \*DestCityName\*, \*DestState\*, \*DepDelayMinutes\*, \*ArrDelay\*, \*ArrDelayMinutes\*, \*CarrierDelay\*, \*WeatherDelay\*, \*NASDelay\*, \*SecurityDelay\*, \*LateAircraftDelay\* (effacez tous les autres champs) |

3.  Cliquez sur **Télécharger**. Le téléchargement de chaque fichier peut nécessiter jusqu'à 15 minutes.
4.  Décompressez le fichier dans le dossier **C:\\Tutorials\\FlightDelays\\Data**. Chaque fichier correspond à un fichier CSV d'environ 60 Go.
5.  Donnez au fichier le nom du mois dont il contient les données. Par exemple, renommez le fichier contenant les données du mois de janvier *Janvier.csv*.
6.  Répétez les étapes 2 et 5 afin de télécharger un fichier pour chacun des 12 mois de l'année 2012. Vous aurez besoin d'au moins un fichier pour exécuter le didacticiel.

**Pour télécharger les données de retard de vol vers le stockage d'objets blob Azure**

1.  Ouvrez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][].
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3.  Définissez les trois premières variables, puis exécutez les commandes.

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $ContainerName = "<BlobStorageContainerName>"

        $localFolder = "C:\Tutorials\FlightDelays\Data"
        $destFolder = "tutorials/flightdelays/data"

        $month = "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    Vous trouverez ci-dessous les variables et leur description :

    <table border="1">

    <tr>
    <td>
    <strong>Nom de la variable</strong>

    </td>
    <td>
    <strong>Description<strong>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Nom de votre abonnement Azure.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Compte Azure Storage utilisé pour stocker les fichiers de données de vol. Il est recommandé d'utiliser le compte de stockage par défaut.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Conteneur de stockage d'objets blob Azure utilisé pour stocker les fichiers de données de vol. Il est recommandé d'utiliser le conteneur d'objets blob du système de fichiers du cluster HDInsight. Par défaut, il porte le même nom que le cluster HDInsight.

    </td>
    </tr>

    <tr>
    <td>
    $localFolder

    </td>
    <td>
    Dossier de votre poste de travail dans lequel les fichiers de retard de vol sont stockés.

    </td>
    </tr>

    <tr>
    <td>
    $destFolder

    </td>
    <td>
    Chemin WASB vers lequel les données de retard de vol seront téléchargées. Le chemin Hadoop (HDInsight) est sensible à la casse.

    </td>
    </tr>

    <tr>
    <td>
    $month

    </td>
    <td>
    Si vous n'avez pas téléchargé les 12 fichiers, vous devez mettre à jour cette variable.

    </td>
    </tr>

    </table>
    </p>
4.  Exécutez les commandes suivantes pour télécharger et répertorier les fichiers :

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB        
        foreach ($item in $month){
            $fileName = "$localFolder\$item.csv"
            $blobName = "$destFolder/$item.csv"

            Write-Host "Copying $fileName to $blobName" -BackgroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

Si vous décidez d'utiliser une autre méthode pour télécharger les fichiers, vérifiez que le chemin d'accès au fichier est *tutorials/flightdelays/data*. La syntaxe permettant d'accéder aux fichiers est la suivante :

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* correspond au dossier virtuel que vous avez créé lors du téléchargement des fichiers. Assurez-vous qu'il existe 12 fichiers, un par mois.

**Pour préparer la base de données SQL**

1.  Ouvrez Azure PowerShell.
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

    Vous êtes invité à entrer les informations d'identification de votre compte Azure.

3.  Définissez les six premières variables du script suivant, puis exécutez les commandes :

        # Azure subscription name
        $subscriptionName = "<AzureSubscriptionName>"

        # SQL database server variables
        $sqlDatabaseServer = ""  # specify the Azure SQL database server name if you have one created. Otherwise use "".
        $sqlDatabaseUsername = "<SQLDatabaseUserName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #For example, West US.

        # SQL database variables
        $sqlDatabaseName = ""  # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        $sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        $sqlDatabaseTableName = "AvgDelays"
        $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
            (
                [origin_city_name] ASC
            )
            )"

    Vous trouverez ci-dessous les variables et leur description :

    <table border="1">

    <tr>
    <td>
    <strong>Nom de la variable</strong>

    </td>
    <td>
    <strong>Description</strong>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Nom de votre abonnement Azure.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Nom du serveur de base de données SQL utilisé par Sqoop pour exporter les données. Si vous le laissez tel quel, le script la crée automatiquement pour vous. Sinon, indiquez une base de données ou un serveur SQL existant.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    Nom de l'utilisateur de la base de données SQL/du serveur SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    Mot de passe de l'utilisateur de la base de données SQL/du serveur SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseLocation

    </td>
    <td>
    Uniquement utilisé pour permettre au script de créer automatiquement un serveur de base de données SQL.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Nom de la base de données SQL vers laquelle Sqoop exporte les données. Si vous le laissez tel quel, le script la crée automatiquement pour vous. Sinon, indiquez une base de données ou un serveur SQL existant.

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseMaxSizeGB

    </td>
    <td>
    Uniquement utilisé pour permettre au script de créer automatiquement une base de données SQL.

    </td>
    </tr>

    </table>
    </p>
4.  Exécutez les commandes suivantes pour créer un(e) serveur/base de données/table de base de données SQL.

        # Select the current Azure subscription in case there are multiple subscriptions
        Select-AzureSubscription $subscriptionName

        # create a new Azure SQL Database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
            Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
        }

        # Create a new SQL database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB

            Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
        }

        #Create AvgDelays table
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

## <span id="createscript"></span></a>Création et téléchargement d'un script HiveQL

À l'aide d'Azure PowerShell, vous pouvez exécuter plusieurs instructions HiveQL, une par une, ou empaqueter l'instruction HiveQL dans un fichier de script. Dans ce didacticiel, vous allez créer un script HiveQL. Le fichier de script doit être téléchargé sur WASB. Dans la section suivante, vous allez exécuter le fichier de script à l'aide d'Azure PowerShell.

Le script HiveQL exécutera les opérations suivantes :

1.  **Il supprime la table delays\_raw**, le cas échéant.
2.  **Il crée la table externe Hive delays\_raw** pointant vers l'emplacement WASB où se trouvent les fichiers de retard de vol. Cette requête spécifie les champs délimités par « , » et les lignes se terminant par « \\n ». Cela pose un problème lorsque les valeurs des champs *contiennent* des virgules, car Hive n'est pas en mesure de différencier une virgule utilisée en tant que délimiteur de champ d'une virgule faisant partie d'une valeur de champ (ce qui est le cas pour les valeurs des champs ORIGIN\_CITY\_NAME et DEST\_CITY\_NAME). Pour y remédier, la requête crée des colonnes TEMP afin de contenir les données incorrectement réparties dans les colonnes.
3.  **Il supprime la table des retards**, le cas échéant.
4.  **Il crée la table des retards**. Il est conseillé de nettoyer les données avant tout traitement plus approfondi. Cette requête crée une nouvelle table, *retards*, à partir de la table *delays\_raw*. Notez que les colonnes TEMP (comme indiqué précédemment) ne sont pas copiées et que la fonction *substring* est utilisée pour supprimer les guillemets présents dans les données.
5.  **Il calcule les retards moyens liés aux conditions météo et regroupe les résultats par nom de ville.** Il transmet également les résultats à WASB. Notez que la requête supprime les apostrophes des données et exclut les lignes dans lesquelles la valeur correspondant à *weather\_delay* est *null*, ce qui est nécessaire car Sqoop, utilisé plus tard dans ce didacticiel, ne gère pas ces valeurs par défaut.

Pour obtenir la liste complète des commandes HiveQL, consultez la rubrique [Langage de définition des données Hive][HiveQL]. Chaque commande HiveQL doit se terminer par un point virgule.

**Pour créer un fichier de script HiveQL**

1.  Ouvrez Azure PowerShell.
2.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

3.  Définissez les deux premières variables, puis exécutez les commandes.

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
    <strong>Nom de la variable</strong>

    </td>
    <td>
    <strong>Description</strong>

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
4.  Exécutez les commandes suivantes pour définir les instructions HiveQL :

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

5.  Exécutez les commandes suivantes pour écrire le fichier de script Hive sur le poste de travail et l'envoyer à WASB :

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

## <span id="executehqlscript"></span></a>Exécution d'un script HiveQL

Plusieurs cmdlets Azure PowerShell vous permettent d'exécuter Hive. Ce didacticiel utilise Invoke-Hive. Pour connaître les autres méthodes, consultez la rubrique [Utilisation de Hive avec HDInsight][]. Invoke-Hive vous permet d'exécuter une instruction HiveQL ou un script HiveQL. Vous allez utiliser le script HiveQL que vous avez créé et envoyé au stockage d'objets blob Azure

Il existe un problème connu de chemin d'accès à Hive. Les instructions permettant d'y remédier sont disponibles dans la rubrique [Wiki TechNet][].

**Pour exécuter des requêtes Hive à l'aide de PowerShell**

1.  Ouvrez Azure PowerShell.
2.  Exécutez la commande suivante pour modifier le répertoire actuel :

    cd \\Tutorials\\FlightDelays\\

    Cette étape est obligatoire, car vous allez télécharger une copie du résultat Hive sur votre poste de travail. Par défaut, vous ne disposez pas de l'autorisation d'accès en écriture sur le dossier PowerShell.

3.  Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

        Add-AzureAccount

4.  Définissez les trois premières variables, puis exécutez-les :

        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureBlobStorageContainerName>"
        $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
        $outputBlobName = "tutorials/flightdelays/output/000000_0"

    Vous trouverez ci-dessous les variables et leur description :

    <table border="1">

    <tr>
    <td>
    <strong>Nom de la variable</strong>

    </td>
    <td>
    <strong>Description</strong>

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Cluster HDInsight exécutant le script Hive et l'export Sqoop.

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    Compte Azure Storage utilisé pour stocker le script HiveQL. Consultez la rubrique <a href = "#createScript">Création et téléchargement d'un script HiveQL</a>.

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    Conteneur de stockage d'objets blob Azure utilisé pour stocker le script HiveQL. Consultez la rubrique <a href = "#createScript">Création et téléchargement d'un script HiveQL</a>.

    </td>
    </tr>

    <tr>
    <td>
    $hqlScriptFile

    </td>
    <td>
    URI du fichier de script HiveQL.

    </td>
    </tr>

    <tr>
    <td>
    $outputBlobName

    </td>
    <td>
    Fichier de résultat du script HiveQL. Par défaut, le nom de ce fichier est *000000\_0*.

    </td>
    </tr>

    </table>
    </p>
5.  Exécutez la commande suivante pour appeler Hive :

        Use-AzureHDInsightCluster $clusterName

        Invoke-Hive –File $hqlScriptFile

6.  Exécutez la commande suivante pour vérifier les résultats :

        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 

        cat ".\$outputBlobName" | findstr /B "Ch"

    Le résultat doit ressembler à ceci :

        Champaign/Urbana 19.023255813953487
        Charleston 24.93975903614458
        Charleston/Dunbar 16.954545454545453
        Charlotte 16.88616981831665
        Charlotte Amalie 12.88888888888889
        Charlottesville 25.444444444444443
        Chattanooga 19.883561643835616
        Cheyenne 9.875
        Chicago 16.77321649680922
        Chico 12.340909090909092
        Christiansted 12.318181818181818

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
    <strong>Nom de la variable</strong>

    </td>
    <td>
    <strong>Remarque</strong>

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

    ![HDI.FlightDelays.AvgDelays.Dataset][]

## <span id="nextsteps"></span></a>Étapes suivantes

Vous savez à présent télécharger un fichier vers le stockage d'objets blob, renseigner une table Hive à l'aide des données du stockage d'objets blob, exécuter des requêtes Hive et utiliser Sqoop pour exporter des données entre HDFS et la base de données SQL Azure. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main de HDInsight][]
-   [Utilisation de Hive avec HDInsight][]
-   [Utilisation d'Oozie avec HDInsight][]
-   [Utilisation de Sqoop avec HDInsight][]
-   [Utilisation de Pig avec HDInsight][]
-   [Développement de programmes MapReduce en Java pour HDInsight][]
-   [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][]

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [Prise en main de HDInsight]: ../hdinsight-get-started/
  [Mise en place de clusters HDInsight]: ../hdinsight-provision-clusters/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [Préparation du didacticiel]: #prepare
  [Création et téléchargement d'un script HiveQL]: #createscript
  [Exécution d'un script HiveQL]: #executehqlscript
  [Exportation du résultat vers la base de données Azure SQL]: #exportdata
  [Étapes suivantes]: #nextsteps
  [Research and Innovative Technology Administration, Bureau of Transportation Statistics]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [Wiki TechNet]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [1]: #createScript
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Utilisation d'Oozie avec HDInsight]: ../hdinsight-use-oozie/
  [Utilisation de Sqoop avec HDInsight]: ../hdinsight-use-sqoop/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
  [Développement de programmes MapReduce en Java pour HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
