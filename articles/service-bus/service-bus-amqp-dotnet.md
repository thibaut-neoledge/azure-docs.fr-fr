<properties 
    pageTitle="Service Bus avec .NET et AMQP 1.0 | Microsoft Azure"
    description="Utilisation de Service Bus à partir de .NET avec AMQP"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="07/05/2016"
    ms.author="sethm" />

# Utilisation de Service Bus à partir de .NET avec AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

## Téléchargement du Kit de développement logiciel (SDK) Service Bus

La prise en charge d’AMQP 1.0 est disponible dans le Kit de développement logiciel (SDK) Service Bus version 2.1 ou supérieure. Vous pouvez vérifier que vous disposez de la dernière version en téléchargeant les éléments Service Bus à partir de [NuGet][].

## Configuration des applications .NET pour utiliser AMQP 1.0

Par défaut, la bibliothèque cliente .NET Service Bus communique avec le service Service Bus à l’aide d’un protocole SOAP dédié. Pour utiliser AMQP 1.0 au lieu du protocole par défaut, vous avez besoin d’une configuration explicite sur la chaîne de connexion Service Bus, comme indiqué dans la section suivante. À l’exception de cette modification, le code de l’application reste inchangé lors de l’utilisation d’AMQP 1.0.

Dans la version actuelle, quelques fonctionnalités de l’API ne sont pas prises en charge lors de l’utilisation d’AMQP. Celles-ci sont répertoriées plus bas dans la section [Fonctionnalités non prises en charge, restrictions et différences de comportement](#unsupported-features-restrictions-and-behavioral-differences). Certains paramètres de configuration avancés ont également une signification différente lors de l’utilisation d’AMQP.

### Configuration à l’aide d’App.config

Il est conseillé pour les applications d’utiliser le fichier de configuration App.config afin de stocker les paramètres. Pour les applications Service Bus, vous pouvez utiliser App.config pour stocker les paramètres pour la valeur **ConnectionString** de Service Bus. Voici un exemple de fichier App.config :

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <appSettings>
	        <add key="Microsoft.ServiceBus.ConnectionString"
	             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    </appSettings>
	</configuration>

La valeur du paramètre `Microsoft.ServiceBus.ConnectionString` est la chaîne de connexion Service Bus utilisée pour configurer la connexion à Service Bus. au format suivant :

	Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp

Où les valeurs `[namespace]` et `SharedAccessKey` sont obtenues à partir du [portail Azure Classic][]. Pour plus d’informations, consultez [Utilisation des files d’attente Service Bus][].

Lorsque vous utilisez AMQP, ajoutez la chaîne de connexion avec `;TransportType=Amqp`. Cette notation informe la bibliothèque cliente afin qu’elle effectue sa connexion à Service Bus à l’aide d’AMQP 1.0.

## Sérialisation de messages

Lorsque vous utilisez le protocole par défaut, le comportement de sérialisation par défaut de la bibliothèque cliente .NET consiste à utiliser le type [DataContractSerializer][] pour sérialiser une instance [BrokeredMessage][] pour le transport entre la bibliothèque cliente et le service Service Bus. Lorsque vous utilisez le mode de transport AMQP, la bibliothèque cliente utilise le système de type AMQP pour la sérialisation du [message réparti][BrokeredMessage] dans un message AMQP. Cette sérialisation permet au message d’être reçu et interprété par une application réceptrice potentiellement exécutée sur une plateforme différente, par exemple, une application Java qui utilise l’API JMS pour accéder à Service Bus.

Lorsque vous construisez une instance [BrokeredMessage][], vous pouvez fournir un objet .NET en tant que paramètre au constructeur pour servir de corps du message. Pour les objets qui peuvent être mappés sur des types primitifs AMQP, le corps est sérialisé en types de données AMQP. Si l’objet ne peut pas être directement mappé sur un type primitif AMQP, c’est-à-dire, un type personnalisé défini par l’application, l’objet est sérialisé à l’aide de [DataContractSerializer][] et les octets sérialisés sont envoyés dans un message de données AMQP.

Pour faciliter l’interopérabilité avec les clients autres que .NET, utilisez uniquement des types .NET qui peuvent être sérialisés directement en types AMQP pour le corps du message. Le tableau suivant détaille ces types et le mappage correspondant sur le système de types AMQP.

| Type d’objet de corps .NET | Type AMQP mappé | Type de section de corps AMQP |
|--------------------------------|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| valeur booléenne | booléenne | Valeur AMQP |
| byte | ubyte | Valeur AMQP |
| ushort | ushort | Valeur AMQP |
| uint | uint | Valeur AMQP |
| ulong | ulong | Valeur AMQP |
| sbyte | byte | Valeur AMQP |
| short | short | Valeur AMQP |
| int | int | Valeur AMQP |
| long | long | Valeur AMQP |
| float | float | Valeur AMQP |
| double | double | Valeur AMQP |
| décimal | decimal128 | Valeur AMQP |
| char | char | Valeur AMQP |
| DateTime | timestamp | Valeur AMQP |
| Guid | uuid | Valeur AMQP |
| byte | binaire | Valeur AMQP |
| string | string | Valeur AMQP |
| System.Collections.IList | list | Valeur AMQP : les éléments contenus dans la collection peuvent uniquement être ceux qui sont définis dans ce tableau. |
| System.Array | array | Valeur AMQP : les éléments contenus dans la collection peuvent uniquement être ceux qui sont définis dans ce tableau. |
| System.Collections.IDictionary | map | Valeur AMQP : les éléments contenus dans la collection peuvent uniquement être ceux qui sont définis dans ce tableau. Remarque : seules les clés de chaîne sont prises en charge. |
| Uri | Valeur string décrite (voir le tableau suivant) | Valeur AMQP |
| DatetimeOffset | Valeur long décrite (voir le tableau suivant) | Valeur AMQP |
| TimeSpan | Valeur long décrite (voir la suite) | Valeur AMQP |
| Stream | binaire | Données AMQP (peuvent être multiples). Les sections de données contiennent les octets bruts lus à partir de l’objet Stream. |
| Objet Other | binaire | Données AMQP (peuvent être multiples). Contient le binaire sérialisé de l’objet qui utilise DataContractSerializer ou un sérialiseur fourni par l’application. |

| Type .NET | Type décrit AMQP mappé | Remarques |
|----------------|-------------------------------------------------------------------------------------------------------------------------|-------------------------|
| Uri | `<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` | Uri.AbsoluteUri |
| DatetimeOffset | `<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` | DateTimeOffset.UtcTicks |
| TimeSpan | `<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> ` | TimeSpan.Ticks |

## Fonctionnalités non prises en charge, restrictions et différences de comportement

Les fonctionnalités suivantes de l’API .NET Service Bus ne sont actuellement pas prises en charge lors de l’utilisation d’AMQP :

-   Transactions.

-   Envoi via destination de transfert.

-   Réception montée en charge.

Il existe également quelques petites différences dans le comportement de l’API .NET Service Bus lors de l’utilisation d’AMQP par rapport au protocole par défaut :

-   La propriété [OperationTimeout][] est ignorée.

-   `MessageReceiver.Receive(TimeSpan.Zero)` est implémenté en tant que `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.

## Contrôle des paramètres de protocole AMQP

Les API .NET exposent plusieurs paramètres pour contrôler le comportement du protocole AMQP :

-   **MessageReceiver.PrefetchCount** : contrôle le crédit initial appliqué à un lien. La valeur par défaut est 0.

-   **MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize** : contrôle la taille de trame AMQP maximale fournie durant la négociation lors de l’ouverture de la connexion. La valeur par défaut est 65 536 octets.

-   **MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval** : si les transferts sont exécutables par lots, cette valeur détermine le délai maximal pour l’envoi des dispositions. Héritée par les expéditeurs/destinataires par défaut. Un expéditeur/destinataire individuel peut remplacer la valeur par défaut, qui est de 20 millisecondes.

-   **MessagingFactorySettings.AmqpTransportSettings.UseSslStreamSecurity** : contrôle si les connexions AMQP sont établies via une connexion SSL. La valeur par défaut est **true**.

## Étapes suivantes

Prêt à en savoir plus ? Visitez les liens suivants :

- [Vue d’ensemble d’AMQP de Service Bus]
- [Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente partitionnées Service Bus]
- [AMQP dans Service Bus pour Windows Server]

  [Utilisation des files d’attente Service Bus]: service-bus-dotnet-get-started-with-queues.md
  [DataContractSerializer]: https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: https://msdn.microsoft.com/library/azure/jj657638.aspx
  [Microsoft.ServiceBus.Messaging.MessagingFactory.CreateMessageSender(System.String,System.String)]: https://msdn.microsoft.com/library/azure/jj657703.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
[NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[portail Azure Classic]: http://manage.windowsazure.com
[Vue d’ensemble d’AMQP de Service Bus]: service-bus-amqp-overview.md
[Prise en charge d’AMQP 1.0 dans les rubriques et files d’attente partitionnées Service Bus]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[AMQP dans Service Bus pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

<!---HONumber=AcomDC_0706_2016-->