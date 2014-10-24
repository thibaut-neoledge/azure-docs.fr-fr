<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="Machine Learning Sample: Flight delay prediction | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts whether a scheduled passenger flight will be delayed by more than 15 minutes." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Exemple Azure Machine Learning : Prédiction des retards de vol

*Vous pouvez retrouver l'exemple d'expérience associé à ce modèle dans la section **EXPÉRIENCE** de l'onglet **EXEMPLES**. Le nom de l'expérience est :*

    Sample Experiment - Flight Delay Prediction - Development

## Description du problème

Prévoir si un vol passager est retardé de plus de 15 minutes à l'arrivée en utilisant les données de ponctualité et météo.

## Données

**Données de ponctualité des vols passagers provenant de la collection de données TranStats du ministère des transports des États-Unis :** [][]<http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time></a>

Ce jeu de données couvre la période allant d'avril à octobre 2013. Avant de le charger dans AzureML Studio :

-   Le jeu de données a été filtré afin de prendre en compte les 70 aéroports les plus fréquentés aux États-Unis.
-   Les colonnes suivantes sont sélectionnées : Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelled
-   Les vols annulés ont été considérés comme ayant été retardés de plus de 15 minutes.
-   Les vols déviés ont été supprimés.

**Observations météorologiques heure par heure constatées à terre par le NOAA :** <http://cdo.ncdc.noaa.gov/qclcd_ascii/>

Les données météorologiques comprennent les observations des stations météo associées à chaque aéroport, d'avril à octobre 2013. Avant chargement dans ML Studio :

-   Les identifiants des stations météo ont été alignés sur les identifiants des aéroports correspondants.
-   Les stations météo non associées à un des 70 aéroports sélectionnés ont été supprimées.
-   La colonne de date a été divisée en colonnes Année, Mois et Jour.
-   Les colonnes suivantes sont sélectionnées : AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter

## Modèle

**Prétraitement des données de vol**

Tout d'abord, certaines colonnes sont susceptibles d'indiquer la cible : DepDelay, DepDel15, ArrDelay et Cancelled sont supprimées du jeu de données.

Les colonnes DayOfWeek, OriginAirportID et DestAirportID représentent des attributs de catégorie. Cependant, comme elles contiennent une valeur de type entier, elles sont tout d'abord analysées de façon numérique continue, puis converties en données de catégorie à l'aide de l'éditeur de métadonnées.

Nous souhaitons lier les informations de vol aux informations météorologiques en utilisant l'heure de départ prévue comme clé de jointure. Pour cela, la colonne CSRDepTime est arrondie à l'heure qui précède.

**Prétraitement des données météorologiques**

Tout d'abord, certaines colonnes présentant une grande quantité de valeurs manquantes sont supprimées. Par exemple :

-   Toutes les colonnes contenant une valeur de chaîne.
-   ValueForWindCharacter, WetBulbFarenheit, WetBulbCelsius, PressureTendency, PressureChange, SeaLevelPressure, StationPressure

Ensuite, le programme de nettoyage Missing Value Scrubber est appliqué aux colonnes restantes.

L'heure de l'observation météorologique est arrondie à l'heure juste la plus proche, afin d'être jointe à une heure de départ prévu des vols. Notez que les heures de départ des vols et les heures des observations météorologiques sont arrondies dans des directions opposées. Ceci afin de s'assurer que le modèle utilise uniquement des observations météorologiques passées par rapport à l'heure de départ du vol.

**Jointure des jeux de données**

Les enregistrements de vol sont joints aux données météorologiques au point de départ et au point d'arrivée du vol.

Notez que les données météorologiques utilisent l'heure locale, mais que l'origine et la destination peuvent se trouver dans des fuseaux horaires différents. Ainsi, un ajustement en fonction des fuseaux horaires est appliqué en soustrayant la colonne du fuseau horaire de l'heure de départ prévu et de l'heure de l'observation météorologique.

**Préparation d'exemples d'apprentissage et de validation**

Les exemples d'apprentissage et de validation sont créés en prenant les enregistrements d'avril à septembre pour l'apprentissage, et ceux d'octobre pour la validation. Les colonnes de mois et d'année sont ensuite supprimées du jeu de données.

De plus, les données d'apprentissage sont quantifiées en égalisant la hauteur. La même égalisation est appliquée aux données de validation.

**Apprentissage et validation du modèle**

Un modèle d'arbre de décision optimisé à deux classes effectue l'apprentissage avec les données d'exemple. Ce modèle est optimisé pour obtenir la meilleure intégration en utilisant un balayage de paramètres aléatoires répété 10 fois. À titre de comparaison, le modèle de régression logistique à deux classes est optimisé de la même manière.

Le modèle créé est ensuite évalué par rapport au jeu de validation. Le module Évaluer le modèle est utilisé pour comparer la qualité des modèles.

**Post-traitement**

Les identifiants d'aéroport sont mis en relation avec les noms d'aéroports complets, afin de faciliter l'analyse des résultats.

## Résultats

Le modèle d'arbres de décision optimisés présente une intégration de 0,697 par rapport au jeu de validation, ce qui est légèrement meilleur que le modèle de régression logistique dont l'intégration est de 0,675.

  []: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
