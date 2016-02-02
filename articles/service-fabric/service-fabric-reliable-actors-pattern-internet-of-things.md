
<properties
   pageTitle="Reliable Actors pour l'Internet des objets | Microsoft Azure"
   description="Service Fabric Reliable Actors constitue la clé de voûte d’une solution qui combine un système de messagerie frontal prenant en charge plusieurs transports, tels que HTTPS, MQTT et AMQP."
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
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Modèle de conception Acteurs fiables : Internet des Objets (IoT)
L’environnement IoT (Internet des Objets) étant devenu la nouvelle tendance accompagnant les avancées technologiques à la fois dans les appareils et les services cloud, les développeurs ont commencé à examiner les principaux blocs de construction pour bâtir leurs systèmes. Le diagramme suivant montre les principaux scénarios obtenus à l’aide d’Azure Service Fabric Reliable Actors :

![Principaux scénarios Service Fabric Reliable Actors][1]

Service Fabric Reliable Actors constitue la clé de voûte (comme une couche intermédiaire) d’une solution qui combine un système de messagerie frontal prenant en charge plusieurs transports tels que HTTPS, MQTT et AMQP, et la communication avec les acteurs représentant des appareils individuels. Comme les acteurs peuvent conserver leur état, la modélisation des flux — en particulier le traitement de flux avec état — et l’agrégation par appareil sont simples. Si les données doivent être conservées, vous pouvez également facilement effectuer des opérations de vidage à la demande ou selon un minuteur tout en conservant les N bits de données les plus récents dans une autre variable pour accélérer l’interrogation.

Notez que dans nos exemples, pour nous concentrer sur le modèle Reliable Actors, nous avons délibérément omis des détails au niveau de l’événement/de la messagerie, ce qui permet aux acteurs de communiquer avec les appareils. Il existe principalement deux scénarios combinés :

* **Collecter les données de télémétrie et d’état d’un seul appareil ou d’un groupe d’appareils et conserver leur état**. Imaginez des dizaines de milliers de pièges à souris (il s’agit d’un scénario client réel) qui transmettent des données, indiquant tout simplement si l’appareil a capturé ou non un rongeur. Ces données sont agrégées par région, et quand suffisamment de souris sont capturées dans une région, un ingénieur est envoyé sur place pour nettoyer les appareils. Un piège à souris comme acteur ? Oui. Un acteur de groupe par région comme agrégation ? Absolument.

* **Envoyer le comportement et la configuration d’un appareil à un ou plusieurs appareils**. Imaginez une situation où un fournisseur de panneaux solaires domestiques peut envoyer différentes configurations en fonction de modèles de consommation et de la saison.

## Groupement des données de télémétrie et des appareils

Commençons par examiner un cas où des appareils (plusieurs dizaines de milliers) sont regroupés et envoient tous des données de télémétrie à leur groupe associé. Dans l'exemple suivant, le client a déployé des appareils dans chaque région. Quand un appareil est allumé, il commence par envoyer un message **ActivateMe** avec des informations pertinentes incluant la position et la version. L’acteur associé à l’appareil (par le biais de l’identifiant de l’appareil) définit l’état initial de l’appareil, par exemple l’enregistrement local de l’état (qui peut également être conservé) et l’inscription d’un acteur de groupe. Dans ce cas, nous avons attribué un groupe aléatoire pour notre simulation.

Dans le cadre du processus d'initialisation, nous pouvons configurer l'appareil en récupérant les données de configuration à partir d'un acteur de groupe ou d'un autre agent. De cette façon, des appareils « basiques » au départ peuvent être améliorés au moment de l’initialisation. Une fois cette opération effectuée, l’appareil et l’acteur sont prêts à envoyer et à traiter les données de télémétrie.

Tous les appareils envoient régulièrement leurs informations de télémétrie à leurs acteurs correspondants. Si un acteur est déjà activé, le même acteur est utilisé. Sinon, il est activé. À ce stade, il peut restaurer l’état à partir d’un magasin stable si cela est nécessaire. Quand l’acteur reçoit les informations de télémétrie, il les stocke dans une variable locale.

Nous faisons cela pour simplifier l’exemple. Dans une implémentation réelle, nous les aurions probablement enregistrées dans un magasin externe. Ainsi, les opérations pourraient surveiller et diagnostiquer l’intégrité et les performances de l’appareil. Enfin, nous envoyons les données de télémétrie à l’acteur de groupe auquel appartient logiquement l’acteur de l’appareil.

## Exemple de code IoT : télémétrie

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

Au niveau du groupe, l’objectif de notre exemple est de contrôler les appareils du groupe et d’agréger les données de télémétrie afin de générer des alertes pour les ingénieurs. Dans ce cas, notre client souhaite envoyer des ingénieurs dans les régions où un certain nombre d’appareils sont défectueux. Bien sûr, notre client souhaite également réduire les coûts en réduisant le temps que les ingénieurs passent sur la route. Pour cette raison, chaque acteur du groupe maintient un état agrégé des appareils défectueux par région. Quand ce nombre atteint un seuil, notre client envoie un technicien dans la région pour réparer ou remplacer les appareils défectueux.

Voyons comment cela fonctionne :

## Exemple de code IoT : regroupement et agrégation

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

Comme nous pouvons le constater, le processus est assez simple. Une fois des tests simples exécutés, le résultat ressemble à ceci :

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Vous pensez peut-être qu’il aurait été préférable de regrouper les appareils par région ? Vous pouvez choisir librement comment regrouper et/ou partitionner les appareils, que ce soit par emplacement géographique, type d’appareil, version, client, etc.

Un point doit ici être pris en compte, concernant l’état de l’appareil, ainsi que la création de rapports et l’analyse : vous devez éviter les requêtes de distribution ramifiées aux acteurs pour générer des acteurs de création de rapports. Les instanciations inutiles et les problèmes de performances qui peuvent en découler représentent deux inconvénients, entre autres. C'est pourquoi nous avons rendu l'illustration de ce modèle explicite. Nous recommandons deux approches pour la création de rapports :

* Utiliser un acteur au niveau du groupe, par exemple une agrégation, afin de conserver une vue pour le groupe. Laisser chaque acteur envoyer de façon proactive uniquement les données pertinentes à cet acteur. L’acteur au niveau du groupe peut servir à afficher l’état des appareils dans le groupe.

* Conserver un magasin explicite conçu pour les rapports. Une agrégation peut mettre en mémoire tampon et envoyer régulièrement des données à un magasin de rapports pour poursuivre l'interrogation et l'analyse.

## Opération sur l'appareil
Pour l’instant, tout se déroule parfaitement. Mais qu’en est-il des opérations sur ces appareils ? Comme dans le cas des appareils, les acteurs peuvent exposer des interfaces opérationnelles et permettre ainsi à un administrateur d’effectuer des opérations sur les appareils. Par exemple, imaginez un administrateur qui souhaite envoyer une nouvelle configuration à un groupe d’appareils à énergie solaire domestiques (un autre scénario réel) en fonction de modifications saisonnières et régionales.

Ici, l’idée est de conserver un contrôle granulaire sur chaque appareil à l’aide d’acteurs « Thing », ainsi qu’un contrôle sur les opérations de groupe à l’aide d’acteurs « ThingGroup ». Cette approche est pertinente, que vous agrégiez des données provenant d’appareils (par exemple, des données de télémétrie) ou envoyiez des données (par exemple, des données de configuration) à un grand nombre d’appareils. La plateforme gère la distribution des acteurs de l’appareil et la messagerie entre eux. Cette opération est totalement transparente pour le développeur.

Concernant les communications de machine à machine (M2M), le modèle « hub-and-spoke » évoqué dans la [section sur les réseaux distribués et les graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md) et l’interaction directe d’acteur à acteur fonctionnent bien. Pour les scénarios M2M, vous pouvez modéliser un acteur « Répertoire/Index » pour un groupe d’appareils qui leur permettrait de découvrir et d’échanger des messages comme illustré ci-dessous :

![Modèle d’un acteur Répertoire/Index pour un groupe d’appareils][2]

Le modèle Service Fabric Reliable Actors gère également la durée de vie des acteurs. Considérez la question ainsi : si vous possédez des appareils toujours connectés, ainsi que des appareils connectés de façon occasionnelle, pourquoi devriez-vous conserver en mémoire un acteur qui surveille un appareil qui se connecte toutes les 14 heures ? Service Fabric vous permet d’enregistrer et de restaurer l’état d’un appareil quand et où vous le souhaitez.

De plus en plus, les clients considèrent Service Fabric Reliable Actors comme la clé de voûte de leurs implémentations IoT.

## Étapes suivantes
[Modèle : cache intelligent](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : composition d’un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Introduction à Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->