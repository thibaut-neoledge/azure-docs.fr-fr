<properties
   pageTitle="Utilisation des sources de données « données volumineuses »"
   description="Article de procédure expliquant comment utiliser Azure Data Catalog avec des sources de données « volumineuses », notamment le stockage d’objets blob Azure, Azure Data Lake et les fichiers Hadoop HDFS."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/30/2016"
   ms.author="maroche"/>


# Utilisation des sources de données volumineuses dans Azure Data Catalog

## Introduction
**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui sert de système d'inscription et de détection des sources de données d'entreprise. En d’autres termes, **Azure Data Catalog** vise essentiellement à aider les utilisateurs à détecter, comprendre et utiliser des sources de données et permet aux organisations de mieux exploiter leurs données, et notamment les données volumineuses.

**Azure Data Catalog** prend en charge l’inscription d’objets et de répertoire de Stockage d’objets blob Azure ainsi que des fichiers et des répertoires HDFS Hadoop. La nature des sources de données semi-structurées offre une grande souplesse, mais cela signifie également que les utilisateurs doivent prévoir l’organisation de leurs sources de données afin de tirer le meilleur partir de leur enregistrement auprès de **Azure Data Catalog**.

## Répertoires sous forme de jeux de données logiques

Un modèle répandu d’organisation de source de données volumineuses consiste à traiter des répertoires sous forme de jeux de données logique. Des répertoires de niveau supérieur sont utilisés pour définir un jeu de données, les sous-dossiers définissent des partitions, et les fichiers qu’ils contiennent stockent les données elles-mêmes.

Voici quelques exemples de ce modèle :

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

Dans cet exemple, vehicle\_maintenance\_events et location\_tracking\_events représentent les jeux de données logiques. Chacun de ces dossiers contient les fichiers de données organisés par année et par mois dans les sous-dossiers. Chacun de ces dossiers peut contenir des centaines ou des milliers de fichiers.

Dans ce modèle, l’enregistrement des fichiers individuels auprès d’**Azure Data Catalog** ne sert sans doute à rien. Au lieu de cela, inscrivez-vous dans les répertoires qui représentent les jeux de données significatifs pour les utilisateurs travaillant avec les données.

## Référence de fichiers de données

Un modèle complémentaire consiste à stocker des jeux de données de référence sous forme de fichiers individuels. Ces ensembles de données peuvent être considérés comme le côté « émergé » des données volumineuses et sont souvent comparables aux dimensions d’un modèle de données analytiques. Les fichiers de données de référence contiennent des enregistrements utilisés pour offrir un contexte aux lots de fichiers de données stockées ailleurs dans le magasin de données volumineuses.

Voici quelques exemples de ce modèle :

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

Lorsqu’un analyste ou un spécialiste des données travaille avec les données contenues dans des structures de répertoire plus grandes, les données présentes dans ces fichiers de référence fournissent des informations plus détaillées pour les entités référencées uniquement par nom ou ID du jeu de données plus grand.

Dans ce modèle, il serait judicieux d’enregistrer les fichiers de données de référence avec **Azure Data Catalog**. Chaque fichier représente un jeu de données, et chacun d’eux peut être annoté et découvert individuellement.

## Modèles alternatifs

Les modèles décrits ci-dessus représentent deux organisations de magasin de données volumineuses possibles, mais chaque implémentation sera différente. Quelle que soit la façon dont vos sources de données sont structurées, lors de l’enregistrement des sources de données volumineuses auprès de **Azure Data Catalog**, concentrez-vous sur l’enregistrement des fichiers et des répertoires qui représentent les jeux de données qui auront une valeur pour les autres au sein de votre organisation. L’inscription de tous les fichiers et répertoires peut encombrer le catalogue, ce qui complique les opérations de recherche pour les utilisateurs.

## Résumé
L’inscription des sources de données auprès de **Azure Data Catalog** les facilite leur détection et leur compréhension. En enregistrant et en annotant les fichiers et les répertoires de données volumineuses qui représentent les jeux de données logiques, vous pouvez aider les utilisateurs à trouver et à utiliser les sources de données volumineuses dont ils ont besoin.

<!---HONumber=AcomDC_0330_2016-->