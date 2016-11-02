<properties
   pageTitle="Gestion des mesures avec Azure Service Fabric Cluster Resource Manager | Microsoft Azure"
   description="Découvrir comment configurer et utiliser les mesures dans Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures
Les mesures sont le terme générique utilisé dans Service Fabric pour les ressources qui intéressent vos services et qui sont fournies par les nœuds dans le cluster. En règle générale, une mesure est ce que vous souhaitez gérer afin de traiter les performances de vos services.

Il peut s’agir d’éléments tels que la mémoire, un disque, l’utilisation du processeur, qui sont tous des exemples de mesures. Il s’agit de mesures physiques, des ressources qui correspondent aux ressources physiques sur le nœud qui doivent être gérées. Les mesures peuvent également être (et sont généralement) des mesures logiques, des ressources telles que « MyWorkQueueDepth » qui sont définies par l’application et qui correspondent à un certain niveau de consommation de ressources (mais que l’application ne connaît pas vraiment ou ne sait pas mesurer). Nous avons remarqué que la plupart des mesures utilisées sont des mesures logiques. Il existe diverses raisons à cela, mais on peut essentiellement expliquer cette tendance par le simple fait que la plupart de nos clients écrivent aujourd’hui leurs services dans un code managé et qu’il est assez difficile de mesurer et rendre compte de la consommation des ressources physiques réelles à partir d’une instance de service sans état ou d’un objet de réplica de service avec état. La complexité liée à la création de rapports retraçant vos propres mesures explique également pourquoi nous proposons des mesures par défaut immédiatement prêtes à l’emploi.

## Mesures par défaut
Supposons par exemple que vous souhaitez simplement commencer et que vous ne savez pas quelles ressources vous allez utiliser, ni même quelles sont les ressources importantes pour vous. Vous allez donc implémenter, puis créer vos services sans spécifier de mesures. Pas de problème ! Nous allons choisir des mesures à votre place. Les mesures par défaut que nous choisissons aujourd’hui si vous n’en spécifiez aucune sont appelées PrimaryCount, ReplicaCount et (plus vague, certes) Count. Le tableau ci-dessous indique la quantité de charge associée à chaque objet de service pour chacune de ces mesures :

| Mesure | Charge de l’instance sans état |	Charge secondaire avec état |	Charge principale avec état |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Nombre |	1 |	1 |	1 |

OK, donc avec ces mesures par défaut, qu’obtenez-vous ? Il s’avère que pour les charges de travail de base, vous obtenez une bonne répartition des tâches. Dans l’exemple ci-dessous, voyons ce qui se passe lorsque nous créons un service avec état avec trois partitions et une taille de jeu de réplica cible de trois, et également un service sans état unique avec un nombre d’instances de trois. Vous obtiendrez quelque chose comme ceci !

![Disposition du cluster avec des mesures par défaut][Image1]

Dans cet exemple, nous constatons :
-	Les réplicas principaux du service avec état ne sont pas empilés sur un nœud unique
-	Les réplicas pour la même partition ne sont pas sur le même nœud
-	Le nombre total de réplicas principaux et secondaires est bien distribué dans le cluster
-	Le nombre total d’objets de service (avec et sans état) est alloué de façon égale sur chaque nœud

C’est parfait !

Tout cela fonctionne très bien jusqu’à ce que vous vous posiez la question suivante : quelle est la probabilité que le schéma de partitionnement que vous avez choisi garantisse dans le temps une utilisation des ressources parfaitement uniforme par l’ensemble des partitions ? De plus, quelle est la probabilité que la charge d’un service donné reste constante au fil du temps, ou même simplement identique à celle observée actuellement ? À vrai dire, la probabilité que tous les réplicas soient équivalents est relativement faible dans le cas d’une charge de travail importante. Autrement dit, si vous souhaitez tirer le meilleur parti de votre cluster, la solution optimale est certainement d’utiliser des mesures personnalisées.

Dans la pratique, vous pourriez tout à fait utiliser simplement les mesures par défaut, mais cela signifierait que l’utilisation de votre cluster est inférieure à ce que vous souhaitez (étant donné que la création de rapports n’est pas adaptative et part du principe que tous les éléments sont équivalents). Dans le pire des cas, cela pourrait également entraîner des surplanifications de nœuds, et par conséquent, des problèmes de performances. Des mesures personnalisées et des rapports de charge dynamique, qui seront abordées dans la suite de cet article, sont mieux adaptés.

## Mesures personnalisées
Nous avons déjà vu qu’il peut y avoir à la fois des mesures physiques et des mesures logiques, et que les utilisateurs peuvent définir leurs propres mesures. Parfait ! Mais comment ? De manière assez simple, à vrai dire ! Il suffit de configurer la mesure et la charge initiale par défaut lors de la création du service, et voilà ! N’importe quel jeu de mesures et de valeurs par défaut représentant la consommation du service attendue peut être configuré sur la base d’une instance de service nommée lorsque vous créez le service.

Notez que lorsque vous commencez à définir des mesures personnalisées, vous devez ajouter explicitement les mesures par défaut si vous voulez les utiliser pour équilibrer également votre service. Nous tenons en effet à ce que vous précisiez clairement la relation entre les mesures par défaut et vos mesures personnalisées ; peut-être accordez-vous plus d’importance à la consommation de la mémoire ou au paramètre WorkQueueDepth qu’à la distribution principale.

Supposons que vous souhaitez configurer un service qui signale une mesure appelée « Memory » (mémoire) (en plus des mesures par défaut). Pour la mémoire, nous allons dire que vous avez effectué des mesures de base et que vous savez que normalement un réplica principal de ce service occupe 20 Mo de mémoire, tandis que les réplicas secondaires de ce même service occuperont 5 Mo. Vous savez que la mémoire est la mesure la plus importante en termes de gestion des performances de ce service spécifique, mais vous souhaitez que les réplicas principaux soient équilibrés afin que la perte d’un nœud ou d’un domaine d’erreur n’entraîne pas de nombre exagéré de réplicas principaux. Autrement, choisissez les valeurs par défaut.

Voici ce que vous feriez :

Code :

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription memoryMetric = new StatefulServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Rappel : si vous voulez simplement utiliser les mesures par défaut, vous n’avez pas du tout besoin d’utiliser la collection de mesures ou d’intervenir.)

Maintenant que nous vous avons montré comment définir vos propres mesures, examinons les différentes propriétés possibles pour les mesures. Nous vous les avons déjà présentées, mais nous allons maintenant vous détailler leur signification. Une mesure peut avoir quatre propriétés différentes :

-	Metric Name : c’est le nom de la mesure. Il s’agit d’un identificateur unique pour la mesure au sein du cluster du point de vue de Resource Manager.
- Charge par défaut : la charge par défaut est représentée différemment selon que le service est avec ou sans état.
  - Pour les services sans état, chaque mesure a une propriété unique nommée Charge par défaut
  - Pour des services avec état, vous définissez les éléments suivants :
    -	PrimaryDefaultLoad : charge par défaut qui sera exercée par ce service pour cette mesure en tant que réplica principal.
    -	SecondaryDefaultLoad : charge par défaut qui sera exercée par ce service pour cette mesure en tant que réplica secondaire.
-	Weight : importance de la mesure par rapport aux autres mesures configurées pour ce service.

## charger
La charge est la notion générale correspondant à la quantité d’une métrique donnée qui est consommée par une instance ou un réplica de service sur un nœud donné.

## Charge par défaut
La charge par défaut est la quantité de charge supposée par Cluster Resource Manager qui sera consommée par chaque instance ou réplica de ce service jusqu’à la réception de mises à jour de ces instances ou réplicas de service. Pour les services plus simples, c’est une définition statique qui n’est jamais mise à jour dynamiquement et qui, par conséquent, sera utilisée pendant toute la durée de vie du service. Cela fonctionne parfaitement pour une planification de capacité simple, car c’est exactement ce que nous avons l’habitude de faire : dédier certaines ressources à certaines charges de travail, mais l’avantage est qu’au moins maintenant nous fonctionnons dans un état d’esprit de microservices où les ressources ne sont pas réellement statiquement affectées à des charges de travail particulières et où les utilisateurs ne font pas partie de la boucle de prise de décision.

Nous autorisons les services avec état à spécifier la charge par défaut à la fois pour les réplicas principaux et secondaires. En réalité, pour de nombreux services, ces nombres sont différents en raison des différentes charges de travail exécutées par les réplicas principaux et secondaires, et puisque les réplicas principaux assument souvent les opérations de lecture et d’écriture (ainsi que la plupart des tâches de calcul), la charge par défaut d’un réplica principal est supérieure à celle des réplicas secondaires.

Maintenant, disons que vous avez exécuté votre service pendant un certain temps et que vous avez remarqué que certains réplicas ou instances de votre service consomment beaucoup plus de ressources que d’autres ou que leur consommation varie dans le temps. Ils sont peut-être associés à un client spécifique, ou ils correspondent simplement à des charges de travail qui varient au cours de la journée, telles que le trafic de messagerie, les appels téléphoniques ou les actions. De toute façon, vous remarquez que vous ne pouvez utiliser aucun « nombre unique » pour la charge sans désactivation par un nombre important pendant au moins un intervalle de temps. Vous remarquez également que la « désactivation » dans l’estimation fait que Cluster Resource Manager a tendance à sur ou sous-allouer des ressources à votre service, ce qui se traduit par des nœuds qui sont sur ou sous-utilisés.

Que faire, alors ? Eh bien, votre service pourrait signaler la charge à la volée !

## Charge dynamique
Les rapports de charge dynamique permettent aux réplicas ou aux instances d’ajuster leur allocation/utilisation de mesures signalée dans le cluster pendant toute leur durée de vie. Un réplica ou une instance de service qui était à froid et n’effectuait aucun travail signale généralement qu’il/elle utilisait peu d’une mesure spécifique, tandis que les réplicas ou les instances occupés signalent qu’ils en utilisent plus. Ce niveau général d’attrition du cluster permet de réorganiser les réplicas et les instances de service dans le cluster à la volée afin de garantir qu’ils obtiennent les ressources dont ils ont besoin, ou plutôt que les services occupés sont capables de réclamer des ressources d’autres réplicas ou instances actuellement à froid ou effectuant moins de travail. Le signalement de charge à la volée peut être effectué par le biais de la méthode ReportLoad disponible sur la ServicePartition, et disponible en tant que propriété sur le StatefulService de base ou la classe StatelessService par le biais du modèle de programmation Reliable Services. Au sein de votre service, le code ressemblerait à ceci :

Code :

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("metric1", 42) });
```

Les réplicas ou les instances de service peuvent signaler la charge uniquement pour les mesures qu’ils sont autorisés à utiliser. La liste de mesures est définie lors de la création de chaque service et peut être mise à jour ultérieurement. Si un réplica ou une instance de service tente de signaler la charge pour une mesure non autorisée par leur configuration, Service Fabric consigne l’état mais l’ignore, ce qui signifie que nous ne l’utiliserons pas pour le calcul ou le signalement de l’état du cluster. C’est parfait car cela permet une meilleure expérimentation. Le code peut mesurer et signaler tout élément, s’il sait le faire, et l’opérateur peut configurer, modifier et mettre à jour les règles d’équilibrage des ressources pour ce service à la volée sans avoir à modifier le code. Il peut s’agir, par exemple, de la désactivation d’une mesure ayant un état incorrect, de la reconfiguration du poids des mesures en fonction du comportement ou de l’activation d’une nouvelle mesure uniquement une fois que le code a déjà été déployé et validé par le biais d’autres mécanismes.

## Mélange des valeurs de charge par défaut et des rapports de charge dynamique
Est-il judicieux d’avoir une charge par défaut spécifiée pour un service qui va signaler la charge dynamiquement ? Absolument ! Dans ce cas, la charge par défaut représente une estimation jusqu’à ce que les vrais rapports commencent à parvenir du réplica ou de l’instance de service réel(le). Et cela permet au Cluster Resource Manager de travailler avec des données. La charge par défaut lui permet de placer les instances de service ou les réplicas à un emplacement adéquat dès le début. Lorsqu’aucune information n’est fournie pour le chargement par défaut, le positionnement des services est effectivement aléatoire lors de la création. Si des charges sont modifiées ultérieurement, le Cluster Resource Manager doit certainement déplacer des éléments.

Reprenons l’exemple précédent et voyons ce qui se passe lorsque nous ajoutons une charge personnalisée, puis, une fois le service créé, lorsqu’il est mis à jour dynamiquement. Dans cet exemple, nous allons utiliser « Memory » et supposer que nous avons initialement créé le service avec état avec la commande suivante :

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Nous avons parlé de cette syntaxe précédemment (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad), mais nous détaillerons plus tard la signification de la valeur spécifique de Weight.

Voyons quelle pourrait être une disposition de cluster possible :

![Équilibre de cluster avec des mesures par défaut et des mesures personnalisées][Image2]

Informations importantes à noter :

-	Puisque les réplicas ou les instances utilisent la charge par défaut du service jusqu’à ce qu’ils signalent leur propre charge, nous savons que les réplicas à l’intérieur de la partition 1 du service avec état n’ont pas signalé de charge propre
-	Les réplicas secondaires dans une partition peuvent avoir leur propre charge
-	Dans l’ensemble, les mesures semblent assez bonnes, avec une différence entre les charges maximale et minimale sur un nœud (pour la mémoire, la mesure personnalisée qui nous intéresse le plus, comme dit précédemment) d’un facteur de 1,75 seulement (le nœud ayant le plus de charge de mémoire est N3, le moins de charge N2, et 28/16 = 1,75), donc un assez bon équilibre !

Il reste certaines choses à expliquer :

-	Quel élément a permis de déterminer qu’un rapport de 1,75 était raisonnable ou non ? Comment savoir si c’est suffisant ou s’il reste d’autres actions à effectuer ?
-	À quel moment l’équilibrage se produit-il ?
-	Qu’est-ce que cela signifie que Memory (mémoire) était pondérée comme étant « High » (haute) ?

## Poids des mesures
Les poids des mesures permettent à deux services différents de signaler les mêmes mesures, mais en affichant l’importance de l’équilibrage des mesures de manière différente. Par exemple, prenez un moteur d’analyse en mémoire et une base de données persistante. Tous deux s’intéressent probablement à la mesure « Memory » (Mémoire), mais le service en mémoire n’accorde sans doute guère d’importance à la mesure « Disk » (Disque). En effet, il se peut qu’il en consomme un peu, mais ce n’est pas un élément critique pour les performances du service, et il ne le signale probablement même pas. La possibilité de suivre les mêmes mesures sur différents services est très utile, car c’est ce qui permet à Cluster Resource Manager d’effectuer le suivi de la consommation réelle dans le cluster et de s’assurer que les nœuds ne dépassent pas la capacité, etc.

Les poids des mesures permettent également à Cluster Resource Manager de prendre des décisions réelles sur la façon d’équilibrer le cluster lorsqu’il n’existe aucune réponse adaptée (la plupart du temps). Les mesures peuvent avoir quatre niveaux de poids : Zero, Low, Medium et High (zéro, faible, moyen et haut). Une mesure avec un poids de Zero ne contribue en rien quant à la décision d’équilibrage des éléments, mais sa charge contribue toujours à des éléments tels que la mesure de capacité.

Le véritable impact des différents poids de mesure dans le cluster est que les dispositions des services sont fondamentalement différentes puisque Cluster Resource Manager a globalement intégré le fait que certaines mesures sont plus importantes que d’autres. En effet, il sait que, lorsque les mesures qui ont différents poids sont en conflit avec d’autres, le Cluster Resource Manager peut préférer des solutions qui équilibrent mieux les mesures à pondération supérieure.

Prenons un exemple simple de quelques rapports de charge et regardons de quelle façon différents poids de mesure peuvent entraîner des allocations différentes dans le cluster. Dans cet exemple, nous voyons que le fait de basculer le poids relatif des mesures entraîne Resource Manager à préférer certaines solutions en créant différentes dispositions des services.

![Exemple de poids de mesure et impact sur les solutions d’équilibrage][Image3]

Dans cet exemple, nous avons quatre services différents qui signalent tous des valeurs différentes pour deux mesures, A et B. Dans un cas, tous les services définissent la mesure A comme la plus importante (Weight = High) et la mesure B comme relativement peu importante (Weight = Low). En effet, nous constatons que Cluster Resource Manager place les services de façon à ce que la mesure A soit mieux équilibrée (écart standard plus faible) que la mesure B. Dans le deuxième cas, nous avons inversé les poids des mesures et nous voyons que Cluster Resource Manager échangerait probablement les services A et B afin de proposer une allocation où la mesure B (MetricB) est mieux équilibrée que la mesure A (MetricA).

### Poids globaux des mesures
Si le service A (ServiceA) définit la mesure A (MetricA) comme étant la plus importante et que le service B (ServiceB) n’a aucune préférence, quel est le poids réel finalement utilisé ?

Il existe en fait deux poids dont nous effectuons le suivi pour chaque mesure : le poids défini par le service lui-même et le poids moyen global sur tous les services intéressés par cette mesure. Nous utilisons les deux poids pour calculer les scores des solutions que nous générons, puisqu’il est important de s’assurer qu’un service est équilibré en fonction de ses propres priorités, mais également que l’ensemble du cluster est correctement alloué.

Que se passerait-il si nous n’accordions aucune importance à l’équilibre global et local ? Eh bien, il est facile de créer des solutions qui sont globalement équilibrées mais qui entraînent un équilibre et une allocation des ressources très pauvres pour les services individuels. Dans l’exemple ci-dessous, étudions les mesures par défaut PrimaryCount, ReplicaCount et Count configurées pour un service avec état, et observons ce qui se passe lorsque nous n’accordons d’importance qu’à l’équilibre global :

![Impact d’une solution globale uniquement][Image4]

Dans l’exemple ci-dessus où nous avons uniquement regardé l’équilibre global, le cluster dans son ensemble est bien équilibré. Tous les nœuds ont le même nombre de réplicas principaux et de réplicas totaux. Cependant, si vous examinez l’impact réel de cette allocation, la situation n’est pas si bonne : la perte d’un nœud a un impact disproportionné sur une charge de travail, car tous ses réplicas principaux sont également perdus. Par exemple, si nous perdions le premier nœud. Si cela se produisait, les trois réplicas principaux des trois différentes partitions du service Circle seraient tous perdus simultanément. À l’inverse, les partitions des deux autres services (Triangle et Hexagon) perdent un réplica, mais cela ne crée aucune interruption (à part celle de devoir récupérer le réplica perdu).

Dans l’exemple du bas, nous avons distribué les réplicas basés à la fois sur l’équilibre global et l’équilibre par service. Lors du calcul du score de la solution, nous donnons la majorité du poids à la solution globale, mais une partie (configurable) permet de s’assurer que les services sont également équilibrés entre eux autant que possible. Par conséquent, si nous perdions le même premier nœud, nous constatons que la perte des réplicas principaux (et secondaires) est répartie sur toutes les partitions de tous les services et que l’impact est le même pour chacun.

Tenant compte des poids des mesures, l’équilibre global est calculé en fonction de la moyenne des poids des mesures configurée pour chaque service. Nous équilibrons un service par rapport à ses propres poids de mesure définis.

## Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
- La définition des mesures de défragmentation est une façon de consolider la charge sur les nœuds au lieu de la répartir. Pour savoir comment configurer la défragmentation, reportez-vous à [cet article](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Commencez au début pour [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Le coût du déplacement est une façon de signaler à Cluster Resource Manager que certains services sont plus coûteux à déplacer que d’autres. Pour en savoir plus sur le coût du déplacement, reportez-vous à [cet article](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0824_2016-->