<properties
   pageTitle="Rapport de charge dynamique"
   description="Une présentation du rapport de charge dynamique à l'équilibreur de ressources"
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

# Présentation du rapport de charge dynamique

Lors de l'exécution, les objets de service avec ou sans état peuvent signaler la charge avec la méthode ReportLoad (membre des interfaces IStatefulServicePartition et IStatelessServicePartition). Il est important de signaler les valeurs de charge d'exécution car cela permet la compression précise des services dans les nœuds et permet de s'assurer que l'utilisation des ressources est précisément suivie par l'équilibreur de ressources central de Service Fabric, tel que les services en font l'expérience sur les nœuds.

Notez que lorsqu'un réplica rapporte la charge, ce rapport est d'abord regroupé avec d'autres rapports de charge localement, puis un rapport unique est envoyé à l'équilibreur de ressources. Ce processus signifie que si un service rapporte une charge très rapidement et à plusieurs reprises, seul le dernier rapport est réellement envoyé à l'équilibreur de ressources.

Lorsque l'équilibreur de ressources de Service Fabric s'exécute, il examine toutes les informations de charge qui sont agrégées à partir de tous les nœuds et effectue quelques vérifications. Ces contrôles incluent le seuil d'équilibrage et les seuils d'activité pour les mesures, tels que définis dans le manifeste de cluster. Ils déterminent si le cluster est suffisamment déséquilibré pour exécuter l'équilibreur de ressources et si un nœud spécifique se situe au-dessus de la capacité de l'une des mesures pour lesquelles il a une capacité définie. Dans le cas de dépassement de capacité, l'équilibreur de ressources commence par examiner uniquement les services sur le(s) nœud(s) au-dessus de la capacité qui partagent la mesure qui dépasse la capacité. En cas de déséquilibre du cluster, l'équilibreur de ressources examine tous les services reliés par des mesures à un service qui signale la mesure déséquilibrée. Si l'équilibreur de ressources de Service Fabric détermine que l'une de ces situations s'est produite, il exécute une simulation qui tente de trouver une meilleure disposition des services.

Les services doivent signaler la charge à chaque fois que celle-ci change et ils ne doivent pas effectuer d'agrégation ou de lissage des rapports de charge par eux-mêmes.

Notez que lorsqu'un service rapporte la charge, ces rapports de charge remplacent les valeurs de charge primaire et secondaire par défaut qui étaient définies lors de la création du service et deviennent les nouvelles valeurs de charge qui sont utilisées lorsque l'équilibreur de ressources de Service Fabric doit prendre des décisions en termes d'équilibrage ou de placement à partir de ce moment.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Pour plus d'informations : [Architecture de l'équilibreur de ressources](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=July15_HO4-->