<properties
   pageTitle="Vue d’ensemble des modèles de programmation Service Fabric | Microsoft Azure"
   description="Service Fabric propose deux infrastructures pour la création de services : l’infrastructure d’acteurs et l’infrastructure de services. Elles offrent des compromis distincts en termes de simplicité et de contrôle."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="seanmck"/>

# Vue d’ensemble des modèles de programmation Service Fabric

Service Fabric offre plusieurs méthodes pour écrire et gérer vos services. Les services peuvent utiliser les API Service Fabric pour tirer pleinement parti des infrastructures d’application et des fonctionnalités de la plateforme, ou tout simplement être un programme exécutable compilé, écrit dans n'importe quel langage et hébergé sur un cluster Service Fabric.

## Exécutable invité

Un exécutable invité est un exécutable quelconque, écrit dans n'importe quel langage, qui vous permet d’héberger vos applications existantes sur un cluster Service Fabric. Un exécutable invité peut être empaqueté dans une application et hébergé avec d’autres services. Service Fabric gère l'orchestration et la simple gestion de l'exécution de l'exécutable, dans le but de le maintenir en état de marche conformément à la description du service. Toutefois, étant donné que les exécutables invités ne s'intègrent pas directement aux API Service Fabric, ils ne bénéficient pas de l'ensemble des fonctionnalités offertes par la plateforme, notamment les rapports personnalisés d’intégrité et de charge, l'enregistrement des points de terminaison de service et le calcul avec état.

Familiarisez-vous avec les exécutables invités en déployant votre première [application d’exécutable invité](service-fabric-deploy-existing-app.md).

## Services fiables (Reliable Services)

Reliable Services est une infrastructure légère d'écriture de services qui s'intègrent à la plateforme Service Fabric et tirent parti de l'ensemble de ses fonctionnalités. Reliable Services fournissent un ensemble minimal d'API qui permettent au runtime Service Fabric de gérer le cycle de vie de vos services et permettent à vos services d’interagir avec le runtime. L'infrastructure d'application est minime, ce qui vous donne un contrôle total sur les choix de conception et d'implémentation. Elle peut être utilisée pour héberger une autre infrastructure d'application, par exemple ASP.NET MVC ou une API web.

Reliable Services peut être sans état, comme la plupart des plateformes de service, par exemple les serveurs web ou les rôles de travail dans Azure Cloud Services, dans lesquels toutes les instances du service sont égales et l’état est conservé dans une solution externe, telle que la base de données Azure ou Azure Table Storage.

Reliable Services peut également être avec état, ce qui est propre à Service Fabric, où l’état est conservé directement dans le service lui-même à l'aide de Reliable Collections. L’état est hautement disponible grâce à la réplication et distribué grâce au partitionnement, l’ensemble étant géré automatiquement par Service Fabric.

[En savoir plus sur Reliable Services](service-fabric-reliable-services-introduction.md) ou commencez par [écrire votre premier service Reliable Services](service-fabric-reliable-services-quick-start.md).

## Acteurs fiables (Reliable Actors)

Reposant sur Reliable Services, l'infrastructure Reliable Actor est une infrastructure d'application qui implémente le modèle Reliable Actor, selon le modèle de conception des acteurs. L'infrastructure Reliable Actor utilise des unités indépendantes d'état et de calcul avec exécution monothread, nommées acteurs. L'infrastructure Reliable Actor fournit une communication intégrée aux acteurs, ainsi que des configurations prédéfinies de persistance d’état et de montée en charge.

Étant donné que Reliable Actors lui-même est une infrastructure d'application conçue sur Reliable Services, il est entièrement intégré à la plateforme Service Fabric et bénéficie de l'ensemble complet des fonctionnalités offertes par la plateforme.

## Étapes suivantes
[En savoir plus sur Reliable Actors](service-fabric-reliable-actors-introduction.md) ou commencez par [écrire votre premier service Reliable Actors](service-fabric-reliable-actors-get-started.md).

<!---HONumber=AcomDC_0720_2016-->