---
title: Azure Service Fabric sur Linux | Microsoft Docs
description: "Les clusters Service Fabric prenant en charge Linux et Java, vous pouvez déployer et héberger des applications Service Fabric écrites en Java et C# sur Linux."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 459afade-145d-4ee6-b72b-ddf380ccd1bf
ms.service: service-fabric
ms.devlang: Java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2016
ms.author: SubramaR
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 175edd2c45319f197d1df65ae22853ca0dc3d310


---
# <a name="service-fabric-on-linux"></a>Service Fabric sur Linux
La version d’évaluation de Service Fabric sur Linux vous permet de créer, déployer et gérer des applications à haut niveau de disponibilité et d’extensibilité dans l’environnement Linux de la même manière que sur Windows. De plus, les frameworks Service Fabric (Reliable Services et Reliable Actors) sont désormais disponibles dans Java sur Linux, en plus de C# (.NET Core).  Vous pouvez également créer des [services exécutables invités](service-fabric-deploy-existing-app.md) via tous les langages ou frameworks. En outre, la version préliminaire prend également en charge l’orchestration des conteneurs Docker. Les conteneurs Docker peuvent lancer des exécutables invités ou des services Service Fabric natifs, qui ont recours à des frameworks Service Fabric.

Service Fabric sur Linux est conceptuellement équivalent à Service Fabric sur Windows (à l’exception des caractéristiques du système d’exploitation et de la prise en charge du langage de programmation). Par conséquent, la plupart de notre [documentation existante](http://aka.ms/servicefabricdocs) s’applique pour vous permettre de vous familiariser avec la technologie.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Service-Fabric-Linux-Preview/player]
> 
> 

## <a name="supported-operating-systems-and-programming-languages"></a>Systèmes d’exploitation et langages de programmation pris en charge
La version préliminaire limitée vous permet de créer des clusters de développement à boîtier unique ainsi que des clusters de plusieurs machines Azure exécutant Ubuntu Server 16.04. La version préliminaire prend en charge les frameworks Reliable Actors et Reliable Stateless Services dans les langages Java et C# en plus des exécutables invités et de l’orchestration de conteneurs Docker.  

> [!NOTE]
> Les infrastructures Reliable Collections ne sont pas encore prises en charge dans Linux. Par ailleurs, les clusters autonomes ne sont pas gérés. La version préliminaire ne prend en charge que les clusters impliquant plusieurs machines Linux Azure et un boîtier.
> 
> 


## <a name="supported-tooling"></a>Outils pris en charge
La version préliminaire prend en charge l’interaction avec le cluster via l’interface de ligne de commande Azure. Pour les développeurs Java, l’intégration avec Eclipse et Yeoman est assurée, la fonction Eclipse étant prise en charge sous Linux et OSX. L’intégration d’OSX repose sur une machine virtuelle Linux, via vagrant. Pour les développeurs C#, l’intégration avec Yeoman est assurée pour générer des modèles d’application.

## <a name="next-steps"></a>Étapes suivantes
1. Se familiariser avec les infrastructures de programmation [Reliable Actors](service-fabric-reliable-actors-introduction.md) et [Reliable Services](service-fabric-reliable-services-introduction.md).
2. [Préparer votre environnement de développement sur Linux](service-fabric-get-started-linux.md)
3. [Prepare your development environment on OSX (Préparer votre environnement de développement sur OSX)](service-fabric-get-started-mac.md)
4. [Create your first Service Fabric Java application on Linux](service-fabric-create-your-first-linux-application-with-java.md)




<!--HONumber=Jan17_HO5-->


