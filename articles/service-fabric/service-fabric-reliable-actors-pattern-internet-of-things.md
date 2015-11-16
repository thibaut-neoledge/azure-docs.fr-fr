
<properties
   pageTitle="Reliable Actors pour l'Internet des objets | Microsoft Azure"
   description="Service Fabric Reliable Actors constituent la clé de voûte d'une solution qui combine un système de messagerie frontal prenant en charge plusieurs transports tels que HTTPS, MQTT ou AMQP, puis qui communique avec les acteurs représentant des appareils individuels."
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Modèle de conception Acteurs fiables : Internet des Objets (IoT)
L'environnement IoT étant devenu la nouvelle tendance accompagnant les avancées technologiques à la fois dans les appareils et les services cloud, les développeurs ont commencé à examiner les principaux blocs de construction sur lesquels bâtir leurs systèmes. Le diagramme suivant montre les principaux scénarios obtenus à l'aide des Acteurs fiables Service Fabric :

![][1]

Les Acteurs fiables Service Fabric constituent la clé de voûte (comme une couche intermédiaire) d'une solution qui combine un système de messagerie frontal prenant en charge plusieurs transports tels que HTTPS, MQTT ou AMQP, puis qui communique avec les acteurs représentant des appareils individuels. Comme les acteurs peuvent conserver leur état, la modélisation des flux — en particulier le traitement de flux avec état — et l'agrégation par appareil est simple. Si les données doivent être conservées, alors nous pouvons également facilement vider le cache à la demande ou selon un minuteur tout en conservant facilement les N bits de données les plus récents dans une autre variable pour accélérer l'interrogation. Notez que dans nos exemples, nous avons délibérément omis les détails au niveau de l'événement/de la messagerie, ce qui permettra aux acteurs de communiquer avec les appareils et de se concentrer sur le modèle d'acteur. Il existe principalement deux scénarios combinés :

* *Collecter les données de télémétrie et d'état d'un seul appareil ou d'un groupe d'appareils et conserver leur état*. Imaginez des dizaines de milliers de pièces à souris (oui, il s'agit d'un scénario client réel) qui transmettent des données, indiquant tout simplement si l'appareil a capturé ou non un rongeur. Les données sont agrégées par région, et lorsque suffisamment de souris sont capturées dans une région, un ingénieur est envoyé sur place pour nettoyer les appareils. Un piège à souris comme acteur ? Absolument. Un acteur de groupe par région comme agrégation ? Bien sûr.

* *Envoi du comportement et de la configuration d'un appareil à un ou plusieurs appareils*. Imaginez une situation où un fournisseur de panneaux solaires domestiques peut envoyer différentes configurations en fonction de modèles de consommation et de la saison.

## Groupement des données de télémétrie et des appareils

Commençons par examiner le cas où des appareils, plusieurs dizaines de milliers, sont regroupés et envoient tous des données de télémétrie à leur groupe associé. Dans l'exemple suivant, le client a déployé des appareils dans chaque région. Lorsque l'appareil est allumé, il commence par envoyer un message ActivateMe avec les informations pertinentes sur la position, la version, etc. À son tour, l'acteur associé à l'appareil (via l'identifiant de l'appareil) définit l'état initial de l'appareil, par exemple l'enregistrement local de l'état (qui aurait pu également être conservé) et l'inscription d'un acteur de groupe. Dans ce cas, nous attribuons un groupe aléatoire pour notre simulation.

Dans le cadre du processus d'initialisation, nous pouvons configurer l'appareil en récupérant les données de configuration à partir d'un acteur de groupe ou d'un autre agent. De cette façon, des appareils basiques au départ peuvent être améliorés lors de l'initialisation. Une fois cette opération effectuée, l'appareil et l'acteur sont prêts à envoyer et à traiter les données de télémétrie.

Tous les appareils envoient régulièrement leurs informations de télémétrie à l'acteur correspondant. Si l'acteur est déjà activé, le même acteur sera utilisé. Sinon, il sera activé. À ce stade, il peut restaurer l'état à partir d'un magasin stable si nécessaire. Lorsque l'acteur reçoit les informations de télémétrie, il les stocke dans une variable locale. Nous faisons cela pour simplifier l'exemple. Dans une implémentation réelle, nous les aurions probablement enregistrées dans un magasin externe pour permettre aux opérations de surveiller et de diagnostiquer l'intégrité et les performances de l'appareil. Enfin, nous envoyons les données de télémétrie à l'acteur de groupe auquel appartient logiquement l'acteur de l'appareil.

## Exemple de code IoT – Télémétrie

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

public class Thing : Actor<ThingState>, IThing
{

    public override Task OnActivateAsync()
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
        return TaskDone.Done;
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

Au niveau du groupe, comme le montre notre exemple, notre objectif est de contrôler les appareils du groupe et à agréger les données de télémétrie afin de générer des alertes pour les ingénieurs. Dans ce cas, notre client souhaite envoyer des ingénieurs dans des régions spécifiques où il existe un certain nombre d'appareils défectueux. Bien sûr, notre client souhaite réduire les coûts en réduisant le temps que les ingénieurs passent sur la route. Pour cette raison, chaque acteur du groupe maintient un état agrégé des appareils défectueux par région. Lorsque ce nombre atteint un seuil, notre client envoie un technicien dans la région pour remplacer/réparer ces appareils. Voyons comment cela fonctionne :

## Exemple de code IoT – regroupement et agrégation

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

public class ThingGroup : Actor<ThingGroupState>, IThingGroup
{

    public override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return TaskDone.Done;
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return TaskDone.Done;
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

        return TaskDone.Done;
    }
}
```

Comme nous pouvons le constater, le processus est assez simple. Après avoir exécuté des tests simples, le résultat se ressemble à ceci :

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Vous pensez peut-être qu'il aurait été préférable de regrouper les appareils par région ? Bien sûr, nous pouvons choisir librement comment regrouper/partitionner les appareils, que ce soit par emplacement géographique, type d'appareil, version, client, etc. Ici, un élément doit être pris en compte : état de l'appareil et rapport/analyse. C'est pourquoi nous avons rendu l'illustration de ce modèle explicite. Nous devons éviter les requêtes de distribution ramifiées aux acteurs pour générer des acteurs de création de rapports ; les instanciations inutiles et les performances représentent deux inconvénients, entre autres. Nous recommandons deux approches pour la création de rapports :

* Utiliser un acteur au niveau du groupe, par exemple une agrégation, afin de conserver une vue pour le groupe. Laisser chaque acteur envoyer uniquement les données pertinentes de façon proactive à cet acteur. Cet acteur au niveau du groupe peut ensuite servir à afficher l'état des appareils dans le groupe.

* Conserver un magasin explicite conçu pour les rapports. Une agrégation peut mettre en mémoire tampon et envoyer régulièrement des données à un magasin de rapports pour poursuivre l'interrogation et l'analyse.

## Opération sur l'appareil
Pour l'instant, tout se déroule parfaitement, mais qu'en est-il des opérations sur ces appareils ? Comme pour les appareils, les acteurs peuvent exposer des interfaces opérationnelles et permettre ainsi à un administrateur d'effectuer des opérations sur les appareils. Par exemple, un administrateur souhaite envoyer une nouvelle configuration à un groupe d'appareils à énergie solaire domestiques (oui, un autre scénario réel) en fonction de modifications saisonnières/régionales.

Ici, l'idée est de disposer d'un contrôle granulaire sur chaque appareil à l'aide d'acteurs « Thing » et d'opérations de groupe à l'aide d'acteur « ThingGroup » — qu'il s'agisse d'agréger des données provenant d'appareils de télémétrie ou d'envoyer des données de configuration à un grand nombre d'appareils. La plateforme gère la distribution des acteurs de l'appareil et la messagerie entre eux, opération totalement transparente pour le développeur.

Lorsqu'il s'agit de communications de machine à machine (M2M), le modèle « Hub and Spoke » évoqué dans la section sur les réseaux distribués et les graphiques et l'interaction directe d'acteur à acteur fonctionnent bien. Pour les scénarios M2M, nous pouvons modéliser un acteur « Répertoire/Index » pour un groupe d'appareils afin qu'ils puissent découvrir et échanger des messages comme illustré ci-dessous :

![][2]

Azure Service Fabric Actors gère également la durée de vie des acteurs. Considérez la question ainsi : nous utiliserons des appareils toujours connectés ainsi que des appareils connectés de façon occasionnelle. Pourquoi conserver en mémoire l'acteur qui surveille l'appareil qui se connecte toutes les 14 heures ? Azure Service Fabric permet d'enregistrer et de restaurer l'état d'un appareil quand et où nous le souhaitons.

Nous pouvons en conclure que de plus en plus de clients considéreront Azure Service Fabric comme en la clé de voûte de leurs implémentations IoT.

## Étapes suivantes
[Modèle : Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : Réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : Gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : Composition d'un service avec état](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Modèle : Calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Introduction à Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=Nov15_HO2-->