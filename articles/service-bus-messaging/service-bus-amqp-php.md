---
title: Service Bus et PHP avec AMQP 1.0 | Microsoft Docs
description: Utilisation de Service Bus à partir de PHP avec AMQP.
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
# <a name="using-service-bus-from-php-with-amqp-1.0"></a>Utilisation de Service Bus à partir de PHP avec AMQP 1.0
[!INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP est une liaison de langage PHP à Proton-C. En d’autres termes, Proton-PHP est implémenté comme un wrapper autour d’un moteur implémenté en C.

## <a name="downloading-the-proton-client-library"></a>Téléchargement d’une bibliothèque cliente Proton
Vous pouvez télécharger Proton-C et les liaisons associées (y compris PHP) à partir de [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Le téléchargement est sous forme de code source. Pour générer le code, suivez les instructions contenues dans le package téléchargé.

> [!IMPORTANT]
> Au moment de la rédaction de cet article, la prise en charge SSL dans Proton-C est uniquement disponible pour les systèmes d’exploitation Linux. Étant donné qu’Azure Service Bus requiert l’utilisation de SSL, Proton-C (et les liaisons de langage) peut uniquement être utilisé pour accéder à Service Bus depuis Linux pour le moment. Le travail pour l’activation de Proton-C avec SSL sur Windows est en bonne voie, donc vérifiez régulièrement les mises à jour.
> 
> 

## <a name="working-with-service-bus-queues,-topics,-and-subscriptions-from-php"></a>Utilisation des files d’attente, rubriques et abonnements Service Bus depuis PHP
Le code suivant montre comment envoyer et recevoir des messages à partir d’une entité de messagerie Service Bus.

### <a name="sending-messages-using-proton-php"></a>Envoi de messages à l’aide de Proton-PHP
Le code suivant montre comment envoyer un message à une entité de messagerie Service Bus.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### <a name="receiving-messages-using-proton-php"></a>Réception de messages à l’aide de Proton-PHP
Le code suivant montre comment recevoir un message d’une entité de messagerie Service Bus.

```
$messenger = new Messenger();
$address = "amqps://[keyname]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## <a name="messaging-between-.net-and-proton-php"></a>Messagerie entre .NET et Proton-PHP
### <a name="application-properties"></a>Propriétés de l’application
#### <a name="protonphp-to-service-bus-.net-apis"></a>API .NET Proton-PHP à Service Bus
Les messages Proton-PHP prennent en charge les propriétés d’application de types suivants : **integer**, **double**, **Boolean**, **string** et **object**. Le code PHP suivant montre comment définir les propriétés d’un message à l’aide de chacun de ces types de propriété.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

Dans les API .NET Service Bus, les propriétés de l’application de messagerie sont exécutées dans la collection **Properties** de [BrokeredMessage][BrokeredMessage]. Le code suivant montre comment lire les propriétés d’application d’un message reçu d’un client PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

Le tableau suivant mappe les types de propriétés PHP sur les types de propriétés .NET.

| Type de propriété PHP | Type de propriété .NET |
| --- | --- |
| integer |int |
| double |double |
| booléenne |valeur booléenne |
| string |string |
| objet |Object |

#### <a name="service-bus-.net-apis-to-php"></a>API .NET Service Bus à PHP
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
```

Le code PHP suivant montre comment lire les propriétés d’application d’un message reçu d’un client .NET Service Bus.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

Le tableau suivant mappe les types de propriétés .NET sur les types de propriétés PHP.

| Type de propriété .NET | Type de propriété PHP | Remarques |
| --- | --- | --- |
| byte |integer |- |
| sbyte |integer |- |
| char |Char |Classe Proton-PHP |
| short |integer |- |
| ushort |integer |- |
| int |integer |- |
| uint |Integer |- |
| long |integer |- |
| ulong |integer |- |
| float |double |- |
| double |double |- |
| Décimal |string |Le séparateur décimal n’est pas encore pris en charge par Proton. |
| valeur booléenne |booléenne |- |
| Guid |UUID |Classe Proton-PHP |
| string |string |- |
| DateTime |integer |- |
| Datetimeoffset |DescribedType |DateTimeOffset.UtcTicks mappé au type AMQP :<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> |
| TimeSpan |DescribedType |Timespan.Ticks mappé au type AMQP :<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> |
| Uri |DescribedType |Uri.AbsoluteUri mappé au type AMQP :<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### <a name="standard-properties"></a>Propriétés standard
Les tables suivantes montrent le mappage entre les propriétés de message standard Proton-PHP et les propriétés de message standard [BrokeredMessage][BrokeredMessage].

| Proton-PHP | .NET Service Bus | Remarques |
| --- | --- | --- |
| Durable |n/a |Service Bus prend uniquement en charge les messages durables. |
| Priorité |n/a |Service Bus prend uniquement en charge une priorité de message. |
| Ttl |Message.TimeToLive |Conversion, durée de vie Proton-PHP définie en millisecondes. |
| first\_acquirer |- |- |
| delivery\_count |- |- |
| Id |Message.Id |- |
| user\_id |- |- |
| Adresse |Message.To |- |
| Objet |Message.Label |- |
| reply\_to |Message.ReplyTo |- |
| correlation\_id |Message.CorrelationId |- |
| content\_type |Message.ContentType |- |
| content\_encoding |n/a |- |
| expiry\_time |Message.ExpiresAtUTC |- |
| creation\_time |n/a |- |
| group\_id |Message.SessionId |- |
| group\_sequence |- |- |
| reply\_to\_group\_id |Message.ReplyToSessionId |- |
| Format |n/a |- |

#### <a name="service-bus-.net-apis-to-proton-php"></a>API .NET Service Bus à Proton-PHP
| .NET Service Bus | Proton-PHP | Remarques |
| --- | --- | --- |
| ContentType |Message-\>content\_type |- |
| CorrelationId |Message-\>correlation\_id |- |
| EnqueuedTimeUtc |Message-\>annotations[x-opt-enqueued-time] |- |
| Étiquette |Message-\>subject |- |
| MessageId |Message-\>id |- |
| ReplyTo |Message-\>reply\_to |- |
| ReplyToSessionId |Message-\>reply\_to\_group\_id |- |
| ScheduledEnqueueTimeUtc |Message-\>annotations ["x-opt-scheduled-enqueue-time"] |- |
| SessionId |Message-\>group\_id |- |
| TimeToLive |Message-\>ttl |Conversion, durée de vie Proton-PHP définie en millisecondes. |
| À |Message-\>address |- |

## <a name="next-steps"></a>Étapes suivantes
Prêt à en savoir plus ? Visitez les liens suivants :

* [Vue d’ensemble d’AMQP de Service Bus]
* [AMQP dans Service Bus pour Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP dans Service Bus pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Vue d’ensemble d’AMQP de Service Bus]: service-bus-amqp-overview.md



<!--HONumber=Oct16_HO2-->


