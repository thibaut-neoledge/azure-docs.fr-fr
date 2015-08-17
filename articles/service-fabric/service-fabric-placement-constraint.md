<properties
   pageTitle="Contraintes de placement de l'orchestration du cluster Service Fabric"
   description="Vue d'ensemble conceptuelle des contraintes de placement dans Service Fabric"
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
   ms.date="03/17/2015"
   ms.author="abhic"/>

# Vue d'ensemble des contraintes de placement

Service Fabric permet aux développeurs de contraindre le placement des réplicas de service sur les nœuds qui répondent à des conditions particulières. Ces conditions sont exprimées par une expression booléenne évaluée avec des valeurs appropriées, spécifiques du contexte du service.


## Fonctionnalités
Les contraintes de placement vous permettent d'effectuer les opérations suivantes :

- Limiter un type différent de services sur les différents types de nœuds via la définition de NodeProperties sur les nœuds.

- Appliquer certaines contraintes aux réplicas principaux, mais pas aux secondaires


## Concepts clés
NodeProperty : mappage défini par l'utilisateur ou le système d'une chaîne à une valeur, pouvant varier pour chaque nœud, par ex., NodeName.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Pour plus d'informations, consultez [Scénarios d'application](../service-fabric-application-scenarios).
 

<!---HONumber=August15_HO6-->