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
ms.date: 8/9/2017
ms.author: SubramaR
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: dddc9f698d9776999d406117b46285a0f90d9620
ms.contentlocale: fr-fr
ms.lasthandoff: 08/24/2017

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
L’aperçu prend en charge l’interaction avec le cluster via l’interface CLI Service Fabric. Pour les développeurs Java, l’intégration avec Eclipse et Yeoman est assurée, la fonction Eclipse étant prise en charge sous Linux et OSX. L’intégration d’OSX repose sur une machine virtuelle Linux, via vagrant. Pour les développeurs C#, l’intégration avec Yeoman est assurée pour générer des modèles d’application.

## <a name="next-steps"></a>Étapes suivantes

* Se familiariser avec les infrastructures de programmation [Acteurs fiables](service-fabric-reliable-actors-introduction.md) et [Services fiables](service-fabric-reliable-services-introduction.md)
* [Préparer votre environnement de développement sur Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX (Préparer votre environnement de développement sur OSX)](service-fabric-get-started-mac.md)
* [Create your first Service Fabric Java application on Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Configurer l’intégration et le déploiement continus de Service Fabric avec Jenkins et GitHub](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
* [Différences entre Service Fabric Windows/Linux](service-fabric-linux-windows-differences.md)

