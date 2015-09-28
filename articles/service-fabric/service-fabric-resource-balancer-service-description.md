<properties
   pageTitle="Descriptions du service de l'équilibreur de ressources"
   description="Présentation de la configuration des descriptions de service pour l'équilibreur de ressources"
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
   ms.date="09/15/2015"
   ms.author="masnider"/>

# Présentation de la description du service

## Contraintes de placement
Les [contraintes de placement](../service-fabric-placement-constraint) sur un service constituent le mécanisme par lequel une instance de service spécifique sélectionne les propriétés du nœud dont elle a besoin. Tout comme les propriétés du nœud, il s'agit de paires clé/valeur qui décrivent le nom de la propriété et les exigences du service pour la valeur. Les instructions individuelles peuvent être regroupées avec une logique booléenne simple pour créer la contrainte nécessaire. Notez que l'équilibreur de ressources de Service Fabric interprète les contraintes.

Les contraintes de placement du service peuvent être définies par le biais du manifeste de service ou de l'application, ou directement dans le code.

Le manifeste de service fournit les définitions ServiceTypes.

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

Le manifeste de l'application fournit les définitions ServiceTemplates ou DefaultServices.

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Le diagramme suivant montre que dans le cluster avec des nœuds, le seul nœud valide pour le placement de ce service est N5.

![Contraintes de placement][Image1]

Si le service spécifie une contrainte pour une propriété qui n'est pas définie sur un nœud spécifique, celui-ci n'est pas considéré comme une correspondance, quelle que soit la valeur spécifiée.

## Affinité de service
L'affinité de service, qui est une forme spéciale de contrainte de placement, permet la description de différents services qui, pour diverses raisons, ont une dépendance locale les uns aux autres. Ce qui signifie que les deux services doivent être exécutés sur le même nœud pour fonctionner. Bien que ce type d'architecture ne soit pas courant dans les applications Service Fabric, Service Fabric reconnaît qu'il s'agit d'une architecture de transition possible pour certains types d'applications héritées. Il la rend donc disponible.

Le fait d'établir une affinité de service entre deux services garantit que les réplicas principaux de ces services sont toujours situés sur le même nœud.

Les relations d'affinités sont représentées par une hiérarchie parent-enfant, autrement dit il y a des « parents » et des « enfants ». Le service établi en premier est le parent. Le service établi en second est l'enfant dans la relation. La relation est modélisée comme contrainte « dure ».

L'affinité de service présente actuellement les limites suivantes :

- Elle ne peut pas être utilisée pour les services sans et avec état.
- Elle ne peut pas être utilisée pour les services sans état avec des nombres d'instances différents. Par exemple, les deux services sans état doivent avoir la même propriété InstanceCount lorsqu'ils sont créés.
- Elle ne peut pas être utilisée pour les services volatiles ou persistants avec état avec des nombres différents de réplicas. Par exemple, les deux services doivent avoir les mêmes valeurs ReplicaSetSizes Target et Min.
- Elle ne peut pas être utilisée avec les services partitionnés. Chaque service doit avoir un schéma de partition FABRIC\_PARTITION\_SCHEME\_SINGLETON.
- Les relations d'affinité, comme d'autres propriétés de la description du service, sont définies lorsque le service est créé et ne peuvent pas être modifiées.
- Les chaînes de services ne sont pas autorisées. Si plusieurs services doivent être introduits dans une relation d'affinité, ils doivent utiliser un modèle « en étoile ».

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

L'exemple de code illustre une utilisation alternative des définitions DefaultServices.

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

L'exemple de code montre comment créer une relation d'affinité une fois l'application conteneur créée.

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Mesures d'équilibrage des ressources
Tandis que les contraintes de placement de service et les propriétés du nœud représentent les règles et les stratégies strictes qui décrivent les modalités de service valides, les mesures d'équilibrage des ressources permettent, en général, de décrire la disposition optimale des services au sein du cluster. Les mesures qui sont définies par le service ont plusieurs propriétés différentes qui décrivent comment elles sont utilisées au sein du cluster.

### Utilisation des mesures par défaut
La première chose à comprendre est que l'utilisation des mesures de ressource est facultative pour les services et que, par défaut, l'équilibreur de ressources de Service Fabric utilise certaines mesures système « intégrées » pour exécuter un équilibrage des ressources de base sans que ce service ou les administrateurs du cluster n'effectuent aucune action. Les mesures par défaut que l'équilibreur de ressources de Service Fabric suit et qu'il équilibre au sein du cluster sont PrimaryCount, ReplicaCount et Count. PrimaryCount est le nombre de réplicas de service avec état principal sur un nœud. ReplicaCount est le nombre de tous les réplicas avec état sur un nœud. Count est le nombre total de tous les objets de service sur le nœud, y compris les services avec et sans état. En général, la mesure PrimaryCount est considérée comme la plus importante. Par conséquent, sa pondération est la plus élevée, comme décrit dans la section suivante. La mesure PrimaryCount est suivie de la mesure ReplicaCount et enfin, de la mesure Count.

Le diagramme suivant illustre un exemple d'équilibrage par défaut de trois services avec état avec trois réplicas chacun.

![Mesures par défaut][Image2]

En général, les mesures par défaut sont suffisantes pour de nombreux services et doivent être utilisées, sauf si des mesures ou des fonctions supplémentaires sont requises.

### Définition de mesures personnalisées
Si les mesures par défaut sont insuffisantes pour un service particulier, ou si ce service a des exigences connues concernant une ressource particulière, comme l'espace disque ou la mémoire, utilisez des mesures personnalisées. Les mesures personnalisées sont utiles lorsqu'un service a une ou plusieurs ressources qui doivent être correctement équilibrées pour éviter tout conflit. L'utilisation de ces mesures peut varier considérablement entre les réplicas au sein du cluster. Par exemple, un réplica principal peut utiliser 100 % de la mesure, tandis qu'un autre réplica peut n'utiliser que 20 %. Il peut également être utile d'utiliser des mesures personnalisées pour capturer toute ressource qui est sévèrement limitée sur un ordinateur, comme la mémoire, l'espace disque ou les connexions. Il est parfois possible de créer des mesures personnalisées supplémentaires. Celles-ci peuvent servir à capturer ou représenter des mesures sans limite supérieure sur les nœuds, mais qui représentent la consommation de travail et de ressources du point de vue du service, par exemple une mesure « transactions en cours en attente ». Même s'il existe une limite à cette mesure, une « capacité » sur un nœud donné, elle présente tout de même un avantage du point de vue des performances en veillant à ce que cette mesure soit distribuée uniformément dans l'ensemble du cluster.

Notez que si un service définit une mesure personnalisée, il n'utilise pas les mesures par défaut fournies par le système. Les mesures par défaut peuvent toujours être utilisées, mais elles doivent être explicitement incluses à nouveau dans la liste des mesures du service lors de la création.

### Noms des mesures
Les mesures personnalisées peuvent être créées simplement en définissant un nom de mesure lorsque vous créez un service. Notez que l'équilibreur de ressources de Service Fabric associe les mesures à l'aide de leur nom. Donc, si la mesure est utilisée en tant que capacité dans des définitions de nœud ou entre plusieurs services, alors les noms des mesures doivent correspondre exactement afin que l'équilibreur de ressources de Service Fabric puisse les associer.

### Poids des mesures
Lorsqu'un service définit plusieurs mesures, il est également utile de définir les poids de ces mesures. Des poids différents indiquent à l'équilibreur de ressources de Service Fabric quelle mesure est la plus importante pour le fonctionnement du service. Par exemple, une file d'attente en mémoire peut être affectée par la bande passante réseau mais elle est probablement plus contrainte par l'utilisation réelle de la mémoire sur le nœud. Ainsi, alors que la file d'attente peut contenir plusieurs mesures, la mesure qui représente l'utilisation de la mémoire a le poids le plus élevé. De même, une base de données persistante dépend probablement de l'utilisation de la mémoire et du disque, mais tandis que la mémoire limitée réduit la possibilité de traiter des requêtes complexes, le fait de manquer d'espace disque empêcherait toute opération de stockage supplémentaire, ce qui constitue probablement une situation plus critique. Par conséquent, le magasin persistant choisit probablement l'espace disque en tant que mesure avec le poids le plus élevé.

Notez que le poids n'est vraiment utilisé au sein de l'équilibreur de ressources de Service Fabric que lorsque celui-ci ne peut pas équilibrer complètement un ensemble de mesures et doit donc trouver des solutions où une mesure est globalement moins équilibrée qu'une autre mesure. Dans ce cas, l'équilibre de la mesure avec le poids le plus faible est déterminé comme étant de priorité inférieure à celui de la mesure de poids plus élevé. Donc, l'équilibreur de ressources de Service Fabric propose des solutions qui favorisent l'équilibrage de la mesure de poids supérieur.

De plus, lorsque l'équilibreur de ressources de Service Fabric reconnaît que plusieurs services ont défini la même mesure par nom, il considère ces mesures comme étant la même mesure. Dans les cas où la mesure est la même, mais que les poids sont différents, l'équilibreur de ressources calcule la moyenne des poids spécifiés pour déterminer les poids réels qui seront utilisés lors de l'exécution. Par conséquent, si un service définit un poids égal à zéro, ce qui indique qu'il ne nécessite pas d'équilibrage, mais qu'un autre service définit la même mesure comme ayant un poids élevé, le poids réel est situé entre ces deux valeurs.

![Poids des mesures][Image3]

Cet exemple montre comment deux scénarios de poids de mesures différents résultent en deux résultats d'équilibrage différents où la mesure avec la plus grande priorité est mieux répartie. Dans l'exemple de gauche, B est mieux équilibré, tandis que sur la droite, A est mieux équilibré. Étant donné que dans cet exemple il n'existe aucune solution d'équilibrage qui résulte dans l'équilibre des deux mesures, l'équilibreur de ressources de Service Fabric utilise le poids des mesures pour déterminer ce qui est le plus important, et par conséquent la mesure à privilégier lors de l'équilibrage.

Notez que le poids de mesure égal à zéro est fourni pour les cas où un service assure le suivi d'une mesure, mais ne nécessite pas d'équilibrage basé sur cette mesure. Par exemple, prenons une mesure qui a une limite explicite sur plusieurs nœuds, mais pour laquelle une « utilisation uniforme sur les nœuds », qui est généralement garantie par l'équilibrage des ressources, est sans importance. Pour ces mesures qui ne nécessitent pas l'équilibrage, mais qui nécessitent l'application des limites définies au niveau des nœuds, le poids d'équilibrage de zéro peut être défini. Lors de l'exécution, l'équilibreur de ressources de Service Fabric applique la capacité mais n'essaie pas d'équilibrer la mesure de manière proactive, même si l'utilisation de cette mesure est très déséquilibrée entre les nœuds.

### Charge de mesure par défaut pour un rôle principal ou secondaire
Lorsque vous définissez une mesure pour un service, indiquez la consommation prévue pour cette mesure lorsque le service est dans un rôle principal ou secondaire. Cette information aide non seulement l'équilibreur de ressources à placer initialement le service de façon efficace, mais peut servir d'approximation satisfaisante de l'utilisation réelle des mesures par le service qui est lié à ces dernières tout au long de sa durée de vie. L'équilibreur de ressources de Service Fabric prend automatiquement cette consommation de mesure en compte ; non seulement lors du placement du service, mais aussi à chaque fois qu'il doit déplacer des réplicas en raison de l'équilibrage ou d'autres modifications dans le cluster. Si la charge d'un service est relativement prévisible et stable, alors cela signifie que grâce à la définition de ces valeurs, le service peut refuser de créer un rapport sur la charge pendant l'exécution.

L'exemple de code suivant montre un service qui définit complètement deux mesures personnalisées : l'une pour l'utilisation de la mémoire et l'autre pour l'espace disque.

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

L'exemple de code montre des définitions ServiceTypes par le biais du manifeste de service.

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

L'exemple de code montre des définitions ServiceTemplates par le biais du manifeste de l'application.

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
L’exemple de code montre des définitions DefaultServices par le biais du manifeste de l’application.

``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

Étant donné que les valeurs de charge par défaut ne sont pas mises à jour, sauf si les services choisissent spécifiquement de créer un rapport sur la charge lors de l'exécution par le biais du code, les valeurs peuvent également être utilisées pour instituer davantage un modèle d'équilibrage de ressources de « réservation de capacité ». Par exemple, si les charges de travail appartiennent en général à plusieurs compartiments de taille différente et si un nombre connu d'unités de travail peut être placé sur un nœud à tout moment, une mesure personnalisée d'« unités » peut être créée, et ensuite la capacité du nœud et la charge par défaut du service sont définis en termes d'unités.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Pour plus d'informations : [Architecture de l'équilibreur de ressources](service-fabric-resource-balancer-architecture.md), [Contraintes de placement](service-fabric-placement-constraint.md)

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png
 

<!---HONumber=Sept15_HO3-->