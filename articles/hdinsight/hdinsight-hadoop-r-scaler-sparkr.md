---
title: "Combinaison de ScaleR et SparkR avec Azure HDInsight | Microsoft Docs"
description: "Utiliser ScaleR et SparkR avec R Server et HDInsight"
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 5e8fb7642dca815c64b9aed8184672259d3facf8
ms.lasthandoff: 03/10/2017


---

# <a name="mixing-use-of-scaler-and-sparkr-in-hdinsight"></a>Combiner l’utilisation de ScaleR et SparkR dans HDInsight

Découvrez comment combiner l’utilisation de SparkR pour la manipulation des données sur Spark avec Microsoft R Server pour Analytics. Bien que les deux packages s’exécutent par-dessus le moteur d’exécution de Hadoop Spark pour tirer parti des dernières fonctionnalités de traitement distribué, ils sont exclus du partage de données en mémoire en exigeant leurs propres sessions Spark. Jusqu’à ce que ce problème soit résolu dans une prochaine version de R, Server, la solution de contournement consiste à mettre à jour les sessions Spark qui ne se recouvrent pas et à échanger les données via des fichiers intermédiaires. Comme vous le constaterez, ces deux conditions sont relativement faciles à remplir.

Pour illustrer ces propos, nous utiliserons un exemple partagé initialement par Mario Inchiosa et Roni Burd lors de leur intervention dans le cadre de la conférence Strata 2016, également disponible via le webinaire [Création d’une plateforme de science des données évolutive avec R](http://event.on24.com/eventRegistration/console/EventConsoleNG.jsp?uimode=nextgeneration&eventid=1160288&sessionid=1&key=8F8FB9E2EB1AEE867287CD6757D5BD40&contenttype=A&eventuserid=305999&playerwidth=1000&playerheight=650&caller=previewLobby&text_language_id=en&format=fhaudio). L’exemple utilise SparkR pour associer le jeu de données bien connu des retards d’arrivée des compagnies aériennes aux données météorologiques dans les aéroports de départ et d’arrivée, puis exploite le résultat obtenu comme entrée vers un modèle de régression logistique ScaleR pour la prédiction des retards d’arrivée des vols.

Le code que nous allons présenter pas à pas a été écrit à l’origine pour R Server s’exécutant sur Spark dans un cluster HDInsight dans Azure, mais le concept d’utilisation de SparkR et ScaleR dans un script s’applique également pour les environnements sur site. L’exemple suivant suppose un niveau intermédiaire de connaissances de R et de la bibliothèque [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) de R Serve et présente l’utilisation de [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) tout au long du processus.

## <a name="the-airline-and-weather-datasets"></a>Jeux de données météorologiques et jeux de données des compagnies aériennes

Le jeu de données public des compagnies aériennes AirOnTime08to12CSV contient des informations sur l’arrivée et le départ de tous les vols commerciaux aux États-Unis, d’octobre 1987 à décembre 2012. Il s’agit d’un jeu de données volumineux puisqu’il contient près de 150 millions d’enregistrements au total. Une fois décompressé, sa taille est légèrement inférieure à 4 Go. Il est disponible à partir des [archives du gouvernement américain](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236), mais aussi sous la forme plus exploitable de fichier zip (AirOnTimeCSV.zip) contenant un ensemble de 303 fichiers CSV mensuels distincts à partir du [référentiel de jeu de données Revolution Analytics](http://packages.revolutionanalytics.com/datasets/AirOnTime87to12/)

Pour constater l’influence de la météo sur les retards des vols, nous aurons également besoin des données météorologiques pour chacun des aéroports. Celles-ci peuvent être téléchargées tous les mois sous forme de fichiers zip au format brut à partir du [référentiel de la National Oceanic and Atmospheric Administration](http://www.ncdc.noaa.gov/orders/qclcd/). Dans le cadre de cet exemple, nous avons extrait les données météorologiques entre mai 2007 et décembre 2012 et nous avons utilisé les fichiers de données horaires dans chacun des 68 fichiers zip mensuels. Les fichiers zip mensuels contiennent également un mappage (YYYYMMstation.txt) entre l’ID de station météorologique (WBAN), l’aéroport auquel elle est associée (CallSign) et le décalage de fuseau horaire à l’heure UTC (TimeZone). Nous aurons besoin de tous ces éléments lorsque nous associerons les données sur les retards des compagnies aériennes.

## <a name="setting-up-the-spark-environment"></a>Configuration de l’environnement Spark

La configuration de l’environnement Spark constitue la première étape avant la préparation des données météorologiques et la fusion avec les données de la compagnie aérienne pour la modélisation. Nous commençons par pointer vers le répertoire contenant les répertoires des données d’entrée, créer un contexte de calcul Spark, puis créer une fonction de journalisation pour la journalisation d’informations sur la console :

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAcccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Ensuite, nous ajouterons l’élément « Spark_Home » au chemin de recherche pour les packages R afin d’utiliser SparkR et nous initialiserons une session SparkR.

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Préparation des données météorologiques

Pour préparer les données météorologiques, nous les ajouterons en tant que sous-ensemble aux colonnes nécessaires à la modélisation : « Visibility », « DryBulbCelsius », « DewPointCelsius », « RelativeHumidity », « WindSpeed » et « Altimeter ». Nous ajouterons ensuite un code d’aéroport associé à la station météorologique et nous convertirons l’heure locale au format UTC.

Nous commençons par créer un fichier pour mapper les informations de station météorologique (WBAN) à un code d’aéroport. Nous pouvons obtenir cela à partir du fichier de mappage inclus avec les données météorologiques en mappant le champ CallSign (par exemple, LAX) du fichier de données météorologiques au champ Origin des données de la compagnie aérienne. Toutefois,nous disposons d’un autre mappage associant WBAN à AirportID (par exemple, 12892 pour LAX) et comprenant l’élément TimeZone enregistré dans le fichier CSV appelé « wban-to-airport-id-tz.CSV » que nous allons utiliser, par exemple :

| AirportID | WBAN | TimeZone
|-----------|------|---------
| 10685 | 54831 | -6
| 14871 | 24232 | -8
| .. | .. | ..

Le code suivant lit chacun des fichiers de données horaires météorologiques brutes, les ajoute en tant que sous-ensembles aux colonnes dont nous avons besoin, fusionne le fichier de mappage de station météorologique, ajuste les dates à l’heure UTC, puis écrit une nouvelle version du fichier.

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Importation des données météorologiques et des données de compagnies aériennes vers Spark DataFrames

Nous allons maintenant utiliser la fonction [read.df()](https://docs.databricks.com/spark/latest/sparkr/functions/read.df.html) de SparkR pour importer les données météorologiques et des compagnies aériennes vers Spark DataFrame. Cette fonction, comme d’autres méthodes Spark, est exécutée en différé, ce qui signifie qu’elle est placée en file d’attente en vue de l’exécution, mais qu’elle n’est exécutée que lorsque cela est nécessaire.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Nettoyage et transformation des données

Nous allons ensuite procéder au nettoyage des données de la compagnie aérienne que nous avons importées pour renommer les colonnes et conserver uniquement les variables dont nous avons besoin. Nous arrondirons les heures de départ prévues jusqu’à l’heure la plus proche afin d’activer la fusion avec les dernières données météorologiques avant le départ.

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Nous allons maintenant effectuer des opérations similaires sur les données météorologiques :

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Joindre les données météorologiques et les données de compagnies aériennes

Nous allons maintenant utiliser la fonction [join()](https://docs.databricks.com/spark/latest/sparkr/functions/join.html) de SparkR pour effectuer une jointure externe gauche des données météorologiques et des données de compagnies aériennes par ID d’aéroport et date à l’aéroport de départ. La jointure externe permet de conserver tous les enregistrements de données de compagnies aériennes même s’il n’existe aucune donnée météorologique correspondante. Une fois la jointure effectuée, nous supprimons les colonnes redondantes et renommons les colonnes conservées afin d’éliminer le préfixe DataFrame entrant émanant de la jointure.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

De la même manière, nous joindrons les données météorologiques et les données de compagnies aériennes en fonction de l’ID d’aéroport et de la date à l’aéroport d’arrivée.

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Enregistrer les résultats au format CSV pour l’échange avec ScaleR

Maintenant que vous disposez de toutes les informations nécessaires sur les jointures, nous en avons fini avec SparkR. Nous allons enregistrer les données du fichier final Spark DataFrame « joinedDF5 » dans un fichier CSV pour les saisir dans ScaleR, puis nous fermerons la session SparkR. Nous indiquons explicitement à SparkR d’enregistrer le fichier CSV résultant dans 80 partitions distinctes afin d’établir un parallèle suffisant avec le traitement ScaleR.

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importer vers XDF pour une utilisation par ScaleR

Nous pourrions utiliser le fichier CSV de données jointes concernant la météo et les compagnies aériennes en l’état pour la modélisation via une source de données texte ScaleR, mais nous allons l’importer vers XDF, car il est plus efficace lorsqu’il est exécuté sur plusieurs opérations sur le jeu de données.

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for R Server 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Fractionnement des données pour le test et l’apprentissage

Nous utilisons rxDataStep afin de fractionner les données 2012 pour le test et nous conservons le reste pour l’apprentissage.

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Effectuer l’apprentissage et tester un modèle de régression logistique

OK, nous sommes prêts à créer un modèle ! Pour voir l’impact des données météorologiques sur le retard par rapport à l’heure d’arrivée, nous allons utiliser la routine de régression logistique de ScaleR pour modéliser si un retard d’arrivée de plus de 15 minutes est influencé par la date relative, les aéroports de départ et d’arrivée, les conditions météorologiques aux aéroports de départ et d’arrivée, etc.

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Nous allons maintenant voir comment ce modèle fonctionne sur les données de test en effectuant des prédictions, et en étudiant la courbe ROC et l’aire sous la courbe.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Notation depuis un autre emplacement

Nous pouvons également utiliser le modèle pour évaluer les données sur une autre plate-forme en l’enregistrant dans un fichier RDS, puis en transférant et en important ce RDS vers l’environnement de notation de destination, tel que SQL Server R Services. Lors de cette opération, il est important de vérifier que les niveaux de facteur des données à évaluer correspondent à ceux sur lesquels le modèle a été généré. Pour ce faire, il suffit d’extraire et d’enregistrer les informations de colonne associées aux données de modélisation via la fonction rxCreateColInfo() de ScaleR, puis d’appliquer ces informations de colonne à la source de données d’entrée pour la prédiction. Dans l’exemple suivant, nous avons simplement enregistré quelques lignes du jeu de données test, puis extrait et utilisé les informations de colonne à partir de cet exemple dans le script de prédiction.

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Résumé

Et voilà ! Dans cet article, nous avons montré comment combiner l’utilisation de SparkR pour la manipulation des données avec ScaleR pour le développement de modèle dans Hadoop Spark en veillant à mettre à jour des sessions Spark distinctes, en exécutant uniquement une session à la fois et en échangeant des données via des fichiers CSV. Bien que simple, ce processus sera largement simplifié dans une prochaine version de R Server lorsque SparkR et ScaleR pourront partager une session Spark et, par conséquent Spark DataFrames.

## <a name="next-steps-and-more-information"></a>Étapes suivantes et informations supplémentaires

- Pour plus d’informations sur l’utilisation de R Server sur Spark, voir [Guide de prise en main de MSDN](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)

- Pour des informations générales sur R Server, voir l’article [Prise en main de R](https://msdn.microsoft.com/microsoft-r/microsoft-r-get-started-node).

- Vous pouvez également consulter les articles [R Server sur Azure HDInsight](hdinsight-hadoop-r-server-get-started.md) et [Vue d’ensemble de R Server sur Azure HDInsigh](hdinsight-hadoop-r-server-overview.md).

Pour plus d’informations sur l’utilisation de SparkR, consultez les pages suivantes :

- [Document Apache SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html)

- [Vue d’ensemble de SparkR](https://docs.databricks.com/spark/latest/sparkr/overview.html) à partir de Databricks

