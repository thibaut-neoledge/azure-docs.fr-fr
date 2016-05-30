<properties
   pageTitle="Azure Service Fabric sur Linux | Microsoft Azure"
   description="Les clusters Service Fabric prenant en charge Linux et Java, vous pouvez déployer et héberger des applications Service Fabric écrites en Java sur Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/13/2016"
   ms.author="SubramaR"/>

# Service Fabric sur Linux

Service Fabric est actuellement disponible en version préliminaire limitée sur Linux. Il vous permet de créer, déployer et gérer des applications à haut niveau de disponibilité et d’extensibilité dans l’environnement Linux de la même manière que sur Windows. De plus, les infrastructures générales de Service Fabric (Reliable Services et Reliable Actors) peuvent désormais être conçues dans Java.

## Systèmes d’exploitation et langages de programmation pris en charge

La version préliminaire limitée vous permet de créer des clusters de développement à boîtier unique ainsi que des clusters de plusieurs machines Azure exécutant Ubuntu Server 15.10.

Vous pouvez créer des [services exécutables invités](service-fabric-deploy-existing-app.md) dans tous les langages ou infrastructures. Vous pouvez également utiliser Java pour créer des services basés sur les infrastructures Reliable Services et Reliable Actors.

>[AZURE.NOTE] Les infrastructures Reliable Collections ne sont pas encore prises en charge dans Java.

## Participer à la version préliminaire

Si vous souhaitez participer au programme d’évaluation de la version préliminaire limitée, [veuillez répondre à cette enquête](http://aka.ms/sflinuxsurvey) pour nous permettre de mieux comprendre votre scénario et vos besoins. La version préliminaire sera très succincte dans un premier temps, mais nous la développerons au fil du temps.

Notez que Service Fabric sur Linux sera semblable du point de vue conceptuel à la version disponible sur Windows (à l’exception des caractéristiques propres au système d’exploitation et de la prise en charge des langages de programmation). La majeure partie de notre [documentation existante](http://aka.ms/servicefabricdocs) s’applique aussi à cette version et peut vous aider à vous familiariser avec la technologie.

## Étapes suivantes

Se familiariser avec les infrastructures de programmation [Reliable Actors](service-fabric-reliable-actors-introduction.md) et [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0518_2016-->