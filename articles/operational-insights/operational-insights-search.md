<properties
   pageTitle="Recherche de données dans Operational Insights"
   description="Vous pouvez utiliser la fonctionnalité de recherche dans Microsoft Azure Operational Insights afin de trouver les données que vous recherchez"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/21/2015"
   ms.author="banders" />

# Recherche de données dans Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

La fonctionnalité de recherche de journal se trouve au cœur de Microsoft Azure Operational Insights et vous permet de combiner et de mettre en corrélation des données machine de plusieurs sources dans votre environnement. Des solutions sont également alimentées par la recherche de journal pour vous proposer des mesures cernant un domaine problématique en particulier.

Sur la page Recherche, vous pouvez créer une requête, puis lorsque vous effectuez une recherche, vous pouvez filtrer les résultats en utilisant des contrôles de facette. Vous pouvez également créer des requêtes avancées pour la transformation, le filtrage et les rapports relatifs aux résultats.

Les requêtes de recherche de journal courantes apparaissent dans la plupart des pages de solutions. Vous pouvez cliquer sur les vignettes ou explorer d’autres éléments dans l’ensemble de la console Operational Insights afin d’afficher les détails de l’élément à l’aide de la recherche de journal.

Dans ce didacticiel, nous examinerons des exemples afin de couvrir toutes les notions de base dont vous avez besoin lorsque vous utilisez la recherche de journal.

Nous allons commencer par des exemples simples et pratiques, puis nous développerons à partir de ceux-ci pour vous permettre d’avoir une compréhension pratique de l’utilisation de la syntaxe pour extraire les informations dont vous avez besoin à partir des données.

Pour effectuer des recherches de journaux dans Operational Insights, vous utiliserez les techniques suivantes :

- Utilisation de filtres de base
- Utilisation de filtres supplémentaires
- Manipulation des résultats de la recherche
- Utilisation de la commande measure
- Utilisation des fonctions min et max avec la commande measure
- Utilisation de la fonction avg avec la commande measure
- Utilisation de la commande where

Une fois que vous êtes familiarisé avec les techniques de recherche, vous pouvez consulter les [informations de référence relatives à la syntaxe de recherche](#search-syntax-reference) et les [informations de référence relatives aux facettes et aux champs de recherche](#search-field-and-facet-reference).

## Utilisation de filtres de base

La première chose à savoir est que la première partie d'une requête de recherche avant un caractère de barre verticale « | » est toujours une *filtre*. Considérez-la comme une clause WHERE dans TSQL : elle détermine *quel* sous-ensemble de données doit être extrait du magasin de données Operational Insights. La recherche à partir d'un magasin de données consiste principalement à préciser les caractéristiques de données que vous souhaitez extraire ; il est donc naturel qu'une requête commence avec la clause WHERE.

Les filtres les plus simples que vous pouvez utiliser sont des *mots clés* tels que « error » ou « timeout » ou un nom d'ordinateur. Ces types de requêtes simples retournent généralement différentes formes de données dans le même jeu de résultats. Cela est dû au fait qu’Operational Insights dispose de différents *types* des données dans le système.


### Pour effectuer une recherche simple
1. Dans le portail Operational Insights, cliquez sur l’**Explorateur de données de recherche**. ![Recherche de vignette](./media/operational-insights-search/overview-search.png)
2. Dans le champ de requête, tapez `error` puis cliquez sur **Rechercher**. ![Recherche d’erreur](./media/operational-insights-search/search-error.png) Par exemple, la requête pour `error` dans l'image suivante a retourné 100 000 enregistrements **Événement** (collectés par la gestion du journal), 18 enregistrements **ConfigurationAlert** (générés par l'évaluation de la configuration) et 12 enregistrements **ConfigurationChange** (capturés par le suivi des modifications). ![Recherche de résultats](./media/operational-insights-search/results01.png)

Ces filtres ne sont pas vraiment des classes/types d'objet. Le *type* est simplement une balise, une propriété ou une chaîne/un nom/une catégorie, qui est attachée à un élément de données. Certains documents dans le système sont marqués en tant que **Type:ConfigurationAlert**, d’autres en tant que **Type:PerfHourly** ou **Type:Event** et ainsi de suite. Chaque résultat de recherche, document, enregistrement ou entrée affiche toutes les propriétés brutes et leurs valeurs pour chacun de ces éléments de données. Vous pouvez utiliser ces noms de champs pour préciser dans le filtre lorsque vous souhaitez récupérer uniquement les enregistrements dont le champ a cette valeur donnée.

Le *type* est simplement un champ que tous les enregistrements ont. Il n'est pas différent des autres champs. Cela a été établi suivant la valeur du champ Type. Cet enregistrement aura une autre forme. De plus, **Type=PerfHourly** ou **Type=Event** constitue également la syntaxe dont vous avez besoin pour apprendre à interroger toutes les heures des événements ou des agrégats de données de performances

Vous pouvez utiliser les deux-points (:) ou le signe égal (=) après le nom du champ et avant la valeur. **Type:Event** et **Type=Event** ont la même signification. Vous pouvez donc choisir le style qui vous convient.

Ainsi, si les enregistrements Type=PerfHourl ont un champ appelé « CounterName », vous pouvez alors écrire une requête qui ressemble à `Type=PerfHourly CounterName="% Processor Time"`.

Vous obtiendrez uniquement les données de performances où le nom du compteur de performance est « % Processor Time ».

### Pour rechercher des données de performance de temps du processeur
- Dans le champ de requête de recherche, tapez `Type=PerfHourly CounterName="% Processor Time"`

Vous pouvez également être plus précis et utiliser **InstanceName=_’Total’** dans la requête, qui est un compteur de performance Windows. Vous pouvez également sélectionner une facette et une autre valeur **field:value**. Ce filtre est automatiquement ajouté à votre filtre dans la barre de requête. Vous pouvez le voir dans l'image suivante. Il indique où cliquer pour ajouter **InstanceName:’_Total’** à la requête sans avoir à taper quoi que ce soit.

![Recherche de facette](./media/operational-insights-search/search-facet.png)

Votre requête devient alors `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”`

Dans cet exemple, vous n'êtes pas obligé de spécifier **Type=PerfHourly** pour obtenir ce résultat. Étant donné que les champs CounterName et InstanceName existent uniquement pour les enregistrements de Type=PerfHourly, la requête est suffisamment spécifique pour retourner les mêmes résultats que le résultat précédent plus long : ```
CounterName=”% Processor Time” InstanceName=”_Total”
```

Cela est dû au fait que tous les filtres dans la requête sont évalués comme étant dans chacun d’eux *ET* entre eux. En effet, plus vous ajoutez des champs aux critères, moins vous obtenez des résultats plus spécifiques et affinés.

Par exemple, la requête `Type=Event EventLog="Windows PowerShell"` est identique à `Type=Event AND EventLog="Windows PowerShell"`. Elle retourne tous les événements qui ont été enregistrés dans et collectés à partir du journal des événements Windows PowerShell. Si vous ajoutez un filtre plusieurs fois en sélectionnant de manière répétée la même facette, le problème est alors purement esthétique : il peut surcharger la barre de recherche, mais il retourne toujours les mêmes résultats, car l'opérateur AND implicite est toujours présent.

Vous pouvez facilement inverser l'opérateur AND implicite à l'aide d'un opérateur NOT explicite. Par exemple :

`Type:Event NOT(EventLog:"Windows PowerShell")` ou son équivalent `Type=Event EventLog!="Windows PowerShell"` retourne tous les événements de tous les autres journaux qui ne sont PAS le journal Windows PowerShell.

Vous pouvez également utiliser un autre opérateur booléen tel que « OR ». La requête suivante retourne les enregistrements pour lesquels le journal des événements est une application OU un système.

```
EventLog=Application OR EventLog=System
```

À l'aide de la requête ci-dessus, vous obtiendrez les entrées pour les journaux dans le même jeu de résultats.

Cependant, si vous supprimez l'opérateur OR en laissant l’opérateur implicite AND en place, la requête suivante ne produira pas de résultats car aucune entrée du journal des événements n’appartient aux DEUX journaux. Chaque entrée de journal des événements a été écrite pour seulement un des deux journaux.

```
EventLog=Application EventLog=System
```


## Utilisation de filtres supplémentaires

La requête suivante retourne les entrées pour deux journaux des événements pour tous les ordinateurs qui ont envoyé des données.

```
EventLog=Application OR EventLog=System
```

![Recherche de résultats](./media/operational-insights-search/search-results03.png)

La sélection de l'un des champs ou des filtres restreindra la requête pour un ordinateur spécifique, en excluant tous les autres. La requête obtenue peut se présenter comme suit.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Ce qui revient à la commande suivante, en raison de l'opérateur AND implicite.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Chaque requête est évaluée dans l'ordre explicite suivant. Notez la parenthèse.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Comme le champ du journal des événements, vous pouvez récupérer des données uniquement pour un ensemble d'ordinateurs spécifiques en ajoutant OR. Par exemple :

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

De même, cette requête suivante retourne le**pourcentage de temps processeur** uniquement pour les deux ordinateurs sélectionnés.

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### Opérateurs booléens
Avec les champs numériques et DateHeure, vous pouvez rechercher des valeurs à l'aide de *supérieur à*, *inférieur à*, et *inférieure ou égal à*. Vous pouvez utiliser des opérateurs simples tels que >, <, >=, <=, != dans la barre de recherche de requête.


Vous pouvez interroger un journal des événements spécifique pour une période spécifique. Par exemple, l’expression mnémonique suivante permet d’exprimer les dernières 24 heures.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### Pour effectuer une recherche à l'aide d'un opérateur booléen
- Dans le champ de requête de recherche, tapez `EventLog=System TimeGenerated>NOW-24HOURS"` ![Recherche avec des opérateurs boléens](./media/operational-insights-search/search-boolean.png)

Bien que vous puissiez contrôler graphiquement l'intervalle de temps, et nous vous invitons à faire cela la plupart du temps, l’ajout d’un filtre de temps directement dans la requête présente certains avantages. Par exemple, cela fonctionne très bien avec les tableaux de bord qui vous permettent de remplacer le temps pour chaque vignette, quel que soit le sélecteur de temps *global* sur la page du tableau de bord. Pour plus d'informations, consultez [Questions relatives au temps dans le tableau de bord](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Lors du filtrage par heure, n'oubliez pas que vous obtenez des résultats pour l’*intersection* des deux périodes : celle indiquée dans le portail Operational Insights (S1) et celle indiquée dans la requête (S2).

![intersection](./media/operational-insights-search/intersection.png)

Cela signifie que, si deux périodes ne se chevauchent pas, par exemple dans le portail Operational Insights où vous choisissez **Cette semaine** et dans la requête où vous définissez **La semaine dernière**, il n'existe aucune intersection et vous ne recevrez aucun résultat.

Les opérateurs de comparaison utilisés pour le champ TimeGenerated sont également utiles dans d'autres situations. Par exemple, avec des champs numériques.

Par exemple, étant donné que les alertes d'évaluation de configuration ont les valeurs de gravité suivantes :

- 0 = Information
- 1 = Avertissement
- 2 = Critique

Vous pouvez interroger pour les alertes d’avertissements et les alertes critiques et exclure également les alertes d'information avec la requête suivante :

```
Type=ConfigurationAlert  Severity>=1
```


Vous pouvez aussi utiliser des requêtes de plage de données. Cela signifie que vous pouvez fournir la plage de valeurs de début et de fin dans une séquence. Par exemple, si vous souhaitez obtenir des événements du journal des événements Operations Manager où l'ID de l’événement est supérieur ou égal à 2100 mais inférieur ou égal à 2199, la requête suivante va alors les retourner.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]La syntaxe de plage de données que vous devez utiliser est le séparateur field:value avec les deux-points (:) field:value et *non* le signe égal (=). Ajoutez l’extrémité supérieure et inférieure de la plage de données entre crochets et séparez-les par deux points (..).

## Manipulation des résultats de la recherche

Lorsque vous recherchez des données, vous devez affiner votre requête de recherche et avoir un bon niveau de contrôle sur les résultats. Lorsque les résultats sont récupérés, vous pouvez appliquer des commandes pour les transformer.

Les commandes dans les recherches d'Operational Insights *doivent* se trouver après la barre verticale (|). La première partie d'une chaîne de requête doit toujours être un filtre. Celui-ci définit le jeu de données avec lequel vous travaillez, puis il envoie ces résultats dans une commande. Vous pouvez ensuite utiliser ce pipe pour ajouter des commandes supplémentaires. Cela est relativement similaire au pipeline Windows PowerShell.

En général, le langage de recherche d’Operational Insights tente de respecter le style et les instructions PowerShell de manière à ce qu’ils soient similaires à ceux des professionnels de l’informatique et pour faciliter cette étape de l’apprentissage.

Le nom des commandes est un verbe, ce qui vous permet de connaître facilement leur fonction.

### Trier

La commande de tri vous permet de définir l'ordre de tri avec un ou plusieurs champs. Même si vous ne l'utilisez pas, un ordre de temps décroissant est appliqué par défaut. Les résultats les plus récents sont toujours en haut des résultats de recherche. Cela signifie que lorsque vous exécutez une recherche avec `Type=Event EventID=1234`, ce qui est réellement exécuté pour vous est :

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

C’est parce qu’il s’agit du type d'expérience que vous connaissez avec les journaux. Par exemple, dans l'observateur d'événements Windows.

Vous pouvez utiliser la fonction Sort pour modifier la façon dont les résultats sont retournés. Les exemples suivants illustrent son fonctionnement.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Ces simples exemples ci-dessus vous montrent le fonctionnement des commandes : elles modifient la forme des résultats que le filtre a retournés.

### Limit et Top
Une autre commande moins connue est LIMIT. Limit est un verbe similaire à PowerShell. Limit a la même fonction que la commande TOP. Les requêtes suivantes retournent les mêmes résultats.

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### Pour effectuer une recherche à l'aide de Top
- Dans le champ de requête de recherche, tapez `Type=Event EventID=2110 | Top 1` ![Recherche top](./media/operational-insights-search/search-top.png)

Dans l'image ci-dessus, il existe des 988 enregistrements avec EventID=2110. Les champs, les facettes et les filtres sur la gauche affichent toujours des informations sur les résultats retournés *par la partie du filtre* de la requête, qui est la partie située avant une barre verticale. Le panneau **Résultats** retourne uniquement le résultat le plus récent car l’exemple de commande a formé et transformé les résultats.

### Sélectionnez

La commande SELECT agit comme Select-Object dans PowerShell. Elle retourne des résultats filtrés qui n'ont pas toutes leurs propriétés d'origine. Au lieu de cela, elle sélectionne uniquement les propriétés que vous avez spécifiées.

#### Pour exécuter une recherche à l'aide de la commande select

1. Dans Rechercher, tapez `Type=Event` puis cliquez sur **Rechercher**.
2. Cliquez sur **+ Afficher plus** dans un des résultats pour afficher toutes les propriétés dont disposent les résultats.
3. Sélectionnez certains d’entre eux de façon explicite ; la requête devient alors `Type=Event | Select Computer,EventID,RenderedDescription`. ![Recherche select](./media/operational-insights-search/search-select.png)

Il s'agit d’une commande particulièrement utile lorsque vous souhaitez contrôler les résultats de recherche et choisir uniquement des portions de données qui importent vraiment pour votre exploration et qui, bien souvent, ne sont pas l’enregistrement complet. Cela est également utile lorsque des enregistrements de différents types présentent *certaines* propriétés communes, mais *toutes* leurs propriétés ne sont pas communes. Vous pouvez générer des résultats qui ressemblent plus naturellement à une table ou fonctionnent bien lorsqu’ils sont exportés vers un fichier CSV puis envoyés dans Excel.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

## Utilisation de la commande measure

MEASURE est une des commandes les plus polyvalentes dans les recherches d’Operational Insights. Elle vous permet d'appliquer des *fonctions* statistiques à vos données et de regrouper des résultats par champ donné. Il existe plusieurs fonctions statistiques qui prennent en charge Measure.

### La fonction count() de Measure

La première fonction statistique à utiliser et la plus simple à comprendre est la fonction *count()*.

Les résultats d'une requête de recherche telle que `Type=Event`, affichent des filtres, également appelés facettes, sur le côté gauche des résultats de la recherche. Les filtres montrent une distribution de valeurs pour un champ donné dans les résultats de la recherche effectuée.

![Recherche measure count](./media/operational-insights-search/search-measure-count01.png)

Par exemple, dans l'image ci-dessus, vous verrez le champ **Ordinateur** qui montre que dans les près de 3 millions d’événements des résultats, 20 valeurs uniques et distinctes pour le champ **Ordinateur** sont présentes dans ces enregistrements. La vignette affiche uniquement les 5 premières, qui sont les 5 valeurs les plus courantes écrites dans le champ **Ordinateur**, triées par nombre de documents contenant cette valeur spécifique dans ce champ. Dans l'image, vous pouvez voir que, parmi ces près de 3 millions d’événements, 880 000 proviennent de l'ordinateur DM, 602 000 de l'ordinateur DE etc.


Comment faire si vous souhaitez voir toutes les valeurs, étant donné que la vignette ne montre que les 5 premières ?

C'est ce que la commande Measure permet de faire avec la fonction count(). Cette fonction n'utilise aucun paramètre. Vous spécifiez simplement le champ par lequel vous voulez regrouper – le champ **Ordinateur** dans ce cas :

`Type=Event | Measure count() by Computer`

![Recherche measure count](./media/operational-insights-search/search-measure-count-computer.png)

Toutefois, le champ **Ordinateur** est simplement un champ utilisé *dans* chaque élément de données : aucune base de données relationnelle n’est impliquée et il n'existe aucun objet distinct **Ordinateur** en aucun emplacement. Seules les valeurs *dans* les données peuvent décrire quelle entité les a générées, ainsi que plusieurs autres caractéristiques et aspects des données, d’où le terme *facette*. Toutefois, vous pouvez également les regrouper par d'autres champs. Étant donné que les résultats d'origine des près de 3 millions d’événements transmis dans la commande Measure ont également un champ appelé **EventID**, vous pouvez appliquer la même technique pour les regrouper par ce champ et obtenir le nombre d'événements par ID d'événement :

```
Type=Event | Measure count() by EventID
```

Si vous n'êtes pas intéressé par le nombre d'enregistrements réels qui contiennent une valeur spécifique, mais que vous souhaitez seulement une liste de valeurs elles-mêmes, vous pouvez ajouter une commande *Sélect* à la fin de celle-ci et simplement sélectionner la première colonne :

```
Type=Event | Measure count() by EventID | Select EventID
```

Vous pouvez ensuite obtenir des résultats de requête plus complexes et les trier, ou vous pouvez aussi simplement cliquer sur les colonnes dans la grille.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### Pour effectuer une recherche à l'aide de measure count

- Dans le champ de requête de recherche, tapez `Type=Event | Measure count() by EventID`
- Ajoutez `| Select EventID` à la fin de la requête.
- Enfin, ajoutez `| Sort EventID asc` à la fin de la requête.


Il est important de comprendre et de mettre en évidence certains points essentiels :

Tout d'abord, les résultats que vous voyez ne sont plus les résultats bruts d'origine. Il s’agit en fait de résultats agrégés : autrement dit, des groupes de résultats. Cela n'est pas un problème, mais vous devez comprendre que vous interagissez avec une toute autre forme de données qui diffère de la forme brute d'origine créée en cours de route lorsque la fonction d'agrégation ou statistique est utilisée.

Ensuite, la fonction **Measure count** ne retourne pour le moment que les 100 premiers résultats distincts. Cette limite ne s'applique pas aux autres fonctions statistiques. Par conséquent, vous devez généralement utiliser d’abord un filtre plus précis pour rechercher des éléments spécifiques avant d'appliquer measure count().

## Utilisation des fonctions min et max avec la commande measure

Il existe plusieurs scénarios pour lesquels **Measure Max()** et **Measure Min()** sont utiles. Toutefois, étant donné que chaque fonction est l'opposé de l'autre, nous démontrerons la fonction Max() et vous pourrez ensuite tester vous-même la fonction Min().

Si vous interrogez des alertes de l'évaluation de la configuration, celles-ci ont une propriété **Severity** qui peut être de 0, 1 ou 2 et qui indique une information, un avertissement ou un état critique. Par exemple :

```
Type=ConfigurationAlert
```

![Recherche du début de la fonction measure count](./media/operational-insights-search/search-measure-max01.png)

Si vous souhaitez afficher la valeur la plus élevée pour toutes les alertes pour un même ordinateur, puis les grouper par champ, vous pouvez utiliser :

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Recherche l’ordinateur de la fonction measure max](./media/operational-insights-search/search-measure-max02.png)

Il affichera que, pour les ordinateurs ayant des enregistrements d’**alerte**, la plupart d'entre eux ont au moins une alerte critique, et que l'ordinateur Bacc indique un avertissement comme niveau de gravité le plus élevé.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![Recherche l’ordinateur qui génère la valeur horaire measure max](./media/operational-insights-search/search-measure-max03.png)

Cette fonction fonctionne bien avec les nombres, mais elle fonctionne également avec les champs DateHeure. Il est utile de vérifier l'horodatage de la dernière heure ou de l’heure la plus récente pour tout type de données indexé pour chaque ordinateur. Par exemple : à quand remonte le dernier signalement d’une modification de configuration par la solution de suivi des modifications pour chaque machine ?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## Utilisation de la fonction avg avec la commande measure

La fonction statistique Avg() utilisée avec measure vous permet de calculer la valeur moyenne pour un champ et de grouper les résultats selon ce même champ ou un autre. Cela est utile dans de nombreux cas, pour les données de performances par exemple.

Nous allons commencer par les données de performances. Toutefois, notez qu'Operational Insights ne recueille pour le moment que certains compteurs de performance liés à la structure pour les hôtes Virtual Machine Manager et Hyper-V dans le cadre de la solution de gestion de la capacité.

Pour rechercher *toutes* les données de performances, la requête la plus simple est :

```
Type=PerfHourly
```

![Recherche du début de la fonction avg](./media/operational-insights-search/search-avg01.png)

La première chose que vous remarquerez est qu'Operational Insights vous montre des graphiques des compteurs de performance. En bas des résultats, vous verrez les enregistrements réels derrière les graphiques.

![Recherche du début de la fonction avg](./media/operational-insights-search/search-avg02.png)

Dans l'image ci-dessus, il existe deux ensembles de champs marqués qui indiquent les éléments suivants :

- le premier jeu identifie le nom du compteur de performances Windows, le nom de l'objet et le nom de l'instance dans le filtre de la requête. Il s’agit des champs que vous utiliserez probablement le plus souvent en tant que facettes ou filtres.
- **SampleValue** est la valeur réelle du compteur
- dans la requête, **Type=PerfHourly** est un agrégat de toutes les heures
- **TimeGenerated** est défini à 21:00, au format 24 heures. Il s’agit de l'agrégation pour cette période horaire de 20:00 à 21:00.
- **SampleCount** est l'agrégation, calculée à l'aide de 12 échantillons (un toutes les 5 minutes)
- les valeurs **min**, **max**, et **Percentile95** pour la période horaire étaient, pour cet exemple de mémoire sur une machine virtuelle, de 6144 (mégaoctets)
- **SampleValue** est un agrégat de toutes les heures et il est rempli avec la *moyenne* pour la période horaire et correspond à ce qui est utilisé pour tracer les graphiques de performances

Après avoir lu ce qui concerne la forme d'enregistrement PerfHourly et ce qui concerne d'autres techniques de recherche, vous pouvez utiliser measure Avg() pour agréger ce type de données numériques.

Voici un exemple simple :

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

![Recherche de l’exemple de valeur de la fonction avg](./media/operational-insights-search/search-avg03.png)

Dans cet exemple, vous sélectionnez le compteur de performance du temps processeur total et la moyenne par ordinateur. Étant donné que **SampleValue** est déjà une moyenne, vous interrogez en fait une moyenne de la moyenne. À ce stade, cela est correct avec Type=PerfHourly. Vous devez toujours utiliser un filtre sur TimeGenerated pour limiter l'opération à un jeu de données petit ou récent, par exemple les 4 dernières heures, mais pas les 7 derniers jours.

Par conséquent, la requête ci-dessus devient :

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### Rechercher à l’aide de la fonction avg avec la commande measure
1. Dans le champ de requête de recherche, tapez `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer`.
2. Notez que la moyenne récente sera généralement plus élevée.
3. Calculez la moyenne des valeurs horaires maximum en modifiant votre requête de recherche avec `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer`

Vous pouvez agréger et corréler les données *entre* plusieurs ordinateurs. Par exemple, imaginez que vous disposez d'un ensemble d'hôtes dans une sorte de batterie de serveurs où chaque nœud est égal à n'importe quel autre et ils effectuent simplement le même type de travail et la charge doit être à peu près équilibrée. Vous pouvez obtenir leurs compteurs en une seule fois avec la requête suivante et obtenir des moyennes pour l’ensemble de la batterie. Vous pouvez commencer en choisissant les ordinateurs avec l'exemple suivant :

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Maintenant que vous avez les ordinateurs, vous ne devez sélectionner que deux indicateurs de performance clés (KPI) : le pourcentage d’utilisation du processeur et le pourcentage d’espace disque disponible. Par conséquent, cette partie de la requête devient :

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Vous pouvez maintenant ajouter des ordinateurs et des compteurs avec l'exemple suivant :

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Étant donné que vous avez une sélection très spécifique, la commande **measure Avg()** peut retourner la moyenne non par ordinateur mais pour l’ensemble de la batterie de serveurs, simplement en effectuant un regroupement par CounterName. Par exemple :

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

Cela vous donne une vue compacte utile de quelques indicateurs de performance clés de votre environnement.

![Recherche du groupement de la fonciton avg](./media/operational-insights-search/search-avg04.png)


Vous pouvez facilement utiliser cela dans un tableau de bord. Pour en savoir plus sur l'utilisation des tableaux de bord, consultez [Tableaux de bord d’Operational Insights](operational-insights-use-dashboards).

![Tableau de bord de recherche avg](./media/operational-insights-search/search-avg05.png)

### Utilisation de la fonction sum avec la commande measure

La fonction sum est similaire à d'autres fonctions de la commande measure. Vous pouvez voir un exemple sur la façon d'utiliser la fonction sum dans [Recherche de journaux IIS W3C dans Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Vous pouvez utiliser les fonctions Max() et Min() avec des chaînes de nombres, de DatesHeures et du texte. Avec des chaînes de texte, elles sont triées par ordre alphabétique et vous pouvez obtenir la première et la dernière.

Toutefois, vous ne pouvez pas utiliser Sum() uniquement avec des champs numériques. Cela vaut également pour la fonction Avg().

## Utilisation de la commande where

La commande where fonctionne comme un filtre, mais elle peut être appliquée dans le pipeline pour filtrer davantage les résultats agrégés résultant d’une commande Measure – au lieu des résultats bruts qui sont filtrés au début d'une requête.

Par exemple :

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

Vous pouvez ajouter une autre barre verticale « | » et la commande where pour obtenir uniquement les ordinateurs dont la moyenne du processeur est supérieure à 80 %, avec l'exemple suivant :

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

Si vous êtes familiarisé avec Microsoft System Center - Operations Manager, vous pouvez considérer la commande where comme un pack d’administration. S'il l'exemple était une règle, la première partie de la requête serait la source de données et la commande where serait la détection de condition.

Vous pouvez utiliser la requête sous forme de vignette dans **Mon tableau de bord**, comme un moniteur de tri, afin de voir à quel moment les processeurs de l'ordinateur sont surexploités. Pour en savoir plus sur les tableaux de bord, consultez [Tableaux de bord d’Operational Insights](operational-insights-use-dashboards). Vous pouvez également créer et utiliser des tableaux de bord à l'aide de l'application mobile. Pour plus d'informations, consultez [Application mobile d’Azure Operational Insights ](http://www.windowsphone.com/fr-fr/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). En bas des deux vignettes de l'image suivante, vous pouvez voir que le moniteur a affiché une liste et un nombre. Essentiellement, le nombre doit toujours être zéro et la liste doit toujours être vide. Dans le cas contraire, il indique une condition d'alerte. Au besoin, vous pouvez l'utiliser pour voir quels ordinateurs sont sous pression.

![Tableau de bord mobile](./media/operational-insights-search/search-mobile.png)

## Informations de référence sur la syntaxe de recherche

La section d’informations de référence suivante sur le langage de recherche décrit les options de syntaxe de requête générales que vous pouvez utiliser lorsque vous recherchez des données et que vous filtrez des expressions pour affiner votre recherche. Elle décrit également les commandes que vous pouvez utiliser pour effectuer une opération sur les données récupérées.

Vous pouvez lire au sujet des champs retournés dans les recherches et les facettes qui vous aident à explorer des catégories de données similaires dans les [Informations de référence sur la recherche de champ et de facette](#Search-field-and-facet-reference).

### Syntaxe de requête générale

Syntaxe :

filterExpression | Command1 | Command2...

L'expression de filtre (** filterExpression **) définit la condition « where » pour la requête. Les commandes s'appliquent aux résultats retournés par la requête. Plusieurs commandes doivent être séparées par la barre verticale (|).

#### Exemples de syntaxe générale

Exemples :

	system

Cette requête retourne les résultats qui contiennent le mot « système » dans un champ qui a été indexé pour la recherche de termes ou en texte intégral.

>[AZURE.NOTE]Tous les champs ne sont pas indexés de cette façon, mais les champs textuels les plus courants (tels que les descriptions et les noms) le sont généralement.

	system error

Cette requête retourne des résultats qui contiennent les mots « système » et « erreur ».

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

Cette requête retourne des résultats qui contiennent les mots « système » et « erreur ». Elle trie ensuite les résultats selon le champ **ManagementGroupName** (dans l'ordre croissant), puis le champ **TimeGenerated** (dans l'ordre décroissant). Seuls les 10 premiers résultats sont nécessaires.

>[AZURE.IMPORTANT]Tous les noms de champ et les valeurs pour les champs de texte et de chaîne respectent la casse.

### Expression de filtre

Les sous-sections suivantes expliquent les expressions de filtre.

#### Littéraux de chaîne

Un littéral de chaîne est une chaîne qui n'est pas reconnue par l'analyseur comme un mot clé ou un type de données prédéfini (par exemple un nombre ou une date).

Exemples :

	These all are string literals


Cette requête recherche des résultats qui contiennent les occurrences des cinq mots. Pour effectuer une recherche de chaîne complexe, placez le littéral de chaîne entre guillemets, par exemple :

	" Windows Server"

Cela renvoie uniquement les résultats avec des correspondances exactes pour « Windows Server »

#### Nombres

L'analyseur prend en charge l'entier décimal et une syntaxe de nombre à virgule flottante pour les champs numériques.

Exemples :

	Type:PerfHourly 0.5


	HTTP 500

#### Date/Heure

Chaque élément de données dans le système a une propriété **TimeGenerated** qui représente la date et l'heure d’origine de l'enregistrement. Certains types de données peuvent en outre avoir plus de champs Date/Heure (par exemple, **LastModified**).

Le sélecteur de graphique/heure de chronologie dans Operational Insights montre une répartition des résultats au fil du temps (en fonction de la requête actuelle en cours d'exécution), basée sur la valeur du champ **TimeGenerated**. Les champs Date/Heure ont un format de chaîne spécifique qui peut être utilisé dans des requêtes pour limiter celles-ci à une période particulière. Vous pouvez également utiliser la syntaxe pour faire référence à des intervalles de temps relatifs (par exemple, « entre il y a 3 jours et il y a 2 heures »).

Syntaxe :

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



Exemple :

	TimeGenerated:2013-10-01T12:20

La commande précédente retourne uniquement les enregistrements avec une valeur **TimeGenerated** indiquant exactement 12:20 le 1er octobre 2013. Il est peu probable qu’elle fournira des résultats, mais cela vous permet d’en comprendre le principe.

L'analyseur prend également en charge la valeur Date/Heure mnémonique NOW.

Exemple :


Là encore, il est peu probable que cela génère des résultats, car les données ne traversent pas le système si rapidement.

Ces exemples sont des blocs de construction à utiliser pour des dates relatives et absolues. Dans les trois sous-sections suivantes, nous expliquerons comment les utiliser dans des filtres plus avancés avec des exemples qui utilisent des plages de dates relatives.

#### Mathématique de Date/Heure

Utilisez les opérateurs mathématiques de Date/Heure pour décaler ou arrondir la valeur de Date/Heure en utilisant de simples calculs de Date/Heure.

Syntaxe :

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>Opérateur </th>
		<th>Description</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>Arrondit la valeur Date/Heure à l'unité spécifiée. </p>
		<p>Exemple&#160;: NOW/DAY arrondit la valeur Date/Heure actuelle à minuit du jour en cours. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+&#160;ou&#160;-</p>
		</td>
		<td>
		<p>Décale la valeur Date/Heure du nombre d'unités spécifié</p>
		<p>Exemples&#160;:&#160; </p>
		<ul>
			<li class="unordered">NOW+1HOUR décale la valeur Date/Heure actuelle d'une heure à l'avance.<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS décale la valeur Date de 10&#160;jours en arrière.</li>
		</ul>
		</td>
	</tr>
</table>





Vous pouvez chaîner les opérateurs mathématiques Date/Heure, par exemple :

	NOW+1HOUR-10MONTHS/MINUTE

Le tableau suivant répertorie les unités Date/Heure prises en charge.

<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Unité Date/Heure </th> <th>Description </th> </tr> <tr> <td> <p>YEAR, YEARS</p> </td> <td> <p>Arrondit à l'année en cours ou décale du nombre d’années spécifié.</p> </td> </tr> <tr> <td> <p>MONTH, MONTHS</p> </td> <td> <p>Arrondit au mois en cours ou décale du nombre de mois spécifié.</p> </td> </tr> <tr> <td> <p>DAY, DAYS, DATE </p></td> <td> <p>Arrondit au jour du mois en cours ou décale du nombre de jours spécifié.</p> </td> </tr> <tr> <td> <p>HOUR, HOURS</p> </td> <td> <p>Arrondit à l’heure en cours ou décale du nombre d’heures spécifié.</p> </td> </tr> <tr> <td> <p>MINUTE, MINUTES</p> </td> <td> <p>Arrondit à la minute en cours ou décale du nombre de minutes spécifié.</p> </td> </tr> <tr> <td> <p>SECOND, SECONDS</p> </td> <td> <p>Arrondit à la seconde en cours ou décale du nombre de secondes spécifié.</p> </td> </tr> <tr> <td> <p>MILLISECOND, MILLISECONDS, MILLI, MILLIS</p> </td> <td> <p>Arrondit à la milliseconde en cours ou décale du nombre de millisecondes spécifié.</p> </td> </tr> </table>


#### Facettes de champ

À l'aide de facettes de champ, vous pouvez spécifier la condition de recherche pour des champs spécifiques et leurs valeurs exactes, au lieu d’écrire des requêtes de « texte libre » pour différents termes dans l'index. Nous avons déjà utilisé cette syntaxe dans plusieurs exemples dans les paragraphes précédents. Nous vous proposons maintenant des exemples plus complexes.

**Syntaxe**

*field:value*

*field=value*

**Description**

Recherche la valeur spécifique dans le champ. La valeur peut être un littéral de chaîne, un nombre ou une Date/Heure.

Exemple :


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**Syntaxe**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**Description**

Fournit des comparaisons.

Exemple :

	TimeGenerated>NOW+2HOURS


**Syntaxe**

*field:[from..to]*

**Description**

Fournit des facettes de plage.

Exemple :

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### Opérateurs logiques

Les langages de requête prennent en charge les opérateurs logiques (AND, OR et NOT) et de leurs alias C-style (&&, ||, et !) respectivement. Vous pouvez utiliser des parenthèses pour regrouper ces opérateurs.

Exemples :

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
Vous pouvez omettre l'opérateur logique pour les arguments de filtre de niveau supérieur. Dans ce cas, l'opérateur AND est supposé.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Expression de filtre</th> <th>Équivaut à</th> </tr> <tr> <td> <p>system error</p> </td> <td> <p>system AND error</p> </td> </tr> <tr> <td> <p>system "; Windows Server"; OR Severity:1</p> </td> <td> <p>system AND (";Windows Server"; OR Severity:1)</p> </td> </tr> </table>



### Commandes

Les commandes s'appliquent aux résultats qui sont retournés par la requête. Utilisez la barre verticale (|) pour appliquer une commande aux résultats récupérés. Les commandes multiples doivent être séparées par la barre verticale (|).

>[AZURE.NOTE]Les noms de commande peuvent être écrits en majuscules ou en minuscules, contrairement aux noms de champ et aux données.

#### Trier

Syntaxe :

	sort field1 asc|desc, field2 asc|desc, …

Trie les résultats en fonction de champs particuliers. Le préfixe asc/desc est facultatif. S’il est omis, l'ordre de tri « asc » (croissant) est supposé. Si une requête n'utilise pas la commande **Sort** explicitement, Sort **TimeGenerated** desc est le comportement par défaut, et elle retourne toujours les résultats les plus récents en premier.

#### Top/Limit

Syntaxe :

	top number


	limit number
Limite la réponse aux N premiers résultats.

Exemple :

	Type:Alert errors detected | top 10

Retourne les 10 premiers résultats de correspondance.

#### Skip

Syntaxe :

	skip number

Ignore le nombre de résultats répertoriés.

Exemple :

	Type:Alert errors detected | top 10 | skip 200

Retourne les 10 premiers résultats correspondants en commençant au résultat 200.

#### Sélectionnez

Syntaxe :

	select field1, field2, ...

Limite les résultats aux champs que vous choisissez.

Exemple :

	Type:Alert errors detected | select Name, Severity

Limite les champs de résultats retournés à **Name** et **Severity**.

#### Measure

La commande **measure** est utilisée pour appliquer des fonctions statistiques pour les résultats de recherche bruts. Cela est très utile pour obtenir un affichage *groupé* des données. Lorsque vous utilisez la commande **measure**, Operational Insights affiche un tableau avec des résultats agrégés.

Syntaxe :

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

Agrège les résultats par valeur de **groupField** et calcule les valeurs de mesure agrégées à l'aide de **aggregatedField**.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Fonction statistique de mesure </th> <th>Description </th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>Nom de la fonction d'agrégation (ne respecte pas la casse). Les fonctions d’agrégation suivantes sont prises en charge :</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>Champ en cours d’agrégation. Ce champ est facultatif pour la fonction d'agrégation COUNT, mais il doit être un champ numérique existant pour SUM, MAX, MIN, AVG ou STDDEV.</p> </td> </tr> <tr> <td> <p><em>fieldAlias</em> </p> </td> <td> <p>Alias (facultatif) pour la valeur agrégée calculée. S’il n’est pas indiqué, le nom du champ sera <em>AggregatedValue.</em></p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>Nom du champ groupant le jeu de résultats. </p> </td> </tr> <tr> <td> <p><em>Interval</em> </p> </td> <td> <p>Intervalle de temps au format : </p> <p><em>nnnNAME</em> </p> <p></p> <p>où : </p> <p>nnn est le nombre entier positif</p> <p><em>NAME</em> est le nom de l’intervalle</p> <p>Les noms d’intervalle pris en charge sont les suivants (respectent la casse) : </p> <ul> <li class="unordered">MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S]<br><br></li> <li class="unordered">MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



L'option d’intervalle ne peut être utilisée que dans des champs de groupe Date/Heure (tels que **TimeGenerated** et **TimeCreated**). Actuellement, elle n'est pas appliquée par le service, mais un champ sans la valeur Date/Heure qui est transmis au serveur principal entraîne une erreur d'exécution. Lorsque le schéma de validation est implémenté, l’API de service rejette les requêtes qui utilisent des champs sans la valeur Date/Heure pour l’agrégation d’intervalles. L’implémentation actuelle de **Measure** prend en charge le regroupement d’intervalles pour la fonction d’agrégation **Count** uniquement.

Si la clause BY est omise mais un intervalle est spécifié (comme seconde syntaxe), le champ **TimeGenerated** est supposé par défaut.

Exemples :

**Exemple 1**

	Type:Alert | measure count() as Count by ObjectId

*Explication*

Groupe les alertes par **ObjectID** et calcule le nombre d'alertes pour chaque groupe. La valeur d'agrégation est retournée en tant que champ (alias) **Count**.

**Exemple 2**

	Type:Alert | measure count() interval 1HOUR

*Explication*

Groupe les alertes par intervalles d'une heure à l'aide du champ **TimeGenerated** et retourne le nombre d'alertes dans chaque intervalle.

**Exemple 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Explication*

Comme pour l'exemple précédent, mais avec un alias de champ agrégé (** AlertsPerHour **).

**Exemple 4**

	* | measure count() by TimeCreated interval 5DAYS

*Explication*

Groupe les résultats par intervalles de 5 jours à l'aide du champ **TimeCreated** et retourne le nombre de résultats dans chaque intervalle.

**Exemple 5**

	Type:Alert | measure max(Severity) by WorkflowName

*Explication*

Groupe les alertes par nom de la charge de travail et retourne la valeur de gravité d'alerte maximum pour chaque flux de travail.

**Exemple 6**

	Type:Alert | measure min(Severity) by WorkflowName

*Explication*

Comme pour l'exemple précédent, mais avec la fonction agrégée **Min**.

**Exemple 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*Explication*

Groupe PerfHourly par ID d’objet et calcule la moyenne.

**Exemple 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*Explication*

Comme pour l'exemple précédent, mais utilise **Sum**.

**Exemple 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*Explication*

Comme pour l'exemple précédent, mais utilise **STDDEV**.

**Exemple 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Explication*

Obtient les cinq premiers flux de travail avec le nombre maximal d'alertes.

#### Where

Syntaxe :

**where** AggregatedValue>20

Peut être utilisée uniquement après une commande **Measure** pour filtrer davantage les résultats agrégés que la fonction d’agrégation **Measure** a produits.

Exemples :

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## Référence de champ et de facette de recherche

Lorsque vous utilisez la fonction de recherche pour rechercher des données, les résultats affichent différents champs et facettes. Toutefois, certaines informations que vous verrez ne sont pas très descriptives. Vous pouvez utiliser les informations suivantes pour vous aider à comprendre les résultats.

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>Champ</b></th>
		<th><b>Type de recherche<b></th>
		<th><b>Description</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>Tout</p>
		</td>
		<td>
		<p>Utilisé pour partitionner les données</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>Tout</p>
		</td>
		<td>
		<p>Il est utilisé pour piloter la chronologie et des sélections d’heure (dans la recherche et dans d'autres écrans). Il indique le moment où l'élément de données a été généré (en général sur l'agent). L'heure est exprimée au format ISO et toujours en UTC. Dans le cas de «&#160;types&#160;» basés sur une instrumentation existante (c'est-à-dire les événements dans un journal), il s'agit généralement l’heure réelle à laquelle l'entrée/la ligne/l’enregistrement a été enregistré. Pour d’autres types produits via des packs d’administration ou dans le cloud (c'est-à-dire les recommandations, les alertes, les agents de mise à jour, etc.) il s’agit de l'heure à laquelle ce nouvel élément de données associé à un instantané de configuration quelconque a été collecté, ou de l’heure à laquelle une recommandation/alerte a été générée en fonction de celui-ci.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventID</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>ID d'événement dans le journal des événements Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>Journal des événements où l'événement a été enregistré par Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>Critique, avertissement, information ou réussite</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>Valeur numérique indiquant le niveau d’événement&#160;: critique, avertissement, information ou réussite (utilisez la valeur EventLevelName pour des requêtes plus faciles et mieux lisibles)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>Tout</p>
		</td>
		<td>
		<p>Origine des données (en mode «&#160;attachées&#160;» au service, c'est-à-dire Operations Manager, Operational Insights (= les données sont générées dans le cloud), Azure Storage (données provenant de Windows Azure Diagnostic), etc.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nom d'objet de performances Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Noms d'instance du compteur de performances Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CounteName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Noms du compteur de performances Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nom d’affichage de l'objet ciblé par une règle de collecte des performances dans Operations Manager, ou de l'objet découvert par Operational Insights, ou pour lequel une alerte a été générée</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nom d'affichage du parent du parent (dans une relation d'hébergement double&#160;: c'est-à-dire SqlDatabase hébergé par SqlInstance, hébergé par un ordinateur Windows) de l'objet ciblé par une règle de collecte des performances dans Operations Manager, ou de l'objet découvert par Operational Insights, ou pour lequel une alerte a été générée</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Ordinateur</p>
		</td>
		<td>
		<p>La plupart des types </p>
		</td>
		<td>
		<p>Nom de l'ordinateur auquel appartiennent les données</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Nom de l'ordinateur auquel appartiennent les données (identique à «&#160;Computer&#160;»)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Le classement d’état de la menace est une représentation numérique de l'état de la menace. Comme pour les codes de réponse HTTP, nous avons laissé des intervalles entre les nombres (c'est pourquoi le niveau qui indique aucune menace est&#160;150 et non 100&#160;ou&#160;0). Cela nous permet d’avoir de l’espace pour ajouter de nouveaux états. En cas de cumul de l'état de la menace et de l'état de la protection, nous souhaitons montrer l’état le plus grave qu’a connu l’ordinateur sur une période sélectionnée. Nous utilisons des numéros pour classer les différents états afin que nous puissions rechercher l’enregistrement avec le nombre le plus élevé.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Description de ThreatStatus, mappe&#160;1:1 avec ThreatStatusRank</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Produit anti-programme malveillant qui est détecté sur l'ordinateur&#160;: aucun, outil de suppression de logiciels malveillants de Microsoft, Forefront, etc.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus, RequiredUpdate</p>
		</td>
		<td>
		<p>ID du service de contrôle d'intégrité de l'agent de cet ordinateur</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>La plupart des types </p>
		</td>
		<td>
		<p>ID du service de contrôle d'intégrité de l'agent de cet ordinateur</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>La plupart des types </p>
		</td>
		<td>
		<p>Nom du groupe d'administration pour les agents liés à Operations Manager&#160;; dans le cas contraire, il sera Null/vide</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>Type (comme «&#160;type&#160;» ou «&#160;classe&#160;» dans le pack d’administration d’Operations Manager) pour cet objet découvert avec l'évaluation de configuration d’Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Nom de la mise à jour qui a été trouvée mais pas installée</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Date à laquelle la mise à jour a été publiée sur Microsoft Update.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Serveur</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Nom de l'ordinateur auquel appartiennent les données (identique à «&#160;Computer&#160;»)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Produit</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Produit auquel s’applique la mise à jour</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Type de mise à jour (correctif cumulatif de mise à jour, Service Pack, etc.)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>ID d'article de la base de connaissances qui décrit cette meilleure pratique ou mise à jour</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Nom de la règle ou du moniteur qui a généré l'alerte</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Niveau de gravité</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Gravité de l'alerte</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Priorité</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Priorité de l'alerte</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Cette alerte est-elle générée par une analyse (true) ou une règle (false)&#160;?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML avec les paramètres de l'alerte d’Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Context</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML avec le «&#160;contexte&#160;» de l'alerte d’Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Charge de travail</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Technologie ou «&#160;charge de travail&#160;» à laquelle l’alerte fait référence </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>Recommandation</p>
		</td>
		<td>
		<p>Technologie ou «&#160;charge de travail&#160;» à laquelle la recommandation fait référence</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Description</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Description de l'alerte (en bref)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Nombre de jours (par rapport à la valeur «&#160;TimeGenerated&#160;» de cet enregistrement) auquel remonte la dernière installation par l’agent d’une mise à jour quelconque provenant de WSUS ou de Microsoft Update.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Catégorisation, basée sur la valeur DaysSinceLastUpdate, en «&#160;intervalles de planification&#160;» du temps écoulé depuis qu’un ordinateur a installé pour la dernière fois une mise à jour quelconque provenant de WSUS ou de Microsoft Update.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Activation ou désactivation de la vérification de mise à jour automatique sur cet agent.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Configuration de la vérification de mise à jour automatique&#160;: téléchargement et installation, téléchargement uniquement ou vérification uniquement.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Numéro de version de l'agent Microsoft&#160;Update</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Quel serveur WSUS cet agent de mise à jour cible-t-il&#160;?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion:</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Version du système d'exploitation sur laquelle l’agent de cette mise à jour est exécuté</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Nom</p>
		</td>
		<td>
		<p>Recommendation, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nom ou titre de la recommandation, ou nom de la propriété de l'évaluation de configuration d’Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Valeur</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Valeur d'une propriété de l'évaluation de configuration d’Operational Insights</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>Recommandation</p>
		</td>
		<td>
		<p>URL menant à l’article de la base de connaissances qui décrit cette meilleure pratique ou mise à jour</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>Recommandation</p>
		</td>
		<td>
		<p>Les recommandations font partie des quelques types dont les enregistrements sont «&#160;mis à jour&#160;» au lieu d’être simplement ajoutés à l'index de recherche. Cet état change si la recommandation est active/ouverte ou si Operational Insights détecte qu'elle a été résolue</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>Description rendue (texte réutilisé avec des paramètres remplis) d'un événement Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>XML avec les paramètres de la section «&#160;données&#160;» d'un événement Windows (tel qu’elle s’affiche dans l'observateur d'événements)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>XML avec toute la section «&#160;données&#160;» d'un événement Windows (tel qu’elle s’affiche dans l'observateur d'événements)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Source</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>Source du journal des événements ayant généré l'événement</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>Catégorie de l'événement, extraite directement du journal des événements Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Nom d’utilisateur</p>
		</td>
		<td>
		<p>Événement</p>
		</td>
		<td>
		<p>Nom d'utilisateur de l'événement Windows (en général, NT AUTHORITY\LOCALSYSTEM)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valeur moyenne de l'agrégation de toutes les heures d'un compteur de performance </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Min</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valeur minimale de l'intervalle horaire d'un agrégat de toutes les heures du compteur de performances</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Max</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valeur maximale de l'intervalle horaire d'un agrégat de toutes les heures du compteur de performances</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>La valeur du 95ème centile de l’intervalle horaire d’un agrégat de toutes les heures du compteur de performance.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Combien d’exemples de compteurs de performances «&#160;bruts&#160;» ont été utilisés pour produire cet enregistrement d'agrégation toutes les heures</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Menace</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Nom du programme malveillant trouvé</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Compte de stockage Azure à partir duquel le journal a été lu</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ID de déploiement Azure du service cloud auquel appartient le journal</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Rôle</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Rôle du service cloud Azure auquel appartient le journal</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Instance du rôle Azure auquel appartient le journal</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Site web&#160;IIS auquel appartient le journal (notation métabase)&#160;; le champ s-sitename dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Champ s-computername dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Adresse IP du serveur à laquelle la requête HTTP a été envoyée. Champ s-ip dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Méthode HTTP (GET, POST/etc.) utilisée par le client dans la requête HTTP. La méthode cs-methode dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Adresse IP cliente d’où provenait la requête HTTP. Le champ c-ip dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Agent utilisateur HTTP déclaré par le client (navigateur ou autre). Le champ cs-user-agent dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Code d'état HTTP (200/403/500 /, etc.) retourné par le serveur au client. Le champ cs-methode dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>La durée (en millisecondes) nécessaire à la requête pour se terminer. Le champ timetaken dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>URI relatif (sans adresse d’hôte, c’est-à-dire «&#160;/search&#160;») qui a été demandé. Le champ cs-uristem dans le journal d'origine</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Requête d’URI. Les requêtes d’URI sont nécessaires uniquement pour les pages dynamiques, telles que des pages ASP. Par conséquent, ce champ contient souvent un trait d’union pour les pages statiques.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Port du serveur auquel la demande HTTP a été envoyée (et qui est écouté par IIS puisqu’il l’a récupéré)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Nom d'utilisateur authentifié, si la demande est authentifiée et n’est pas anonyme</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Version du protocole HTTP utilisée dans la demande (c’est-à-dire «&#160;HTTP/1.1&#160;»)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Informations sur le cookie</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Dernier site visité par l’utilisateur. Ce site a fourni un lien vers le site actuel. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>En-tête d'hôte (par exemple, «&#160;www.mysite.com&#160;») qui a été demandée</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Code d'erreur du sous-état</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Code d’état Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Octets envoyés dans la demande du client au serveur</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Octets retournés dans la réponse du serveur au client</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Type de modification (services Windows, logiciels, etc.)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Catégorie de la modification (modifié, ajouté, supprimé)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Type de logiciel (mise à jour, application)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nom du logiciel (applicable uniquement aux modifications de logiciels)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Éditeur </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Fournisseur qui publie le logiciel (applicable uniquement aux modifications de logiciels)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Type de modification appliquée à un service Windows (état, type d’état, chemin d’accès, compte de service) - applicable uniquement aux modifications de service Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nom d’affichage du service qui a été modifié</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nom du service qui a été modifié</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nouvel état (actuel) du service</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Précédent état connu du service (applicable uniquement si l'état du service a été modifié)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Type de démarrage du service</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Dernier type de démarrage du service (applicable uniquement si le type de démarrage du service a été modifié)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Compte de service</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Compte de service précédent (applicable uniquement si le compte de service a été modifié)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Chemin d'accès au fichier exécutable du service Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Chemin d'accès précédent de l'exécutable du service Windows (applicable uniquement s’il a été modifié)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Description du service</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Précédent </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>État précédent de ce logiciel (installé, non installé, version précédente)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Current</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Dernier état de ce logiciel (installé, non installé, version actuelle)</p>
		</td>
	</tr>
</table>

## Billets de blog - Cas d’utilisation de Recherche
- [Recherche de journaux IIS W3C dans Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Surveillance des échecs de sauvegarde de SQL avec la Recherche et les tableaux de bord Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [Équivalents de recherches OpInsights des règles d’alertes d’événements de pack d’administration IIS](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [Collection de requêtes de recherche Operational Insights utiles](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## Autres ressources
Stefan Roth a créé un aide-mémoire de recherche pratique. Découvrez son [blog](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/) pour en savoir plus et télécharger son aide-mémoire.

<!---HONumber=July15_HO4-->