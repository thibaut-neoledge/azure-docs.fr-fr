<properties 
   pageTitle="Compteurs de performances Windows et Linux dans Log Analytics | Microsoft Azure"
   description="Log Analytics collecte les compteurs de performances pour analyser les performances sur les agents Windows et Linux.  Cet article explique comment configurer la collecte des compteurs de performances sur les agents Windows et Linux, comment ils sont stockés dans le référentiel OMS et comment les analyser dans le portail OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="bwren" />


# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Sources de données de performance Windows et Linux dans Log Analytics 

Les compteurs de performances dans Windows et Linux fournissent des informations sur les performances des composants matériels, systèmes d’exploitation et applications.  Log Analytics peut non seulement collecter les compteurs de performances à intervalles réguliers pour effectuer une analyse en temps quasi réel, mais aussi agréger les données de performances pour réaliser des analyses à plus long terme et créer des rapports.

![Compteurs de performances](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configuration des compteurs de performances

Configurez les compteurs de performances dans le [menu Données de Paramètres Log Analytics](log-analytics-data-sources.md#configuring-data-sources).

Lorsque vous configurez initialement des compteurs de performances Windows ou Linux pour un espace de travail OMS, vous avez la possibilité de créer rapidement plusieurs compteurs communs.  Ils s’affichent avec une case à cocher en regard.  Vérifiez que les compteurs que vous voulez créer sont cochés, puis cliquez sur **Ajouter les compteurs de performances sélectionnés**.

![Configurer des compteurs de performances Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Suivez cette procédure pour ajouter un nouveau compteur de performances Windows à collecter.

1. Tapez le nom du compteur dans la zone de texte, au format *objet(instance)\compteur*.  Lorsque vous commencez à taper, la liste des compteurs correspondants s’affiche.  Vous pouvez soit choisir un compteur dans cette liste, soit taper le nom de votre choix.  Vous pouvez également retourner toutes les instances d’un compteur particulier en spécifiant *objet\compteur*. 
2. Cliquez sur **+** ou appuyez sur **Entrée** pour ajouter le compteur à la liste.
3. Lorsque vous ajoutez un compteur, il utilise la valeur par défaut de 10 secondes comme **Intervalle d’échantillonnage**.  Vous pouvez configurer jusqu’à 1 800 secondes (30 minutes) si vous souhaitez réduire l’espace de stockage requis pour les données de performances collectées.
4. Après avoir ajouté les compteurs souhaités, cliquez sur le bouton **Enregistrer** en haut de l’écran pour enregistrer la configuration.

![Configurer des compteurs de performances Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Suivez cette procédure pour ajouter un nouveau compteur de performances Linux à collecter.

1. Par défaut, toutes les modifications de configuration sont automatiquement transmises à l’ensemble des agents.  Pour les agents Linux, un fichier de configuration est envoyé au collecteur de données Fluentd.  Si vous souhaitez modifier ce fichier manuellement sur chaque agent Linux, décochez la case *Appliquer la configuration ci-dessous à mes machines Linux*.
2. Tapez le nom du compteur dans la zone de texte, au format *objet(instance)\compteur*.  Lorsque vous commencez à taper, la liste des compteurs correspondants s’affiche.  Vous pouvez soit choisir un compteur dans cette liste, soit taper le nom de votre choix.  
2. Cliquez sur **+**, ou appuyez sur **Entrée** pour ajouter le compteur à la liste des autres compteurs de l’objet.
3. Tous les compteurs d’un objet utilisent le même **Intervalle d’échantillonnage**.  La valeur par défaut est 10 secondes.  Vous pouvez configurer jusqu’à 1 800 secondes (30 minutes) si vous souhaitez réduire l’espace de stockage requis pour les données de performances collectées.
4. Après avoir ajouté les compteurs souhaités, cliquez sur le bouton **Enregistrer** en haut de l’écran pour enregistrer la configuration.

## <a name="data-collection"></a>Collecte des données

Log Analytics collecte tous les compteurs de performances spécifiés selon l’intervalle d’échantillonnage spécifié sur tous les agents où le compteur est installé.  Les données ne sont pas agrégées, et les données brutes sont disponibles dans toutes les vues de recherche de journal pendant la durée spécifiée par votre abonnement OMS.


## <a name="performance-record-properties"></a>Propriétés des enregistrements de performances

Les enregistrements de performances sont de type **Perf** et leurs propriétés sont décrites dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Ordinateur         | Ordinateur sur lequel l’événement a été collecté. |
| CounterName      | Nom du compteur de performances. |
| CounterPath      | Chemin d’accès complet du compteur au format \\\\\<ordinateur>\\objet(instance)\\. |
| CounterValue     | Valeur numérique du compteur.  |
| InstanceName     | Nom de l’instance de l’événement.  Vide si aucune instance. |
| ObjectName       | Nom de l’objet de performance. |
| SourceSystem  | Type d’agent sur lequel les données ont été collectées. <br> Ops Manager – Agent Windows, connexion directe ou SCOM <br>  Linux – Tous les agents Linux  <br>  AzureStorage – Diagnostics Azure |
| TimeGenerated       | Date et heure d’échantillonnage des données. |


## <a name="sizing-estimates"></a>Tailles estimées

 La collecte d’un compteur toutes les 10 secondes correspond environ à 1 Mo par jour et par instance.  La formule suivante vous permet d’estimer l’espace de stockage requis pour un compteur particulier.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Recherches de journal avec des enregistrements de performances

Le tableau suivant fournit plusieurs exemples de recherches qui extraient des enregistrements de performances.

| Interroger | Description |
|:--|:--|
| Type=Perf | Toutes les données de performances |
| Type=Perf Computer="MonOrdinateur" | Toutes les données de performances d’un ordinateur particulier |
| Type=Perf CounterName="Taille de file d’attente du disque actuelle" | Toutes les données de performances d’un compteur particulier |
| Type=Perf (ObjectName=Processor) CounterName="% du temps processeur" InstanceName=_Total &#124; measure Avg(Average) as AVGCPU by Computer | Utilisation moyenne du processeur entre tous les ordinateurs |
| Type=Perf (CounterName="% de temps processeur") &#124; measure max(Max) by Computer | Utilisation maximale du processeur entre tous les ordinateurs |
| Type=Perf ObjectName=LogicalDisk CounterName="Taille de file d’attente du disque actuelle" Computer="NomMonOrdinateur" &#124; measure Avg(Average) by InstanceName | Longueur actuelle moyenne de file d’attente du disque pour toutes les instances d’un ordinateur donné |
| Type=Perf CounterName="Transferts disque/s" &#124; measure percentile95(Average) by Computer | 95e centile de transferts disque/s entre tous les ordinateurs |
| Type=Perf CounterName="% du temps processeur" InstanceName="_Total"  &#124; measure avg(CounterValue) by Computer Interval 1HOUR | Moyenne horaire d’utilisation du processeur sur tous les ordinateurs |
| Type=Perf Computer="Monordinateur" CounterName=%* InstanceName=_Total &#124; measure percentile70(CounterValue) by CounterName Interval 1HOUR | 70e centile horaire de chaque compteur de pourcentage pour un ordinateur particulier |
| Type=Perf CounterName="% du temps processeur" InstanceName="_Total"  (Computer="MonOrdinateur") &#124; measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR | Moyenne horaire, minimum, maximum et 75e centile d’utilisation du processeur pour un ordinateur spécifique |

## <a name="viewing-performance-data"></a>Affichage des données de performances

Lorsque vous recherchez des données de performances dans un journal, la vue **Journal** s’affiche par défaut.  Pour afficher les données sous forme graphique, cliquez sur **Mesures**.  Pour une vue graphique détaillée, cliquez sur le signe **+** en regard d’un compteur.  

![Vue Mesures réduite](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Si l’intervalle sélectionné ne dépasse pas 6 heures, le graphique est actualisé à intervalles de quelques secondes.  Les données actives s’affichent à droite du graphique en bleu clair.

![Vue Mesures développée avec des données actives](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Pour agréger des données de performances dans une recherche de journal, voir [Agrégation et visualisation de mesures à la demande dans OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [recherches de journal](log-analytics-log-searches.md) pour analyser les données collectées dans des sources de données et des solutions.  
- Exporter les données collectées vers [Power BI](log-analytics-powerbi.md) à des fins d’analyse et de visualisation.


<!--HONumber=Oct16_HO2-->


