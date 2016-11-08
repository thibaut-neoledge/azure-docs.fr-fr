---
title: Service Bus et Python avec AMQP 1.0 | Microsoft Docs
description: Utilisation de Service Bus à partir de Python avec AMQP.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2016
ms.author: sethm

---
# <a name="using-service-bus-from-python-with-amqp-1.0"></a>Utilisation de Service Bus à partir de Python avec AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python est une liaison de langage Python à Proton-C. En d’autres termes, Proton-Python est implémenté comme un wrapper autour d’un moteur implémenté en C.

## <a name="download-the-proton-client-library"></a>Téléchargement d'une bibliothèque cliente Proton
Vous pouvez télécharger Proton-C et les liaisons associées (y compris Python) à partir de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le téléchargement est sous forme de code source. Pour générer le code, suivez les instructions contenues dans le package téléchargé.

Notez qu’au moment de la rédaction de cet article, la prise en charge SSL dans Proton-C est uniquement disponible pour les systèmes d’exploitation Linux. Étant donné qu’Azure Service Bus requiert l’utilisation de SSL, Proton-C (et les liaisons de langage) peut uniquement être utilisé pour accéder à Service Bus depuis Linux pour le moment. Le travail pour l’activation de Proton-C avec SSL sur Windows est en bonne voie, donc vérifiez régulièrement les mises à jour.

## <a name="working-with-service-bus-queues,-topics,-and-subscriptions-from-python"></a>Utilisation des files d’attente, rubriques et abonnements Service Bus depuis Python
Le code suivant montre comment envoyer et recevoir des messages à partir d’une entité de messagerie Service Bus.

### <a name="send-messages-using-proton-python"></a>Envoi de messages à l'aide de Proton-Python
Le code suivant montre comment envoyer un message à une entité de messagerie Service Bus.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### <a name="receive-messages-using-proton-python"></a>Réception de messages à l'aide de Proton-Python
Le code suivant montre comment recevoir un message d’une entité de messagerie Service Bus.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## <a name="messaging-between-.net-and-proton-python"></a>Messagerie entre .NET et Proton-Python
### <a name="application-properties"></a>Propriétés de l’application
#### <a name="proton-python-to-service-bus-.net-apis"></a>API .NET Proton-Python à Service Bus
Les messages Proton-Python prennent en charge les propriétés d’application de types suivants : **int**, **long**, **float**, **uuid**, **bool**, **string**. Le code Python suivant montre comment définir les propriétés d’un message à l’aide de chacun de ces types de propriété.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

Dans l’API .NET Service Bus, les propriétés de l’application de messagerie sont exécutées dans la collection **Propriétés** de [BrokeredMessage][BrokeredMessage]. Le code suivant montre comment lire les propriétés d’application d’un message reçu d’un client Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

Le tableau suivant mappe les types de propriétés Python sur les types de propriétés .NET.

| Type de propriété Python | Type de propriété .NET |
| --- | --- |
| int |int |
| float |double |
| long |int64 |
| uuid |guid |
| valeur booléenne |valeur booléenne |
| string |string |

#### <a name="service-bus-.net-apis-to-proton-python"></a>API .NET Service Bus à Proton-Python
Le type [BrokeredMessage][BrokeredMessage] prend en charge les propriétés d’application de types suivants : **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** et **TimeSpan**. Le code .NET suivant montre comment définir les propriétés d’un objet [BrokeredMessage][BrokeredMessage] à l’aide de chacun de ces types de propriété.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

Le code Python suivant montre comment lire les propriétés d’application d’un message reçu d’un client .NET Service Bus.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

Le tableau suivant mappe les types de propriétés .NET sur les types de propriétés Python.

| Type de propriété .NET | Type de propriété Python | Remarques |
| --- | --- | --- |
| byte |int |- |
| sbyte |int |- |
| char |char |Classe Proton-Python |
| short |int |- |
| ushort |int |- |
| int |int |- |
| uint |int |- |
| long |int |- |
| ulong |long |Classe Proton-Python |
| float |float |- |
| double |float |- |
| Décimal |String |Le séparateur décimal n’est pas encore pris en charge par Proton. |
| valeur booléenne |valeur booléenne |- |
| Guid |uuid |Classe Proton-Python |
| string |string |- |
| DateTime |timestamp |Classe Proton-Python |
| Datetimeoffset |DescribedType |DateTimeOffset.UtcTicks mappé au type AMQP :<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan |DescribedType |Timespan.Ticks mappé au type AMQP :<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> |
| Uri |DescribedType |Uri.AbsoluteUri mappé au type AMQP :<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### <a name="standard-properties"></a>Propriétés standard
Les tableaux suivants montrent le mappage entre les propriétés de message standard Proton-Python et les propriétés de message standard [BrokeredMessage][BrokeredMessage].

#### <a name="proton-python-to-service-bus-.net-apis"></a>API .NET Proton-Python à Service Bus
| Proton-Python | .NET Service Bus | Remarques |
| --- | --- | --- |
| durable |n/a |Service Bus prend uniquement en charge les messages durables. |
| priority |n/a |Service Bus prend uniquement en charge une priorité de message. |
| Ttl |Message.TimeToLive |Conversion, durée de vie Proton-Python définie en millisecondes. |
| first\_acquirer |n/a |- |
| delivery\_count |n/a |- |
| Id |Message.MessageID |- |
| user\_id |n/a |- |
| address |Message.To |- |
| subject |Message.Label |- |
| reply\_to |Message.ReplyTo |- |
| correlation\_id |Message.CorrelationID |- |
| content\_type |Message.ContentType |- |
| content\_encoding |n/a |- |
| expiry\_time |n/a |- |
| creation\_time |n/a |- |
| group\_id |Message.SessionId |- |
| group\_sequence |n/a |- |
| reply\_to\_group\_id |Message.ReplyToSessionId |- |
| format |n/a |- |

| .NET Service Bus | Proton | Remarques |
| --- | --- | --- |
| ContentType |Message.content\_type |- |
| CorrelationId |Message.correlation\_id |- |
| EnqueuedTimeUtc |n/a |- |
| Étiquette |Message.subject |- |
| MessageId |Message.id |- |
| ReplyTo |Message.reply\_to |- |
| ReplyToSessionId |Message.reply\_to\_group\_id |- |
| ScheduledEnqueueTimeUtc |n/a |- |
| SessionId |Message.group\_id |- |
| TimeToLive |Message.ttl |Conversion, durée de vie Proton-Python définie en millisecondes. |
| À |Message.address |- |

## <a name="next-steps"></a>Étapes suivantes
Prêt à en savoir plus ? Visitez les liens suivants :

* [Vue d’ensemble d’AMQP de Service Bus]
* [AMQP in Service Bus for Windows Server (AMQP dans Service Bus pour Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP dans Service Bus pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Vue d’ensemble du protocole AMQP de Service Bus]: service-bus-amqp-overview.md



<!--HONumber=Oct16_HO2-->


