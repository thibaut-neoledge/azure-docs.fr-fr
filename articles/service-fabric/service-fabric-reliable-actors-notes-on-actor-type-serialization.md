<properties
   pageTitle="Remarques sur Reliable Actors pour la sérialisation de type d’acteur | Microsoft Azure"
   description="Traite des exigences de base pour la définition des classes sérialisables pouvant servir à définir les interfaces et les états Service Fabric Reliable Actors"
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Remarques sur la sérialisation de type des Acteurs fiables Service Fabric

Vous devez prendre en compte certains aspects importants quand vous définissez les interfaces et l’état d’un acteur. Les types doivent être sérialisables en contrat de données. Pour plus d’informations sur les contrats de données, consultez [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Types d’interface d’acteur

Les arguments de toutes les méthodes, ainsi que les types de résultat des tâches retournées par chaque méthode telle que définie dans l’[interface d’acteur](service-fabric-reliable-actors-introduction.md#actors), doivent être sérialisables en contrat de données. Cela s’applique également aux arguments des méthodes définies dans les [interfaces d’événement d’acteur](service-fabric-reliable-actors-events.md#actor-events). (Les méthodes d’interface d’événement d’acteur retournent toujours la valeur nulle.) Par exemple, si l'interface `IVoiceMail` définit une méthode en tant que :

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

`List<T>` est un type .NET standard déjà sérialisable en contrat de données. Le type `Voicemail` doit être sérialisable en contrat de données.

```csharp

[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}

```

## Classe d’état d’acteur

L’état d’acteur doit être sérialisable en contrat de données. Par exemple, une définition de classe d’acteur peut ressembler à ceci :

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

La classe d’état va être définie avec la classe et ses membres annotés avec les attributs **DataContract** et **DataMember**, respectivement.

```csharp

[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}

```

<!---HONumber=AcomDC_0121_2016-->