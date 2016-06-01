<properties
   pageTitle="Gestion des états de Reliable Actors | Microsoft Azure"
   description="Décrit la manière dont l’état de Reliable Actors est géré, conservé et répliqué pour garantir une haute disponibilité."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Gestion des états de Reliable Actors

Reliable Actors désignent des objets monothread capables d’encapsuler la logique et l’état. Étant donné que les acteurs s’exécutent sur Reliable Services, ils peuvent conserver leur état de façon fiable à l’aide des mêmes mécanismes de persistance et de réplication que ceux utilisés par Reliable Services. De cette façon, les acteurs ne perdent pas leur état après des incidents, après une réactivation consécutive à une opération de garbage collection, ou encore après leur déplacement entre des nœuds d’un cluster dans le cadre d’un équilibrage des ressources ou de mises à niveau.

## Persistance et réplication de l’état

Toutes les instances Reliable Actors sont considérées comme des instances *avec état* étant donné que chaque instance d’acteur est mappée à un identifiant unique. Autrement dit, les appels répétés au même ID d’acteur seront acheminés vers la même instance d’acteur. Ce principe de fonctionnement s’oppose à un système sans état dans lequel rien ne peut garantir que les appels client seront acheminés vers le même serveur à chaque fois. Pour cette raison, les services d’acteur sont toujours des services avec état.

Toutefois, même si les acteurs sont considérés comme des services avec état, cela ne signifie pas qu’ils doivent stocker l’état de manière fiable. Les acteurs peuvent choisir le niveau de persistance et de réplication de l’état en fonction de leurs exigences en matière de stockage de données :

 - **État persistant :** l’état est conservé sur le disque et est répliqué sur au moins 3 réplicas. Il s’agit de l’option de stockage d’état la plus fiable, où l’état peut persister après une panne complète du cluster.
 - **État volatil :** l’état est répliqué sur au moins 3 réplicas et est conservé uniquement en mémoire. Cette option garantit une résilience contre les défaillances de nœud et d’acteur, ainsi que pendant les mises à niveau et l’équilibrage des ressources. L’état n’est toutefois pas conservé sur le disque ; une perte simultanée de tous les réplicas entraînera donc également la perte de l’état.
 - **État non persistant :** l’état n’est ni répliqué ni écrit sur le disque. Pour les acteurs qui n’ont simplement pas besoin de maintenir leur état de manière fiable.
 
Chaque niveau de persistance représente simplement une autre configuration du *fournisseur d’état* et de la *réplication* de votre service. Le *fournisseur d’état* (le composant Reliable Service conçu pour stocker l’état) détermine si l’état sera ou non écrit sur le disque. La réplication varie selon le nombre de réplicas avec lesquels est déployé un service. De la même manière que Reliable Services, le fournisseur d’état et le nombre de réplicas peuvent être définis manuellement de façon très simple. L’infrastructure d’acteurs fournit un attribut, qui, lorsqu’il est utilisé sur un acteur, sélectionne automatiquement un fournisseur d’état par défaut et génère automatiquement des paramètres pour le nombre de réplicas afin d’obtenir un de ces trois paramètres de persistance.

### État persistant
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```  
Ce paramètre utilise un fournisseur d’état qui stocke les données sur disque et définit automatiquement le nombre de réplicas de service à 3.

### État volatil
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
Ce paramètre utilise un fournisseur d’état uniquement en mémoire et définit le nombre de réplicas à 3.

### État non persistant

```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
Ce paramètre utilise un fournisseur d’état uniquement en mémoire et définit le nombre de réplicas à 1.

### Valeurs par défaut et paramètres générés

Lorsque vous utilisez l’attribut `StatePersistence`, un fournisseur d’état est automatiquement sélectionné pour vous lors de l’exécution au démarrage du service d’acteur. Toutefois, le nombre de réplicas est défini au moment de la compilation par les outils de génération d’acteurs Visual Studio. Ces outils génèrent automatiquement un *service par défaut* pour le service d’acteur dans ApplicationManifest.xml. Les paramètres sont créés pour la **taille minimale du jeu de réplicas** et la **taille cible du jeu de réplicas**. Vous pouvez bien évidemment modifier ces paramètres manuellement ; cependant, chaque fois que l’attribut `StatePersistence` est modifié, les paramètres seront rétablis aux valeurs de taille de jeu de réplicas par défaut pour l’attribut `StatePersistence`, ce qui remplacera toutes les valeurs précédentes.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Gestionnaire d’état

Chaque instance d’acteur possède son propre gestionnaire d’état, c’est-à-dire une structure de données de type dictionnaire qui stocke les paires clé-valeur de manière fiable. Le gestionnaire d’état est un wrapper autour d’un fournisseur d’état. Il peut être utilisé pour stocker des données quel que soit le paramètre de persistance utilisé, mais il ne garantit pas qu’un service d’acteur en cours d’exécution puisse être modifié pour passer d’un paramètre d’état volatil (en mémoire uniquement) à un paramètre d’état persistant via une mise à niveau propagée, tout en conservant les données. Toutefois, il est possible de modifier le nombre de réplicas d’un service en cours d’exécution.

Les clés du gestionnaire d’état doivent être des chaînes ; les valeurs sont génériques et peuvent être de n’importe quel type, y compris les types personnalisés. Les valeurs stockées dans le gestionnaire d’état doivent être sérialisables en contrat de données, car elles peuvent être transmises sur le réseau vers d’autres nœuds pendant la réplication et peuvent être écrites sur le disque, en fonction du paramètre de persistance d’état d’un acteur.

Le gestionnaire d’état expose pour la gestion des états des méthodes de dictionnaire courantes similaires à celles disponibles dans Reliable Dictionary.

### Accès à l’état

L’état est accessible via le gestionnaire d’état par l’intermédiaire d’une clé. Les méthodes du gestionnaire d’état sont toutes asynchrones car elles peuvent nécessiter des E/S disque lorsque les acteurs sont à l’état persistant. Lors du premier accès, les objets d’état sont mis en mémoire cache. Les opérations d’accès répétées permettent d’accéder aux objets directement à partir de la mémoire et sont retournées de façon synchrone sans entraîner d’E/S disque ou de surcharge asynchrone en cas de changement de contexte. Un objet d’état est supprimé du cache dans les cas suivants :

 - Une méthode d’acteur lève une exception non gérée après avoir récupéré un objet à partir du gestionnaire d’état.
 - Un acteur est réactivé soit après avoir été désactivé, soit en raison d’un échec.
 - Si le fournisseur d’état écrit l’état sur le disque. Ce comportement dépend de l’implémentation du fournisseur d’état. Le fournisseur d’état par défaut pour le paramètre `Persisted` présente ce comportement. 

L’état peut être récupéré à l’aide d’une opération *Get* standard qui lève l’exception `KeyNotFoundException` s’il n’existe aucune entrée pour la clé donnée :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```

L’état peut être également récupéré à l’aide d’une opération *TryGet* qui ne lève pas d’exception s’il n’existe aucune entrée pour la clé donnée :

```csharp
class MyActor : Actor, IMyActor
{
    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```

### Enregistrement de l’état

Les méthodes de récupération du gestionnaire d’état renvoient une référence à un objet dans la mémoire locale. La modification de cet objet dans la mémoire locale uniquement ne permet pas de l’enregistrer durablement. Lorsqu’un objet est récupéré à partir du gestionnaire d’état puis modifié, il doit être réinséré dans le gestionnaire d’état afin d’être enregistré de façon durable.

L’état peut être inséré en utilisant une méthode *Set* inconditionnelle, équivalant à la syntaxe `dictionary["key"] = value` :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```

L’état peut être ajouté à l’aide d’une méthode *Add* qui lève l’exception `InvalidOperationException` lors d’une tentative d’ajout d’une clé existante :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```

L’état peut être également ajouté à l’aide d’une méthode *TryAdd* qui ne lève pas d’exception lors d’une tentative d’ajout d’une clé existante :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```

À la fin d’une méthode d’acteur, le gestionnaire d’état enregistre automatiquement toutes les valeurs qui ont été ajoutées ou modifiées par une opération insert ou update. Une opération « save » peut inclure la conservation sur disque et la réplication, selon les paramètres utilisés. Les valeurs qui n’ont pas été modifiées ne sont pas conservées ou répliquées. Si aucune valeur n’a été modifiée, l’opération n’aura aucun effet. En cas d’échec de l’enregistrement, l’état modifié est ignoré et l’état d’origine rechargé.

L’état peut également être enregistré manuellement en appelant la méthode `SaveStateAsync` sur la base d’acteur :

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);
            
    await this.SaveStateAsync();
}
```

### Suppression de l’état

L’état peut être supprimé définitivement du gestionnaire d’état d’un acteur en appelant la méthode *Remove*. Cette méthode lève l’exception `KeyNotFoundException` lorsque vous tentez de supprimer une clé qui n’existe pas :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```

L’état peut également être supprimé définitivement à l’aide de la méthode *TryRemove*, laquelle ne lèvera pas d’exception si vous tentez de supprimer une clé qui n’existe pas :

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```

## Étapes suivantes
 - [Sérialisation du type d’acteur](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
 - [Polymorphisme des acteurs et modèles de conception orientée objet](service-fabric-reliable-actors-polymorphism.md)
 - [Diagnostics et surveillance des performances d’acteur](service-fabric-reliable-actors-diagnostics.md)
 - [Documentation de référence de l’API Actor](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemple de code](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0518_2016-->