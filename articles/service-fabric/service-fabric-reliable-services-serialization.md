<properties
   pageTitle="Sérialisation de Service fiable | Microsoft Azure"
   description="Documentation conceptuelle relative à la sérialisation d’un service fiable Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Sérialisation dans des Services fiables (Reliable Services)
Le gestionnaire des états fiables peut contenir plusieurs objets fiables. Certains de ces objets fiables peuvent être des structures de données génériques telles que le Dictionnaire fiable et la File d’attente fiable prêts à l’emploi. Étant donné qu’il s’agit de structures de données génériques fiables, les objets génériques qu’ils contiennent doivent être sérialisés afin de pouvoir être répliqués et persistants sur le disque.

Le gestionnaire des états fiables prend en charge trois types de sérialiseurs : * les sérialiseurs personnalisés * les sérialiseurs intégrés pour un nombre limité de types et le * DataContractSerilizer

Lorsqu’un objet fiable doit sérialiser un objet, il interroge le gestionnaire des états fiables pour savoir si un sérialiseur existe pour le type donné. Le gestionnaire des états fiables vérifie d’abord s’il existe un sérialiseur personnalisé enregistré pour le type d’entrée. Si ce n’est pas le cas, il vérifie si l’un des sérialiseurs intégrés peut sérialiser le type. Le gestionnaire des états fiables comprend des sérialiseurs intégrés pour les types suivants : guid, booléen, octet, sbyte, caractère, décimal, double, flottant, int, uint, long, ulong, short, ushort et chaîne. Si ce n’est pas le cas, il renvoie DataContractSerializer.

Cet article se concentre sur quand et comment utiliser la sérialisation personnalisée.

## Quand utiliser la sérialisation personnalisée
Il existe deux raisons courantes d’utiliser la sérialisation personnalisée : * performances * chiffrement

Pour les types qui ne sont pas couverts par les types intégrés, une optimisation considérable des performances peut être obtenue à l’aide de sérialiseurs personnalisés au lieu de DataContractSerializer. L’une des raisons est que les sérialiseurs personnalisés n’ont pas besoin de sérialiser les informations de type. Service Fabric garantit qu’un sérialiseur d’état pour un type donné peut uniquement sérialiser et désérialiser ce type.

Les données sérialisées générées par les sérialiseurs sont répliquées et rendues persistantes sur disque en l’état. Pour les données confidentielles, il est souhaitable de s’assurer que les bits sur réseau et sur disque sont chiffrés.

## Comment utiliser la sérialisation personnalisée
Pour utiliser un sérialiseur personnalisé pour un type donné, vous devez : * générer un sérialiseur d’état personnalisé et * inscrire le sérialiseur d’état personnalisé dans le Service fiable

### Comment implémenter un sérialiseur personnalisé
Les sérialiseurs personnalisés doivent implémenter l’interface IStateSerializer<T>. Les deux méthodes principales dans cette interface sont * T Read(BinaryReader binaryReader) et * void Write(T value, BinaryWriter binaryWriter).

La première est utilisée par l’objet fiable (ReliableObject) pour lire l’objet sérialisé à partir d’un flux à l’aide d’un BinaryReader. La seconde est utilisée pour l’opération inverse : écrire l’objet dans un flux à l’aide d’un enregistreur binaire.

Voici un exemple de classe personnalisée et de sérialiseur qui lui est associé.

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]dans l’exemple ci-dessus, l’implémentation de surcharges d’opérations de lecture et d’écriture appelle simplement leurs surcharges équivalentes. Les deux méthodes suivantes sont en effet utilisées pour une fonctionnalité qui n’est pas encore disponible.

### Procédure d’inscription d’un sérialiseur personnalisé
Pour inscrire un sérialiseur personnalisé, nous avons d’abord besoin d’une méthode qui peut inscrire tous les sérialiseurs personnalisés. Cette méthode doit ne prendre aucun argument et renvoyer une tâche. Dans cette méthode, IReliableStateManager.TryAddStateSerializer<T> doit être utilisé pour inscrire tous les sérialiseurs personnalisés au Service fiable.

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

L’étape suivante consiste à inscrire la méthode ci-dessus en tant que délégué que le Gestionnaire des états fiables peut appeler lorsque tous les sérialiseurs d’état personnalisés doivent être inscrits. Cette méthode est appelée uniquement au début du réplica du Service fiable avant le démarrage de la récupération locale, car les sérialiseurs peuvent être nécessaires pour lire les données sérialisées à partir du disque. Une fois le sérialiseur inscrit, le type approprié parmi tous les objets fiables utilise ce sérialiseur pour sérialiser et désérialiser leurs objets.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### Contrôle de version
Pour Service Fabric, les sérialiseurs doivent avoir une compatibilité ascendante et descendante illimitée. Service Fabric assure une compatibilité ascendante et descendante pour les types qui utilisent des sérialiseurs intégrés. Pour les types qui utilisent DataContractSerializer ou un sérialiseur personnalisé, l’utilisateur ne doit jamais apporter de modification avec rupture. Pour le contrôle de version de DataContract, consultez [Contrôle de version des contrats de données (en anglais)](https://msdn.microsoft.com/library/ms731138.aspx). Si une modification avec rupture est nécessaire, l’état doit être transféré d’une instance de service avec l’ancienne version des données vers un service avec la nouvelle version des données au niveau de l’application.

### Lorsqu’un sérialiseur est utilisé
 * Les opérations d’écriture sur des objets fiables entraînent leur sérialisation, leur réplication et leur stockage dans un journal.
 * Une fois qu’un nombre suffisant d’opérations enregistré, les données les plus récentes de la mémoire sont sérialisées et contrôlées sur le disque.
 * Pour recréer un réplica, les fichiers contrôlés sur disque et les données récentes du journal sont envoyés directement octet par octet (par exemple, les données ne sont pas sérialisées à nouveau) à partir d’un réplica principal. Ces octets sont désérialisés en objets C# sur le réplica secondaire.
 * Pendant la récupération, les fichiers contrôlés et les données récentes du journal sont désérialisés.
 * Lors de la sauvegarde, les fichiers contrôlés et les données récentes du journal sont copiés octet par octet.
 * Pendant la restauration, les fichiers contrôlés et les données récentes du journal précédemment sauvegardée sont copiés dans leur emplacement original et désérialisés.

## Étapes suivantes
 * [Utilisation avancée du modèle de programmation de services fiables](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->