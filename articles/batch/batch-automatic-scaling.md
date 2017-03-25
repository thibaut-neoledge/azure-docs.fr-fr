---
title: "Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch | Microsoft Docs"
description: "Activer la mise à l’échelle automatique sur un pool de cloud pour ajuster dynamiquement le nombre de nœuds de calcul dans le pool."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: multiple
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: 9dbfa813ea64666779f1f85b3ccda2b4fa1a755b
ms.lasthandoff: 03/15/2017


---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Mettre automatiquement à l’échelle les nœuds de calcul dans un pool Azure Batch

Grâce à la mise à l’échelle automatique, le service Azure Batch peut ajouter ou supprimer de manière dynamique des nœuds de calcul dans un pool en fonction des paramètres que vous définissez. Vous pouvez potentiellement gagner du temps et de l’argent en ajustant automatiquement la quantité de puissance de calcul utilisée par votre application : ajoutez des nœuds lorsque les demandes des tâches de votre travail augmentent et supprimez-les lorsqu’elles diminuent.

Vous activez la mise à l’échelle automatique sur un pool de nœuds de calcul en lui associant une *formule de mise à l’échelle automatique* que vous définissez selon le même principe que la méthode [PoolOperations.EnableAutoScale][net_enableautoscale] dans la bibliothèque [Batch .NET](batch-dotnet-get-started.md). Le service Batch utilise ensuite cette formule pour déterminer le nombre de nœuds de calcul nécessaires à l’exécution de vos charges de travail. Le service Batch répond aux échantillons de données de mesure de service collectés à intervalles réguliers, et ajuste le nombre de nœuds de calcul du pool à un intervalle configurable en fonction de la formule associée.

Vous pouvez activer la mise à l’échelle automatique lors de la création d’un pool ou dans un pool existant. Vous pouvez également modifier une formule existante dans un pool dont la mise à l’échelle automatique est activée. Le service Batch vous permet d’évaluer vos formules avant de les assigner à des pools et de surveiller l’état de l’exécution des mises à l’échelle automatiques.

## <a name="automatic-scaling-formulas"></a>Formules de mise à l’échelle automatique
Une formule de mise à l’échelle automatique est une valeur de chaîne qui contient une ou plusieurs instructions, et qui est affectée à un élément de pool [autoScaleFormula][rest_autoscaleformula] (REST Batch) ou à la propriété [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET). Une fois affectées à un pool, le service Batch utilise votre formule pour déterminer le nombre de nœuds de calcul cibles d’un pool pour le prochain intervalle de traitement (les intervalles seront décrits en détail dans la suite de cet article). La chaîne de formule ne peut pas dépasser 8 Ko. Elle peut inclure jusqu’à 100 instructions séparées par des points-virgules, et peut comprendre des sauts de ligne et des commentaires.

Les formules de mise à l’échelle automatique reviennent en quelque sorte à utiliser un « langage » de mise à l’échelle Batch. Les instructions de formules sont des expressions de forme libre qui peuvent inclure des variables définies par le service (variables définies par le service Batch) et des variables définies par l’utilisateur (variables que vous définissez). Elles peuvent effectuer différentes opérations sur ces valeurs à l’aide de types, d’opérateurs et de fonctions intégrés. Par exemple, une instruction peut prendre la forme suivante :

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Les formules contiennent généralement plusieurs instructions qui effectuent des opérations sur les valeurs qui sont obtenues dans les instructions précédentes : Par exemple, nous obtenons tout d’abord une valeur pour `variable1`, puis la transmettons à une fonction pour renseigner `variable2` :

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

En utilisant ces instructions dans votre formule, vous cherchez à atteindre un nombre de nœuds de calcul auquel le pool doit être mis à l’échelle, soit le nombre **cible** de **nœuds dédiés**. Ce nombre peut être supérieur, inférieur ou égal au nombre actuel de nœuds dans le pool. Le service Batch évalue la formule de mise à l’échelle automatique d’un pool à un intervalle spécifique (les[intervalles de mise à l’échelle automatique](#automatic-scaling-interval) sont décrits ci-dessous). Il ajuste ensuite le nombre cible de nœuds dans le pool en fonction du nombre spécifié par votre formule de mise à l’échelle automatique au moment de l’évaluation.

Prenons un exemple rapide : cette formule de mise à l’échelle automatique de deux lignes indique que le nombre de nœuds doit être ajusté en fonction du nombre de tâches actives, en respectant un maximum de 10 nœuds de calcul :

```
$averageActiveTaskCount = avg($ActiveTasks.GetSample(TimeInterval_Minute * 15));
$TargetDedicated = min(10, $averageActiveTaskCount);
```

Les sections suivantes de cet article décrivent les différentes entités qui composeront vos formules de mise à l’échelle automatique, à savoir les variables, les opérateurs, les opérations et les fonctions. Vous allez découvrir comment obtenir différentes métriques de ressource et de tâche de calcul au sein de Batch. Vous pouvez utiliser ces métriques pour ajuster intelligemment votre nombre de nœuds de calcul en fonction de l’utilisation des ressources et de l’état des tâches. Vous apprendrez ensuite à construire une formule et à activer la mise à l’échelle automatique dans un pool à l’aide des API REST et .NET de Batch. Nous terminerons par quelques exemples de formule.

> [!IMPORTANT]
> Chaque compte Azure Batch est limité à un nombre maximal de cœurs (et donc de nœuds de calcul) utilisables pour le traitement. Le service Batch crée des nœuds uniquement jusqu’à cette limite de cœurs. Par conséquent, il peut ne pas atteindre le nombre cible de nœuds de calcul spécifié par une formule. Consultez [Quotas et limites du service Azure Batch](batch-quota-limit.md) pour obtenir des instructions sur l’affichage et l’augmentation des quotas de votre compte.
> 
> 

## <a name="variables"></a>Variables
Vous pouvez utiliser aussi bien des variables **définies par le service** que des variables **définies par l’utilisateur** dans les formules de mise à l’échelle automatique. Les variables définies par le service sont intégrées au service Batch ; certaines sont en lecture-écriture, tandis que d’autres sont en lecture seule. Les variables définies par l’utilisateur sont des variables que *vous* définissez. Dans l’exemple de formule de deux lignes ci-dessus, `$TargetDedicated` est une variable définie par le service et `$averageActiveTaskCount` est une variable définie par l’utilisateur.

Les tableaux ci-dessous montrent des variables en lecture-écriture et en lecture seule définies par le service Batch.

Vous pouvez **obtenir** et **définir** les valeurs de ces variables définies par le service pour gérer le nombre de nœuds de calcul dans un pool :

| Variables définies par le service en lecture-écriture | Description |
| --- | --- |
| $TargetDedicated |Nombre **cible** de **nœuds de calcul dédiés** pour le pool. Il s’agit du nombre de nœuds de calcul auquel le pool doit être mis à l’échelle. Il s’agit d’un nombre « cible » dans la mesure où il est possible qu’un pool ne puisse pas atteindre le nombre cible de nœuds. Cela peut se produire si le nombre cible de nœuds est modifié de nouveau par une évaluation de la mise à l’échelle automatique suivante avant que le pool n’ait atteint la cible initiale. Cela peut également se produire si un nœud de compte ou un quota principal Batch est atteint avant que le nombre cible de nœuds ne le soit. |
| $NodeDeallocationOption |Action exécutée lorsque des nœuds de calcul sont supprimés d’un pool. Les valeurs possibles sont les suivantes :<ul><li>**requeue** : arrête immédiatement les tâches et les replace dans la file d’attente des travaux pour qu’elles soient replanifiées.<li>**terminate** : arrête immédiatement les tâches et les supprime de la file d’attente des travaux.<li>**taskcompletion** : attend la fin des tâches en cours d’exécution, puis supprime le nœud du pool.<li>**retaineddata** : attend que toutes les données de tâche locales conservées sur le nœud aient été nettoyées avant de supprimer le nœud du pool.</ul> |

Vous pouvez **obtenir** la valeur des variables définies par le service ci-après pour effectuer des ajustements basés sur les métriques à partir du service Batch :

| Variables définies par le service en lecture seule | Description |
| --- | --- |
| $CPUPercent |Pourcentage moyen d’utilisation du processeur. |
| $WallClockSeconds |Nombre de secondes consommées. |
| $MemoryBytes |Nombre moyen de mégaoctets utilisés. |
| $DiskBytes |Nombre moyen de gigaoctets utilisés sur les disques locaux. |
| $DiskReadBytes |Nombre d’octets lus. |
| $DiskWriteBytes |Nombre d’octets écrits. |
| $DiskReadOps |Nombre d’opérations de lecture sur disque effectuées. |
| $DiskWriteOps |Nombre d’opérations d’écriture sur disque effectuées. |
| $NetworkInBytes |Nombre d’octets entrants. |
| $NetworkOutBytes |Nombre d’octets sortants. |
| $SampleNodeCount |Nombre de nœuds de calcul. |
| $ActiveTasks |Nombre de tâches à l’état actif. |
| $RunningTasks |Nombre de tâches en cours d’exécution. |
| $PendingTasks |Somme de $ActiveTasks et de $RunningTasks. |
| $SucceededTasks |Nombre de tâches ayant abouti. |
| $FailedTasks |Nombre de tâches ayant échoué. |
| $CurrentDedicated |Nombre actuel de nœuds de calcul dédiés. |

> [!TIP]
> Les variables en lecture seule définies par le service qui sont illustrées ci-dessus sont des *objets* qui fournissent diverses méthodes pour accéder aux données qui leur sont associées. Consultez la section [Obtenir des échantillons de données](#getsampledata) ci-dessous pour plus d’informations.
> 
> 

## <a name="types"></a>Types
Ces **types** sont pris en charge dans une formule.

* double
* doubleVec
* doubleVecList
* string
* timestamp : structure composée qui inclut les éléments suivants :
  
  * year
  * mois (1-12)
  * jour (1-31)
  * jour de la semaine (sous forme de chiffre, par exemple, 1 pour lundi).
  * hour (au format 24 heures, par exemple, 13 signifie 1 PM).
  * minute (00-59)
  * seconde (00-59)
* timeinterval
  
  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Opérations
Les **opérations** autorisées sur les types répertoriés ci-dessus sont les suivantes.

| Opération | Opérateurs pris en charge | Type de résultat |
| --- | --- | --- |
| double *opérateur* double |+, -, *, / |double |
| double *opérateur* timeinterval |* |timeinterval |
| doubleVec *opérateur* double |+, -, *, / |doubleVec |
| doubleVec *opérateur* doubleVec |+, -, *, / |doubleVec |
| timeinterval *opérateur* double |*, / |timeinterval |
| timeinterval *opérateur* timeinterval |+, - |timeinterval |
| timeinterval *opérateur* timestamp |+ |timestamp |
| timestamp *opérateur* timeinterval |+ |timestamp |
| timestamp *opérateur* timestamp |- |timeinterval |
| *opérateur*double |-, ! |double |
| *opérateur*timeinterval |- |timeinterval |
| double *opérateur* double |<, <=, ==, >=, >, != |double |
| string *opérateur* string |<, <=, ==, >=, >, != |double |
| timestamp *opérateur* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *opérateur* timeinterval |<, <=, ==, >=, >, != |double |
| double *opérateur* double |&&, &#124;&#124; |double |

Quand vous testez un double avec un opérateur ternaire (`double ? statement1 : statement2`), la valeur différente de zéro est **true** et zéro est **false**.

## <a name="functions"></a>Fonctions
Les **fonctions** prédéfinies disponibles pour la définition d’une formule de mise à l’échelle automatique sont les suivantes.

| Fonction | Type de retour | Description |
| --- | --- | --- |
| avg(doubleVecList) |double |Retourne la valeur moyenne de toutes les valeurs de l’élément doubleVecList. |
| len(doubleVecList) |double |Retourne la longueur du vecteur créé à partir de l’élément doubleVecList. |
| lg(double) |double |Retourne la base logarithmique 2 de l’élément double. |
| lg(doubleVecList) |doubleVec |Retourne la base logarithmique 2 au niveau composant de l’élément doubleVecList. Un élément vec(double) doit être explicitement transmis pour le paramètre. Dans le cas contraire, la version double lg(double) est prise en compte. |
| ln(double) |double |Retourne le logarithme naturel de l’élément double. |
| ln(doubleVecList) |doubleVec |Retourne la base logarithmique 2 au niveau composant de l’élément doubleVecList. Un élément vec(double) doit être explicitement transmis pour le paramètre. Dans le cas contraire, la version double lg(double) est prise en compte. |
| log(double) |double |Retourne la base logarithmique 10 de l’élément double. |
| log(doubleVecList) |doubleVec |Retourne la base logarithmique 10 au niveau composant de l’élément doubleVecList. Un élément vec(double) doit être explicitement transmis pour le paramètre de type double unique. Dans le cas contraire, la version double log(double) est prise en compte. |
| max(doubleVecList) |double |Retourne la valeur maximale de l’élément doubleVecList. |
| min(doubleVecList) |double |Retourne la valeur minimale de l’élément doubleVecList. |
| norm(doubleVecList) |double |Retourne la double norme du vecteur créé à partir de l’élément doubleVecList. |
| percentile(doubleVec v, double p) |double |Retourne l’élément de percentile du vecteur v. |
| rand() |double |Retourne une valeur aléatoire comprise entre 0,0 et 1,0. |
| range(doubleVecList) |double |Retourne la différence entre les valeurs minimale et maximale de l’élément doubleVecList. |
| std(doubleVecList) |double |Retourne l’écart type de l’échantillon des valeurs dans l’élément doubleVecList. |
| stop() | |Arrête l’évaluation de l’expression de mise à l’échelle automatique. |
| sum(doubleVecList) |double |Retourne la somme de tous les composants de l’élément doubleVecList. |
| time(string dateTime="") |timestamp |Retourne l’horodatage de l’heure actuelle si aucun paramètre n’est transmis, ou l’horodatage de la chaîne dateTime dans le cas contraire. Les formats dateTime pris en charge sont W3C-DTF et RFC 1123. |
| val(doubleVec v, double i) |double |Retourne la valeur de l’élément qui est à l’emplacement i du vecteur v avec un index de départ de zéro. |

Certaines des fonctions décrites dans le tableau ci-dessus peuvent accepter une liste en tant qu’argument. La liste séparée par des virgules se compose de n’importe quelle combinaison d’éléments *double* et *doubleVec*. Par exemple :

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

La valeur *doubleVecList* est convertie en un seul paramètre *doubleVec* avant l’évaluation. Par exemple, si `v = [1,2,3]`, alors appeler `avg(v)` revient à appeler `avg(1,2,3)`. Appeler `avg(v, 7)` équivaut à appeler `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Obtenir des échantillons de données
Les formules de mise à l’échelle automatique agissent sur les données métriques (échantillons) qui sont fournies par le service Batch. Une formule augmente ou réduit la taille du pool en fonction des valeurs obtenues à partir du service. Les variables définies par le service qui sont illustrées ci-dessus sont des objets qui fournissent diverses méthodes pour accéder aux données associées à chaque objet. Par exemple, l’expression ci-après présente une requête visant à obtenir les cinq dernières minutes de l’utilisation du processeur :

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Méthode | Description |
| --- | --- |
| GetSample() |La méthode `GetSample()` retourne un vecteur d’échantillons de données.<br/><br/>Un échantillon correspond à 30 secondes de données de métrique. En d’autres termes, des échantillons sont obtenus toutes les 30 secondes, mais comme noté ci-dessus, il existe un délai entre le moment où un échantillon est collecté et le moment où il est disponible pour une formule. Par conséquent, tous les échantillons pour une période donnée ne sont pas forcément disponibles pour évaluation par une formule.<ul><li>`doubleVec GetSample(double count)`<br/>Spécifie le nombre d’échantillons à obtenir à partir des échantillons collectés les plus récents.<br/><br/>`GetSample(1)` retourne le dernier échantillon disponible. Toutefois, pour les métriques comme `$CPUPercent`, cette fonction ne doit pas être utilisée, car il est impossible de savoir *quand* l’échantillon a été collecté. Il peut s’agir d’un événement récent ou plus ancien en raison de problèmes système. Dans ce cas de figure, il est préférable d’utiliser un intervalle de temps, comme indiqué ci-dessous.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Spécifie le délai d’exécution de la collecte des exemples de données. Elle spécifie éventuellement le pourcentage d’échantillons qui doivent être disponibles dans le délai d’exécution demandé.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` doit retourner 20 échantillons si tous les échantillons des dix dernières minutes sont présents dans l’historique CPUPercent. Cependant, si la dernière minute de l’historique n’est pas disponible, seuls 18 échantillons seraient renvoyés, Dans ce cas :<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` échouerait, car seuls 90 % des échantillons sont disponibles ;<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` aboutirait.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Spécifie le délai d’exécution de la collecte des données avec une heure de début et une heure de fin.<br/><br/>Comme indiqué ci-dessus, il existe un délai entre le moment où un échantillon est collecté et le moment où il est disponible pour une formule. Cet aspect doit être pris en compte lorsque vous utilisez la méthode `GetSample`. Consultez `GetSamplePercent` ci-dessous. |
| GetSamplePeriod() |Retourne la période des échantillons considérés dans un jeu de données d’échantillon historiques. |
| Count() |Renvoie le nombre total d’échantillons dans l’historique des métriques. |
| HistoryBeginTime() |Retourne l’horodateur du plus ancien échantillon de données disponible pour la métrique. |
| GetSamplePercent() |Retourne le pourcentage d’échantillons disponibles pour un intervalle de temps donné. Par exemple :<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Comme la méthode `GetSample` échoue si le pourcentage d’échantillons retourné est inférieur au `samplePercent` spécifié, vous pouvez utiliser la méthode `GetSamplePercent` pour procéder d’abord à une vérification. Vous pouvez ensuite effectuer une autre action si des échantillons insuffisants sont présents, sans arrêter l’évaluation de la mise à l’échelle automatique. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Échantillons, pourcentage d’échantillonnage et méthode *GetSample()*
La principale opération d’une formule de mise à l’échelle automatique vise à obtenir des données métriques des tâches et des ressources, puis à ajuster la taille du pool en fonction de ces données. Par conséquent, il est important de comprendre clairement comment les formules de mise à l’échelle automatique interagissent avec les données de mesure, également appelées « échantillons ».

**Exemples**

Le service Batch prélève régulièrement des *échantillons* de métriques de tâches et de ressources pour les mettre à la disposition de vos formules de mise à l’échelle automatique. Ces échantillons sont enregistrés toutes les 30 secondes par le service Batch. Cependant, un temps de latence entraîne généralement un retard entre l’enregistrement de ces échantillons et leur mise à disposition (en lecture) pour vos formules de mise à l’échelle automatique. En outre, en raison de différents facteurs tels que les problèmes de réseau ou d’autres problèmes d’infrastructure, il arrive que des échantillons ne soient pas enregistrés pendant un intervalle donné. C’est ce que l’on appelle les échantillons « manquants ».

**Pourcentage d’échantillonnage**

Quand `samplePercent` est transmis à la méthode `GetSample()` ou que la méthode `GetSamplePercent()` est appelée, le terme « pourcentage » désigne une comparaison entre le nombre total *possible* d’échantillons enregistrés par le service Batch et le nombre d’échantillons effectivement *disponibles* pour la formule de mise à l’échelle automatique.

Prenons l’exemple d’un intervalle de 10 minutes. Comme les échantillons sont enregistrés toutes les 30 secondes, au cours d’un intervalle de 10 minutes, le nombre total maximal d’échantillons enregistrés par Batch devrait être de 20 échantillons (soit 2 par minute). Toutefois, en raison de la latence inhérente au mécanisme de création de rapports, ou d’un quelque autre problème au sein de l’infrastructure Azure, il se peut que 15 échantillons seulement soient disponibles en lecture pour votre formule de mise à l’échelle automatique. Cela signifie que, pour cette période de 10 minutes, seuls **75 pour cent** du nombre total d’échantillons enregistrés sont réellement disponibles pour votre formule.

**Méthode GetSample() et plages d’échantillons**

Vos formules de mise à l’échelle automatique vont agrandir et réduire vos pools : ajout ou suppression de nœuds. Comme les nœuds vous coûtent de l’argent, vous souhaitez vous assurer que vos formules utiliseront une méthode d’analyse intelligente basée sur des données suffisantes. Par conséquent, nous vous recommandons d’utiliser une analyse des types de tendance dans vos formules. Ce type augmentera ou réduira vos pools en fonction d’une *plage* d’échantillons collectés.

Pour ce faire, utilisez `GetSample(interval look-back start, interval look-back end)` pour retourner un **vecteur** d’échantillons :

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Lorsque le service Batch évalue la ligne ci-dessus, il retourne une plage d’échantillons sous la forme d’un vecteur de valeurs : Par exemple :

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Une fois que vous avez collecté le vecteur d’échantillons, vous pouvez utiliser des fonctions telles que `min()`, `max()` et `avg()` pour dériver des valeurs significatives à partir de la plage collectée.

Pour plus de sécurité, vous pouvez forcer *l’échec* d’une évaluation de formule si le pourcentage d’échantillons disponible pendant une période donnée est inférieur à un certain seuil. Lorsque vous forcez l’échec d’une évaluation de formule, Batch reçoit l’instruction de cesser toute nouvelle évaluation de la formule si le pourcentage d’échantillons spécifié n’est pas disponible, auquel cas la taille du pool ne sera pas modifiée. Pour spécifier un pourcentage d’échantillons à respecter pour que l’évaluation aboutisse, spécifiez ce pourcentage en tant que troisième paramètre de `GetSample()`. Dans l’exemple ci-dessous, une exigence de 75 pour cent d’échantillons est spécifiée :

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Il est également important, en raison du délai de disponibilité des échantillons dont nous avons parlé ci-dessus, de toujours spécifier une plage horaire avec une heure de début différée antérieure à une minute. En effet, il faut environ une minute aux échantillons pour se propager dans le système, ce qui signifie que les échantillons situés dans la plage `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` ne seront généralement pas disponibles. Là encore, vous pouvez utiliser le paramètre pourcentage de `GetSample()` pour forcer une exigence de pourcentage d’échantillon particulière.

> [!IMPORTANT]
> Nous vous **déconseillons fortement** **de vous appuyer*uniquement* sur `GetSample(1)` dans vos formules de mise à l’échelle automatique**, car la méthode `GetSample(1)` dit globalement au service Batch : « Donne-moi le dernier échantillon disponible, quelle que soit son ancienneté ». Dans la mesure où il s’agit uniquement d’un simple échantillon (potentiellement ancien), il risque de ne pas être représentatif de l’état récent de la tâche ou de la ressource. Si vous utilisez tout de même `GetSample(1)`, veillez à l’intégrer dans une instruction plus générale pour éviter de l’utiliser comme unique point de données sur lequel reposera votre formule.
> 
> 

## <a name="metrics"></a>Mesures
Vous pouvez utiliser à la fois les métriques de **ressource** et de **tâche** quand vous définissez une formule. Vous ajustez le nombre cible de nœuds dédiés dans le pool en fonction des données métriques que vous obtenez et évaluez. Consultez la section [Variables](#variables) ci-dessus pour plus d’informations sur chaque métrique.

<table>
  <tr>
    <th>Mesure</th>
    <th>Description</th>
  </tr>
  <tr>
    <td><b>Ressource</b></td>
    <td><p><b>Les métriques de ressource</b> sont basées sur l’utilisation du processeur, de la bande passante et de la mémoire par les nœuds de calcul, ainsi que sur le nombre de nœuds.</p>
        <p> Ces variables définies par le service sont utiles pour effectuer des ajustements en fonction du nombre de nœuds :</p>
    <p><ul>
      <li>$TargetDedicated</li>
            <li>$CurrentDedicated</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Ces variables définies par le service sont utilisées pour effectuer des ajustements en fonction de l’utilisation des ressources du nœud :</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Tâche</b></td>
    <td><p><b>Les métriques de tâche</b> sont basées sur l’état des tâches (Active, En attente et Terminée). Les variables suivantes définies par le service sont utiles pour ajuster la taille du pool en fonction des métriques de tâche :</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Écrire une formule de mise à l’échelle automatique
Vous générez une formule de mise à l’échelle automatique en formant des instructions qui utilisent les composants ci-dessus, puis vous combinez ces instructions dans une formule complète. Dans cette section, nous allons créer un exemple de formule capable de prendre des décisions concrètes en matière de mise à l’échelle automatique.

Commençons par définir les exigences de notre nouvelle formule de mise à l’échelle automatique. La formule doit :

1. **Augmenter** le nombre cible de nœuds de calcul dans un pool si l’utilisation du processeur est intensive.
2. **Réduire** le nombre cible de nœuds de calcul dans un pool si l’utilisation du processeur est faible.
3. Toujours limiter le nombre **maximal** de nœuds à 400.

Pour *augmenter* le nombre de nœuds en cas d’utilisation intensive du processeur, nous définissons l’instruction qui renseigne une variable définie par l’utilisateur (`$totalNodes`) en utilisant une valeur équivalente à 110 pour cent du nombre cible actuel de nœuds si l’utilisation moyenne du processeur minimale au cours des 10 dernières minutes est supérieure à 70 pour cent. Sinon, nous utilisons la valeur actuelle dédiée.

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
```

Pour *réduire* le nombre de nœuds en cas de faible utilisation du processeur, la prochaine instruction dans notre formule affecte à la même variable `$totalNodes` 90 pour cent du nombre cible actuel de nœuds si l’utilisation moyenne du processeur au cours des 60 dernières minutes est inférieure à 20 pour cent. Sinon, nous utilisons la valeur actuelle de `$totalNodes` que nous avons renseigné dans l’instruction ci-dessus.

```
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
```

Cet exemple définit le nombre cible de nœuds de calcul dédiés sur un **maximum** de 400 :

```
$TargetDedicated = min(400, $totalNodes)
```

Voici la formule complète :

```
$totalNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicated * 1.1) : $CurrentDedicated;
$totalNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicated * 0.9) : $totalNodes;
$TargetDedicated = min(400, $totalNodes)
```

## <a name="create-an-autoscale-enabled-pool"></a>Créer un pool avec mise à l’échelle automatique
Pour créer un pool avec mise à l’échelle automatique, vous pouvez utiliser l’une des techniques suivantes :

**Batch .NET**

1. Créez le pool avec [BatchClient.PoolOperations.CreatePool](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx).
2. Affectez à la propriété [CloudPool.AutoScaleEnabled](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleenabled.aspx) la valeur `true`.
3. Affectez à la propriété [CloudPool.AutoScaleFormula](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx) votre formule de mise à l’échelle automatique.
4. (Facultatif) Définissez la propriété [CloudPool.AutoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoScaleevaluationinterval.aspx) (la valeur par défaut est de 15 minutes).
5. Validez le pool avec [CloudPool.Commit](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commit.aspx) ou [CommitAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.commitasync.aspx).

**API REST Batch**

* [Ajouter un pool à un compte](https://msdn.microsoft.com/library/azure/dn820174.aspx) : spécifiez les éléments `enableAutoScale` et `autoScaleFormula` dans votre requête API REST pour configurer la mise à l’échelle automatique d’un pool au moment de sa création.

L’extrait de code suivant crée un pool avec mise à l’échelle automatique à l’aide de la bibliothèque [Batch .NET][net_api]. La formule de mise à l’échelle automatique du pool définit le nombre cible de nœuds comme suit : 5 les lundis et 1 les autres jours de la semaine. L’[intervalle de mise à l’échelle automatique](#automatic-scaling-interval) est de 30 minutes. Dans cet extrait de code et les autres extraits de cet article, « myBatchClient » est une instance entièrement initialisée de [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool("mypool", "3", "small");
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
pool.Commit();
```

Outre l’API REST Batch et le SDK .NET, vous pouvez utiliser d’autres [SDK Batch](batch-apis-tools.md#batch-development-apis), des [applets de commande Batch PowerShell](batch-powershell-cmdlets-get-started.md) et l’[interface CLI Batch](batch-cli-get-started.md) avec la mise à l’échelle automatique.

> [!IMPORTANT]
> Quand vous créez un pool avec mise à l’échelle automatique, le paramètre `targetDedicated` **ne doit pas** être spécifié. De même, si vous souhaitez redimensionner manuellement un pool compatible avec une mise à l’échelle automatique (par exemple, avec [BatchClient.PoolOperations.ResizePool][net_poolops_resizepool]), vous devez dans un premier temps **désactiver** la mise à l’échelle automatique dans le pool avant de le redimensionner.
> 
> 

### <a name="automatic-scaling-interval"></a>Intervalle de mise à l’échelle automatique
Par défaut, le service Batch ajuste la taille d’un pool en fonction de sa formule de mise à l’échelle toutes les **15 minutes**. Cet intervalle peut toutefois être configuré à l’aide des propriétés de pool suivantes :

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (API REST)

L’intervalle doit être compris entre cinq minutes et 168 heures. Si un intervalle en dehors de cette plage est spécifié, le service Batch renvoie une erreur de demande incorrecte (400).

> [!NOTE]
> La mise à l’échelle automatique ne peut pas actuellement répondre aux modifications en moins d’une minute, mais vise plutôt à ajuster progressivement la taille de votre pool pendant l’exécution d’une charge de travail.
> 
> 

## <a name="enable-autoscaling-on-an-existing-pool"></a>Activer la mise à l’échelle automatique sur un pool existant
Si vous avez déjà créé un pool avec un nombre défini de nœuds de calcul à l’aide du paramètre *targetDedicated*, vous pouvez encore activer la mise à l’échelle automatique sur le pool. Chaque SDK Batch propose une opération d’activation de mise à l’échelle automatique, par exemple :

* [BatchClient.PoolOperations.EnableAutoScale][net_enableautoscale] (Batch .NET)
* [Activer la mise à l’échelle automatique sur un pool][rest_enableautoscale] (API REST)

Quand vous activez la mise à l’échelle automatique sur un pool existant, les règles suivantes s’appliquent :

* Si la mise à l’échelle automatique est actuellement **désactivée** sur le pool au moment de l’émission de la requête d’activation, vous *devez* spécifier une formule de mise à l’échelle automatique valide quand vous émettez la requête. Vous pouvez *éventuellement* spécifier un intervalle d’évaluation de mise à l’échelle automatique. Si aucun intervalle n’est spécifié, la valeur par défaut de 15 minutes est utilisée.
* Si la mise à l’échelle automatique est actuellement **activée** sur le pool, vous pouvez spécifier une formule de mise à l’échelle automatique, un intervalle d’évaluation ou les deux. Vous ne pouvez pas omettre les deux propriétés.
  
  * Si vous spécifiez un nouvel intervalle d’évaluation de mise à l’échelle automatique, la planification d’évaluation existante est arrêtée, puis une nouvelle planification est démarrée. L’heure de début de la nouvelle planification est l’heure à laquelle la requête d’activation de mise à l’échelle automatique a été émise.
  * Si vous omettez la formule de mise à l’échelle automatique ou l’intervalle d’évaluation, le service Batch continue d’utiliser la valeur actuelle de ce paramètre.

> [!NOTE]
> La valeur spécifiée pour le paramètre *targetDedicated* au moment de la création du pool est ignorée lorsque la formule de mise à l’échelle automatique est évaluée.
> 
> 

Cet extrait de code en C# utilise la bibliothèque [Batch .NET][net_api] pour activer la mise à l’échelle automatique sur un pool existant :

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicated = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Mettre à jour une formule de mise à l’échelle automatique
La même requête d’activation de mise à l’échelle automatique est utilisée pour *mettre à jour* la formule sur un pool existant avec mise à l’échelle automatique (par exemple, avec [EnableAutoScale][net_enableautoscale] dans Batch .NET). Il n’y a aucune opération spéciale de « mise à jour de la mise à l’échelle automatique ». Par exemple, si la mise à l’échelle automatique est déjà activée sur « myexistingpool » quand le code suivant est exécuté, la formule de mise à l’échelle automatique est remplacée par le contenu de `myNewFormula`.

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Mettre à jour l’intervalle de mise à l’échelle automatique
Comme pour la mise à jour d’une formule, vous utilisez la même méthode [EnableAutoScale][net_enableautoscale] pour modifier l’intervalle d’évaluation d’un pool existant avec mise à l’échelle automatique. Par exemple, pour définir un intervalle d’évaluation de 60 minutes pour un pool avec mise à l’échelle automatique :

```csharp
myBatchClient.PoolOperations.EnableAutoScale(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Évaluer une formule de mise à l’échelle automatique
Vous pouvez évaluer une formule avant de l’appliquer à un pool. De cette façon, vous pouvez effectuer une « série de tests » sur la formule pour évaluer ses instructions avant de l’utiliser en production.

Pour évaluer une formule de mise à l’échelle automatique, vous devez d’abord **activer la mise à l’échelle automatique** sur le pool à l’aide d’une **formule valide**. Pour tester une formule sur un pool sur lequel la mise à l’échelle automatique n’est pas encore activée, vous pouvez utiliser la formule à une ligne `$TargetDedicated = 0` lors de l’activation initiale de la mise à l’échelle automatique. Ensuite, utilisez l’une des méthodes suivantes pour évaluer la formule à tester :

* [BatchClient.PoolOperations.EvaluateAutoScale](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscale.aspx) ou [EvaluateAutoScaleAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.evaluateautoscaleasync.aspx)
  
    Ces méthodes Batch .NET nécessitent l’ID d’un pool existant et une chaîne contenant la formule de mise à l’échelle automatique à évaluer. Les résultats de l’évaluation sont contenus dans l’instance [AutoScaleEvaluation](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscaleevaluation.aspx) retournée.
* [Évaluer une formule de mise à l’échelle automatique](https://msdn.microsoft.com/library/azure/dn820183.aspx)
  
    Dans cette requête API REST, spécifiez l’ID du pool dans l’URI et la formule de mise à l’échelle automatique dans l’élément *autoScaleFormula* du corps de la requête. La réponse de l’opération contient les éventuelles informations d’erreur associées à la formule.

Dans cet extrait de code [Batch .NET][net_api], nous évaluons une formule avant de l’appliquer à [CloudPool][net_cloudpool]. Si la mise à l’échelle automatique n’est pas activée sur le pool, la première chose que nous faisons est de l’activer.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = batchClient.PoolOperations.GetPool("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    pool.EnableAutoScale(
        autoscaleFormula: $"$TargetDedicated = {pool.CurrentDedicated};",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScale calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    pool.Refresh();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicated = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        batchClient.PoolOperations.EvaluateAutoScale(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        batchClient.PoolOperations.EnableAutoScale(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

L’évaluation réussie de la formule dans cet extrait de code entraîne une sortie similaire à ce qui suit :

```
AutoScaleRun.Results:
    $TargetDedicated=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Obtenir des informations sur les exécutions de mise à l’échelle automatique
Pour vérifier que votre formule fonctionne comme prévu, nous vous recommandons d’examiner régulièrement les résultats des exécutions de mise à l’échelle automatique effectuées par Batch sur votre pool. Pour ce faire, obtenez (ou actualisez) une référence au pool et examinez les propriétés de la dernière exécution de mise à l’échelle automatique.

Dans Batch .NET, la propriété [CloudPool.AutoScaleRun](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscalerun.aspx) comprend plusieurs propriétés qui fournissent des informations sur la dernière exécution de mise à l’échelle automatique effectuée sur le pool par le service Batch.

* [AutoScaleRun.Timestamp](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.timestamp.aspx)
* [AutoScaleRun.Results](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.results.aspx)
* [AutoScaleRun.Error](https://msdn.microsoft.com/library/azure/microsoft.azure.batch.autoscalerun.error.aspx)

Dans l’API REST, la requête [Obtenir des informations sur un pool](https://msdn.microsoft.com/library/dn820165.aspx) retourne des informations sur le pool, notamment des détails sur la dernière exécution de mise à l’échelle automatique dans [autoScaleRun](https://msdn.microsoft.com/library/dn820165.aspx#bk_autrun).

L’extrait de code en C# suivant utilise la bibliothèque Batch .NET pour imprimer des informations sur la dernière exécution de mise à l’échelle automatique sur le pool « myPool » :

```csharp
Cloud pool = myBatchClient.PoolOperations.GetPool("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Exemple de sortie de l’extrait de code précédent :

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicated=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Exemples de formules de mise à l’échelle automatique
Passons en revue quelques formules illustrant les différentes façons d’ajuster la quantité de ressources de calcul dans un pool.

### <a name="example-1-time-based-adjustment"></a>Exemple 1 : ajustement en fonction du temps
Peut-être souhaitez-vous ajuster la taille du pool en fonction du jour de la semaine et de l’heure de la journée, pour augmenter ou diminuer le nombre de nœuds dans le pool en conséquence.

Cette formule obtient dans un premier temps l’heure actuelle. S’il s’agit d’un jour de la semaine (1 à 5) et des heures de travail (8 heures à 18 heures), la taille du pool cible est définie sur 20 nœuds. Sinon, elle est définie sur 10 nœuds.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicated = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Exemple 2 : ajustement en fonction de la tâche
Dans cet exemple, la taille du pool est ajustée en fonction du nombre de tâches présentes dans la file d’attente. Notez que les commentaires et les sauts de ligne sont acceptés dans les chaînes de formule.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicated/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicated = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Exemple 3 : comptabilisation des tâches parallèles
Il s’agit d’un autre exemple qui ajuste la taille du pool en fonction du nombre de tâches. Cette formule prend également en compte la valeur [MaxTasksPerComputeNode][net_maxtasks] qui a été définie pour le pool. Cela est particulièrement utile dans les situations où [l’exécution parallèle de tâches](batch-parallel-node-tasks.md) a été activée sur votre pool.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicated * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicated + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicated = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Exemple 4 : définition d’une taille de pool initiale
Cet exemple montre un extrait de code en C# avec une formule de mise à l’échelle automatique qui définit la taille du pool sur un certain nombre de nœuds pour une période initiale. La taille du pool est ensuite ajustée en fonction du nombre de tâches en cours d’exécution et actives une fois la période initiale écoulée.

Dans l’extrait de code suivant, la formule :

* Définit la taille initiale du pool sur quatre nœuds.
* N’ajuste pas la taille du pool dans les 10 premières minutes de cycle de vie du pool.
* Après 10 minutes, obtient la valeur maximale du nombre de tâches en cours d’exécution et actives au cours des 60 dernières minutes.
  * Si les deux valeurs sont égales à 0 (ce qui indique qu’aucune tâche n’était en cours d’exécution ni active au cours des 60 dernières minutes), la taille du pool est définie sur 0.
  * Si l’une des valeurs est supérieure à zéro, aucune modification n’est apportée.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicated = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicated = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicated) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Étapes suivantes
* [Optimiser l’utilisation des ressources de calcul Azure Batch avec des tâches de nœud simultanées](batch-parallel-node-tasks.md) contient des informations sur la façon dont vous pouvez effectuer plusieurs tâches simultanément sur les nœuds de calcul de votre pool. En plus de la mise à l’échelle automatique, cette fonctionnalité peut aider à réduire la durée du travail pour certaines charges de travail et vous permettre d’économiser de l’argent.
* Afin d’améliorer encore l’efficacité, assurez-vous que votre application Batch interroge le service Batch de la manière la plus optimale qui soit. Dans [Interroger efficacement le service Azure Batch](batch-efficient-list-queries.md), vous allez apprendre à limiter la quantité de données qui transitent par le réseau lorsque vous interrogez l’état des milliers de nœuds de calcul ou de tâches potentiels.

[net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_batchclient]: http://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_autoscaleformula]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleformula.aspx
[net_cloudpool_autoscaleevalinterval]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval.aspx
[net_enableautoscale]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.enableautoscale.aspx
[net_maxtasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.maxtaskspercomputenode.aspx
[net_poolops_resizepool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.resizepool.aspx

[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_autoscaleformula]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_autoscaleinterval]: https://msdn.microsoft.com/library/azure/dn820173.aspx
[rest_enableautoscale]: https://msdn.microsoft.com/library/azure/dn820173.aspx

