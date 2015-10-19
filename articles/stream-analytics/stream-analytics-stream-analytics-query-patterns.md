<properties
	pageTitle="Modèles de requêtes Azure Stream Analytics | Microsoft Azure"
	description="Modèles courants de requêtes Azure Stream Analytics"
	keywords="stream analytics, sample, query, language, guide, patterns"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="10/05/2015"
	ms.author="jeffstok"/>


# Modèles courants de requêtes Azure Stream Analytics  #

## Introduction ##
Les requêtes Azure Stream Analytics sont exprimées dans un langage de requête de type SQL présenté [ici](https://msdn.microsoft.com/library/azure/dn834998.aspx). Ce document décrit les solutions à plusieurs modèles de requête habituels, inspirés de scénarios réels. Il est en cours et sera mis à jour avec de nouveaux modèles de manière continue.

## Concepts de base ##

## Conversions de types de données ##
**Description** : définir les types des propriétés sur le flux d'entrée. Par exemple, le poids de la voiture arrive sur le flux d'entrée sous forme de chaînes et doit être converti en INT pour effectuer la synthèse SUM.

**Entrée** :

| Assurez-vous | Time | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Sortie** :

| Assurez-vous | Poids |
| --- | --- |
| Honda | 3000 |

**Solution** :

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**Explication** : utilisation d’une instruction CAST sur le champ Weight pour spécifier son type (voir la liste des types de données pris en charge [ici](https://msdn.microsoft.com/library/azure/dn835065.aspx)).

## Utilisation de Like/Not like pour la correspondance de modèle ##
**Description** : vérification qu'une valeur de champ sur l'événement correspond à un certain modèle, par exemple renvoyer les immatriculations commençant par A et se terminant par 9

**Entrée** :

| Assurez-vous | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Sortie** :

| Assurez-vous | LicensePlate | Time |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Solution** :

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**Explication** : utilisation de l'instruction LIKE pour vérifier que la valeur du champ LicensePlate commence par A, suivi d’une chaîne de zéro caractère ou plus et se termine par 9.

## Spécification de la logique pour différentes casses/valeurs (instructions CASE) ##
**Description** : fourniture de calculs différents pour un champ en fonction de certains critères. Par exemple, fourniture d’une description de chaîne pour le nombre de voitures de la même marque, avec une casse spéciale pour 1.

**Entrée** :

| Assurez-vous | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Sortie** :

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyota | 2015-01-01T00:00:10.0000000Z |

**Solution** :

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**Explication** : la clause CASE permet de fournir un calcul différent en fonction de certains critères (dans ce cas, le nombre de voitures dans la fenêtre d'agrégation).

## Envoi de données vers plusieurs sorties ##
**Description** : envoi de données vers plusieurs cibles de sortie depuis un projet unique. Par exemple, une analyse des données pour une alerte de seuil et l’archivage de tous les événements pour le stockage d'objets blob

**Entrée** :

| Assurez-vous | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1** :

| Assurez-vous | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2** :

| Assurez-vous | Time | Nombre |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solution** :

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**Explication** : la clause INTO indique à Stream Analytics la sortie sur laquelle écrire les données à partir de cette instruction. La première requête est un transfert des données que nous avons reçues vers une sortie nommée ArchiveOutput. La deuxième requête effectue une agrégation et un filtrage simples et envoie les résultats vers un système d'alerte en aval. *Remarque* : vous pouvez également réutiliser des résultats d'expressions de table communes (par exemple, avec des instructions WITH) dans plusieurs instructions de sortie : cela présente l'avantage supplémentaire d’ouvrir moins de lecteurs à la source d'entrée par exemple.

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## Modèles ##

## Comptage des valeurs uniques
**Description** : le nombre de valeurs de champ uniques qui apparaissent dans le flux de données dans une fenêtre de temps. Par exemple, combien de voitures de marques différentes sont passées au péage pendant une fenêtre de 2 secondes ?

**Entrée** :

| Assurez-vous | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output:**

| Nombre | Time |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solution :**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**Explication :** une agrégation initiale est effectuée pour obtenir les marques uniques avec leur nombre au-dessus de la fenêtre. Ensuite, nous effectuons une agrégation du nombre de marques obtenues. Étant donné que toutes les valeurs uniques dans une fenêtre obtiennent le même horodatage, la deuxième fenêtre d'agrégation doit être minime afin de ne pas agréger 2 fenêtres de la première étape.

## Déterminer si une valeur a changé ##
**Description** : examen d’une valeur précédente pour déterminer si elle est différente de la valeur actuelle. Par exemple, la voiture actuellement sur la voie de péage est-elle de la même marque que la voiture précédente ?

**Entrée** :

| Assurez-vous | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Sortie** :

| Assurez-vous | Time |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Solution** :

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explication** : utilisez LAG pour lire l’événement précédent du flux d’entrée et obtenir la valeur de Make. Elle est ensuite comparée à la marque de l’événement en cours, puis l'événement émet une sortie si elles sont différentes.

## Recherche du premier événement dans une fenêtre ##
**Description** : trouver la première voiture de chaque intervalle de 10 minutes.

**Entrée** :

| LicensePlate | Assurez-vous | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Sortie** :

| LicensePlate | Assurez-vous | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solution** :

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

À présent, nous allons modifier le problème et trouver la première voiture d’une marque donnée dans chaque intervalle de 10 minutes.

| LicensePlate | Assurez-vous | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution** :

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## Recherche du dernier événement dans une fenêtre ##
**Description** : trouver la dernière voiture de chaque intervalle de 10 minutes.

**Entrée** :

| LicensePlate | Assurez-vous | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Sortie** :

| LicensePlate | Assurez-vous | Time |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution** :

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**Explication** : la requête comprend deux étapes, la première servant à rechercher l'horodatage le plus récent dans une plage de 10 minutes. La deuxième étape joint les résultats de la première requête avec des flux de données d'origine pour rechercher les événements qui correspondent aux derniers horodatages dans chaque fenêtre.

## Détection de l'absence d'événements ##
**Description** : vérification qu'un flux de données n'a aucune valeur correspondant à un critère donné. Par exemple, 2 voitures consécutives de la même marque sont-elles entrées sur la voie de péage en 90 secondes ?

**Entrée** :

| Assurez-vous | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF-987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI-345 | 2015-01-01T00:00:04.0000000Z |

**Sortie** :

| Assurez-vous | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Solution** :

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explication** : utilisez LAG pour lire l’événement précédent du flux d’entrée et obtenir la valeur de Make. Comparez-la ensuite à la valeur de Make de l’événement actuel, créez une sortie si elles sont identiques, et utilisez LAG pour obtenir des données sur la voiture précédente.

## Détection de la durée d'une condition ##
**Description** : découvrez pendant combien de temps un problème s'est produit. Par exemple, supposons qu'un bogue entraînant un poids incorrect pour toutes les voitures (d’un poids supérieur à 20 000 livres) se produit et que nous voulons calculer la durée du bogue.

**Entrée** :

| Assurez-vous | Time | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Sortie** :

| StartFault | EndFault | FaultDurationSeconds |
| --- | --- | --- |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |

**Solution** :

	SELECT
	    PrevGood.Time AS StartFault,
	    ThisGood.Time AS Endfault,
	    DATEDIFF(second, PrevGood.Time, ThisGood.Time) AS FaultDuraitonSeconds
	FROM
	    Input AS ThisGood TIMESTAMP BY Time
	    INNER JOIN Input AS PrevGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, ThisGood) BETWEEN 1 AND 3600
	    AND PrevGood.Weight < 20000
	    INNER JOIN Input AS Bad TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, Bad) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, Bad, ThisGood) BETWEEN 1 AND 3600
	    AND Bad.Weight >= 20000
	    LEFT JOIN Input AS MidGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, MidGood) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, MidGood, ThisGood) BETWEEN 1 AND 3600
	    AND MidGood.Weight < 20000
	WHERE
	    ThisGood.Weight < 20000
	    AND MidGood.Weight IS NULL

**Explication** : nous recherchons deux événements corrects, avec un événement incorrect et sans événement correct entre eux, ce qui signifie que les deux événements sont les premiers événements avant et après au moins un événement incorrect. L’obtention de deux événements corrects avec un événement incorrect entre eux est simple à l'aide de deux instructions JOIN et c’est en validant que nous obtenons un schéma correct -> incorrect -> correct en vérifiant le poids et en comparant les horodatages.

Grâce à ce que nous avons appris dans « Jonction extérieure LEFT pour inclure NULL ou l’absence d’événements », nous savons comment vérifier qu'aucun événement correct ne s'est produit entre les deux événements corrects récupérés.

En les utilisant ensemble, nous obtenons un schéma correct -> incorrect -> correct sans aucun autre événement correct entre les deux. Nous pouvons maintenant calculer la durée entre les événements corrects de début et de fin, ce qui nous donne la durée du bogue.

## Obtenir de l'aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/fr-FR/home?forum=AzureStreamAnalytics)

## Étapes suivantes

- [Présentation d'Azure Stream Analytics](stream-analytics-introduction.md)
- [Prise en main d'Azure Stream Analytics](../stream.analytics.get.started.md)
- [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
- [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=Oct15_HO2-->