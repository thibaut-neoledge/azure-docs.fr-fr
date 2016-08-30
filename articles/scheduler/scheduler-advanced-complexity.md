<properties
 pageTitle="Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler"
 description="Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="06/30/2016"
 ms.author="krisragh"/>

# Comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler  

## Vue d'ensemble

La *planification* constitue le cœur d'un travail Azure Scheduler. La planification détermine quand et comment Azure Scheduler exécute le travail.

Azure Scheduler vous permet de spécifier des planifications à usage unique et récurrentes pour un travail. Les planifications *à usage unique* se déclenchent une seule fois à un moment précis. Il s'agit en fait de planifications *récurrentes* qui ne s'exécutent qu'une seule fois. Les planifications récurrentes se déclenchent selon une fréquence prédéfinie.

Grâce à cette flexibilité, Azure Scheduler vous permet de prendre en charge un large éventail de scénarios professionnels :

-	Nettoyage périodique des données : par exemple, tous les jours, supprimer tous les tweets de plus de 3 mois.
-	Archivage : par exemple, tous les mois, envoyer l'historique de facturation au service de sauvegarde.
-	Demandes de données externes : par exemple, toutes les 15 minutes, extraire un nouveau bulletin météorologique NOAA pour le ski.
-	Traitement des images : par exemple, tous les jours ouvrables, pendant les heures creuses, utiliser le cloud computing pour compresser les images téléchargées ce même jour.


Dans cet article, nous traitons d'exemples de travaux que vous pouvez créer avec Azure Scheduler. Nous fournissons les données JSON qui décrivent chaque planification. Si vous utilisez l'[API REST Scheduler](https://msdn.microsoft.com/library/mt629143.aspx), vous pouvez utiliser les mêmes données JSON pour [créer un travail Azure Scheduler](https://msdn.microsoft.com/library/mt629145.aspx).

## Scénarios pris en charge

Les nombreux exemples de cette rubrique illustrent l’éventail de scénarios pris en charge par Azure Scheduler. Globalement, ces exemples illustrent comment créer des planifications pour de nombreux modèles de comportement, y compris ceux ci-dessous :

-	Exécuter une seule fois à une date et une heure spécifiques
-	Exécuter et répéter un nombre de fois explicites
-	Exécuter immédiatement et répéter
-	Exécuter et répéter tou(te)s les *n* minutes, heures, jours, semaines ou mois, en commençant à un moment spécifique
-	Exécuter et répéter selon une fréquence hebdomadaire ou mensuelle, mais uniquement des jours spécifiques, des jours spécifiques de la semaine ou des jours spécifiques du mois
-	Exécuter et répéter à plusieurs reprises dans une période : par exemple, le dernier vendredi et lundi de chaque mois, ou à 5h15 et 17h15 chaque jour

## Dates et dates/heures

Les dates des travaux Azure Scheduler respectent la [spécification ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) et incluent uniquement la date.

Les références date-heure des travaux Azure Scheduler respectent la [spécification ISO-8601](http://en.wikipedia.org/wiki/ISO_8601) et incluent des parties de date et d'heure. Une date-heure qui ne spécifie pas de décalage UTC est supposée être de type UTC.

## Procédure : Utiliser JSON et l'API REST pour la création de planifications

Pour créer une simple planification à l’aide de [l’API REST Azure Scheduler](https://msdn.microsoft.com/library/mt629143), [inscrivez votre abonnement auprès d’un fournisseur de ressources](https://msdn.microsoft.com/library/azure/dn790548.aspx) (le nom du fournisseur pour Scheduler est _Microsoft.Scheduler_), puis [créez une collection de travaux](https://msdn.microsoft.com/library/mt629159.aspx) et [un travail](https://msdn.microsoft.com/library/mt629145.aspx). Lorsque vous créez un travail, vous pouvez spécifier la planification et la périodicité à l'aide de JSON, comme indiqué dans l'extrait ci-dessous :

	{
	    "startTime": "2012-08-04T00:00Z", // optional
	     …
	    "recurrence":                     // optional
	    {
	        "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
	        "interval": 1,                // optional, how often to fire (default to 1)
	        "schedule":                   // optional (advanced scheduling specifics)
	        {
	            "weekDays": ["monday", "wednesday", "friday"],
	            "hours": [10, 22]                      
	        },
	        "count": 10,                  // optional (default to recur infinitely)
	        "endTime": "2012-11-04",      // optional (default to recur infinitely)
	    },
	    …
	}

## Vue d'ensemble : Notions fondamentales du schéma de travail

Le tableau suivant fournit une vue d'ensemble des éléments majeurs liés à la périodicité et la planification d'un travail :

|**Nom JSON**|**Description**|
|:--|:--|
|**_startTime_**|_startTime_ est une Date-Heure. Pour les planifications simples, _startTime_ est la première occurrence et pour les planifications complexes, le travail ne démarre pas avant la valeur _startTime_.|
|**_recurrence_**|L'objet _recurrence_ spécifie les règles de périodicité pour le travail et la périodicité selon laquelle le travail s'exécutera. L'objet de périodicité prend en charge les éléments _frequency, interval, endTime, count_ et _schedule_. Si la valeur _recurrence_ est définie, l'élément _frequency_ est obligatoire. Les autres éléments de la valeur _recurrence_ sont facultatifs.|
|**_frequency_**|La chaîne _frequency_ représente l'unité de fréquence selon laquelle le travail se répète. Les valeurs prises en charge sont _« minute », « hour », « day », « week »_ ou _« month »_.|
|**_interval_**|La valeur _interval_ est un entier positif et indique l'intervalle de la valeur _frequency_ qui détermine la fréquence d'exécution du travail. Par exemple, si la valeur _interval_ est définie sur 3 et la valeur _fréquency_ est définie sur « week » (semaine), le travail se répète toutes les 3 semaines. Azure Scheduler prend en charge un _intervalle_ maximum de 18 mois pour la fréquence mensuelle, 78 semaines pour la fréquence hebdomadaire ou 548 jours pour la fréquence quotidienne. Pour la fréquence heure et minute, la plage prise en charge est 1 <= _intervalle_ <= 1 000.|
|**_endTime_**|La chaîne _endTime_ spécifie la dernière valeur date-heure après laquelle le travail ne doit pas s'exécuter. La chaîne _endTime_ ne peut pas se situer dans le passé. Si aucune valeur _endTime_ ou count n'est spécifiée, le travail s'exécute à l'infini. Vous ne pouvez pas inclure _endTime_ et _count_ dans le même travail.|
|**_count_**|<p>La valeur _count_ est un entier positif (supérieur à zéro) qui spécifie le nombre de fois où ce travail doit s'exécuter avant de se terminer.</p><p>La valeur _count_ représente le nombre de fois où le travail s'exécute avant d'être défini comme étant terminé. Par exemple, pour un travail qui est exécuté quotidiennement avec la valeur _count_ égale à 5 et une date de début égale à lundi, le travail se termine après son exécution le vendredi. Si la date de début se situe dans le passé, la première exécution est calculée à partir de l'heure de création.</p><p>Si aucune valeur _endTime_ ou _count_ n'est spécifiée, le travail s'exécute à l'infini. Vous ne pouvez pas inclure _endTime_ et _count_ dans le même travail.</p>|
|**_schedule_**|Un travail avec une fréquence spécifiée modifie sa périodicité selon une planification périodique. Une _planification_ contient des modifications basées sur des minutes, heures, jours de la semaine, jour du mois et numéro de semaine.|

## Vue d'ensemble : Valeurs par défaut, limites et exemples du schéma de travail

Après cette introduction, examinons chacun de ces éléments en détail.

|**Nom JSON**|**Type de valeur**|**Obligatoire ?**|**Valeur par défaut**|**Valeurs valides**|**Exemple**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|String|Non|Aucun|Dates-Heures ISO-8601|<code>"startTime" : "2013-01-09T09:30:00-08:00"</code>|
|**_recurrence_**|Object|Non|Aucun|Objet de périodicité|<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code>|
|**_frequency_**|String|Oui|Aucun|"minute", "hour", "day", "week", "month"|<code>"frequency" : "hour"</code> |
|**_interval_**|Number|Non|1|1 à 1 000.|<code>"interval":10</code>|
|**_endTime_**|String|Non|Aucun|Valeur date-heure représentant une heure dans le futur|<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
|**_count_**|Number|Non|Aucun|>= 1|<code>"count": 5</code>|
|**_schedule_**|Object|Non|Aucun|Objet de planification|<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code>|

## Présentation approfondie : _startTime_

Le tableau suivant capture comment _startTime_ contrôle la manière dont un travail est exécuté.

|**valeur startTime**|**Aucune périodicité**|**Périodicité. Aucune planification**|**Périodicité avec planification**|
|:--|:--|:--|:--|
|**Aucune heure de début**|Exécuter une fois immédiatement|Exécuter une fois immédiatement. Les exécutions suivantes sont basées sur le calcul à partir de la dernière exécution|<p>Exécuter une fois immédiatement</p><p>Les exécutions suivantes sont basées sur la planification de périodicité</p>|
|**Heure de début dans le passé**|Exécuter une fois immédiatement|<p>Calculer la première exécution ultérieure après l'heure de début, puis exécuter à ce moment-là</p><p>Les exécutions suivantes sont basées sur le calcul continu à partir de la dernière exécution</p><p>Voir l'exemple situé après ce tableau pour obtenir une explication supplémentaire</p>|<p>Le travail _ne démarre pas avant_ l'heure de début spécifiée. La première occurrence est basée sur la planification calculée à partir de l'heure de début</p><p>Les exécutions suivantes sont basées sur la planification de périodicité</p>|
|**Heure de début dans le futur ou à l'heure actuelle**|Exécuter une fois à l'heure de début spécifiée|<p>Exécuter une fois à l'heure de début spécifiée</p><p>Les exécutions suivantes sont basées sur le calcul à partir de la dernière exécution</p>|<p>Le travail _ne démarre pas avant_ l'heure de début spécifiée. La première occurrence est basée sur la planification calculée à partir de l'heure de début</p><p>Les exécutions suivantes sont basées sur la planification de périodicité</p>|

Examinons un exemple de ce qui se passe lorsque _startTime_ se situe dans le passé, avec la valeur _recurrence_ définie, mais pas la valeur _schedule_. Supposons que la date et l'heure actuelle sont 2015-04-08 13:00, la valeur _startTime_ est 2015-04-07 14:00 et la valeur _recurrence_ est tous les 2 jours (définie avec _frequency_ : day et _interval_ : 2). Notez que la valeur _startTime_ se situe dans le passé et se produit avant l'heure actuelle.

Dans ces conditions, la _première exécution_ aura lieu le 2015-04-09 à 14:00. Le moteur d'Azure Scheduler calcule les occurrences de l'exécution à partir de l'heure de début. Toutes les instances dans le passé sont ignorées. Le moteur utilise l'instance suivante qui se produit dans le futur. Dans ce cas, _startTime_ étant 2015-04-07 à 14:00, l'instance suivante aura lieu dans les 2 jours à partir de cette date, c'est-à-dire le 2015-04-09 à 14:00.

Notez que la première exécution serait la même, que la valeur startTime soit égale à 2015-04-05 14:00 ou 2015-04-01 14:00. Après la première exécution, les exécutions suivantes sont calculées à l'aide de la planification, afin qu'elles aient lieu le 2015-04-11 à 14:00, puis le 2015-04-13 à 14:00, puis le 2015-04-15 à 14:00, etc.

Enfin, lorsqu'un travail a une planification, si les heures et/ou les minutes ne sont pas définies dans la planification, elles prennent la valeur par défaut des heures et/ou minutes de la première exécution, respectivement.

## Présentation approfondie : _schedule_

D'une part, une _planification_ peut _limiter_ le nombre d'exécutions du travail. Par exemple, si un travail avec une fréquence « mois » a une _planification_ qui s'exécute uniquement le 31, le travail s'exécute uniquement pendant les mois qui comptent un 31<sup>ème</sup> jour.

D'autre part, une _planification_ peut également _développer_ le nombre d'exécutions du travail. Par exemple, si un travail avec une fréquence « mois » a une _planification_ qui s'exécute les jours 1 et 2 du mois, le travail s'exécute le 1<sup>er</sup> et le 2<sup>ème</sup> jours du mois au lieu d'une seule fois par mois.

Si plusieurs éléments de planification sont spécifiés, l'ordre d'évaluation va du plus grand au plus petit : numéro de semaine, jour du mois, jour de la semaine, heure et minute.

Le tableau suivant décrit les éléments _schedule_ en détail.

|**Nom JSON**|**Description**|**Valeurs valides**|
|:---|:---|:---|
|**minutes**|Minutes de l'heure auxquelles le travail sera exécuté|<ul><li>Entier ou</li><li>Tableau d'entiers</li></ul>|
|**hours**|Heures de la journée auxquelles le travail sera exécuté|<ul><li>Entier ou</li><li>Tableau d'entiers</li></ul>|
|**weekDays**|Jours de la semaine auxquels le travail sera exécuté Peut uniquement être spécifié avec une fréquence hebdomadaire.|<ul><li>« Monday », « Tuesday », « Wednesday », « Thursday », « Friday », « Saturday » ou « Sunday »</li><li>Tableau d’une des valeurs ci-dessus (taille de tableau maximale 7)</li></ul>_Ne_ respectant pas la casse|
|**monthlyOccurrences**|Détermine les jours du mois pour l'exécution du travail. Peut uniquement être spécifié avec une fréquence mensuelle.|<ul><li>Tableau d'objets monthlyOccurence :</li></ul> <pre>{« day » : _day_,<br /> « occurrence » : _occurrence_<br />}</pre><p> _day_ est le jour de la semaine pour l'exécution du travail. Par exemple, {Sunday} correspond à tous les dimanches du mois. Obligatoire.</p><p>Occurrence est l'_occurrence_ du jour au cours du mois. Par exemple, {Sunday, -1} est le dernier dimanche du mois. Facultatif.</p>|
|**monthDays**|Jour du mois auquel le travail sera exécuté. Peut uniquement être spécifié avec une fréquence mensuelle.|<ul><li>Toute valeur <= -1 et >= -31.</li><li>Toute valeur >= 1 et <= 31.</li><li>Un tableau des valeurs ci-dessus</li></ul>|

## Exemples : planifications de périodicité

Voici divers exemples de planifications de périodicité, avec un accent sur l'objet de planification et ses sous-éléments.

Les planifications ci-dessous supposent toutes que la valeur _interval_ est définie sur 1. En outre, vous devez supposer la bonne fréquence conformément à ce qui est dans la _planification_. Par exemple, un utilisateur ne peut pas utiliser la fréquence « day » et une modification « monthDays » dans la planification. Ces restrictions sont décrites ci-dessus.

|**Exemple**|**Description**|
|:---|:---|
|<code>{"hours":[5]}</code>|Exécution à 5h tous les jours. Azure Scheduler fait correspondre chaque valeur sous « hours » avec chaque valeur sous « minutes », une par une, pour créer une liste de toutes les fois où le travail doit être exécuté.|
|<code>{"minutes":[15], "hours":[5]}</code>|Exécution à 5h15 tous les jours.|
|<code>{"minutes":[15], "hours":[5,17]}</code>|Exécution à 5h15 et 17h15 tous les jours.|
|<code>{"minutes":[15,45], "hours":[5,17]}</code>|Exécution à 5h15, 5h45, 17h15 et 17h45 tous les jours|
|<code>{"minutes":[0,15,30,45]}</code>|Exécution toutes les 15 minutes.|
|<code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code>|Exécution toutes les heures. Ce travail s'exécute toutes les heures. La minute est contrôlée par la valeur _startTime_, si elle est spécifiée ou, dans le cas contraire, par l'heure de création. Par exemple, si l'heure de début ou l'heure de création (selon le cas) est 00h25, le travail sera exécuté à 00h25, 01h25, 02h25, ..., 23h25. La planification équivaut à un travail avec la valeur _frequency_ définie sur « hour », la valeur _interval_ égale à 1 et aucune _planification_. La différence est que cette planification peut être utilisée avec une _fréquence_ et un _intervalle_ différents pour créer d'autres travaux. Par exemple, si la valeur _frequency_ est définie sur « month », la planification serait exécutée une seule fois par mois, au lieu de tous les jours si la valeur _frequency_ était définie sur « day ».|
|<code>{minutes:[0]}</code>|Exécution toutes les heures sur l'heure. Ce travail s'exécute également toutes les heures, mais sur l'heure (par exemple, minuit, 1h, 2h, etc.) Cela équivaut à un travail avec une fréquence définie sur « hour », une heure de début avec zéro minute et aucune planification si la fréquence était définie sur « day » ; mais si la fréquence était définie sur « week » ou « month », la planification ne s'exécuterait qu'un seul jour par semaine ou par mois, respectivement.|
|<code>{"minutes":[15]}</code>|Exécution 15 minutes après l'heure toutes les heures. Exécution toutes les heures, à partir de 00h15, 1h15, 2h15, etc. et se terminant à 22h15 et 23h15.|
|<code>{"hours":[17], "weekDays":["saturday"]}</code>|Exécution à 17h le samedi chaque semaine.|
|<code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code>|Exécution à 17h le lundi, mercredi et vendredi chaque semaine.|
|<code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code>|Exécution à 17h15 et 17h45 le lundi, mercredi et vendredi chaque semaine.|
|<code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code>|Exécution à 5h et 17h le lundi, mercredi et vendredi chaque semaine.|
|<code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code>|Exécution à 5h15, 5h45, 17h15 et 17h45 le lundi, mercredi et vendredi chaque semaine.|
|<code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code>|Exécution toutes les 15 minutes les jours de semaine.|
|<code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code>|Exécution toutes les 15 minutes les jours de semaine entre 9h et 16h45.|
|<code>{"weekDays":["sunday"]}</code>|Exécution le dimanche à l'heure de début.|
|<code>{"weekDays":["tuesday", "thursday"]}</code>|Exécution le mardi et le jeudi à l'heure de début.|
|<code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code>|Exécution à 6h le 28ème jour de chaque mois (en supposant une fréquence mensuelle).|
|<code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code>|Exécution à 6h le dernier jour du mois. Si vous souhaitez exécuter un travail le dernier jour du mois, utilisez -1 au lieu de jour 28, 29, 30 ou 31.|
|<code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code>|Exécution à 6h le premier et le dernier jours de chaque mois.|
|<code>{monthDays":[1,-1]}</code>|Exécution le premier et le dernier jours de chaque mois à l'heure de début.|
|<code>{monthDays":[1,14]}</code>|Exécution le premier et le quatorzième jours de chaque mois à l'heure de début.|
|<code>{monthDays":[2]}</code>|Exécution le deuxième jour du mois à l'heure de début.|
|<code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code>|Exécution le premier vendredi de chaque mois à 5h.|
|<code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code>|: Exécution le premier vendredi de chaque mois à l'heure de début.|
|<code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code>|Exécution le troisième vendredi à partir de la fin du mois, chaque mois, à l'heure de début.|
|<code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code>|Exécution le premier et le dernier vendredi de chaque mois à 5h15.|
|<code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code>|Exécution le premier et le dernier vendredi de chaque mois à l'heure de début.|
|<code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code>|Exécution le cinquième vendredi de chaque mois à l'heure de début. S'il n'existe pas de cinquième vendredi dans un mois, le travail ne s'exécute pas, dans la mesure où il est planifié pour s'exécuter uniquement le cinquième vendredi. Vous pouvez envisager d'utiliser -1 au lieu de 5 pour l'occurrence si vous souhaitez exécuter le travail le dernier vendredi du mois.|
|<code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code>|Exécution toutes les 15 minutes le dernier vendredi du mois.|
|<code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code>|Exécution à 5h15, 5h45, 17h15 et 17h45 le troisième mercredi de chaque mois.|

## Voir aussi


 [Présentation d'Azure Scheduler](scheduler-intro.md)

 [Concepts, terminologie et hiérarchie d’entités d’Azure Scheduler](scheduler-concepts-terms.md)

 [Prise en main de Scheduler dans le portail Azure](scheduler-get-started-portal.md)

 [Plans et facturation dans Azure Scheduler](scheduler-plans-billing.md)

 [Informations de référence sur l’API REST d’Azure Scheluler](https://msdn.microsoft.com/library/mt629143)

 [Informations de référence sur les applets de commande PowerShell d’Azure Scheluler](scheduler-powershell-reference.md)

 [Haute disponibilité et fiabilité d’Azure Scheluler](scheduler-high-availability-reliability.md)

 [Limites, valeurs par défaut et codes d’erreur d’Azure Scheluler](scheduler-limits-defaults-errors.md)

 [Authentification sortante d’Azure Scheluler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0817_2016-->