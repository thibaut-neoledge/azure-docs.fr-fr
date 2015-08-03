<properties
   pageTitle="Compression de mesure proactive"
   description="Présentation de l'utilisation de la compression de mesure proactive dans l'équilibreur de ressources"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhic"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="abhic"/>

# Compression de mesure proactive

Une configuration courante de l'équilibreur de ressources de Service Fabric consiste à obtenir une utilisation égale pour chaque mesure sur chaque nœud. L'équilibreur de ressources doit également trouver un emplacement pour le service à l'arrivée de nouvelles requêtes de service. Si l'espace libre n'est pas suffisant pour placer de nouveaux réplicas de service (tous les réplicas de toutes les partitions de service), l'équilibreur de ressources essaiera de créer de l'espace en déplaçant les charges de travail existantes. Bien que cette méthode soit parfaitement normale, en fonction du remplissage du cluster et du niveau de fragmentation de la charge de travail, elle peut prendre du temps.

Par exemple, si le cluster est correctement utilisé et si le client souhaite ajouter un nouveau service avec une charge élevée par défaut (par exemple, capacité maximale de nœud pour une ou plusieurs mesures), il est possible que l'équilibreur de ressources ait besoin de déplacer plusieurs réplicas afin de placer le nouveau service. En outre, si les services sont avec état et de grande taille, l'exécution des déplacements peut prendre du temps dans la mesure où les données doivent être copiées. Ces deux problèmes peuvent étendre la durée de création du nouveau service. Bien que généralement les services soient tolérants en cas de temps de création occasionnellement lents, certaines charges de travail sont moins tolérantes et veulent être créées dès que possible. Cela signifie que, dans un état stable, l'équilibreur de ressources doit s'assurer que le cluster est « défragmenté » afin de garantir qu'il y ait suffisamment d'espace disponible pour les nouvelles charges de travail.

Le mécanisme de compression de mesure proactive (également appelé défragmentation) s'exécute dans le cadre de la phase d'équilibrage de l'équilibreur de ressources, dans le but de réduire le temps de création du service grâce à la compression des charges de travail sur moins de nœuds, plutôt que de les distribuer comme lors de l'équilibrage. Lorsqu'une mesure est configurée pour la défragmentation, l'équilibreur de ressources a pour but d'atteindre l'écart type moyen maximal, plutôt que l'écart type moyen minimal utilisé lors de l'équilibrage.

Avec un écart maximal, l'équilibreur de ressources tentera de placer autant de services que possible sur certains nœuds tout en conservant autant de nœuds vides que possible. En outre, l'une des contraintes de base pour placer des nouveaux services est que les réplicas ne peuvent pas être dans le même domaine de mise à niveau ou domaine d'erreur. Comme l'objectif est de pouvoir ajouter rapidement de nouveaux services, l'équilibreur de ressources doit essayer d'obtenir l'écart type minimal pour la distribution de charge entre les domaines de mise à niveau et les domaines d'erreur (somme des charges de service par domaine de mise à niveau / domaine d'erreur). Le résultat sera la même quantité d'espace libre par domaine de mise à niveau / domaine d'erreur. La défragmentation respecte également toutes les autres contraintes du système, comme l'affinité, les contraintes de placement et la capacité de mesure du nœud.

## Configuration du cluster de l'équilibreur de ressources
Dans le manifeste de cluster, vous trouverez les différentes valeurs de configuration qui définissent le comportement général de la fonctionnalité de compression de mesure pour l'équilibreur de ressources :

### DefragmentationMetrics – Mesures que l'équilibreur de ressources doit prendre en compte pour la compression/défragmentation proactive.

Toutes les mesures configurées doivent être spécifiées dans cette liste (comme dans les listes de seuil Activité et Équilibrage). Si la mesure est spécifiée avec la valeur « true », elle sera traitée comme mesure de défragmentation, mais avec la valeur « false » (ou si elle n'est pas spécifiée dans cette liste), la valeur ne sera pas prise en compte pour la défragmentation.

``` xml
<FabricSettings>
  <Section Name="DefragmentationMetrics">
    <Parameter Name="MetricName1" Value="true"/>
    <Parameter Name="MetricName2" Value="false"/>
  </Section>
</FabricSettings>
```

### BalacingThreshholds

Les seuils d'équilibrage régissent la fragmentation du cluster en ce qui concerne une mesure particulière avant l'exécution de la phase d'équilibrage par l'équilibreur de ressources (qui effectuera la compression métrique logique). Si la mesure est considérée comme mesure de défragmentation, le seuil d'équilibrage est le rapport minimal entre les nœuds utilisés au maximum et au minimum par domaine de mise à niveau ou d'erreur dont l'équilibreur de ressources autorise l'existence, avant qu'il n'exécute la défragmentation du cluster. Si le rapport d'un domaine de mise à niveau ou d'erreur est inférieur au seuil, la phase de défragmentation est exécutée.

L'illustration suivante montre deux exemples où le seuil d'équilibrage de la mesure donnée est égal à 10.

![Seuil d'équilibrage][Image1]

Notez que pour le moment, l'« utilisation » sur un nœud ne prend pas en compte la taille du nœud telle qu'elle est déterminée par la capacité du nœud, mais uniquement l'utilisation absolue actuellement indiquée sur le nœud pour la mesure spécifiée.

Si la mesure n'est pas spécifiée, la valeur par défaut est 1. Dans ce cas, la défragmentation sera effectuée jusqu'à ce que le cluster possède au moins un nœud vide dans chaque domaine de mise à niveau et chaque domaine d'erreur.

La valeur 0 signifie que cette mesure n'est pas prise en compte lors de la phase d'équilibrage, qu'elle soit prise en compte pour la défragmentation ou non.

L'exemple de code montre que les seuils d'équilibrage pour les mesures sont configurés par mesure à l'aide de l'élément FabricSettings dans le manifeste de cluster.

``` xml
<FabricSettings>
  <Section Name="MetricBalancingThresholds">
    <Parameter Name="MetricName" Value="10"/>
  </Section>
</FabricSettings>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Pour plus d'informations : [Architecture de l'équilibreur de ressources](service-fabric-resource-balancer-architecture.md)

[Image1]: media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png
 

<!---HONumber=July15_HO4-->