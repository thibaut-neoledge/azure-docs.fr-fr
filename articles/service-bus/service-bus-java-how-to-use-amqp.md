<properties 
	pageTitle="Utilisation d’AMQP 1.0 avec l’API Java Service Bus - Azure" 
	description="Découvrez comment utiliser Java Message Service (JMS) avec Azure Service Bus et le protocole Advanced Message Queuing Protocol (AMQP) 1.0." 
	authors="sethmanheim" 
	documentationCenter="java" 
	manager="timlt" 
	editor="" 
	services="service-bus"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="sethm"/>


# Utilisation de l’API Java Message Service (JMS) avec Service Bus et AMQP 1.0

Advanced Message Queuing Protocol (AMQP) 1.0 est un protocole de messagerie « wire-level » efficace et fiable qui peut être utilisé pour créer des applications de messagerie interplateforme robustes. La prise en charge d'AMQP 1.0 a été ajoutée à Azure Service Bus en octobre 2012 et mise à la disposition générale en mai 2013.

Grâce à l'ajout d'AMQP 1.0, vous pouvez désormais tirer parti des fonctionnalités de messagerie de mise en file d’attente et de publication/d'abonnement de Service Bus à partir de diverses plateformes, à l'aide d'un protocole binaire efficace. De plus, vous pouvez créer des applications constituées de composants créés à l’aide de divers langages, structures et systèmes d’exploitation.

Ce manuel d’utilisation explique comment utiliser les fonctionnalités de messagerie répartie Service Bus (rubriques de files d’attente et de publication/d’abonnement) depuis des applications Java en utilisant l’API standard Java Message Service (JMS).

## Prise en main de Service Bus

Ce guide présume que vous êtes déjà doté d’un espace de noms Services Bus contenant une file d’attente nommée « queue1 ». Dans le cas contraire, vous pouvez créer l’espace de noms et la file d’attente à l’aide du [portail de gestion Azure](http://manage.windowsazure.com). Pour plus d’informations sur la création d’espaces de noms et de files d’attente Service Bus, consultez [Utilisation des files d’attente Service Bus](service-bus-dotnet-how-to-use-queues.md).

### Téléchargement des bibliothèques clientes JMS d’AMQP 1.0

Pour plus d'informations sur l'adresse de téléchargement de la dernière version de la bibliothèque cliente Apache Qpid JMS AMQP 1.0, accédez à [http://people.apache.org/\~rgodfrey/qpid-java-amqp-1-0-client-jms.html](http://people.apache.org/~rgodfrey/qpid-java-amqp-1-0-client-jms.html).

Vous devez ajouter les quatre fichiers JAR suivants de l'archive de distribution Apache Qpid JMS AMQP 1.0 au CLASSPATH Java lors de la création et de l'exécution des applications JMS avec Service Bus :

*    geronimo-jms\_1.1\_spec-1.0.jar
*    qpid-amqp-1-0-client-[version].jar
*    qpid-amqp-1-0-client-jms-[version].jar
*    qpid-amqp-1-0-common-[version].jar

## Codage d'applications Java

### JNDI (Java Naming and Directory Interface)

JMS utilise l’interface JNDI (Java Naming and Directory Interface) pour créer une séparation entre les noms logiques et les noms physiques. Deux types d’objets JMS sont résolus à l’aide de JNDI : ConnectionFactory et Destination. JNDI utilise un modèle de fournisseur auquel vous pouvez connecter différents services d’annuaire afin de gérer les tâches de résolution de noms. La bibliothèque Apache Qpid JMS AMQP 1.0 est dotée d'un simple fournisseur JNDI fondé sur un fichier de propriétés qui est configuré à l'aide d'un fichier de propriétés au format suivant :

```
# servicebus.properties – sample JNDI configuration
	
# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[username]:[password]@[namespace].servicebus.windows.net
	
# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### Configuration de ConnectionFactory

L’entrée utilisée pour définir une **ConnectionFactory** dans le fournisseur JNDI de fichier de propriétés Qpid est au format suivant :

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Où **[jndi\_name]** et **[ConnectionURL]** ont les significations suivantes :

- **[jndi\_name]** : nom logique de la ConnectionFactory. Ce nom est résolu dans l'application Java à l'aide de la méthode JNDI IntialContext.lookup().
- **[ConnectionURL]** : URL qui fournit à la bibliothèque JMS les informations nécessaires au service Broker AMQP.

Le format de **ConnectionURL** est le suivant :

```
amqps://[username]:[password]@[namespace].servicebus.windows.net
```

Où **[namespace]**, **[username]** et **[password]** ont les significations suivantes :

- **[namespace]** : l'espace de noms Service Bus.
- **[username]** : nom de l'émetteur Service Bus.
- **[password]** : formulaire codé URL de la clé de l'émetteur Service Bus.

> [AZURE.NOTE]vous devez encoder manuellement le mot de passe dans une URL. Un utilitaire efficace d'encodage dans une URL est disponible à l'adresse [http://www.w3schools.com/tags/ref\_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp).

#### Configuration des destinations

L'entrée qui définit une destination dans le fournisseur JNDI de fichier de propriétés Qpid est au format suivant :

```
queue.[jndi_name] = [physical_name]
```
ou

```
topic.[jndi_name] = [physical_name]
```

Où **[jndi\_name]** et **[physical\_name]** ont les significations suivantes :

- **[jndi\_name]** : nom logique de la destination. Ce nom est résolu dans l'application Java à l'aide de la méthode JNDI IntialContext.lookup().
- **[physical\_name]**: nom de l’entité Service Bus vers laquelle l’application envoie ou reçoit des messages.

> [AZURE.NOTE]lors de la réception d'un abonnement à une rubrique Service Bus, le nom physique spécifié dans JNDI doit être le nom de la rubrique. Le nom de l'abonnement est fourni lors de la création de l'abonnement durable dans le code d'application JMS. [AMQP de Service Bus : guide du développeur](http://msdn.microsoft.com/library/jj841071.aspx) fournit des informations détaillées sur l'utilisation des abonnements aux rubriques Service Bus à partir de JMS.

### Écriture de l’application JMS

Il n’existe aucune API ou option particulière nécessaire à l’utilisation de JMS avec Service Bus. Toutefois, il existe quelques restrictions qui seront abordées ultérieurement. Comme dans toute application JMS, il convient de configurer l’environnement JNDI avant de pouvoir résoudre une **ConnectionFactory** et les destinations.

#### Configuration de JNDI InitialContext

L'environnement JNDI est configuré en transmettant une table de hachage d'informations de configuration dans le constructeur de la classe javax.naming.InitialContext. Les deux éléments requis dans la table de hachage sont le nom de classe de la fabrique de contexte initial et l'URL du fournisseur. Le code suivant montre comment configurer l’environnement JNDI afin d’utiliser le fournisseur JNDI fondé sur le fichier de propriétés Qpid avec un fichier de propriétés nommé **servicebus.properties**.

```
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### Une simple application JMS utilisant une file d’attente Service Bus

L’exemple de programme suivant envoie JMS TextMessages vers une file d’attente Service Bus avec le nom logique JNDI « QUEUE » et reçoit les messages en retour.

	// SimpleSenderReceiver.java
	
	import javax.jms.*;
	import javax.naming.Context;
	import javax.naming.InitialContext;
	import java.io.BufferedReader;
	import java.io.InputStreamReader;
	import java.util.Hashtable;
	import java.util.Random;
	
	public class SimpleSenderReceiver implements MessageListener {
	    private static boolean runReceiver = true;
	    private Connection connection;
	    private Session sendSession;
	    private Session receiveSession;
	    private MessageProducer sender;
	    private MessageConsumer receiver;
	    private static Random randomGenerator = new Random();
	
	    public SimpleSenderReceiver() throws Exception {
	        // Configure JNDI environment
	        Hashtable<String, String> env = new Hashtable<String, String>();
	        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                    "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
	        env.put(Context.PROVIDER_URL, "servicebus.properties");
	        Context context = new InitialContext(env);
	
	        // Lookup ConnectionFactory and Queue
	        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
	        Destination queue = (Destination) context.lookup("QUEUE");
	
	        // Create Connection
	        connection = cf.createConnection();
	
	        // Create sender-side Session and MessageProducer
	        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
	        sender = sendSession.createProducer(queue);
	
	        if (runReceiver) {
	            // Create receiver-side Session, MessageConsumer,and MessageListener
	            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
	            receiver = receiveSession.createConsumer(queue);
	            receiver.setMessageListener(this);
	            connection.start();
	        }
	    }
	
	    public static void main(String[] args) {
	        try {
	
	            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
	                runReceiver = false;
	            }
	
	            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
	            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));
	
	            while (true) {
	                String s = commandLine.readLine();
	                if (s.equalsIgnoreCase("exit")) {
	                    simpleSenderReceiver.close();
	                    System.exit(0);
	                } else {
	                    simpleSenderReceiver.sendMessage();
	                }
	            }
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	
	    private void sendMessage() throws JMSException {
	        TextMessage message = sendSession.createTextMessage();
	        message.setText("Test AMQP message from JMS");
	        long randomMessageID = randomGenerator.nextLong() >>>1;
	        message.setJMSMessageID("ID:" + randomMessageID);
	        sender.send(message);
	        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
	    }
	
	    public void close() throws JMSException {
	        connection.close();
	    }
	
	    public void onMessage(Message message) {
	        try {
	            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
	            message.acknowledge();
	        } catch (Exception e) {
	            e.printStackTrace();
	        }
	    }
	}	

### Exécution de l'application

L’exécution de l’application génère la sortie suivante :

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318
	
Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483
	
Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## Messagerie interplateforme entre JMS et .NET

Ce guide a expliqué comment envoyer et recevoir des messages depuis et vers Service Bus à l'aide de JMS. Néanmoins, l'un des avantages clés d'AMQP 1.0 est qu'il permet aux applications d'être créées à partir de composants écrits en différents langages, avec un échange de messages fiable d'une fidélité optimale.

À l'aide de l'exemple d'application JMS décrit ci-dessus et d'une application .NET similaire extraite du manuel associé intitulé [Utilisation du Service Bus à partir de .NET avec AMQP 1.0](service-bus-dotnet-advanced-message-queuing.md), il est possible d'échanger des messages entre .NET et Java.

Pour plus d'informations sur les détails de la messagerie interplateforme utilisant Service Bus et AMQP 1.0, consultez [AMQP de Service Bus : guide du développeur](http://msdn.microsoft.com/library/jj841071.aspx).

### De JMS à .NET

Démonstration de la messagerie entre JMS et .NET :

* Démarrez l'exemple d'application .NET sans argument de ligne de commande.
* Démarrez l'exemple d'application Java avec l'argument de ligne de commande « sendonly ». Dans ce mode, l'application ne reçoit pas de messages provenant de la file d'attente, elle ne fait qu'en envoyer.
* Appuyez plusieurs fois sur **Entrée** dans la console d'application Java, ce qui provoquera l'envoi des messages.
* Ces messages sont reçus par l'application .NET.

#### Résultat de l'application JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### Résultat de l'application .NET

```
> SimpleSenderReceiver.exe	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### De .NET à JMS

Démonstration de la messagerie entre .NET et JMS :

* Démarrez l'exemple d'application .NET avec l'argument de ligne de commande « sendonly ». Dans ce mode, l'application ne reçoit pas de messages provenant de la file d'attente, elle ne fait qu'en envoyer.
* Démarrez l'exemple d'application Java sans argument de ligne de commande.
* Appuyez plusieurs fois sur **Entrée** dans la console d'application .NET, ce qui provoquera l'envoi des messages.
* Ces messages sont reçus par l'application Java.

#### Résultat de l'application .NET

```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3	
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### Résultat de l'application JMS

```
> java SimpleSenderReceiver	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## Fonctionnalités non prises en charge et restrictions

Les restrictions suivantes existent pour l'utilisation de JMS sur AMQP 1.0 avec Service Bus, à savoir :

* Un seul **MessageProducer** ou **MessageConsumer** est autorisé par **Session**. Si vous devez créer plusieurs **MessageProducers** ou **MessageConsumers** dans une application, créez une **Session** dédiée pour chacun d'eux.
* Les abonnements aux rubriques volatiles ne sont actuellement pas pris en charge.
* Les **MessageSelectors** ne sont actuellement pas pris en charge.
* Les destinations temporaires, c'est-à-dire **TemporaryQueue** et **TemporaryTopic**, ne sont actuellement pas prises en charge, à l'instar des API **QueueRequestor** et **TopicRequestor** qui les utilisent.
* Les sessions traitées et les transactions distribuées ne sont pas prises en charge.

## Résumé

Ce manuel d'utilisation vous a montré comment mettre en œuvre les fonctionnalités de messagerie répartie Service Bus (rubriques files d'attente et publication/abonnement) depuis Java en utilisant l'API JMS (Java Message Service) connue et AMQP 1.0.

Vous pouvez également utiliser l’AMQP 1.0 de Service Bus avec d’autres langages, notamment .NET, C, Python et PHP. Les composants intégrés avec ces différents langages peuvent échanger des messages de manière fiable et fidèle en utilisant le support AMQP 1.0 dans Service Bus. Pour plus d’informations, consultez [AMQP de Service Bus 1.0 : guide du développeur](http://msdn.microsoft.com/library/jj841071.aspx).

## Étapes suivantes

* [Prise en charge d’AMQP 1.0 dans Azure Service Bus](service-bus-amqp-overview.md)
* [Utilisation d’AMQP 1.0 avec l’API .NET Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [AMQP de Service Bus 1.0 : guide du développeur](http://msdn.microsoft.com/library/jj841071.aspx)
* [Utilisation des files d’attente Service Bus](service-bus-dotnet-how-to-use-queues.md)
 

<!---HONumber=August15_HO6-->