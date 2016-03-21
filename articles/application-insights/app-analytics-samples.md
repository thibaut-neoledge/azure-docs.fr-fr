<properties 
	pageTitle="Exemples de requêtes dans Application Insights Analytics" 
	description="Exemples de requêtes dans Application Insights Analytics, outil de recherche puissant pour Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="awills"/>


# Exemples pour Application Insights Analytics

[Application Insights Analytics](app-analytics.md) est un moteur de recherche puissant pour vos données de télémétrie [Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Application Insights Analytics (AIQL). Pour une bonne prise en main, vous pouvez également consulter une [présentation du langage](app-analytics-tour.md).


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Meilleures pratiques relatives aux requêtes 

Il existe plusieurs choses à faire et à ne pas faire pour accélérer l’exécution de votre requête.

Choses à faire :

-	Utiliser les filtres de temps en premier. Application Insights Analytics est optimisé pour utiliser les filtres de temps.
-	Placer les filtres qui doivent écarter la plupart des données au début de la requête (juste après les filtres de temps).
-	Vérifier que la plupart de vos filtres apparaissent au début de la requête (avant de commencer à utiliser « extend »). 
-	Préférer le mot clé « has » à « contains » quand vous recherchez des jetons complets. « has » est plus performant, car il ne recherche pas de sous-chaînes.
-	Préférer la recherche dans une colonne spécifique, au lieu d’utiliser « * » (recherche en texte intégral dans toutes les colonnes).
-	Dans le cas d’une jointure, ne projeter que les colonnes nécessaires des deux côtés de la jointure (afin de réduire la charge utile d’un ordinateur à l’autre).

Choses à éviter :

-	Essayer de nouvelles requêtes sans « limit [petit nombre] » ou « count » à la fin. L’exécution de requêtes indépendantes sur un ensemble de données inconnu peut engendrer des Go de résultats à retourner au client, avec à la clé une réponse lente et un cluster occupé.
-	Si vous appliquez des conversions (JSON, chaîne, etc.) qui portent sur plus d’un milliard d’enregistrements, remodelez votre requête pour réduire la quantité de données chargées dans la conversion.





<a name="activities"></a>
## Obtenir des sessions à partir d’événements de démarrage et d’arrêt

Imaginons un journal d’événements, dans lequel certains événements marquent le début ou la fin d’une session ou d’une activité étendue.

|Nom|City|SessionId|Timestamp|
|---|---|---|---|
|Démarrer|Londres|2817330|2015-12-09T10:12:02.32|
|Game|Londres|2817330|2015-12-09T10:12:52.45|
|Démarrer|Manchester|4267667|2015-12-09T10:14:02.23|
|Arrêter|Londres|2817330|2015-12-09T10:23:43.18|
|Annuler|Manchester|4267667|2015-12-09T10:27:26.29|
|Arrêter|Manchester|4267667|2015-12-09T10:28:31.72|

Chaque événement ayant un ID de session, le problème est de faire correspondre les événements de début et d’arrêt portant le même identifiant.

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Utilisez [`let`](app-analytics-syntax.md#let-statements) pour nommer une projection de la table qui est réduite au minimum avant de passer à la jointure. [`Project`](app-analytics-queries.md#project-operator) est utilisé pour modifier le nom des horodatages afin que les heures de début et de fin s’affichent dans le résultat. En outre, il sélectionne les autres colonnes que nous souhaitons voir dans le résultat. [`join`](app-analytics-queries.md#join-operator) met en correspondance les entrées de démarrage et d’arrêt pour une même activité, en créant une ligne par activité. Enfin, `project` ajoute de nouveau une colonne pour afficher la durée de l’activité.


|City|SessionId|StartTime|StopTime|Durée|
|---|---|---|---|---|
|Londres|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### Obtenir des sessions, sans ID de session

Supposons maintenant que les événements de démarrage et d’arrêt n’ont pas un ID de session facilitant une mise en correspondance. Toutefois, nous avons une adresse IP du client où la session a eu lieu. En supposant que chaque adresse client effectue une seule session à la fois, nous pouvons établir une correspondance entre chaque événement de démarrage et l’événement d’arrêt suivant à partir de la même adresse IP.

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

La jointure fait correspondre chaque heure de démarrage à toutes les heures d’arrêt à partir de la même adresse IP du client. Ainsi, nous commençons par supprimer les correspondances comportant des heures d’arrêt antérieures.

Ensuite, nous effectuons des regroupements par heure de démarrage et adresse IP pour obtenir un groupe par session. Nous devons fournir une fonction `bin` pour le paramètre StartTime ; sinon, AI Analytics utilise automatiquement des emplacements de 1 heure et fait correspondre certaines heures de début avec des heures d’arrêt incorrectes.

`argmin` sélectionne la ligne avec la durée la plus petite dans chaque groupe, tandis que le paramètre `*` englobe toutes les autres colonnes, dont les noms se voient attribuer le préfixe « min\_ ».


![](./media/app-analytics-samples/040.png)

Ensuite, nous pouvons ajouter du code pour compter les durées dans des emplacements correctement dimensionnés. Comme nous avons une légère préférence pour un graphique à barres, nous divisons par `1s` pour convertir les intervalles de temps en nombres.


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### Exemple concret

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## Sessions simultanées du graphique au fil du temps

Supposons que nous disposons d’une table d’activités avec des heures de début et de fin pour chacune d’elles. Nous souhaitons afficher un graphique temporel qui montre le nombre d’activités en cours d’exécution simultanément à un moment donné.

Voici un exemple d’entrée, que nous appellerons `X` :

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Comme nous voulons un graphique avec emplacements de 1 minute, nous voulons créer quelque chose qui, à chaque intervalle de 1m, peut faire l’objet d’un comptage pour chaque activité en cours d’exécution.

Voici un résultat intermédiaire :

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` génère un tableau de valeurs à des intervalles spécifiés :

|SessionId | StartTime | StopTime | exemples|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Mais au lieu de conserver ces tableaux, nous allons [les développer](app-analytics-queries.md#mvexpand-operator) :

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | exemples|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

Nous pouvons maintenant les regrouper par heure d’échantillon, en comptant les occurrences de chaque activité :

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Nous devons utiliser todatetime(), car `mvexpand` génère une colonne de type dynamique.
* En outre, nous devons recourir à bin() car, pour les valeurs numériques et les dates, summarize applique systématiquement une fonction bin avec un intervalle par défaut si vous n’en indiquez aucun. 


| count\_SessionId | exemples|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

Ce résultat peut être restitué sous forme de graphique à barres ou de graphique temporel.


## Variantes de jointure

La variante exacte de l’opérateur de jointure est spécifiée avec le mot clé kind. À ce jour, AI Analytics prend en charge six variantes d’opérateur de jointure : jointure interne avec déduplication à gauche (type par défaut), jointure interne standard, jointure externe gauche, jointure externe droite, jointure externe complète et jointure anti gauche.
 
Type de jointure par défaut (aucun genre spécifié) Nous allons utiliser deux exemples de tables pour expliquer le fonctionnement de la jointure :
 
Table X


|Clé |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Table Y

|Clé |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 

 
La jointure par défaut effectue une jointure interne après déduplication du côté gauche de la clé de la jointure (la déduplication conserve le premier enregistrement). Soit l’instruction suivante :

    X | join Y on Key 

Le côté gauche de la jointure (table X après déduplication) serait :

|Clé |Value1 
|---|---
|a |1 
|b |2 
|c |4 

Tandis que le résultat de la jointure serait :

|Clé |Value1 |Clé |Value2 
|---|---|---|---
|b |2 |b |10 
|c |4 |c |20 
|c |4 |c |30 

(Notez que les clés « a » et « d » n’apparaissent pas dans la sortie, car elles ne figuraient pas à la fois côté gauche et côté droit).
 
(Historiquement, il s’agit de la première implémentation de la jointure prise en charge par la version initiale d’AI Analytics ; elle est utile dans les scénarios d’analyse de journal/suivi classique pour la mise en corrélation de deux événements (chacun correspondant à un critère de filtrage) sous le même ID de corrélation et la récupération de tous les aspects du phénomène étudié, en ignorant plusieurs aspects des enregistrements de suivi contributeurs).
 
### Jointure interne (kind=inner) 

Il s’agit de la jointure interne standard connue du monde SQL. Un enregistrement de sortie est généré chaque fois qu’un enregistrement sur le côté gauche a la même clé de jointure que l’enregistrement sur le côté droit.
 
    X | join kind=inner Y on Key 

a pour résultat :

|Clé |Value1 |Clé |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 

Notez que (b,10) à droite a été joint deux fois : avec (b,2) et (b,3) côté gauche. De même, (c,4) à gauche a été joint deux fois : avec (c,20) et (c,30) côté droit.

### Jointure externe gauche (kind=leftouter) 

Le résultat d’une jointure externe gauche pour les tables X et Y contient toujours tous les enregistrements de la table de gauche (X), même si la condition de jointure ne trouve pas d’enregistrements correspondants dans la table de droite (Y).
 
Soit :

    X | join kind=leftouter Y on Key 

Résultat :

|Clé |Value1 |Clé |Value2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
 
### Jointure externe droite (kind=rightouter) 

Elle ressemble à la jointure externe gauche, mais le traitement des tables est inversé.
 
Soit :

    X | join kind=rightouter Y on Key 

Résultat :


|Clé |Value1 |Clé |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### Jointure externe complète (kind=fullouter) 

Conceptuellement, une jointure externe complète combine l’application d’une jointure externe gauche et d’une jointure externe droite. Quand des enregistrements dans les tables jointes ne correspondent pas, le jeu de résultats contient des valeurs NULL pour toutes les colonnes de la table qui n’a pas de ligne correspondante. Pour les enregistrements sans correspondance, une seule ligne est générée dans le jeu de résultats (les champs étant renseignés à partir des deux tables).
 
Soit :

    X | join kind=fullouter Y on Key 

Résultat :

|Clé |Value1 |Clé |Value2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### Jointure anti gauche (kind=leftanti) 

Une jointure anti gauche retourne tous les enregistrements du côté gauche qui ne correspondent à aucun enregistrement du côté droit.
 
    X | join kind=leftanti Y on Key 
 
Résultat :

|Clé |Value1 
|---|---
|a |1 
 
L’anti-jointure modélise la requête « NOT IN ».



## Jointure avec mappage de dictionnaire en ligne

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!------HONumber=AcomDC_0309_2016-->