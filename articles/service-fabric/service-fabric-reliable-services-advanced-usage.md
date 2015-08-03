<properties
   pageTitle="Utilisation avancée du modèle de programmation des services fiables Service Fabric"
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
   ms.date="06/09/2015"
   ms.author="jesseb"/>

# Utilisation avancée du modèle de programmation de services fiables
Service Fabric simplifie l'écriture et la gestion des services fiables avec et sans état. Ce guide vous présentera les utilisations avancées du modèle de programmation de services fiables offrant plus de contrôle et de flexibilité sur vos services. Avant de lire ce guide, familiarisez-vous avec [le modèle de programmation de services fiables](service-fabric-reliable-services-introduction.md).

## Classes de base de services sans état
La classe de base StatelessService fournit les paramètres CreateCommunicationListener() et RunAsync(), ce qui est suffisant pour la majorité des services sans état. La classe StatelessServiceBase est sous-jacente à la classe StatelessService et présente d'autres événements de cycle de vie du service. Consultez la documentation de référence du développeur sur [StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) et [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx) pour plus d'informations.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize est la première méthode appelée par Service Fabric. Les informations d'initialisation du service sont fournies, notamment le nom du service, l'ID de la partition, l'ID de l'instance et les informations sur le package de code. Aucun traitement complexe ne devrait être effectué ici. L'initialisation de longue durée doit être effectuée dans OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync est appelée lorsque l'instance de service sans état va être utilisée. Les tâches d'initialisation de service étendues peuvent être démarrées à ce stade.

- `Task OnCloseAsync(CancellationToken)` OnCloseAsync est appelée lorsque l'instance de service sans état va être correctement arrêtée. Cela peut se produire lorsque le code du service est mis à niveau, lorsque l'instance du service est déplacée en raison d'un équilibrage de charge, ou lorsqu'une erreur transitoire est détectée. OnCloseAsync peut être utilisée pour fermer toutes les ressources, arrêter tout traitement en arrière-plan, finaliser l'enregistrement de l'état externe, ou fermer les connexions existantes, en toute sécurité.

- `void OnAbort()` OnAbort est appelée lorsque l'instance du service sans état est volontairement arrêtée. Cette méthode est généralement appelée lorsqu'une erreur permanente est détectée sur le nœud, ou lorsque Service Fabric ne peut pas gérer de façon fiable le cycle de vie de l'instance du service en raison de défaillances internes.

## Classes de base de services avec état
La classe de base StatefulService devrait suffire pour la plupart des services avec état. Comme pour les services sans état, la classe StatefulServiceBase est sous-jacente à la classe StatefulService et présente d'autres événements de cycle de vie du service. En outre, elle vous permet de proposer un fournisseur d'état fiable et personnalisé, avec éventuellement la prise en charge d'écouteurs de communication sur les services secondaires. Consultez la documentation de référence du développeur sur [StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) et [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx) pour plus d'informations.

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync est appelée lorsque le service avec état change de rôle, par exemple devient un service principal ou secondaire. Les réplicas principaux disposent d'un état d'écriture (ils sont autorisés à créer et à écrire dans les collections fiables), tandis que les réplicas secondaires disposent d'un état de lecture (ils peuvent uniquement lire à partir de collections fiables existantes). Vous pouvez démarrer ou mettre à jour les tâches en arrière-plan en réponse à des changements de rôle, par exemple une validation en lecture seule, une création de rapport ou l'exploration de données sur un service secondaire.

- `IStateProviderReplica CreateStateProviderReplica()` Un service avec état doit disposer d'un fournisseur d'état fiable. StatefulService utilise la classe ReliableStateManager, qui fournit les collections fiables (par exemple, les dictionnaires et les files d'attente). Vous pouvez choisir votre propre fournisseur si vous souhaitez gérer l'état vous-même ou étendre les fonctionnalités d'un des fournisseurs d'état intégrés.

- `bool EnableCommunicationListenerOnSecondary { get; }` Par défaut, les écouteurs de communication sont créés uniquement sur les services principaux. Grâce à StatefulService et à StatefulServiceBase, vous pouvez remplacer cette propriété pour permettre la création d'écouteurs de communication sur les services secondaires. Vous pouvez autoriser vos services secondaires à traiter les requêtes en lecture seule afin d'améliorer le débit sur les charges de lecture intensives.

    > [AZURE.NOTE]Il vous incombe de vérifier que vos services secondaires n'essaient pas de créer ou d'écrire sur des collections fiables. Les tentatives d'écriture sur un service secondaire provoqueront une exception qui, si elle n'est pas traitée, entraînera la fermeture et la réouverture du réplica.

StatefulServiceBase fournit également les mêmes quatre événements du cycle de vie que StatelessServiceBase, avec la même sémantique et les mêmes cas d'utilisation :

- `void OnInitialize(StatelessServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Étapes suivantes
Pour parcourir des rubriques plus avancées concernant Service Fabric, consultez les articles suivants.

- [Configuration des services fiables avec état](service-fabric-reliable-services-configuration.md)

- [Présentation de l'intégrité de Service Fabric](../service-fabric/service-fabric-health-introduction.md)

- [Utilisation des rapports d'intégrité système pour la résolution des problèmes](../service-fabric/service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Vue d'ensemble des contraintes de placement](../service-fabric/service-fabric-placement-constraint.md)

- [Sécuriser le trafic de réplication des services avec état dans Azure Service Fabric](../service-fabric/service-fabric-replication-security.md)
 

<!---HONumber=July15_HO4-->