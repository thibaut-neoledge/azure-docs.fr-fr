---
title: "En savoir plus sur Azure Service Fabric | Microsoft Docs"
description: "Présentation et guide de prise en main de Service Fabric, où les applications sont composées de nombreux microservices pour assurer mise à l’échelle et résilience."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/07/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 47b3fffb2d5c24b7473884e490be19ff17b61b61
ms.openlocfilehash: 9742523c0a1743ff5982e746aa3c99aed8934499
ms.lasthandoff: 02/21/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Vous voulez en savoir plus sur Service Fabric ?
Cette feuille de route d’apprentissage vous aidera à développer des applications évolutives, fiables et facilement gérables dans Service Fabric.

## <a name="the-five-minute-overview"></a>Présentation rapide
Azure Service Fabric est une plateforme de systèmes distribués qui facilite l’empaquetage, le déploiement et la gestion de microservices fiables et évolutifs, et résout les problèmes importants liés au développement et à la gestion d’applications cloud. En utilisant Service Fabric, les développeurs et administrateurs n’ont plus à résoudre les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques exigeantes, sachant qu’elles sont évolutives, fiables et faciles à gérer. Service Fabric représente la plateforme middleware de nouvelle génération pour la création et la gestion de ces applications d’entreprise de niveau 1 à l’échelle du cloud.  

Cette courte vidéo Channel9 présente Service Fabric et les microservices : <center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>Présentation détaillée
Service Fabric vous permet de créer et gérer des applications évolutives et fiables, composées de microservices qui s’exécutent à très haute densité sur un pool partagé d’ordinateurs appelé cluster. Il fournit un runtime sophistiqué pour générer des microservices avec et sans état distribués et évolutifs. Il fournit également des fonctionnalités complètes de gestion d’application pour la configuration, le déploiement, l’analyse, la mise à niveau/mise à jour corrective et la suppression d’applications.  Pour en savoir plus, consultez la [vue d’ensemble de Service Fabric](service-fabric-overview.md).

Pourquoi une approche de conception de microservices ? Toutes les applications évoluent au fil du temps. Les applications réussies évoluent en étant utiles aux utilisateurs. Que savez-vous de vos besoins aujourd’hui et que seront-ils à l’avenir ? Parfois, la sortie d’une application simple en tant que preuve de concept constitue un facteur déterminant (sachant que l’application peut être repensée ultérieurement). Par ailleurs, lorsque des entreprises parlent de créer pour le cloud, elles ont des attentes particulières en matière de croissance et d’utilisation. Le problème, c’est que la croissance et l’évolutivité sont imprévisibles. On aimerait être en mesure de créer un prototype rapidement, tout en étant sûr que l’application peut réagir à une croissance et une utilisation imprévisibles.  L’article [Que sont les microservices ? ](service-fabric-overview-microservices.md) explique comment l’approche de conception de microservices permet de relever ces défis et comment créer des microservices que vous pouvez monter ou descendre en puissance, tester, déployer et gérer de manière indépendante.

Service Fabric offre une plateforme d’infrastructure fiable et flexible qui vous permet d’écrire et d’exécuter de nombreux types de services et d’applications d’entreprise.  Vous pouvez également exécuter toutes vos applications existantes (quel que soit le langage utilisé pour le développement).  Ces applications et microservices peuvent être sans état ou avec état, et font l’objet d’un équilibrage des ressources entre les machines virtuelles pour optimiser l’efficacité. L'architecture unique de Service Fabric vous permet d'exécuter dans vos applications des traitements d'événement, des transactions parallèles, des calculs en mémoire et des analyses de données pratiquement en temps réel. Vous pouvez facilement augmenter ou réduire l’échelle de vos applications (vraiment dehors ou dedans), en fonction de vos besoins en ressources. Pour en savoir plus sur les catégories d’applications et de services que vous pouvez créer et consulter des études de cas clients, lisez l’article [Scénarios d’application](service-fabric-application-scenarios.md).

Cette vidéo Microsoft Virtual Academy, plus longue, décrit les principaux concepts de Service Fabric :<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>Prise en main et création de votre première application 
À l’aide des kits de développement logiciel (SDK) et des outils Service Fabric, vous pouvez développer des applications dans les environnements Windows, Linux ou MacOS et déployer ces applications sur des clusters fonctionnant sous Windows ou Linux.  Grâce aux guides suivants, vous pourrez déployer une application en quelques minutes.  Une fois que vous avez exécuté votre première application, nous vous invitons à télécharger et à exécuter quelques-uns de nos [exemples d’applications](http://aka.ms/servicefabricsamples). En particulier, nous vous recommandons de commencer par les [exemples de prise en main](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)

### <a name="on-windows"></a>Sur Windows
Le kit de développement logiciel Fabric Service inclut un complément à Visual Studio proposant des outils et des modèles pour la création, le déploiement et le débogage d’applications Service Fabric. Ces rubriques vous expliquent pas à pas comment créer votre première application dans Visual Studio et l’exécuter sur votre ordinateur de développement.

[Configurer votre environnement de développement](service-fabric-get-started.md)
[Créer votre première application (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

#### <a name="practical-hands-on-labs"></a>Exercices pratiques en laboratoire
Essayez ces [travaux pratiques (1ère partie)](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) pour vous familiariser avec le flux de développement de bout en bout pour Service Fabric.  Découvrez comment créer un service sans état, configurer des rapports de surveillance et d’intégrité, et effectuer la mise à niveau d’une application. Après cela, passez aux [travaux pratiques (2e partie)](http://aka.ms/sflab2) qui développent les services avec état.


La vidéo Channel9 suivante vous guide tout au long du processus de création d’une application C# dans Visual Studio :  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>Sur Linux
Service Fabric fournit des Kits de développement logiciel (SDK) pour générer des services Linux dans .NET Core et Java. Ces rubriques vous montrent pas à pas comment créer votre première application Java ou C# sur Linux et l’exécuter sur votre ordinateur de développement.
[Configurer votre environnement de développement](service-fabric-get-started-linux.md)
[Créer votre première application (Java)](service-fabric-create-your-first-linux-application-with-java.md)
[Créer votre première application (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)

La vidéo de la Microsoft Virtual Academy suivante vous guide dans le processus de création d’une application Java sur Linux :  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>Sur MacOS
Vous pouvez créer des applications Service Fabric sur MacOS X à des fins d’exécution sur des clusters Linux. Ces articles expliquent comment configurer votre Mac pour le développement et vous montrent pas à pas comment créer votre première application Java sur MacOS et l’exécuter sur une machine virtuelle Ubuntu.
[Configurer votre environnement de développement](service-fabric-get-started-mac.md)
[Créer votre première application (Java)](service-fabric-create-your-first-linux-application-with-java.md)

## <a name="core-concepts"></a>Principaux concepts
Les articles [Terminologie Service Fabric](service-fabric-technical-overview.md), [Modèle d’application](service-fabric-application-model.md) et [Modèles de programmation pris en charge](service-fabric-choose-framework.md) contiennent des concepts et des descriptions supplémentaires, mais voici les informations de base.

<table><tr><th>Principaux concepts</th><th>Au moment du design</th><th>En cours d’exécution</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>Au moment du design : type d’application, type de service, package et manifeste d’application, package et manifeste de service
Un type d’application est le nom/la version affectés à un ensemble de types de service. Il est défini dans un fichier ApplicationManifest.xml et incorporé à un répertoire de package d’application, lequel est ensuite copié dans le magasin d’images du cluster Service Fabric. Vous pouvez ensuite créer une application nommée à partir de ce type d’application et l’exécuter au sein du cluster. 

Un type de service est le nom/la version affectés aux packages de code, aux packages de données et aux packages de configuration d’un service. Il est défini dans un fichier ServiceManifest.xml et incorporé à un répertoire de package de services, lequel est ensuite référencé par le fichier ApplicationManifest.xml d’un package d’application. Au sein du cluster, après avoir créé une application nommée, vous pouvez créer un service nommé à partir d’un des types de service du type d’application. Un type de service est décrit par son fichier ServiceManifest.xml et se compose de paramètres de configuration de service en code exécutable qui sont chargés au moment de l’exécution et de données statiques qui sont consommées par le service.

![Types d’application service Fabric et types de service][cluster-imagestore-apptypes]

Le package d’application est un répertoire de disque contenant le fichier ApplicationManifest.xml du type d’application, qui fait référence aux packages de service pour chaque type de service qui compose le type d’application. Par exemple, un package d’un type d’application de messagerie peut contenir des références à un package de services de File d’attente, un package de services frontaux et un package de services de base de données. Les fichiers du répertoire de package d’application sont copiés dans le magasin d’images du cluster Service Fabric. 

Un package de service est un répertoire de disque contenant le fichier ServiceManifest.xml du type de service, qui fait référence au code, aux données statiques et aux packages de configuration pour le type de service. Les fichiers contenus dans le répertoire de package de service sont référencés par le fichier ApplicationManifest.xml du type d’application. Par exemple, un package de services peut faire référence au code, aux données statiques et aux packages de configuration qui composent un service de base de données.

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>En cours d’exécution : clusters et nœuds, applications nommées, services nommés, partitions et réplicas
Un [cluster Service Fabric](service-fabric-deploy-anywhere.md) est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés. Les clusters peuvent être mis à l’échelle pour des milliers de machines.

Une machine ou une machine virtuelle appartenant à un cluster est appelée « nœud ». Un nom (chaîne) est affecté à chaque nœud. Les nœuds présentent des caractéristiques, telles que des propriétés de placement. Chaque machine ou machine virtuelle est dotée d’un service Windows à démarrage automatique, FabricHost.exe, qui commence à s’exécuter dès le démarrage, puis démarre deux exécutables : Fabric.exe et FabricGateway.exe. Ces deux exécutables constituent le nœud. Pour les scénarios de développement ou de test, vous pouvez héberger plusieurs nœuds sur une seule et même machine ou sur une seule et même machine virtuelle en exécutant plusieurs instances de Fabric.exe et FabricGateway.exe.

Une application nommée est un ensemble de services nommés qui exécute une ou plusieurs fonctions. Un service exécute une fonction complète et autonome (il peut démarrer et s'exécuter indépendamment des autres services) et est composé de code, d’une configuration et de données. Une fois qu’un package d’application est copié dans le magasin d’images, vous créez une instance de l’application au sein du cluster en spécifiant le type d’application du package d’application (à l’aide de son nom/sa version). Chaque instance de type d’application se voit affecter un nom d’URI ressemblant à ceci : *fabric:/MonApplicationNommée*. Dans un cluster, vous pouvez créer plusieurs applications nommées à partir d’un seul type d’application. Vous pouvez également en créer à partir de différents types d’application. Chaque application nommée est gérée, et sa version est gérée indépendamment.

Après avoir créé une application nommée, vous pouvez créer une instance de l’un de ses types de service (un service nommé) au sein du cluster en spécifiant le type de service (à l’aide de son nom/sa version). Chaque instance du type de service reçoit un nom d’URI inclus dans l’étendue de l’URI de son application nommée. Par exemple, si vous créez un service nommé « MaBaseDeDonnées » dans une application nommée « MonApplicationNommée », l’URI ressemble à ceci : *fabric:/MonApplicationNommée/MaBaseDeDonnées*. Dans une application nommée, vous pouvez créer un ou plusieurs services nommés. Chaque service nommé peut posséder son propre schéma de partition et son propre nombre d’instances/réplicas. 

Il existe deux types de services : sans état et avec état.  L’état persistant d’un service sans état est stocké dans un service de stockage externe, par exemple Stockage Azure, Azure SQL Database ou Azure DocumentDB. Utilisez un service sans état si le service est totalement dépourvu de stockage persistant. Un service avec état utilise Service Fabric pour gérer l’état de votre service via ses modèles de programmation Reliable Collections ou Reliable Actors.  

Lors de la création d’un service nommé, vous indiquez un schéma de partition. Les services comportant de grandes quantités d’état fractionnent les données entre les partitions, ce qui permet de les répartir entre les nœuds du cluster. Cela permet d’adapter l’état de votre service nommé. Dans une partition, les services nommés sans état ont des instances tandis que les services nommés avec état ont des réplicas. En règle générale, les services nommés sans état ne possèdent qu’une partition, car ils sont dépourvus d’état interne. Les instances de partition assurent la disponibilité. En cas d’échec d’une instance, les autres instances continuent de fonctionner normalement, puis Service Fabric en crée une. Les services nommés avec état conservent leur état dans les réplicas, et chaque partition possède son propre jeu de réplicas avec tous les états synchronisés. En cas d’échec d’un réplica, Service Fabric en génère un nouveau à partir des réplicas existants.

Le diagramme suivant montre la relation entre les applications et les instances de service, les partitions et les réplicas.

![Partitions et réplicas au sein d'un service][cluster-application-instances]

## <a name="supported-programming-models"></a>Modèles de programmation pris en charge
Service Fabric offre plusieurs méthodes pour écrire et gérer vos services. Les services peuvent utiliser les API Service Fabric pour tirer pleinement parti des infrastructures d’application et des fonctionnalités de la plateforme, ou tout simplement être un programme exécutable compilé, écrit dans n'importe quel langage et hébergé sur un cluster Service Fabric. Pour plus d’informations, consultez [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).

### <a name="guest-executables"></a>Exécutables invités
Un [exécutable invité](service-fabric-deploy-existing-app.md) est un exécutable quelconque, écrit dans n’importe quel langage, qui vous permet d’héberger vos applications existantes sur un cluster Service Fabric avec d’autres services. Toutefois, les exécutables invités ne s’intègrent pas directement aux API Service Fabric. Ils ne bénéficient donc pas de l’ensemble des fonctionnalités offertes par la plateforme, notamment les rapports personnalisés d’intégrité et de charge, l’enregistrement des points de terminaison de service et le calcul avec état.

### <a name="containers"></a>Conteneurs
Par défaut, Service Fabric déploie et active les services en tant que processus. Service Fabric permet également de déployer les services dans des [images de conteneur](service-fabric-containers-overview.md). Important : dans les conteneurs, vous pouvez combiner des processus et des services au sein de la même application.  Actuellement, Service Fabric prend en charge le déploiement de conteneurs Docker sur Linux, et de conteneurs Windows Server sur Windows Server 2016. Dans le modèle d’application Service Fabric, un conteneur représente un hôte d’application sur lequel sont placés plusieurs réplicas de service.  À l’aide de Service Fabric, vous pouvez déployer des applications existantes, des services sans état ou des services avec état dans un conteneur.  

### <a name="reliable-services"></a>Services fiables (Reliable Services)
[Reliable Services](service-fabric-reliable-services-introduction.md) est une infrastructure légère permettant d’écrire des services qui s’intègrent à la plateforme Service Fabric et tirent parti de l’ensemble de ses fonctionnalités. Les services Reliable Services peuvent être sans état (comme la plupart des plateformes de services, par exemple les serveurs web ou les rôles de travail dans Azure Cloud Services). L’état est alors conservé dans une solution externe, telle qu’Azure DB ou Stockage Table Azure. Les services Reliable Services peuvent également être avec état, auquel cas l’état est conservé directement dans le service lui-même à l’aide de Reliable Collections. L’état est hautement disponible grâce à la réplication et distribué grâce au partitionnement, l’ensemble étant géré automatiquement par Service Fabric.

### <a name="reliable-actors"></a>Acteurs fiables (Reliable Actors)
Reposant sur Reliable Services, l’infrastructure [Reliable Actor](service-fabric-reliable-actors-introduction.md) est une infrastructure d’application qui implémente le modèle Virtual Actor, selon le modèle de conception des acteurs. L'infrastructure Reliable Actor utilise des unités indépendantes d'état et de calcul avec exécution monothread, nommées acteurs. L'infrastructure Reliable Actor fournit une communication intégrée aux acteurs, ainsi que des configurations prédéfinies de persistance d’état et de montée en charge.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez à créer un [cluster dans Azure](service-fabric-cluster-creation-via-portal.md) ou un [cluster autonome sur Windows](service-fabric-cluster-creation-for-windows-server.md).
* Essayez de créer un service en utilisant les modèles de programmation [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Approfondissez votre connaissance du [cycle de vie des applications](service-fabric-application-lifecycle.md).
* Découvrez comment [inspecter l’intégrité du cluster et de l’application](service-fabric-health-introduction.md).
* Apprenez à [surveiller et diagnostiquer les services](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Découvrez les [options de support de Service Fabric](service-fabric-support.md).


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

