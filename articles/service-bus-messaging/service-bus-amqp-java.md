<properties 
    pageTitle="Service Bus et Java avec AMQP 1.0 | Microsoft Azure"
    description="Utilisation de Service Bus à partir de Java avec AMQP"
    services="service-bus-messaging"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# utilisation de Service Bus à partir de Java avec AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Java Message Service (JMS) est une API standard pour travailler avec un middleware orienté messages sur la plateforme Java. Microsoft Azure Service Bus a été testé avec la bibliothèque cliente JMS basée sur AMQP 1.0, développée par le projet Apache Qpid. Cette bibliothèque prend en charge l’API JMS 1.1 complète et peut être utilisée avec n’importe quel service de messagerie compatible AMQP 1.0. Ce scénario est également pris en charge dans [Service Bus pour Windows Server](https://msdn.microsoft.com/library/dn282144.aspx) (Service Bus local). Pour plus d’informations, consultez [AMQP dans Service Bus pour Windows Server][].

## Téléchargement de la bibliothèque cliente JMS AMQP 1.0 Apache Qpid

Pour plus d’informations sur le téléchargement de la dernière version de la bibliothèque cliente AMQP 1.0 JMS Apache Qpid, accédez à [http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

Vous devez ajouter les quatre fichiers JAR suivants de l’archive de distribution Apache Qpid JMS AMQP 1.0 au CLASSPATH Java lors de la création et de l’exécution des applications JMS avec Service Bus :

-   geronimo-jms\_1.1\_spec-[version].jar

-   qpid-amqp-1-0-client-[version].jar

-   qpid-amqp-1-0-client-jms-[version].jar

-   qpid-amqp-1-0-common-[version].jar

## Utilisation des files d'attente, rubriques et abonnements Service Bus à partir de JMS

### JNDI (Java Naming and Directory Interface)

JMS utilise l’interface JNDI (Java Naming and Directory Interface) pour créer une séparation entre les noms logiques et les noms physiques. Deux types d’objets JMS sont résolus à l’aide de JNDI : **ConnectionFactory** et **Destination**. JNDI utilise un modèle de fournisseur auquel vous pouvez connecter différents services d’annuaire afin de gérer les tâches de résolution de noms. La bibliothèque AMQP 1.0 JMS Apache Qpid est dotée d’un simple fournisseur JNDI fondé sur un fichier de propriétés qui est configuré à l’aide d’un fichier texte.

Le fournisseur JNDI du fichier de propriétés Qpid est configuré à l’aide d’un fichier de propriétés au format suivant :

```
# servicebus.properties – sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCONNECTIONFACTORY = amqps://[username]:[password]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
topic.TOPIC = topic1
queue.QUEUE = queue1
```

#### Configuration de la fabrique de connexion

L’entrée utilisée pour définir une **ConnectionFactory** dans le fournisseur JNDI de fichier de propriétés Qpid est au format suivant :

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Où `[jndi\_name]` et `[ConnectionURL]` ont les significations suivantes :

| Nom | Signification | | | | |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------|---|---|---|---|
| `[jndi\_name]` | Nom logique de la fabrique de connexion. Ce nom est résolu dans l’application Java à l’aide de la méthode `IntialContext.lookup()` JNDI. | | | | |
| `[ConnectionURL]` | URL qui fournit à la bibliothèque JMS les informations nécessaires au service Broker AMQP. | | | | |

Le format de l’URL de connexion est le suivant :

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Où `[namespace]`, `[username]` et `[password]` ont les significations suivantes :

| Nom | Signification | | | | |
|---------------|--------------------------------------------------------------------------------|---|---|---|---|
| `[namespace]` | Espace de noms Service Bus obtenu auprès du [portail Azure][]. | | | | |
| `[username]` | Nom de l’émetteur Service Bus obtenu auprès du [portail Azure][]. | | | | |
| `[password]` | Forme encodée dans une URL de la clé de l’émetteur Service Bus obtenue auprès du [portail Azure][]. | | | | |

> [AZURE.NOTE] vous devez encoder manuellement le mot de passe dans une URL. Un utilitaire efficace d'encodage dans une URL est disponible à l'adresse [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

Par exemple, si les informations obtenues auprès du portail sont les suivantes :

| Espace de noms : | test.servicebus.windows.net |
|--------------|----------------------------------------------|
| Nom de l'émetteur : | propriétaire |
| Clé de l'émetteur : | abcdefg |

Pour définir ensuite un objet **ConnectionFactory** nommé `SBCONNECTIONFACTORY`, la chaîne de configuration se présente de la manière suivante :

```
connectionfactory.SBCONNECTIONFACTORY = amqps://owner:abcdefg@test.servicebus.windows.net
```

#### Configurer des destinations

L’entrée qui définit une destination dans le fournisseur JNDI de fichier de propriétés Qpid est au format suivant :

```
queue.[jndi_name] = [physical_name]
topic.[jndi_name] = [physical_name]
```

Où `[jndi\_name]` et `[physical\_name]` ont les significations suivantes :

| Nom | Signification |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| `[jndi\_name]` | Nom logique de la destination. Ce nom est résolu dans l’application Java à l’aide de la méthode `IntialContext.lookup()` JNDI. |
| `[physical\name]` | Nome de l’entité Service Bus vers laquelle l’application envoie ou reçoit des messages. |

Notez les points suivants :

- La valeur `[physical\name]` peut être une file d’attente ou une rubrique Service Bus.
- lors de la réception d’un abonnement à une rubrique Service Bus, le nom physique spécifié dans JNDI doit être le nom de la rubrique. Le nom de l’abonnement est fourni lors de la création de l’abonnement durable dans le code d’application JMS.
- Il est également possible de traiter un abonnement à une rubrique Service Bus comme une file d’attente JMS. Cette approche présente plusieurs avantages : le même code de récepteur peut être utilisé pour les files d’attente et les abonnements à une rubrique, et toutes les informations d’adresse (noms de rubrique et d’abonnement) sont externalisées dans le fichier de propriétés.
- Pour traiter un abonnement à une rubrique Service Bus comme file d’attente JMS, l’entrée dans le fichier de propriétés doit être au format : `queue.[jndi\_name] = [topic\_name]/Subscriptions/[subscription\_name]`.|

Pour définir une destination JMS logique nommée « TOPIC » qui mappe sur une rubrique Service Bus nommée « topic1 », l’entrée dans le fichier de propriétés se présenterait de la façon suivante :

```
topic.TOPIC = topic1
```

### Envoi de messages à l'aide de JMS

Le code suivant montre comment envoyer un message à une rubrique Service Bus. Il est supposé que `SBCONNECTIONFACTORY` et `TOPIC` sont définis dans un fichier de configuration **servicebus.properties**, comme décrit dans la section précédente.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env); 
 
ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
MessageProducer producer = session.createProducer(topic);
TextMessage message = session.createTextMessage("This is a text string"); 
producer.send(message);
```

### Réception de messages à l'aide de JMS

Le code suivant montre `how` pour recevoir un message d’un abonnement à une rubrique Service Bus. Il est supposé que `SBCONNECTIONFACTORY` et TOPIC sont définis dans un fichier de configuration **servicebus.properties**, comme décrit dans la section précédente. On suppose également que le nom de l’abonnement est `subscription1`.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, 
        "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
 
InitialContext context = new InitialContext(env);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCONNECTIONFACTORY");
Topic topic = (Topic) context.lookup("TOPIC");
Connection connection = cf.createConnection();
Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
TopicSubscriber subscriber = session.createDurableSubscriber(topic, "subscription1");
connection.start();
Message message = messageConsumer.receive();
```

### Instructions pour la création d’applications robustes

La spécification JMS définit comment le contrat d’exception des méthodes API et du code d’application doit être écrit pour gérer ces exceptions. Voici d’autres éléments à prendre en considération concernant la gestion des exceptions :

-   Enregistrez un **ExceptionListener** avec la connexion JMS à l’aide de **connection.setExceptionListener**. Cela permet à un client d’être averti d’un problème de façon asynchrone. Cette notification est particulièrement importante pour les connexions qui utilisent uniquement des messages, car elles n’ont aucun autre moyen de savoir que leur connexion a échoué. L’**ExceptionListener** est appelé s’il existe un problème avec la connexion AMQP sous-jacente, la session ou le lien. Dans ce cas, le programme d’application doit recréer les objets **JMS Connection**, **Session**, **MessageProducer** et **MessageConsumer** à partir de zéro.

-   Pour vérifier qu’un message a été envoyé avec succès d’une entité **MessageProducer** à une entité Service Bus, assurez-vous que l’application a été configurée avec le jeu de propriétés système **qpid.sync\_publish**. Pour ce faire, démarrez le programme avec l’option **-Dqpid.sync\_publish=true** de la machine virtuelle Java définie sur la ligne de commande lors du démarrage de l’application. Le paramétrage de cette option configure la bibliothèque pour ne pas retourner depuis l’appel d’envoi jusqu’à la réception de la confirmation que le message a été accepté par Service Bus. Si un problème se produit pendant l’opération d’envoi, une exception **JMSException** est générée. Il existe deux causes possibles :
	1. Si le problème est dû au rejet par Service Bus d’un message particulier envoyé, une exception **MessageRejectedException** est levée. Cette erreur est temporaire ou liée à un problème avec le message. La procédure recommandée consiste à effectuer plusieurs tentatives pour réessayer l’opération avec une logique de temporisation. Si le problème persiste, le message doit être abandonné avec une erreur consignée localement. Il n’est pas nécessaire de recréer les objets **JMS Connection**, **Session** ou **MessageProducer** dans cette situation.
	2. Si le problème est dû à la fermeture du lien AMQP par Service Bus, une exception **InvalidDestinationException** est générée. Cela peut être dû à un problème temporaire ou à la suppression d’une entité de message. Dans les deux cas, les objets **JMS Connection**, **Session** et **MessageProducer** doivent être recréés. Si la condition d’erreur était temporaire, cette opération finit par réussir. Si l’entité a été supprimée, l’échec est permanent.

## Messagerie entre .NET et JMS

### Corps de message

JMS définit cinq types de messages différents : **BytesMessage**, **MapMessage**, **ObjectMessage**, **StreamMessage** et **TextMessage**. L’API .NET Service Bus possède un seul type de message : [BrokeredMessage][].

#### API .NET JMS à Service Bus

Les sections suivantes montrent comment utiliser les messages de chacun des types de messages JMS à partir de .NET. Aucun exemple **ObjectMessage** n’a été inclus, car le corps d’un **ObjectMessage** contient un objet sérialisable dans le langage de programmation Java, qui n’est pas interprétable par une application .NET.

##### BytesMessage

Le code suivant montre comment utiliser le corps d’un objet **BytesMessage** à l’aide des API .NET Service Bus.

```
Stream stream = message.GetBody<Stream>();
int streamLength = (int)stream.Length;

byte[] byteArray = new byte[streamLength];
stream.Read(byteArray, 0, streamLength);

Console.WriteLine("Length = " + streamLength);
for (int i = 0; i < stream.Length; i++)
{
  Console.Write("[" + (sbyte) byteArray[i] + "]");
}
```

##### MapMessage

Le code suivant montre comment utiliser le corps d’un objet **MapMessage** à l’aide des API .NET Service Bus. Ce code effectue une itération dans les éléments de mappage, en affichant le nom et la valeur de chaque élément.

```
Dictionary<String, Object> dictionary = message.GetBody<Dictionary<String, Object>>();

foreach (String mapItemName in dictionary.Keys)
{
  Object mapItemValue = null;
  if (dictionary.TryGetValue(mapItemName, out mapItemValue))
  {
    Console.WriteLine(mapItemName + ":" + mapItemValue);
  }
}
```

##### StreamMessage

Le code suivant montre comment utiliser le corps d’un objet **StreamMessage** à l’aide des API .NET Service Bus. Ce code répertorie chacun des éléments à partir du flux, ainsi que leurs types.

```
List<Object> list = message.GetBody<List<Object>>();

foreach (Object item in list)
{
  Console.WriteLine(item + " (" + item.GetType() + ")");
}
```

##### TextMessage

Le code suivant montre comment utiliser le corps d’un objet **TextMessage** à l’aide des API .NET Service Bus. Ce code affiche la chaîne de texte contenue dans le corps du message.

```
Console.WriteLine("Text: " + message.GetBody<String>());
```

#### API .NET Service Bus à JMS

Les sections suivantes montrent comment une application .NET peut créer un message qui est reçu dans JMS dans chacun des différents types de messages JMS. Aucun exemple **ObjectMessage** n’a été inclus, car le corps d’un **ObjectMessage** contient un objet sérialisable dans le langage de programmation Java, qui n’est pas interprétable par une application .NET.

##### BytesMessage

Le code suivant montre comment créer un objet [BrokeredMessage][] dans .NET qui est reçu par un client JMS comme **BytesMessage**.

```
byte[] bytes = { 33, 12, 45, 33, 12, 45, 33, 12, 45, 33, 12, 45 };
message = new BrokeredMessage(bytes);
```

##### StreamMessage

Le code suivant montre comment créer un objet [BrokeredMessage][] dans .NET qui est reçu par un client JMS comme un **StreamMessage**.

```
List<Object> list = new List<Object>();
list.Add("String 1");
list.Add("String 2");
list.Add("String 3");
list.Add((double)3.14159);
message = new BrokeredMessage(list);
```

##### TextMessage

Le code suivant montre comment utiliser le corps d’un **TextMessage** à l’aide de l’API .NET Service Bus. Ce code affiche la chaîne de texte contenue dans le corps du message.

```
message = new BrokeredMessage("this is a text string");
```

### Propriétés de l’application

####API .NET JMS à Service Bus

Les messages JMS prennent en charge les propriétés d’application de types suivants : **boolean**, **byte**, **short**, **int**, **long**, **float**, **double** et **String**. Le code Java suivant montre comment définir les propriétés d’un message à l’aide de chacun de ces types de propriété.

```
message.setBooleanProperty("TestBoolean", true); 
message.setByteProperty("TestByte", (byte) 33); 
message.setDoubleProperty("TestDouble", 3.14159D); 
message.setFloatProperty("TestFloat", 3.13159F); 
message.setIntProperty("TestInt", 100); 
message.setStringProperty("TestString", "Service Bus");
```

Dans les API .NET Service Bus, les propriétés de l’application de messagerie sont exécutées dans la collection **Propriétés** de [BrokeredMessage][]. Le code suivant montre comment lire les propriétés d’application d’un message reçu d’un client JMS.

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

Le tableau suivant montre comment les types de propriétés JMS mappent sur les types de propriétés .NET.

| Type de propriété JMS | Type de propriété .NET |
|-------------------|--------------------|
| Byte | sbyte |
| Integer | int |
| Float | float |
| Double | double |
| Boolean | valeur booléenne |
| String | string |

Le type [BrokeredMessage][] prend en charge les propriétés d’application de types suivants : **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** et **TimeSpan**. Le code .NET suivant montre comment définir les propriétés d’un objet [BrokeredMessage][] à l’aide de chacun de ces types de propriété.

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

Le code Java suivant montre comment lire les propriétés d’application d’un message reçu d’un client .NET Service Bus.

```
Enumeration propertyNames = message.getPropertyNames(); 
while (propertyNames.hasMoreElements()) 
{ 
  String name = (String) propertyNames.nextElement(); 
  Object value = message.getObjectProperty(name); 
  System.out.println(name + ": " + value + " (" + value.getClass() + ")"); 
}
```

Le tableau suivant montre comment les types de propriétés .NET mappent sur les types de propriétés JMS.

| Type de propriété .NET | Type de propriété JMS | Remarques |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | UnsignedByte | - | 
| sbyte | Byte | - | 
| char | Character | - | 
| short | Short | - | 
| ushort | UnsignedShort | - | 
| int | Integer | - | 
| uint | UnsignedInteger | - | 
| long | Long | - | 
| ulong | UnsignedLong | - | 
| float | Float | - | 
| double | Double | - | 
| decimal | BigDecimal | - | 
| bool | Boolean | - | 
| Guid | UUID | - | 
| string | String | - | 
| DateTime | Date | - | 
| DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks mappée sur le type AMQP : <type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> | 
| TimeSpan | DescribedType | Timespan.Ticks mapped to AMQP type:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> | 
| Uri | DescribedType | Uri.AbsoluteUri mapped to AMQP type:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### En-têtes standard

Les tables suivantes montrent comment les en-têtes standard JMS et les propriétés standard [BrokeredMessage][] sont mappés à l’aide d’AMQP 1.0.

#### API .NET JMS à Service Bus

| JMS | .NET Service Bus | Remarques |
|------------------|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| JMSCorrelationID | Message.CorrelationID | - | 
| JMSDeliveryMode | Actuellement non disponible | Service Bus prend uniquement en charge les messages durables ; par exemple, DeliveryMode.PERSISTENT, indépendamment de ce qui est spécifié. | 
| JMSDestination | Message.To | - | 
| JMSExpiration | Message. TimeToLive | Conversion | 
| JMSMessageID | Message.MessageID | Par défaut, JMSMessageID est codé sous forme binaire dans le message AMQP. À la réception du message-id binaire, la bibliothèque cliente .NET est convertie en une représentation de chaîne basée sur les valeurs Unicode des octets. Pour passer à la bibliothèque JMS afin d’utiliser les ID de message de chaîne, ajoutez la chaîne binary-messageid=false aux paramètres de requête de l’URL de connexion JNDI. Par exemple : amqps://[username]:[password]@[namespace].servicebus.windows.net? binary-messageid=false. | 
| JMSPriority | Actuellement non disponible | Service Bus ne prend pas en charge la priorité de message. | 
| JMSRedelivered | Actuellement non disponible | - | 
| JMSReplyTo | Message. ReplyTo | - | 
| JMSTimestamp | Message.EnqueuedTimeUtc | Conversion | 
| JMSType | Message.Properties[“jms-type”] | - |

#### API .NET Service Bus à JMS

| .NET Service Bus | JMS | Remarques |
|-------------------------|------------------|-------------------------|
| ContentType | - | Actuellement non disponible | 
| CorrelationId | JMSCorrelationID | - | 
| EnqueuedTimeUtc | JMSTimestamp | Conversion | 
| Label | n/a | Actuellement non disponible | 
| MessageId | JMSMessageID | - | 
| ReplyTo | JMSReplyTo | - | 
| ReplyToSessionId | n/a | Actuellement non disponible | 
| ScheduledEnqueueTimeUtc | n/a | Actuellement non disponible | 
| SessionId | n/a | Actuellement non disponible | 
| TimeToLive | JMSExpiration | Conversion | 
| To | JMSDestination | - |

## Fonctionnalités non prises en charge et restrictions

Les restrictions suivantes existent pour l’utilisation de JMS sur AMQP 1.0 avec Service Bus :

-   Un seul **MessageProducer** ou **MessageConsumer** est autorisé par session. Si vous souhaitez créer plusieurs objets **MessageProducer** ou **MessageConsumer** dans une application, créez des sessions dédiées pour chacun d’eux.

-   Les abonnements aux rubriques volatiles ne sont actuellement pas pris en charge.

-   Les objets **MessageSelector** ne sont pas pris en charge.

-   Les destinations temporaires (par exemple **TemporaryQueue** ou **TemporaryTopic**) ne sont pas prises en charge, à l’instar des API **QueueRequestor** et **TopicRequestor** qui les utilisent.

-   Les sessions transactionnelles ne sont pas prises en charge.

-   Les transactions distribuées ne sont pas prises en charge.

## Étapes suivantes

Prêt à en savoir plus ? Visitez les liens suivants :

- [Vue d’ensemble d’AMQP de Service Bus]
- [AMQP dans Service Bus pour Windows Server]

[AMQP dans Service Bus pour Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

[Vue d’ensemble d’AMQP de Service Bus]: service-bus-amqp-overview.md
[portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0928_2016-->