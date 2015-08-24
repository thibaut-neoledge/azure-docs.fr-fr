<properties
   pageTitle="Remarques sur les Acteurs fiables pour la sérialisation de type d'acteur"
   description="Traite des exigences de base pour la définition des classes sérialisables pouvant servir à définir les interfaces et l'état des Acteurs fiables Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2015"
   ms.author="claudioc"/>

# Remarques sur la sérialisation de type des Acteurs fiables Service Fabric

Certains aspects importants doivent être pris en compte lors de la définition des interfaces et de l'état de l'acteur : les types doivent être sérialisables en contrat de données. Pour plus d'informations sur les contrats de données, consultez [MSDN](https://msdn.microsoft.com/library/ms731923.aspx).

## Types utilisés dans les interfaces d'acteur

Les arguments de toutes les méthodes et le type de résultat de la tâche retournée par chaque méthode définie dans l'[interface d'acteur](service-fabric-reliable-actors-introduction.md#actors) doivent être sérialisables en contrat de données. Cela s'applique également aux arguments des méthodes définies dans les [interfaces d'événement d'acteur](service-fabric-reliable-actors-events.md#actor-events). (Les méthodes d'interface d'événement d'acteur retournent toujours la valeur nulle). Par exemple, si l'interface `IVoiceMail` définit une méthode en tant que :

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

## Classe d'état d'acteur

L'état d'acteur doit être sérialisable en contrat de données. Par exemple, si nous avons une définition de classe d'acteur qui ressemble à :

```csharp

public class VoiceMailActor : Actor<VoicemailBox>, IVoiceMail
{
...

```

La classe d'état va être définie avec la classe et ses membres annotés avec les attributs DataContract et DataMember respectivement.

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

<!---HONumber=August15_HO7-->