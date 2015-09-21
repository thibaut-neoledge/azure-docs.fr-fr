<properties
   pageTitle="Description du cluster de l'équilibreur de ressources"
   description="Spécification d'une description du cluster pour l'équilibreur de ressources"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Description du cluster

L'équilibreur de ressources de Service Fabric fournit plusieurs mécanismes pour décrire un cluster. Pendant l'exécution, l'équilibreur de ressources utilise ces informations pour s'assurer qu'il place les services de manière à garantir une haute disponibilité des services en cours d'exécution dans le cluster tout en garantissant également une utilisation maximale des ressources du cluster. Les fonctionnalités de l'équilibreur de ressources qui décrivent un cluster sont les domaines d'erreur, les domaines de mise à niveau, les propriétés du nœud et les capacités du nœud. En outre, l'équilibreur de ressources dispose d'options de configuration qui peuvent ajuster ses performances.

## Concepts clés

### Domaines d'erreur :

Les domaines d'erreur permettent aux administrateurs de cluster de définir les nœuds physiques qui sont susceptibles de rencontrer un échec en même temps en raison des dépendances physiques partagées, comme l'alimentation et les sources de mise en réseau. En général, les domaines d'erreur représentent les hiérarchies associées à ces dépendances partagées, avec davantage de nœuds susceptibles d'échouer ensemble depuis un point plus élevé dans l'arborescence du domaine d'erreur. L'illustration suivante montre plusieurs nœuds qui sont organisés par domaines d'erreur hiérarchiques dans l'ordre centre de données, rack et panneau.

![Domaines d'erreur][Image1]

 Lors de l'exécution, le Gestionnaire de ressources de Service Fabric considère les domaines d'erreur dans le cluster et tente de répartir les réplicas d'un service donné afin qu'ils soient tous dans des domaines d'erreur distincts. Ce processus permet de s'assurer, en cas d'échec d'un domaine d'erreur, que la disponibilité de ce service et son état ne sont pas compromis. La figure suivante illustre les réplicas d'un service répartis entre plusieurs domaines d'erreur, même s'il existe suffisamment de place pour les concentrer dans un ou deux domaines uniquement.

![Domaines d'erreur][Image2]

Les domaines d'erreur sont configurés dans le manifeste de cluster. Chaque nœud est défini pour se trouver à l'intérieur d'un domaine d'erreur particulier. Lors de l'exécution, le Gestionnaire de ressources combine les rapports de tous les nœuds pour développer une vue d'ensemble complète de tous les domaines d'erreur dans le système.

### Domaines de mise à niveau

Les domaines de mise à niveau constituent une autre information qui est consommée par le Gestionnaire de ressources. Les domaines de mise à niveau, comme les domaines d'erreur, décrivent des ensembles de nœuds qui sont arrêtés pour les mises à niveau à peu près au même moment. Les domaines de mise à niveau ne sont pas hiérarchiques et peuvent être considérés comme des balises.

Tandis que les domaines d'erreur sont définis par la disposition physique des nœuds dans le cluster, les domaines de mise à niveau sont déterminés par les administrateurs du cluster basés sur les stratégies. Les stratégies sont liés aux mises à niveau au sein du cluster. Davantage de domaines de mise à niveau rendent les mises à niveau plus granulaires en limitant l'impact sur le cluster et les services en cours d'exécution, et en empêchant tout échec d'affecter un grand nombre de services. En fonction d'autres stratégies, notamment le délai que Service Fabric doit attendre après la mise à niveau d'un domaine avant de passer au domaine suivant, avoir plus de domaines de mise à niveau peut également augmenter la durée nécessaire pour effectuer une mise à niveau dans le cluster.

Pour ces raisons, le Gestionnaire de ressources collecte des informations sur le domaine de mise à niveau et répartit les réplicas entre les domaines de mise à niveau d'un cluster à l'instar des domaines d'erreur. Les domaines de mise à niveau peuvent correspondre ou pas aux domaines d'erreur. En général, aucune correspondance exacte n'est attendue. La figure suivante illustre une couche de plusieurs domaines de mise à niveau par dessus les domaines d'erreur définis précédemment. Le Gestionnaire de ressources répartit toujours les réplicas entre les domaines, afin qu'aucun domaine d'erreur ou de mise à niveau ne concentre tous les réplicas pour garantir une haute disponibilité du service, en dépit des mises à niveau ou des erreurs dans le cluster.

![Domaines de mise à niveau][Image3]

Les domaines de mise à niveau et les domaines d'erreur sont configurés dans le cadre de la définition du nœud dans le manifeste de cluster, comme indiqué ci-dessous :

``` xml
<Infrastructure>
    <WindowsServer>
      <NodeList>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node1" FaultDomain="fd:/RACK1/BLADE1" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node2" FaultDomain="fd:/RACK2/BLADE1" UpgradeDomain="ud:/UD2"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node3" FaultDomain="fd:/RACK3/BLADE2" UpgradeDomain="ud:/UD3"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="false" IPAddressOrFQDN="localhost" NodeName="Node4" FaultDomain="fd:/RACK2/BLADE2" UpgradeDomain="ud:/UD1"/>
        <Node NodeTypeRef="NodeType01" IsSeedNode="true" IPAddressOrFQDN="localhost" NodeName="Node5" FaultDomain="fd:/RACK1/BLADE2" UpgradeDomain="ud:/UD2"/>
        </NodeList>
    </WindowsServer>
</Infrastructure>
```
- Dans les déploiements Azure, les domaines d'erreur et les domaines de mise à niveau sont affectés par Azure. Par conséquent, la définition des nœuds et des rôles au sein de l'option Infrastructure pour Azure n'inclut pas les informations sur le domaine d'erreur ou le domaine de mise à niveau.

### Propriétés du nœud
Les propriétés du nœud sont des paires clé/valeur définies par l'utilisateur qui fournissent des métadonnées supplémentaires pour un nœud donné. Si le nœud a un disque dur ou une carte vidéo, le nombre de rotations du disque dur, les noyaux et autres propriétés physiques sont des exemples de propriétés du nœud.

Les propriétés du nœud peuvent également servir à spécifier des propriétés plus abstraites pour vous aider à placer des décisions de stratégie. Par exemple, une « couleur » peut être affectée à plusieurs nœuds dans un cluster comme un moyen de segmentation du cluster en différentes sections. L'exemple de code montre que les propriétés de nœud sont définies pour les nœuds à l'aide du manifeste de cluster dans le cadre des définitions du type de nœud, qui peuvent ensuite être appliquées à plusieurs nœuds dans le cluster.

Les propriétés de placement NodeName, NodeType, FaultDomain et UpgradeDomain ont des valeurs par défaut. Service Fabric vous fournit automatiquement les valeurs par défaut afin que vous puissiez les utiliser lors de la création du service. Les utilisateurs ne doivent pas spécifier leurs propres propriétés de placement avec ces mêmes noms.

``` xml
<NodeTypes>
  <NodeType Name="NodeType1">
    <PlacementProperties>
      <Property Name="HasDisk" Value="true"/>
      <Property Name="SpindleCount" Value="4"/>
      <Property Name="HasGPU" Value="true"/>
      <Property Name="NodeColor" Value="blue"/>
      <Property Name="NodeName" Value="Node1"/>
      <Property Name="NodeType" Value="NodeType1"/>
      <Property Name="FaultDomain" Value="fd:/RACK1/BLADE1"/>
      <Property Name="UpgradeDomain" Value="ud:/UD1"/>
    </PlacementProperties>
  </NodeType>
    <NodeType Name="NodeType2">
    <PlacementProperties>
      <Property Name="HasDisk" Value="false"/>
      <Property Name="SpindleCount" Value="-1"/>
      <Property Name="HasGPU" Value="false"/>
      <Property Name="NodeColor" Value="green"/>
    </PlacementProperties>
  </NodeType>
</NodeTypes>
```

Lors de l'exécution, l'équilibreur de ressources utilise les informations de propriété du nœud pour s'assurer que les services nécessitant des fonctionnalités spécifiques sont placés sur les nœuds appropriés.

### Capacités du nœud
Les capacités du nœud sont des paires clé/valeur qui définissent le nom et la quantité d'une ressource spécifique disponible sur un nœud particulier pour consommation. L'exemple de code montre un nœud qui possède de la capacité pour une mesure appelée « MemoryInMb » et qui a 2 048 Mo de mémoire disponible par défaut. Les capacités sont définies au moyen du manifeste de cluster, tout comme les propriétés d'un nœud.

``` xml
<NodeType Name="NodeType03">
  <Capacities>
    <Capacity Name="MemoryInMB" Value="2048"/>
    <Capacity Name="DiskSpaceInGB" Value="1024"/>
  </Capacities>
</NodeType>
```
![Capacité du nœud][Image4]

Étant donné que les services qui s'exécutent sur un nœud peuvent mettre à jour leurs besoins en capacité via le Rapport de charge, l'équilibreur de ressources vérifie régulièrement si un nœud respecte ou dépasse la capacité de l'une de ses mesures. Le cas échéant, l'équilibreur de ressources peut déplacer des services vers des nœuds moins chargés pour réduire les conflits de ressources et améliorer l'utilisation et les performances globales.

Notez que bien qu'une mesure donnée puisse également être répertoriée dans la section de propriété du type de nœud, il est préférable de la répertorier en tant que capacité s'il s'agit d'une propriété du nœud qui peut être utilisée lors de l'exécution. Le fait de la répertorier également en tant que propriété permet à un service qui dépend de la « configuration matérielle minimale requise » d'interroger le nœud concernant les contraintes de placement, ce qui peut être utile si la ressource est consommée par d'autres services pendant l'exécution. Nous vous recommandons de l'inclure également en tant que capacité afin que l'équilibreur de ressources puisse exécuter des actions supplémentaires.

Lorsque de nouveaux services sont créés, l'équilibreur de ressources du cluster de Service Fabric utilise les informations sur la capacité des nœuds existants et la consommation des services existants pour déterminer s'il existe suffisamment de capacité pour placer le nouveau service entier. Si la capacité est insuffisante, alors la requête Créer le service est rejetée avec un message d'erreur indiquant que la capacité restante dans le cluster est insuffisante.


### Configurations de l'équilibreur de ressources

Dans le manifeste de cluster, vous trouverez les différentes valeurs de configuration qui définissent le comportement général de l'équilibreur de ressources :

- Les seuils d'équilibrage régissent le déséquilibre du cluster en ce qui concerne une mesure particulière avant la réaction de l'équilibreur de ressources. Un seuil d'équilibrage est le rapport maximal entre les nœuds utilisés au maximum et au minimum dont l'équilibreur de ressources autorise l'existence avant de rééquilibrer les clusters.

L'illustration suivante montre deux exemples où le seuil d'équilibrage de la mesure donnée est égal à 10.

![Seuil d'équilibrage][Image5]

Notez que pour le moment, l'« utilisation » sur un nœud ne prend pas en compte la taille du nœud telle qu'elle est déterminée par la capacité du nœud, mais uniquement l'utilisation absolue actuellement indiquée sur le nœud pour la mesure spécifiée.

L'exemple de code montre que les seuils d'équilibrage pour les mesures sont configurés par mesure à l'aide de l'élément FabricSettings dans le manifeste de cluster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

- Les seuils d'activité agissent comme un portail sur la fréquence d'exécution de l'équilibreur de ressources en limitant les cas auxquels l'équilibreur de ressources réagit lorsqu'une quantité absolue significative de charge est présente. De cette façon, si le cluster n'est pas très occupé pour une mesure particulière, l'équilibreur de ressources n'est pas exécuté même si cette petite quantité de mesure est très déséquilibrée au sein du cluster. Cette mesure empêche de gaspiller les ressources par le rééquilibrage du cluster pour un avantage minimal. L'illustration suivante montre que le seuil d'équilibrage de la mesure est égal à 4 et que le seuil d'activité est égal à 1 536.

![Seuil d'activité][Image6] Notez également que les seuils d'activité et d'équilibrage doivent être dépassés pour que la même mesure déclenche l'exécution de l'équilibreur de ressources. Le déclenchement de l'un des seuils pour deux mesures distinctes n'entraîne pas l'exécution de l'équilibreur de ressources.

L'exemple de code montre que comme les seuils d'équilibrage, les seuils d'activité sont configurés par mesure à l'aide de l'élément FabricSettings dans le manifeste de cluster.

``` xml
<FabricSettings>
  <Section Name="MetricActivityThresholds">
    <Parameter Name="MetricName" Value="1536"/>
  </Section>
</FabricSettings>
```

- PLBRefreshInterval – Régit la fréquence à laquelle l'équilibreur de ressources analyse les informations pour vérifier les violations de contrainte. Les violations de contrainte incluent les contraintes de placement qui ne sont pas respectées, les services avec un nombre d'instances ou de réplicas au nombre requis, les nœuds dépassant la capacité pour une mesure et les domaines d'erreur ou de mise à niveau surchargés, ou les déséquilibres du cluster liés à l'utilisation des seuils d'équilibrage et d'activité, ainsi que sa vue actuelle de la charge sur les nœuds dans le cluster. L'intervalle d'actualisation est défini en secondes et la valeur par défaut est 1. La fréquence de vérification de la contrainte et du placement peut également être contrôlée par deux nouveaux intervalles (MinConstraintCheckInterval et MinPlacementInterval). Si les nouveaux paramètres sont définis, PLBRefreshInterval ne sera pas utilisé et ne peut pas être défini.

- MinConstraintCheckInterval - Régit la fréquence à laquelle l'équilibreur de ressources analyse les informations pour vérifier les violations de contrainte. Les violations de contrainte incluent les contraintes de placement qui ne sont pas respectées, les services avec un nombre d'instances ou de réplicas au nombre requis, les nœuds dépassant la capacité pour une mesure et les domaines d'erreur ou de mise à niveau surchargés, ou les déséquilibres du cluster liés à l'utilisation des seuils d'équilibrage et d'activité, ainsi que sa vue actuelle de la charge sur les nœuds dans le cluster. L'intervalle de vérification de la contrainte est défini en secondes. Si l'intervalle de vérification de la contrainte n'est pas défini, sa valeur par défaut est égale à la valeur de PLBRefreshInterval (les deux valeurs ne peuvent pas être spécifiées simultanément).

- MinPlacementInterval – Régit la fréquence à laquelle l'équilibreur de ressources vérifie s'il existe des instances ou des réplicas nouveaux qui doivent être placés, et tente de les placer. L'intervalle de placement est défini en secondes. Si l'intervalle de placement n'est pas défini, sa valeur par défaut est égale à la valeur de PLBRefreshInterval (les deux valeurs ne peuvent pas être spécifiées simultanément).

- MinLoadBalancingInterval – Établit la durée minimale entre les exécutions de l'équilibrage des ressources. Si l'équilibreur de ressources a exécuté une action en fonction d'informations provenant de l'analyse effectuée au cours du dernier intervalle PLBRefreshInterval, il ne s'exécute pas pendant au moins le même laps de temps. Celui-ci est défini en secondes et la valeur par défaut est 5.

Notez que ces valeurs sont strictes, mais que dans l'ensemble, l'équilibrage de charge ne se produit que dans le cluster si les seuils d'équilibrage et d'activité sont respectés pour une mesure donnée. L'exemple de code montre que si un cluster particulier ne requiert pas un équilibrage de charge précis et actif, ces valeurs peuvent être rendues moins agressives avec la configuration suivante dans l'élément FabricSettings. Dans cet exemple de configuration, l'intervalle minimal entre deux vérifications de la contrainte est de 10 secondes, de 5 secondes pour le placement tandis que l'équilibrage de charge se produit toutes les 5 minutes. Notez que PLBRefreshInterval n'est pas défini dans ce cas.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="MinPlacementInterval" Value="5" />
  <Parameter Name="MinConstraintChecknterval" Value="10" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

Dans le deuxième exemple de configuration ci-dessous, PLBRefreshInterval et MinLoadBalancingInterval sont définis. Étant donné que PLBRefreshInterval est défini sur 2 secondes, MinPlacementInterval et MinConstraintCheckInterval ont également une valeur de 2 secondes.

``` xml
<Section Name="PlacementAndLoadBalancing">
  <Parameter Name="PLBRefreshInterval" Value="2" />
  <Parameter Name="MinLoadBalancingInterval" Value="600" />
</Section>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Pour plus d'informations : [Architecture de l'équilibreur de ressources](service-fabric-resource-balancer-architecture.md)


[Image1]: media/service-fabric-resource-balancer-cluster-description/FD1.png
[Image2]: media/service-fabric-resource-balancer-cluster-description/FD2.png
[Image3]: media/service-fabric-resource-balancer-cluster-description/UD.png
[Image4]: media/service-fabric-resource-balancer-cluster-description/NC.png
[Image5]: media/service-fabric-resource-balancer-cluster-description/Config.png
[Image6]: media/service-fabric-resource-balancer-cluster-description/Thresholds.png
 

<!---HONumber=Sept15_HO2-->