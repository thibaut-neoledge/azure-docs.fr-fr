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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Gestion de la consommation des ressources et des charges dans Service Fabric à l’aide de mesures
Les mesures sont le terme générique utilisé dans Service Fabric pour les ressources qui intéressent vos services. En règle générale, une mesure est tout ce que vous souhaitez gérer en termes de ressource afin de traiter les performances de vos services.

Dans tous les exemples ci-dessus, nous avons continué à faire référence aux mesures de façon implicite ; les éléments tels que la mémoire, le disque, l’utilisation du processeur sont tous des exemples de mesures. Il s’agit de mesures physiques, des ressources qui correspondent aux ressources physiques sur le nœud qui doivent être gérées. Les mesures peuvent également être des mesures logiques, des ressources telles que « MyWorkQueueDepth » qui sont définies par l’application et qui correspondent à un certain niveau de consommation de ressources (mais où l’application ne les connaît pas vraiment ou ne sait pas les mesurer).

## Mesures par défaut
Supposons par exemple que vous souhaitez simplement commencer et que vous ne savez pas quelles ressources vous allez utiliser, ni même quelles sont les ressources importantes pour vous. Vous allez donc implémenter, puis créer vos services sans spécifier de mesures. Pas de problème ! Nous allons choisir des mesures à votre place. Les mesures par défaut que nous choisissons aujourd’hui si vous n’en spécifiez aucune sont appelées PrimaryCount, ReplicaCount et (plus vague, certes) Count. Le tableau ci-dessous indique la charge de chacune de ces mesures que nous suivons par défaut :

| Mesure | Charge de l’instance sans état |	Charge secondaire avec état |	Charge principale avec état |
|--------|--------------------------|-------------------------|-----------------------|
| PrimaryCount | 0 |	0 |	1 |
| ReplicaCount | 0 | 1 | 1 |
| Nombre |	1 |	1 |	1 |

OK, donc avec ces mesures par défaut, qu’obtenez-vous ? Il s’avère que pour les charges de travail de base, vous obtenez une bonne répartition des tâches. Dans l’exemple ci-dessous, voyons ce qui se passe lorsque nous créons un service avec état avec trois partitions et une taille de jeu de réplica cible de trois, et également un service sans état unique avec un nombre d’instances de trois. Vous obtiendrez quelque chose comme ceci !

![Disposition du cluster avec des mesures par défaut][Image1]

Dans cet exemple, nous constatons :
-	Les réplicas principaux du service avec état ne sont pas empilés sur un nœud unique
-	Les réplicas pour la même partition ne sont pas sur le même nœud
-	Le nombre total de réplicas principaux et secondaires est bien distribué dans le cluster
-	Le nombre total d’objets de service (avec et sans état) est alloué de façon égale sur chaque nœud

C’est parfait !

Cela fonctionne très bien jusqu’à ce que vous commenciez à réfléchir : quelle est réellement la probabilité que tous les réplicas principaux de mes différents services présentent vraiment la même charge en ce moment ? De plus, quelle est la probabilité que la charge d’un service donné reste constante au fil du temps ? Il s’avère que pour n’importe quelle charge de travail importante, cette probabilité est plutôt faible.

Dans la pratique, vous pourriez tout à fait utiliser les mesures par défaut ou, du moins, des mesures personnalisées statiques, mais cela signifierait que l’utilisation de votre cluster est inférieure à ce que vous souhaitez (étant donné que la création de rapports n’est pas adaptative) ; dans le pire des cas, cela pourrait également entraîner des surplanifications de nœuds, et par conséquent, des problèmes de performances. Nous pouvons faire mieux avec des mesures personnalisées et des rapports de charge dynamique. Cela nous amène aux sections suivantes : Mesures personnalisées et Charge dynamique.

## Mesures personnalisées
Nous avons déjà vu qu’il peut y avoir à la fois des mesures physiques et des mesures logiques, et que les utilisateurs peuvent définir leurs propres mesures. Mais comment faire ? C’est très simple ! Il suffit de configurer la mesure et la charge initiale par défaut lors de la création du service, et voilà ! N’importe quel jeu de mesures et de valeurs par défaut peut être configuré sur la base d’une instance de service lorsque vous créez le service.

Notez que lorsque vous commencez à définir des mesures personnalisées, vous devez ajouter explicitement les mesures par défaut si vous voulez les utiliser pour équilibrer également votre service. C’est parce que nous voulons que vous précisiez clairement la relation entre les mesures par défaut et vos mesures personnalisées ; vous accordez peut-être plus d’importance à la consommation de la mémoire qu’à la distribution principale, ou vous souhaitez peut-être « mélanger » plusieurs mesures avec les valeurs par défaut.

Supposons que vous souhaitez configurer un service qui signale une mesure appelée « Memory » (mémoire) (en plus des mesures par défaut). Pour la mémoire, nous allons dire que vous avez effectué des mesures de base et que vous savez que normalement un réplica principal de ce service occupe 20 Mo de mémoire, tandis que les réplicas secondaires de ce même service occuperont 5 Mo. Vous savez que la mémoire est la mesure la plus importante en termes de gestion des performances de ce service spécifique, mais vous souhaitez que les réplicas principaux soient équilibrés afin que la perte d’un nœud ou d’un domaine d’erreur n’entraîne pas de nombre exagéré de réplicas principaux. Autrement, choisissez les valeurs par défaut.

Voici ce que vous feriez :

Code :

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.PrimaryDefaultLoad = 20;
memoryMetric.SecondaryDefaultLoad = 5;
memoryMetric.Weight = ServiceLoadMetricWeight.High;

ServiceLoadMetricDescription primaryCountMetric = new ServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

ServiceLoadMetricDescription replicaCountMetric = new ServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

ServiceLoadMetricDescription totalCountMetric = new ServiceLoadMetricDescription();
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

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

(Rappel : si vous voulez simplement utiliser les mesures par défaut, vous n’avez pas du tout besoin de toucher à la collection de mesures.)

Maintenant que nous vous avons montré comment définir vos propres mesures, examinons les différentes propriétés possibles pour les mesures. Nous vous les avons déjà présentées, mais nous allons maintenant vous détailler leur signification. Une mesure peut avoir quatre propriétés différentes :

-	Metric Name : c’est le nom de la mesure. Il s’agit d’un identificateur unique pour la mesure au sein du cluster du point de vue de Resource Manager.
-	PrimaryDefaultLoad : charge par défaut qui sera exercée par ce service pour cette mesure en tant que réplica principal.
-	SecondaryDefaultLoad : charge par défaut qui sera exercée par ce service pour cette mesure en tant que réplica secondaire.
-	Weight : importance de la mesure par rapport aux autres mesures configurées pour ce service.

## Charge
La charge est la notion générale correspondant à la quantité d’une métrique donnée qui est consommée par une instance ou un réplica de service sur un nœud donné.

## Charge par défaut
La charge par défaut est la quantité de charge supposée par Resource Manager qui sera consommée par chaque instance ou réplica de ce service jusqu’à la réception de mises à jour de ces instances ou réplicas de service. Pour les services plus simples, c’est une définition statique qui n’est jamais mise à jour dynamiquement et qui, par conséquent, sera utilisée pendant toute la durée de vie du service. Cela fonctionne parfaitement pour une planification de capacité simple, car c’est exactement ce que nous avons l’habitude de faire : dédier certaines ressources à certaines charges de travail, mais l’avantage est qu’au moins maintenant nous fonctionnons dans un état d’esprit de microservices où les ressources ne sont pas réellement statiquement affectées à des charges de travail particulières et où les utilisateurs ne font pas partie de la boucle de prise de décision.

Nous autorisons les services avec état à spécifier la charge par défaut à la fois pour les réplicas principaux et secondaires. En réalité, pour de nombreux services, ces nombres sont différents en raison des différentes charges de travail exécutées par les réplicas principaux et secondaires, et puisque les réplicas principaux assument souvent les opérations de lecture et d’écriture (ainsi que la plupart des tâches de calcul), la charge par défaut d’un réplica principal est supérieure à celle des réplicas secondaires.

Maintenant, disons que vous avez exécuté votre service pendant un certain temps et que vous avez remarqué que certains réplicas ou instances de votre service consomment beaucoup plus de ressources que d’autres ou que leur consommation varie dans le temps. Peut-être qu’ils sont associés à un client particulier, ou qu’ils correspondent simplement à des charges de travail qui varient au cours de la journée, telles que le trafic de messagerie ou les actions. De toute façon, vous remarquez que vous ne pouvez utiliser aucun « nombre unique » pour la charge sans désactivation par un nombre important. Vous remarquez également que la « désactivation » dans l’estimation initiale entraîne Service Fabric à sur ou sous-allouer des ressources à votre service, ce qui se traduit par des nœuds qui sont sur ou sous-utilisés. Que faire, alors ? Eh bien, votre service pourrait signaler la charge à la volée !

## Charge dynamique
Les rapports de charge dynamique permettent aux réplicas ou aux instances d’ajuster leur allocation/utilisation de mesures signalée dans le cluster pendant toute leur durée de vie. Un réplica ou une instance de service qui était à froid et n’effectuait aucun travail signale généralement qu’il/elle utilisait peu de ressources, tandis que les réplicas ou les instances occupés signalent qu’ils en utilisent plus. Ce niveau général d’attrition du cluster permet de réorganiser les réplicas et les instances de service dans le cluster à la volée afin de garantir que les services et les instances obtiennent les ressources dont ils ont besoin, ou plutôt que les services occupés soient capables de réclamer des ressources d’autres réplicas ou instances actuellement à froid ou effectuant moins de travail. Le signalement de charge à la volée peut être effectué via la méthode ReportLoad, disponible sur la ServicePartition, et disponible en tant que propriété sur le StatefulService de base. Au sein de votre service, le code ressemblerait à ceci :

Code :

```csharp
this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("Memory", 1234), new LoadMetric("Foo", 42) });
```

Les réplicas ou les instances de service peuvent signaler la charge uniquement pour les mesures qu’ils sont autorisés à utiliser. La liste de mesures est définie lors de la création de chaque service. Si un réplica ou une instance de service tente de signaler la charge pour une mesure non autorisée par leur configuration, Service Fabric consigne l’état mais l’ignore, ce qui signifie que nous ne l’utiliserons pas pour le calcul ou le signalement de l’état du cluster. C’est parfait car cela permet une meilleure expérimentation. Le code peut mesurer et signaler tout élément, s’il sait le faire, et l’opérateur peut configurer, modifier et mettre à jour les règles d’équilibrage des ressources pour ce service à la volée sans avoir à modifier le code. Il peut s’agir, par exemple, de la désactivation d’une mesure ayant un état incorrect, de la reconfiguration du poids des mesures en fonction du comportement ou de l’activation d’une nouvelle mesure uniquement une fois que le code a déjà été déployé et validé.

## Mélange des valeurs de charge par défaut et des rapports de charge dynamique
Est-il judicieux d’avoir une charge par défaut spécifiée pour un service qui va signaler la charge dynamiquement ? Absolument ! Dans ce cas, la charge par défaut représente une estimation jusqu’à ce que les vrais rapports commencent à parvenir du réplica ou de l’instance de service réel(le). C’est fantastique car cela fournit à Resource Manager des éléments de travail lors du placement du réplica ou de l’instance au moment de la création. La charge par défaut devient une estimation initiale qui permet à Resource Manager de placer les instances ou les réplicas de service au bon endroit dès le début. Si aucune information n’avait été fournie, le placement aurait effectivement été aléatoire et nous aurions certainement dû déplacer des éléments dès l’arrivée des vrais rapports de charge.

Reprenons l’exemple précédent et voyons ce qui se passe lorsque nous ajoutons une charge personnalisée, puis, une fois le service créé, lorsqu’il est mis à jour dynamiquement. Dans cet exemple, nous allons utiliser « Memory » et supposer que nous avons initialement créé le service avec état avec la commande suivante :

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("Memory,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Nous avons parlé de cette syntaxe précédemment (MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad), mais nous détaillerons plus tard la signification de la valeur spécifique de Weight.

Voyons quelle pourrait être une disposition de cluster possible :

![Équilibre de cluster avec des mesures par défaut et des mesures personnalisées][Image2]

Informations importantes à noter :

-	Puisque les réplicas ou les instances utilisent la charge par défaut du service jusqu’à ce qu’ils signalent leur propre charge, nous savons que les réplicas à l’intérieur de la partition 1 du service avec état n’ont pas signalé de charge propre
-	Les réplicas secondaires dans une partition peuvent avoir leur propre charge
-	Dans l’ensemble, les mesures semblent assez bonnes, avec une différence entre les charges maximale et minimale sur un nœud (pour la mémoire, la mesure personnalisée qui nous intéresse le plus, comme dit précédemment) d’un facteur de 1,75 seulement (le nœud ayant le plus de charge de mémoire est N3, le moins de charge N2, et 28/16 = 1,75), donc un assez bon équilibre !

Il reste certaines choses à expliquer :

-	Quel élément a permis de déterminer qu’un rapport de 1,75 était raisonnable ou non ? Comment savoir si c’est suffisant ou s’il reste d’autres actions à effectuer ?
-	À quel moment l’équilibrage se produit-il ?
-	Qu’est-ce que cela signifie que Memory (mémoire) était pondérée comme étant « High » (haute) ?

## Poids des mesures
Les poids des mesures permettent à deux services différents de signaler les mêmes mesures, mais en affichant l’importance de l’équilibrage des mesures de manière différente. Par exemple, prenez un moteur d’analyse en mémoire et une base de données persistante. Tous deux s’intéressent probablement à la mesure « Memory », mais le service en mémoire n’accorde sans doute guère d’importance à la mesure « Disk », car il en utilise un peu, mais ce n’est pas un élément critique pour les performances du service. La possibilité de suivre les mêmes mesures sur différents services est très utile, car c’est ce qui permet à Resource Manager d’effectuer le suivi de la consommation réelle dans le cluster, et de s’assurer que les nœuds ne dépassent pas la capacité, etc.

Les poids des mesures permettent à Resource Manager de prendre des décisions réelles sur la façon d’équilibrer le cluster lorsqu’il n’existe aucune réponse parfaite (la plupart du temps). Les mesures peuvent avoir quatre niveaux de poids : Zero, Low, Medium et High (zéro, faible, moyen et haut). Une mesure avec un poids de Zero ne contribue en rien quant à la décision d’équilibrage des éléments, mais sa charge contribue toujours à des éléments tels que la mesure de capacité.

Le véritable impact de différents poids de mesure dans le cluster est que nous arrivons à des dispositions fondamentalement différentes des services puisque Resource Manager a globalement intégré le fait que certaines mesures sont plus importantes que d’autres et que, par conséquent, ces dernières doivent être préférées pour l’équilibrage si elles sont en conflit avec d’autres mesures de priorité inférieure.

Prenons un exemple simple de quelques rapports de charge et regardons de quelle façon différents poids de mesure peuvent entraîner des allocations différentes dans le cluster. Dans cet exemple, nous voyons que le fait de basculer le poids relatif des mesures entraîne Resource Manager à préférer certaines solutions en créant différentes dispositions des services.

![Exemple de poids de mesure et impact sur les solutions d’équilibrage][Image3]

Dans cet exemple, nous avons quatre services différents qui signalent tous des valeurs différentes pour deux mesures, A et B. Dans un cas, tous les services définissent la mesure A comme la plus importante (Weight = High) et la mesure B comme relativement peu importante (Weight = Low). En effet, nous constatons que Service Fabric Resource Manager place les services de façon à ce que la mesure A soit mieux équilibrée (écart plus faible) que la mesure B. Dans le deuxième cas, nous avons inversé les poids des mesures et nous voyons que Resource Manager échangerait probablement les services A et B afin de proposer une allocation où la mesure B est mieux équilibrée que la mesure A.

### Poids globaux des mesures
Si le service A définit la mesure A comme étant la plus importante et que le service B n’a aucune préférence, quel est le poids réel utilisé finalement par Resource Manager ?

Il existe en fait deux poids dont nous effectuons le suivi pour chaque mesure : le poids défini par le service lui-même et le poids moyen global sur tous les services intéressés par cette mesure. Nous utilisons les deux poids pour calculer les scores des solutions que nous générons, puisqu’il est important de s’assurer qu’un service est équilibré en fonction de ses propres priorités, mais également que l’ensemble du cluster est correctement alloué.

Que se passerait-il si nous n’accordions aucune importance à l’équilibre global et local ? Eh bien, il est facile de créer des solutions qui sont globalement équilibrées mais qui entraînent un équilibre et une allocation des ressources très pauvres pour les services individuels. Dans l’exemple ci-dessous, étudions les mesures par défaut PrimaryCount, ReplicaCount et TotalCount configurées pour un service avec état, et observons ce qui se passe lorsque nous n’accordons d’importance qu’à l’équilibre global :

![Impact d’une solution globale uniquement][Image4]

Dans l’exemple ci-dessus où nous avons uniquement regardé l’équilibre global, le cluster dans son ensemble est bien équilibré ; tous les nœuds ont le même nombre de réplicas principaux et de réplicas totaux, c’est parfait ! Les éléments sont équilibrés. Cependant, si vous examinez l’impact réel de cette allocation, la situation n’est pas si bonne : la perte d’un nœud a un impact disproportionné sur une charge de travail, car tous ses réplicas principaux sont également perdus. Par exemple, si nous perdions le premier nœud. Si cela se produisait, les trois réplicas principaux des trois différentes partitions du service Circle seraient tous perdus simultanément. À l’inverse, les partitions des deux autres services (Triangle et Hexagon) perdent un réplica, mais cela ne crée aucune interruption (à part celle de devoir récupérer le réplica perdu).

Dans l’exemple du bas, nous avons distribué les réplicas basés à la fois sur l’équilibre global et l’équilibre par service. Lors du calcul du score de la solution, nous donnons la majorité du poids à la solution globale, mais une partie (configurable) permet de s’assurer que les services sont également équilibrés entre eux autant que possible. Par conséquent, si nous perdions le même premier nœud, nous constatons que la perte des réplicas principaux (et secondaires) est répartie sur toutes les partitions de tous les services et que l’impact est le même pour chacun.

Tenant compte des poids des mesures, l’équilibre global est calculé en fonction de la moyenne des poids des mesures. Nous équilibrons un service par rapport à ses propres poids de mesure définis.

## Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)
- La définition des mesures de défragmentation est une façon de consolider la charge sur les nœuds au lieu de la répartir. Pour savoir comment configurer la défragmentation, reportez-vous à [cet article](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’[équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Commencer au début et [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Le coût du déplacement est une façon de signaler à Cluster Resource Manager que certains services sont plus coûteux à déplacer que d’autres. Pour en savoir plus sur le coût du déplacement, reportez-vous à [cet article](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]: ./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]: ./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png

<!---HONumber=AcomDC_0316_2016-->