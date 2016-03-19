<properties
   pageTitle="Contraintes de placement de l’orchestration du cluster Service Fabric | Microsoft Azure"
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
   ms.date="02/03/2016"
   ms.author="masnider"/>

# Vue d’ensemble des contraintes de placement

Azure Service Fabric permet aux développeurs de contraindre le placement des réplicas de service sur les nœuds qui répondent à des conditions particulières. Ces conditions sont exprimées par une expression booléenne évaluée avec des valeurs appropriées, spécifiques au contexte du service.


## Fonctionnalités
Les contraintes de placement vous permettent d’effectuer les opérations suivantes :

- Limiter différents types de services sur différents types de nœuds en définissant des NodeProperties sur les nœuds.

- Cibler les contraintes à appliquer à la fois aux réplicas principaux et aux réplicas secondaires d’un service, ou cibler les contraintes à appliquer uniquement aux réplicas principaux.


## Concepts clés
NodeProperty : mappage défini par l’utilisateur ou le système d’une chaîne à une valeur, pouvant varier pour chaque nœud, par ex. NodeName.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Pour plus d’informations, consultez [Scénarios d’application](service-fabric-application-scenarios.md).

<!---HONumber=AcomDC_0211_2016-->