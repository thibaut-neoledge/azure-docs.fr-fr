<properties 
	pageTitle="Utilisation d’AMQP 1.0 avec l’API .NET Service Bus | Microsoft Azure" 
	description="Découvrez comment utiliser le protocole Advanced Message Queuing Protodol (AMQP) 1.0 avec l'API .NET Azure Service Bus." 
	services="service-bus-messaging,service-bus" 
	documentationCenter=".net" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus-messaging" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/10/2016" 
	ms.author="sethm"/>

# Utilisation d’AMQP 1.0 avec l’API .NET Service Bus

Advanced Message Queuing Protocol (AMQP) 1.0 est un protocole de messagerie « wire-level » efficace et fiable qui peut être utilisé pour créer des applications de messagerie interplateforme robustes.

La prise en charge d'AMQP 1.0 dans Service Bus signifie que vous pouvez utiliser des fonctionnalités de messagerie répartie de mise en file d’attente et de publication/d'abonnement à partir de diverses plateformes, à l'aide d'un protocole binaire efficace. De plus, vous pouvez générer des applications constituées de composants créés à l'aide de divers langages, structures et systèmes d'exploitation.

Cet article explique comment utiliser les fonctionnalités de messagerie répartie Service Bus (rubriques de files d'attente et de publication/d'abonnement) depuis des applications .NET à l’aide de l'API .NET Service Bus. Un [article complémentaire](service-bus-java-how-to-use-jms-api-amqp.md) explique en outre comment réaliser les mêmes opérations à l'aide de l'API Java Message Service (JMS) standard. Vous pouvez utiliser ces deux guides ensemble pour découvrir la messagerie interplateforme en utilisant AMQP 1.0.

## Prise en main de Service Bus

Cet article présume que vous disposez déjà d'un espace de noms Service Bus contenant une file d'attente nommée « queue1 ». Dans le cas contraire, vous pouvez créer l'espace de noms et la file d’attente à l'aide du [portail Azure][]. Pour plus d’informations sur la création d’espaces de noms et de files d’attente Service Bus, consultez [Utilisation des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md).

## Téléchargement du Kit de développement logiciel (SDK) Service Bus

La prise en charge d’AMQP 1.0 est disponible dans le Kit de développement logiciel (SDK) Service Bus version 2.1 ou supérieure. Vous pouvez télécharger le dernier Kit de développement logiciel (SDK) depuis NuGet à la page [http://nuget.org/packages/WindowsAzure.ServiceBus/](http://nuget.org/packages/WindowsAzure.ServiceBus/).

## Codage des applications .NET

Par défaut, la bibliothèque cliente .NET Service Bus communique avec le service Service Bus à l’aide d’un protocole SOAP dédié. Pour utiliser AMQP 1.0 au lieu du protocole par défaut, vous avez besoin d'une configuration explicite sur la chaîne de connexion Service Bus, comme indiqué dans la section suivante. À l'exception de cette modification, le code de l'application reste inchangé lors de l'utilisation d'AMQP 1.0.

Dans la version actuelle, quelques fonctionnalités de l’API ne sont pas prises en charge lors de l’utilisation d’AMQP. Celles-ci sont répertoriées plus bas dans la section [Fonctionnalités non prises en charge et restrictions](#unsupported-features-and-restrictions). Certains paramètres de configuration avancés ont également une signification différente lors de l’utilisation d’AMQP. Aucun de ces paramètres n'est utilisé dans cet article, mais de plus amples informations sont disponibles dans la [Vue d'ensemble d'AMQP dans Service Bus](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

### Configuration via App.config

L’utilisation du fichier de configuration App.config par les applications afin de stocker les paramètres est recommandée. Pour les applications Service Bus, vous pouvez utiliser App.config pour stocker la chaîne de connexion (**ConnectionString**) de Service Bus. Cet exemple d’application utilise également le fichier App.config pour stocker le nom de l’entité de messagerie Service Bus qu’il utilise.

Vous trouverez ci-après un exemple de fichier App.config :

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  	<appSettings>
	    <add key="Microsoft.ServiceBus.ConnectionString"
       	     value="Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
	    	<add key="EntityName" value="queue1" />
	</appSettings>
</configuration>
```

### Configuration de la chaîne de connexion de Service Bus

La valeur du paramètre **Microsoft.ServiceBus.ConnectionString** est la chaîne de connexion Service Bus utilisée pour configurer la connexion à Service Bus. au format suivant :

```
Endpoint=sb://[namespace].servicebus.windows.net;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp
```

Où `[namespace]` et `[SAS key]` sont obtenues à partir du [portail Azure][]. Pour plus d’informations, consultez la page [Utilisation des files d’attente Service Bus][].

Lors de l’utilisation d’AMQP, `;TransportType=Amqp` est ajouté à la chaîne de connexion, ce qui indique à la bibliothèque cliente d’établir sa connexion à Service Bus à l’aide d’AMQP 1.0.

### Configuration du nom d’entité

Cet exemple d’application utilise le paramètre `EntityName` dans la section **appSettings** du fichier App.config pour configurer le nom de la file d’attente avec laquelle l’application échange des messages.

### Une simple application .NET utilisant une file d’attente Service Bus

L'exemple suivant envoie et reçoit des messages vers et depuis une file d'attente Service Bus.

```
// SimpleSenderReceiver.cs
	
using System;
using System.Configuration;
using System.Threading;
using Microsoft.ServiceBus.Messaging;
	
namespace SimpleSenderReceiver
{
    class SimpleSenderReceiver
    {
        private static string connectionString;
        private static string entityName;
        private static Boolean runReceiver = true;
        private MessagingFactory factory;
        private MessageSender sender;
        private MessageReceiver receiver;
        private MessageListener messageListener;
        private Thread listenerThread;
	
        static void Main(string[] args)
        {
            try
            {
                if ((args.Length > 0) && args[0].ToLower().Equals("sendonly"))
                    runReceiver = false;
	                
                string ConnectionStringKey = "Microsoft.ServiceBus.ConnectionString";
                string entityNameKey = "EntityName";
                entityName = ConfigurationManager.AppSettings[entityNameKey];
                connectionString = ConfigurationManager.AppSettings[ConnectionStringKey];
                SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
	
                Console.WriteLine("Press [enter] to send a message. " +
                    "Type 'exit' + [enter] to quit.");
	
                while (true)
                {
                    string s = Console.ReadLine();
                    if (s.ToLower().Equals("exit"))
                    {
                        simpleSenderReceiver.Close();
                        System.Environment.Exit(0);
                    }
                    else
                        simpleSenderReceiver.SendMessage();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Caught exception: " + e.Message);
            }
        }
	
        public SimpleSenderReceiver()
        {
            factory = MessagingFactory.CreateFromConnectionString(connectionString);
            sender = factory.CreateMessageSender(entityName);
	
            if (runReceiver)
            {
                receiver = factory.CreateMessageReceiver(entityName);
                messageListener = new MessageListener(receiver);
                listenerThread = new Thread(messageListener.Listen);
                listenerThread.Start();
            }
        }
	
        public void Close()
        {
            messageListener.RequestStop();
            listenerThread.Join();
            factory.Close();
        }
	
        private void SendMessage()
        {
            BrokeredMessage message = new BrokeredMessage("Test AMQP message from .NET");
            sender.Send(message);
            Console.WriteLine("Sent message with MessageID = " 
                + message.MessageId);
        }

    }
	
    public class MessageListener
    {
        private MessageReceiver messageReceiver;
        public MessageListener(MessageReceiver receiver)
        {
            messageReceiver = receiver;
        }
	
        public void Listen()
        {
            while (!_shouldStop)
            {
                try
                {
                    BrokeredMessage message = 
                        messageReceiver.Receive(new TimeSpan(0, 0, 10));
                    if (message != null)
                    {
                        Console.WriteLine("Received message with MessageID = " + 
                            message.MessageId);
                        message.Complete();
                    }
                }
                catch (Exception e)
                {
                    Console.WriteLine("Caught exception: " + e.Message);
                    break;
                }
            }
        }
	
        public void RequestStop()
        {
            _shouldStop = true;
        }
	
        private volatile bool _shouldStop;
    }
}
```

### Exécution de l'application

L'exécution de l'application produit un résultat ressemblant à ce qui suit :

```
> SimpleSenderReceiver.exe
Press [enter] to send a message. Type 'exit' + [enter] to quit.
	
Sent message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
Received message with MessageID = fb7f5d3733704e4ba4bd55f759d9d7cf
	
Sent message with MessageID = d00e2e088f06416da7956b58310f7a06
Received message with MessageID = d00e2e088f06416da7956b58310f7a06
	
Received message with MessageID = f27f79ec124548c196fd0db8544bca49
Sent message with MessageID = f27f79ec124548c196fd0db8544bca49
exit
```

## Messagerie interplateforme entre JMS et .NET

Cette rubrique a expliqué comment envoyer des messages à Service Bus en utilisant .NET et également comment les recevoir à l'aide de .NET. Néanmoins, l'un des avantages clés d'AMQP 1.0 est qu'il permet aux applications d'être créées à partir de composants écrits en différents langages, avec un échange de messages fiable d'une fidélité optimale.

À l'aide de l'exemple d'application .NET décrit ci-dessus et d'une application Java similaire extraite d'un manuel associé, [Utilisation de l'API Java Message Service (JMS) avec Service Bus et AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md), il est possible d'échanger des messages entre .NET et Java.

Pour plus d’informations sur les détails de la messagerie interplateforme utilisant Service Bus et AMQP 1.0, consultez [Vue d’ensemble d’AMQP 1.0 dans Service Bus](service-bus-amqp-overview.md).

### De JMS à .NET

Démonstration de la messagerie entre JMS et .NET :

* Démarrez l'exemple d'application .NET sans argument de ligne de commande.
* Démarrez l'exemple d'application Java avec l'argument de ligne de commande « sendonly ». Dans ce mode, l’application ne reçoit pas de messages provenant de la file d’attente, elle ne fait qu’en envoyer.
* Appuyez plusieurs fois sur **Entrée** dans la console d'application Java, ce qui provoquera l'envoi des messages.
* Ces messages sont reçus par l'application .NET.

### Résultat de l'application JMS

```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

### Résultat de l'application .NET

```
> SimpleSenderReceiver.exe	
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

## De .NET à JMS

Démonstration de la messagerie entre .NET et JMS :

* Démarrez l'exemple d'application .NET avec l'argument de ligne de commande "sendonly". Dans ce mode, l’application ne reçoit pas de messages provenant de la file d’attente, elle ne fait qu’en envoyer.
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

Les fonctionnalités suivantes de l'API .NET Services Bus ne sont actuellement pas prises en charge lors de l'utilisation d'AMQP :

 * Transactions
 * Envoi via destination de transfert

Pour plus d’informations, consultez [Fonctionnalités non prises en charge, restrictions et différences de comportement](service-bus-amqp-dotnet.md#unsupported-features-restrictions-and-behavioral-differences).

## Résumé

Cet article a expliqué comment accéder aux fonctionnalités de messagerie répartie Service Bus (rubriques de files d’attente et de publication/d’abonnement) depuis .NET à l’aide d’AMQP 1.0 et l’API .NET Service Bus.

Vous pouvez également utiliser AMQP 1.0 de Service Bus avec d’autres langages, notamment Java, C, Python et PHP. Les composants intégrés avec ces langages peuvent échanger des messages de manière fiable et fidèle en utilisant AMQP 1.0 dans Service Bus. Pour plus d’informations, consultez [Vue d’ensemble d’AMQP dans Service Bus](service-bus-amqp-dotnet.md).

## Étapes suivantes

Maintenant que vous avez une vue d'ensemble du Service Bus et AMQP avec .NET, consultez les liens suivants pour plus d'informations.

* [Prise en charge d’AMQP 1.0 dans Azure Service Bus](service-bus-amqp-overview.md)
* [Utilisation de l’API Java Message Service (JMS) avec Service Bus et AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
* [Utilisation des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
 
[portail Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0928_2016-->