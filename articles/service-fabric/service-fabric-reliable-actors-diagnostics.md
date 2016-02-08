<properties
   pageTitle="Diagnostics et surveillance des performances pour les acteurs | Microsoft Azure"
   description="Cet article décrit les fonctionnalités de diagnostic et de surveillance des performances dans le runtime Reliable Actors de Service Fabric, notamment les événements et les compteurs de performances émis par celui-ci."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="abhisram"/>

# Diagnostics et surveillance des performances pour Reliable Actors
Le runtime Reliable Actors émet des événements [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) et les [compteurs de performances](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Ils fournissent des informations sur le fonctionnement du runtime et permettent de résoudre les problèmes et de surveiller les performances.

## Événements EventSource
Le nom du fournisseur EventSource du runtime Reliable Actors est « Microsoft-ServiceFabric-Actors ». Les événements issus de cette source d'événements s'affichent dans la fenêtre [Événements de diagnostics](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quand l'application d'acteur est [déboguée dans Visual Studio](service-fabric-debugging-your-application.md).

[PerfView](http://www.microsoft.com/download/details.aspx?id=28567), les [Diagnostics Azure](../cloud-services-dotnet-diagnostics.md), la [Journalisation sémantique](https://msdn.microsoft.com/library/dn774980.aspx) et [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent) sont des exemples d'outils et de technologies permettant de collecter et/ou d'afficher des événements EventSource.

### Mots clés
Tous les événements qui appartiennent à la source d'événements Acteurs fiables sont associés à un ou plusieurs mots clés. Cela permet de filtrer les événements collectés. Les bits de mots clés suivants sont définis.

|Bit|Description|
|---|---|
|0x1|Jeu d'événements importants qui résument le fonctionnement du runtime Fabric Actors.|
|0x2|Jeu d'événements décrivant les appels de méthode d'acteur. Pour plus d'informations, consultez la [rubrique d'introduction sur les acteurs](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Jeu d'événements liés à l'état de l'acteur. Pour plus d'informations, consultez la rubrique sur [les acteurs avec état](service-fabric-reliable-actors-introduction.md#stateful-actors).|
|0x8|Jeu d'événements liés à l'accès concurrentiel en alternance dans l'acteur. Pour plus d'informations, consultez la rubrique sur [l'accès concurrentiel](service-fabric-reliable-actors-introduction.md#concurrency).|

## Compteurs de performances
Le runtime Acteurs fiables définit les catégories suivantes de compteur de performances.

|Catégorie|Description|
|---|---|
|Service Fabric Actor|Compteurs spécifiques des acteurs Azure Service Fabric. Par exemple, la durée d'enregistrement de l'état de l'acteur.|
|Service Fabric Actor Method|Compteurs spécifiques des méthodes implémentées par les acteurs Service Fabric. Par exemple, la fréquence à laquelle une méthode d'acteur est appelée.|

Chacune des catégories ci-dessus possède un ou plusieurs compteurs.

L'application [Analyseur de performances Windows](https://technet.microsoft.com/library/cc749249.aspx), disponible par défaut dans le système d'exploitation Windows, peut être utilisée pour collecter et afficher les données de compteur de performances. [Diagnostics Azure](../cloud-services-dotnet-diagnostics.md) est une autre option pour collecter les données de compteur de performances et les télécharger dans les tables Azure.

### Noms d'instance de compteur de performances
Un cluster avec un grand nombre de services d'acteur ou de partitions de service d'acteur disposera d'un grand nombre d'instances de compteur de performances d'acteur. Les noms d'instance de compteur de performances peuvent aider à identifier la [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) et la méthode d'acteur (le cas échéant) spécifiques associées à l'instance de compteur de performances.

#### Catégorie Service Fabric Actor
Pour la catégorie `Service Fabric Actor`, les noms d'instance de compteur ont le format suivant :

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* est la représentation sous forme de chaîne de l'ID de partition Service Fabric associée à l'instance de compteur de performances. L'ID de partition est un GUID et sa représentation sous forme de chaîne est générée à l'aide de la méthode [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) avec le spécificateur de format « D ».

*ActorRuntimeInternalID* est la représentation sous forme de chaîne d'un entier 64 bits généré par le runtime Fabric Actors pour son usage interne. Il est inclus dans le nom de l'instance de compteur de performances pour garantir l'unicité et éviter tout conflit avec d'autres noms d'instance de compteur de performances. Les utilisateurs ne doivent pas tenter d'interpréter cette partie du nom de l'instance de compteur de performances.

Voici un exemple de nom d'instance de compteur pour un compteur appartenant à la catégorie `Service Fabric Actor` :

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

Dans l'exemple ci-dessus, `2740af29-78aa-44bc-a20b-7e60fb783264` est la représentation sous forme de chaîne de l'ID de partition Service Fabric et `635650083799324046` est l'ID 64 bits généré pour l'usage interne du runtime.

#### Catégorie Service Fabric Actor Method
Pour la catégorie `Service Fabric Actor Method`, les noms d'instance de compteur ont le format suivant :

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* est le nom de la méthode d'acteur associée à l'instance de compteur de performances. Le format du nom de méthode est déterminé selon une logique du runtime Fabric Actors qui équilibre la lisibilité du nom avec des contraintes sur la longueur maximale des noms d'instance de compteur de performances sous Windows.

*ActorsRuntimeMethodId* est la représentation sous forme de chaîne d'un entier 32 bits généré par le runtime Fabric Actors pour son usage interne. Il est inclus dans le nom de l'instance de compteur de performances pour garantir l'unicité et éviter tout conflit avec d'autres noms d'instance de compteur de performances. Les utilisateurs ne doivent pas tenter d'interpréter cette partie du nom de l'instance de compteur de performances.

*ServiceFabricPartitionID* est la représentation sous forme de chaîne de l'ID de partition Service Fabric associée à l'instance de compteur de performances. L'ID de partition est un GUID et sa représentation sous forme de chaîne est générée à l'aide de la méthode [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) avec le spécificateur de format « D ».

*ActorRuntimeInternalID* est la représentation sous forme de chaîne d'un entier 64 bits généré par le runtime Fabric Actors pour son usage interne. Il est inclus dans le nom de l'instance de compteur de performances pour garantir l'unicité et éviter tout conflit avec d'autres noms d'instance de compteur de performances. Les utilisateurs ne doivent pas tenter d'interpréter cette partie du nom de l'instance de compteur de performances.

Voici un exemple de nom d'instance de compteur pour un compteur appartenant à la catégorie `Service Fabric Actor Method` :

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

Dans l'exemple ci-dessus, `ivoicemailboxactor.leavemessageasync` est le nom de la méthode, `2` est l'ID 32 bits généré pour l'usage interne du runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` est la représentation sous forme de chaîne de l'ID de partition Service Fabric et `635650083804480486` est l'ID 64 bits généré pour l'usage interne du runtime.

## Liste d'événements et de compteurs de performances

### Événements et compteurs de performances de la méthode d'acteur
Le runtime Reliable Actors émet les événements suivants liés aux [méthodes d'acteur](service-fabric-reliable-actors-introduction.md#actors).

|Nom de l'événement|ID de l’événement|Level|Mot clé|Description|
|---|---|---|---|---|
|ActorMethodStart|7|Détaillé|0x2|Le runtime Actors est sur le point d'appeler une méthode d'acteur.|
|ActorMethodStop|8|Détaillé|0x2|Une méthode d’acteur a fini de s’exécuter. Cela signifie que l'appel asynchrone du runtime à la méthode d'acteur a été retourné et que la tâche retournée par la méthode d'acteur est terminée.|
|ActorMethodThrewException|9|Avertissement|0x3|Une exception a été levée pendant l'exécution d'une méthode d'acteur, pendant l'appel asynchrone du runtime à la méthode d'acteur ou pendant l'exécution de la tâche retournée par la méthode d'acteur. Cet événement indique une sorte de défaillance dans le code de l'acteur qui nécessite un examen.|

Le runtime Acteurs fiables publie les compteurs de performances suivants liés à l'exécution des méthodes d'acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Service Fabric Actor Method|Appels/s|Nombre de fois où la méthode de service d'acteur est appelée par seconde|
|Service Fabric Actor Method|Moyenne en millisecondes par appel|Durée d'exécution de la méthode de service d'acteur en millisecondes|
|Service Fabric Actor Method|Exceptions levées/s|Nombre de fois où la méthode de service d'acteur lève une exception par seconde|

### Événements et compteurs de performances de l'accès concurrentiel
Le runtime Reliable Actors émet les événements suivants liés à l'[accès concurrentiel](service-fabric-reliable-actors-introduction.md#concurrency).

|Nom de l'événement|ID de l’événement|Level|Mot clé|Description|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Détaillé|0x8|Cet événement est écrit au début de chaque nouveau tour d'un acteur. Il contient le nombre d'appels d'acteur en attente d'acquisition du verrou par acteur qui applique l'accès concurrentiel en alternance.|

Le runtime Acteurs fiables publie les compteurs de performances suivants liés à l'accès concurrentiel.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Service Fabric Actor|Nombre d'appels d'acteur en attente du verrou d'acteur|Nombre d'appels d'acteur en attente d'acquisition du verrou par acteur qui applique l'accès concurrentiel en alternance|

### Événements et compteurs de performances de gestion des états d'acteur
Le runtime Reliable Actors émet les événements suivants liés à la [gestion des états d'acteur](service-fabric-reliable-actors-introduction.md#actor-state-management).

|Nom de l'événement|ID de l’événement|Level|Mot clé|Description|
|---|---|---|---|---|
|ActorSaveStateStart|10|Détaillé|0x4|Le runtime Actors est sur le point d'enregistrer l'état de l'acteur.|
|ActorSaveStateStop|11|Détaillé|0x4|Le runtime Actors a terminé d'enregistrer l'état de l'acteur.|

Le runtime Acteurs fiables publie les compteurs de performances suivants liés à la gestion des états d'acteur.

|Nom de la catégorie|Nom du compteur|Description|
|---|---|---|
|Service Fabric Actor|Moyenne en millisecondes par opération d'enregistrement d'état|Durée d'enregistrement de l'état de l'acteur en millisecondes|

### Événements liés aux instances d'acteur sans état
Le runtime Reliable Actors émet les événements suivants liés aux [instances d'acteur sans état](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateless-actors).

|Nom de l'événement|ID de l’événement|Level|Mot clé|Description|
|---|---|---|---|---|
|ServiceInstanceOpen|3|Informations|0x1|Instance d'acteur sans état ouverte. Cela implique que les acteurs pour cette partition peuvent être créés dans cette instance (et éventuellement dans d'autres instances également).|
|ServiceInstanceClose|4|Informations|0x1|Instance d'acteur sans état fermée. Cela implique que les acteurs pour cette partition ne sont plus créés dans cette instance. Aucune nouvelle demande n'est remise aux acteurs déjà créés dans cette instance. Les acteurs sont détruits une fois effectuées toutes les demandes en cours.|

### Événements liés aux réplicas d'acteur avec état
Le runtime Reliable Actors émet les événements suivants liés aux [réplicas d'acteur avec état](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nom de l'événement|ID de l’événement|Level|Mot clé|Description|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informations|0x1|Rôle de réplica d'acteur avec état changé en rôle principal. Cela implique que les acteurs pour cette partition sont créés dans ce réplica.|
|ReplicaChangeRoleFromPrimary|2|Informations|0x1|Rôle de réplica d'acteur avec état changé en rôle non principal. Cela implique que les acteurs pour cette partition ne sont plus créés dans ce réplica. Aucune nouvelle demande n'est remise aux acteurs déjà créés dans ce réplica. Les acteurs sont détruits une fois effectuées toutes les demandes en cours.|

### Événements d'activation et de désactivation des acteurs
Le runtime Reliable Actors émet les événements suivants liés à l'[activation et la désactivation des acteurs](service-fabric-reliable-actors-lifecycle.md).

|Nom de l'événement|ID de l’événement|Level|Mot clé|Description|
|---|---|---|---|---|
|ActorActivated|5|Informations|0x1|Un acteur a été activé.|
|ActorDeactivated|6|Informations|0x1|Un acteur a été désactivé.|

<!---HONumber=AcomDC_0128_2016-->