---
title: Comprendre les alertes dans OMS Log Analytics | Microsoft Docs
description: "Les alertes dans Log Analytics identifient des informations importantes dans votre référentiel OMS et peuvent de façon proactive vous informer sur des problèmes ou appeler des actions pour tenter de les corriger.  Cet article décrit les différents types de règles d’alerte et comment elles sont définies."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 76db33674c5a3b9e323a1890c0d48d98dc3f03cf
ms.lasthandoff: 03/29/2017


---
# <a name="understanding-alerts-in-log-analytics"></a>Comprendre les alertes dans Log Analytics

Les alertes identifient des informations importantes dans votre référentiel Log Analytics.  Cet article décrit en détail le fonctionnement des règles d’alerte dans Log Analytics et décrit les différences entre les divers types de règles d’alerte.

Pour le processus de création de règles d’alerte, consultez les articles suivants.

- Créer des règles d’alerte avec le [portail Azure](log-analytics-alerts-creating.md)
- Créer des règles d’alerte avec le [modèle Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Créer des règles d’alerte avec l’[API REST](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Règles d'alerte

Les alertes sont créées par des règles dédiées, qui exécutent automatiquement des recherches de journaux à intervalles réguliers.  Si les résultats de la recherche satisfont des critères particuliers, un enregistrement d’alerte est généré.  La règle peut ensuite exécuter automatiquement une ou plusieurs actions pour vous avertir de l’alerte ou appeler un autre processus de façon proactive.  Les différents types de règles d’alerte utilisent une logique différente pour effectuer cette analyse.

![Alertes Log Analytics](media/log-analytics-alerts/overview.png)

Les règles d’alerte sont définies par les détails suivants.

- **Recherche de journaux**.  Il s’agit de la requête qui sera exécutée à chacun des déclenchements de la règle d’alerte.  Les enregistrements renvoyés par cette requête permettent de déterminer si une alerte a été créée.
- **Fenêtre de temps**.  Spécifie l’intervalle de temps pour la requête.  La requête retourne uniquement les enregistrements qui ont été créés dans cette plage précédant son exécution.  Cela peut être toute valeur comprise entre 5 minutes et 24 heures. Par exemple, si la fenêtre de temps est définie sur 60 minutes et que la requête est exécutée à 1h15, seuls les enregistrements créés entre 12h15 et 1h15 apparaissent.
- **Fréquence**.  Spécifie la fréquence à laquelle la requête doit être exécutée. Peut être toute valeur comprise entre 5 minutes et 24 heures. La valeur doit être égale ou inférieure à la fenêtre de temps.  Si la valeur est supérieure à celle de la fenêtre de temps, vous risquez de manquer des enregistrements.<br>Par exemple, imaginons une fenêtre de temps de 30 minutes associée à une fréquence de 60 minutes.  Si la requête est exécutée à 13 h, les enregistrements entre 12 h 30 et 13 h sont renvoyés.  La requête s’exécute ensuite à 14 h, moment auquel elle renvoie les enregistrements entre 13 h 30 et 14 h.  Les enregistrements créés entre 13 h et 13 h 30 ne seront jamais analysés.
- **Seuil**.  Les résultats de la recherche dans les journaux sont évalués pour déterminer si une alerte doit être créée.  Le seuil est différent pour chaque type de règle d’alerte.

Chaque règle d’alerte dans Log Analytics relève de l’un des deux types suivants.  Chacun de ces types est décrit en détail dans les sections suivantes.

- **[Nombre de résultats](#number-of-results-alert-rules)**. Alerte unique créée lorsque le nombre d’enregistrements renvoyés par la recherche dans les journaux dépasse un nombre spécifié.
- **[Mesure métrique](#metric-measurement-alert-rules)**.  Alerte créée pour chaque objet des résultats de la recherche dans les journaux dont les valeurs dépassent le seuil spécifié. 

Les différences entre les types de règles d’alerte sont présentées ci-dessous.

- La règle d’alerte **Nombre de résultats** crée toujours une alerte unique tandis que la règle d’alerte **Mesure métrique** crée une alerte pour chaque objet dépassant le seuil.
- Les règles d’alerte **Nombre de résultats** créent une alerte lorsque le seuil est dépassé une seule fois. Les règles d’alerte **Mesure métrique** peuvent créer une alerte lorsque le seuil est dépassé un certain nombre de fois au cours d’un intervalle de temps spécifique.

## <a name="number-of-results-alert-rules"></a>Règles d’alerte Nombre de résultats
Les règles d’alerte **Nombre de résultats** créent une alerte unique lorsque le nombre d’enregistrement renvoyés par la requête de recherche dépasse le seuil spécifié. 

### <a name="threshold"></a>Seuil
Le seuil pour une règle d’alerte **Nombre de résultats** est simplement supérieur ou inférieur à une valeur particulière.  Si le nombre d’enregistrements renvoyés par cette recherche dans les journaux satisfait à ce critère, une alerte est créée.

### <a name="scenarios"></a>Scénarios

#### <a name="events"></a>Événements
Ce type de règle d’alerte est idéal pour la gestion des événements, par exemple des journaux d’événements Windows, des journaux Syslog et autres journaux personnalisés.  Vous pouvez créer une alerte quand un événement d’erreur particulier est créé, ou quand plusieurs événements d’erreur sont créés dans une fenêtre de temps spécifique.

Pour associer une alerte à un seul événement, définissez le nombre de résultats sur une valeur supérieure à 0, et la fréquence et la fenêtre de temps sur 5 minutes.  Cette configuration exécute la requête toutes les 5 minutes et vérifie si un événement spécifique a été créé depuis la dernière exécution de la requête.  Une fréquence plus longue peut rallonger le temps qui s’écoule entre l’événement collecté et l’alerte créée.

Certaines applications peuvent consigner une erreur occasionnelle qui ne doit pas nécessairement déclencher une alerte.  Par exemple, l’application peut recommencer le processus à l’origine de l’événement d’erreur et voir sa nouvelle tentative couronnée de succès.  Dans ce cas, la création de l’alerte ne se justifie que si plusieurs événements sont créés dans une fenêtre de temps spécifique.  

Dans certains cas, vous pouvez créer une alerte en l’absence d’événement.  Par exemple, un processus peut enregistrer des événements réguliers pour indiquer qu’il fonctionne correctement.  S’il ne consigne pas un de ces événements dans une fenêtre de temps spécifique, une alerte doit être créée.  Dans ce cas, vous devez définir le seuil sur **Inférieur à 1**.

#### <a name="performance-alerts"></a>Alertes de performances
[données de performances](log-analytics-data-sources-performance-counters.md) sont stockées sous la forme d’enregistrements dans le référentiel OMS.  Si vous souhaitez être averti quand un compteur de performances dépasse un seuil spécifique, ce seuil doit être inclus dans la requête.

Par exemple, pour être averti quand le processeur s’exécute à plus de 90 % de ses capacités, utilisez une requête comme la suivante et définissez le seuil de la règle d’alerte sur **Supérieur à 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Pour être averti lorsque la moyenne d’exécution du processeur dépasse 90 % de ses capacités pendant dans une fenêtre de temps spécifique, utilisez une requête comme la suivante avec la [commande measure](log-analytics-search-reference.md#commands) et définissez le seuil de la règle d’alerte sur **Supérieur à 0**. 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90

## <a name="metric-measurement-alert-rules"></a>Règles d’alerte Mesure métrique

>[!NOTE]
> Les règles d’alerte relatives aux mesures métriques sont actuellement en version préliminaire publique.

Les règles d’alerte **Mesure métrique** créent une alerte pour chaque objet d’une requête dont la valeur dépasse un seuil spécifié.  Elles se distinguent des règles d’alerte **Nombre de résultats** comme suit.

#### <a name="log-search"></a>Recherche dans les journaux
Vous pouvez utiliser n’importe quelle requête pour une règle d’alerte **Nombre de résultats**, mais il existe des exigences de requête spécifiques pour la règle d’alerte Mesure métrique.  Elle doit inclure une [commande Measure](log-analytics-search-reference.md#commands) pour regrouper les résultats sur un champ spécifique. Cette commande doit inclure les éléments suivants :

- **Fonction d’agrégation**.  Détermine le calcul à effectuer et, éventuellement, un champ numérique à agréger.  Par exemple, **count()** renvoie le nombre d’enregistrements dans la requête, **avg(CounterValue)** renverra la moyenne du champ CounterValue sur l’intervalle.
- **Champ de groupe**.  Un enregistrement avec une valeur agrégée sera créé pour chaque instance de ce champ, et une alerte peut être générée pour chacun.  Par exemple, si vous souhaitez générer une alerte pour chaque ordinateur, vous pourriez utiliser **by Computer**.   
- **Intervalle**.  Définit l’intervalle de temps pendant lequel les données sont agrégées.  Par exemple, si vous avez spécifié **5minutes**, un enregistrement est créé pour chaque instance du champ de groupe agrégé à intervalles de 5 minutes sur la fenêtre de temps spécifiée pour l’alerte.

#### <a name="threshold"></a>Seuil
Le seuil des règles d’alerte Mesure métrique est défini par une valeur d’agrégation et un nombre de violations.  Si un point de données de la recherche dans les journaux dépasse cette valeur, elle est considérée comme une violation.  Si le nombre de violations pour un objet dans les résultats dépasse la valeur spécifiée, une alerte est créée pour cet objet.

#### <a name="example"></a>Exemple
Prenons le scénario suivant : vous souhaitez créer une alerte si le taux d’utilisation du processeur d’un ordinateur dépasse 90 % à trois reprises en l’espace de 30 minutes.  Il conviendrait de créer une règle d’alerte paramétrée comme suit.  

**Requête :** Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer Interval 5minute<br>
**Fenêtre de temps :** 30 minutes<br>
**Fréquence des alertes :** 5 minutes<br>
**Valeur d’agrégation :** supérieure à 90<br>
**Déclencher une alerte en fonction de :** Nombre total de violations supérieur à 5<br>

La requête créerait une valeur moyenne pour chaque ordinateur à intervalles de 5 minutes.  Cette requête serait exécutée toutes les 5 minutes pour les données collectées au cours des 30 minutes précédentes.  Des exemples de données sont indiqués ci-dessous pour trois ordinateurs.

![Résultats de l’exemple de requête](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Dans cet exemple, des alertes distinctes seraient créées pour srv02 et srv03 dans la mesure où le seuil de 90 % a été dépassé 3 fois au cours de la fenêtre de temps.  Si l’**Alerte Déclencheur basée sur :** a été modifiée sur **Consécutive**, alors une alerte serait créée uniquement pour srv03, car il a dépassé le seuil sur 3 exemples consécutifs.

## <a name="alert-records"></a>Enregistrements d’alerte
Les enregistrements d’alerte créés par des règles d’alerte dans Log Analytics ont pour **Type** **Alert** et pour **SourceSystem** **OMS**.  Les propriétés des enregistrements sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*OMS* |
| *Object*  | Les [alertes Mesure métrique](#metric-measurement-alert-rules) auront une propriété pour le champ de groupe.  Par exemple, si la recherche dans les journaux effectue un groupement sur Ordinateur, l’enregistrement de l’alerte disposera d’un champ Ordinateur avec comme valeur le nom de l’ordinateur.
| AlertName |Nom de l’alerte. |
| AlertSeverity |Niveau de gravité de l’alerte. |
| LinkToSearchResults |Lien vers la recherche de journal Log Analytics qui retourne les enregistrements de la requête ayant créé l’alerte. |
| Requête |Texte de la requête qui a été exécutée. |
| QueryExecutionEndTime |Fin de l’intervalle de temps pour la requête. |
| QueryExecutionStartTime |Début de l’intervalle de temps pour la requête. |
| ThresholdOperator | Opérateur utilisé par la règle d’alerte. |
| ThresholdValue | Valeur utilisée par la règle d’alerte. |
| TimeGenerated |Date et heure de la création de l’alerte. |

Il existe d’autres genres d’enregistrements d’alerte créés par la [solution de gestion des alertes](log-analytics-solution-alert-management.md) et par les [exportations Power BI](log-analytics-powerbi.md).  Ces enregistrements ont tous pour **Type** **Alert**, mais se distinguent par leur propriété **SourceSystem**.


## <a name="next-steps"></a>Étapes suivantes
* Installez la [solution de gestion des alertes](log-analytics-solution-alert-management.md) pour analyser les alertes créées dans Log Analytics, ainsi que les alertes collectées à partir de System Center Operations Manager (SCOM).
* Approfondissez vos connaissances sur les [recherches de journal](log-analytics-log-searches.md) pouvant générer des alertes.
* Effectuez une procédure pas à pas pour [configurer un webhook](log-analytics-alerts-webhooks.md) avec une règle d’alerte.  
* Apprenez à écrire des [runbooks dans Azure Automation](https://azure.microsoft.com/documentation/services/automation) pour corriger les problèmes identifiés par des alertes.


