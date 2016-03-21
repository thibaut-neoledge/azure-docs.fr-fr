<properties
   pageTitle="Utilisation avancée du modèle de programmation Reliable Services | Microsoft Azure"
   description="En savoir plus sur l'utilisation avancée du modèle de programmation de services fiables Service Fabric pour améliorer la flexibilité de vos services."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Utilisation avancée du modèle de programmation de services fiables
Azure Service Fabric simplifie l’écriture et la gestion des services fiables avec et sans état. Ce guide vous présentera les utilisations avancées du modèle de programmation de services fiables offrant plus de contrôle et de flexibilité sur vos services. Avant de lire ce guide, familiarisez-vous avec [le modèle de programmation de services fiables](service-fabric-reliable-services-introduction.md).

## Classes de base pour les services sans état
La classe de base StatelessService fournit les paramètres RunAsync() et CreateServiceInstanceListeners(), ce qui est suffisant pour la majorité des services sans état. La classe StatelessServiceBase est sous-jacente à la classe StatelessService et présente d'autres événements de cycle de vie du service. Si vous avez besoin de contrôle supplémentaire ou de davantage de flexibilité, vous pouvez dériver de StatelessServiceBase Consultez la documentation de référence du développeur sur [StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) et [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx) pour plus d'informations.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize est la première méthode appelée par Service Fabric. Les informations d’initialisation du service sont fournies, notamment le nom du service, l’ID de la partition, l’ID de l’instance et les informations sur le package de code. Aucun traitement complexe ne devrait être effectué ici. L'initialisation de longue durée doit être effectuée dans OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync est appelée lorsque l'instance de service sans état va être utilisée. Les tâches d'initialisation de service étendues peuvent être démarrées à ce stade.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync est appelée quand l’instance de service sans état va être correctement arrêtée. Cela peut se produire lorsque le code du service est mis à niveau, lorsque l'instance du service est déplacée en raison d'un équilibrage de charge, ou lorsqu'une erreur transitoire est détectée. OnCloseAsync peut être utilisée pour fermer toutes les ressources, arrêter tout traitement en arrière-plan, finaliser l'enregistrement de l'état externe, ou fermer les connexions existantes, en toute sécurité.

- `void OnAbort()` OnAbort est appelée quand l’instance du service sans état est volontairement arrêtée. Cette méthode est généralement appelée lorsqu'une erreur permanente est détectée sur le nœud, ou lorsque Service Fabric ne peut pas gérer de façon fiable le cycle de vie de l'instance du service en raison de défaillances internes.

## Classes de base pour les services avec état
La classe de base StatefulService devrait suffire pour la plupart des services avec état. Comme pour les services sans état, la classe StatefulServiceBase est sous-jacente à la classe StatefulService et présente d'autres événements de cycle de vie du service. En outre, vous pouvez l’utiliser pour proposer un fournisseur d’état fiable et personnalisé, avec éventuellement la prise en charge d’écouteurs de communication sur les services secondaires. Si vous avez besoin de contrôle supplémentaire ou de davantage de flexibilité, vous pouvez dériver de StatefulServiceBase. Consultez la documentation de référence du développeur sur [StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) et [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx) pour plus d'informations.

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync est appelée quand le service avec état change de rôle, par exemple quand il devient un service principal ou secondaire. Les réplicas principaux se voient affecter un état d’écriture (ils sont autorisés à créer des collections fiables et à écrire dans celles-ci). Les réplicas secondaires se voient affecter un état de lecture (ils peuvent uniquement lire à partir de collections fiables existantes). Vous pouvez démarrer ou mettre à jour les tâches en arrière-plan en réponse à des changements de rôle, par exemple une validation en lecture seule, une création de rapport ou l’exploration de données sur un service secondaire.

- `IStateProviderReplica CreateStateProviderReplica()` Un service avec état doit disposer d'un fournisseur d'état fiable. StatefulService utilise la classe ReliableStateManager, qui fournit les collections fiables (par exemple, les dictionnaires et les files d'attente). Vous pouvez substituer cette méthode pour configurer la classe ReliableStateManager en transmettant une ReliableStateManagerConfiguration à son constructeur. Vous pouvez ensuite fournir des sérialiseurs d’état personnalisé, spécifier ce qui se passe quand des données ont peut-être été perdues et configurer le réplicateur ou les fournisseurs d’état.

StatefulServiceBase fournit également les mêmes quatre événements du cycle de vie que StatelessServiceBase, avec la même sémantique et les mêmes cas d'utilisation :

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Étapes suivantes
Pour accéder à des rubriques plus avancées concernant Service Fabric, consultez les articles suivants :

- [Configuration de Reliable Services avec état](service-fabric-reliable-services-configuration.md)

- [Présentation de l’intégrité de Service Fabric](service-fabric-health-introduction.md)

- [Utilisation des rapports d’intégrité système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Configuration de services avec Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-configure-services.md)

<!---HONumber=AcomDC_0309_2016-->