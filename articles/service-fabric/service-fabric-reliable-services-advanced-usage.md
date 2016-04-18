<properties
   pageTitle="Utilisation avancée de Reliable Services | Microsoft Azure"
   description="En savoir plus sur l’utilisation avancée de Reliable Services de Service Fabric pour améliorer la flexibilité de vos services."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Utilisation avancée du modèle de programmation de services fiables
Azure Service Fabric simplifie l’écriture et la gestion des services fiables avec et sans état. Ce guide vous présentera les utilisations avancées de Reliable Services offrant plus de contrôle et de flexibilité sur vos services. Avant de lire ce guide, familiarisez-vous avec [le modèle de programmation de services fiables](service-fabric-reliable-services-introduction.md).

Les services avec et sans état ont deux points d’entrée principaux pour le code utilisateur :

 - `RunAsync` est un point d’entrée à usage général pour votre code de service.
 - `CreateServiceReplicaListeners` et `CreateServiceInstanceListeners` sont utilisés pour l’ouverture des écouteurs de communication pour les requêtes du client.
 
Pour la plupart des services, ces deux points d’entrée sont suffisants. Dans de rares cas, lorsque davantage de contrôle sur le cycle de vie d’un service est requis, des événements de cycle de vie supplémentaires sont disponibles.

## Cycle de vie d’instance de service sans état

Le cycle de vie d’un service sans état est très simple. Un service sans état peut uniquement être ouvert, fermé ou abandonné. `RunAsync` dans un service sans état est exécuté lorsqu’une instance de service est ouverte, et annulée lorsqu’une instance de service est fermée ou abandonnée.

Bien que `RunAsync` devrait suffire dans presque tous les cas, les événements d’ouverture, de fermeture et d’abandon dans un service sans état sont également disponibles :

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync est appelée lorsque l'instance de service sans état va être utilisée. Les tâches d'initialisation de service étendues peuvent être démarrées à ce stade.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync est appelée lorsque l’instance de service sans état va être correctement arrêtée. Cela peut se produire lorsque le code du service est mis à niveau, lorsque l'instance du service est déplacée en raison d'un équilibrage de charge, ou lorsqu'une erreur transitoire est détectée. OnCloseAsync peut être utilisée pour fermer toutes les ressources, arrêter tout traitement en arrière-plan, finaliser l'enregistrement de l'état externe, ou fermer les connexions existantes, en toute sécurité.

- `void OnAbort()` OnAbort est appelée lorsque l’instance du service sans état est volontairement arrêtée. Cette méthode est généralement appelée lorsqu'une erreur permanente est détectée sur le nœud, ou lorsque Service Fabric ne peut pas gérer de façon fiable le cycle de vie de l'instance du service en raison de défaillances internes.

## Cycle de vie d’un réplica de service avec état

Le cycle de vie d’un réplica de service avec état est beaucoup plus complexe qu’une instance de service sans état. En plus des événements d’ouverture, de fermeture et d’abandon, un réplica de service avec état subit des modifications de rôle pendant sa durée de vie. Lorsqu’un réplica de service avec état change de rôle, l’événement `OnChangeRoleAsync` est déclenché :

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync est appelée quand le service avec état change de rôle, par exemple quand il devient un service principal ou secondaire. Les réplicas principaux se voient affecter un état d’écriture (ils sont autorisés à créer des collections fiables et à écrire dans celles-ci). Les réplicas secondaires se voient affecter un état de lecture (ils peuvent uniquement lire à partir de collections fiables existantes). Vous pouvez démarrer ou mettre à jour les tâches en arrière-plan en réponse à des changements de rôle, par exemple une validation en lecture seule, une création de rapport ou l’exploration de données sur un service secondaire.

Dans un service avec état, seul le réplica principal dispose d’un accès en écriture à l’état, ce qui a généralement lieu lorsque le service effectue un travail réel. La méthode `RunAsync` dans un service avec état est exécutée uniquement lorsque le réplica de service avec état est principal. La méthode `RunAsync` est annulée lorsque le rôle d’un réplica principal est modifié, ainsi que pendant les événements de fermeture et d’abandon.

L’utilisation de l’événement `OnChangeRoleAsync` vous permet d’effectuer le travail en fonction du rôle de réplica, ainsi que suite à la modification du rôle.

Un service avec état fournit également les mêmes quatre événements du cycle de vie qu’un service sans état, avec la même sémantique et les mêmes cas d’utilisation :

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## Étapes suivantes
Pour accéder à des rubriques plus avancées concernant Service Fabric, consultez les articles suivants :

- [Configuration de Reliable Services avec état](service-fabric-reliable-services-configuration.md)

- [Présentation de l’intégrité de Service Fabric](service-fabric-health-introduction.md)

- [Utilisation des rapports d’intégrité système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configuration de services avec Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-configure-services.md)

<!---HONumber=AcomDC_0406_2016-->