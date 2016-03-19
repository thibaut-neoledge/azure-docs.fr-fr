## Envoi de messages vers Event Hubs

Dans cette section, nous allons écrire une application console Java pour envoyer des événements à votre hub d’événements. Nous allons utiliser le fournisseur JMS AMQP du [projet Apache Qpid](http://qpid.apache.org/). Cette approche est similaire à l’utilisation des rubriques et files d’attente Service Bus avec AMQP en partant du langage Java comme indiqué [ici](../service-bus/service-bus-java-how-to-use-jms-api-amqp.md). Pour plus d’informations, consultez la [documentation Qpid JMS](http://qpid.apache.org/releases/qpid-0.30/programming/book/QpidJMS.html) et la page [Service de messagerie Java](http://www.oracle.com/technetwork/java/jms/index.html).

1. Dans Eclipse, installez le [Kit de ressources Azure pour Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx). Il inclut les bibliothèques clientes Qpid JMS AMQP.

2. Dans Eclipse, créez un projet Java nommé **Sender**.

3. Dans l'Explorateur de Package d'Eclipse, cliquez avec le bouton droit sur le projet **Sender** et sélectionnez **Propriétés**. Dans le volet gauche de la boîte de dialogue, cliquez sur **Chemin d’accès de la génération Java**, puis cliquez sur l’onglet **Bibliothèques** et sur le bouton **Ajouter une bibliothèque**. Sélectionnez **Package pour les bibliothèques clientes Apache Qpid pour JMS (par MS Open Tech)**, cliquez sur **Suivant**, puis sur **Terminer**.

	![][8]

4. Créez un fichier nommé **servicebus.properties** à la racine du projet **Sender**, avec le contenu suivant. N’oubliez pas de remplacer les valeurs de votre :
	- nom d’Event Hub ;
	- nom d’espace de noms (ce dernier est généralement `{event hub name}-ns`) ;
	- clé **SendRule** codée URL (vous avez noté cette clé lorsque vous avez créé votre Event Hub). Vous pouvez la coder par URL [ici](http://www.w3schools.com/tags/ref_urlencode.asp).

			# servicebus.properties - sample JNDI configuration

			# Register a ConnectionFactory in JNDI using the form:
			# connectionfactory.[jndi_name] = [ConnectionURL]
			connectionfactory.SBCF = amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/?sync-publish=false

			# Register some queues in JNDI using the form
			# queue.[jndi_name] = [physical_name]
			# topic.[jndi_name] = [physical_name]
			queue.EventHub = {event hub name}

5. Créez une classe appelée **Sender**. Ajoutez les instructions `import` suivantes :

		import java.io.BufferedReader;
		import java.io.IOException;
		import java.io.InputStreamReader;
		import java.io.UnsupportedEncodingException;
		import java.util.Hashtable;

		import javax.jms.BytesMessage;
		import javax.jms.Connection;
		import javax.jms.ConnectionFactory;
		import javax.jms.Destination;
		import javax.jms.JMSException;
		import javax.jms.MessageProducer;
		import javax.jms.Session;
		import javax.naming.Context;
		import javax.naming.InitialContext;
		import javax.naming.NamingException;

6. Ensuite, ajoutez le code suivant :

		public static void main(String[] args) throws NamingException,
				JMSException, IOException, InterruptedException {
			// Configure JNDI environment
			Hashtable<String, String> env = new Hashtable<String, String>();
			env.put(Context.INITIAL_CONTEXT_FACTORY,
					"org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
			env.put(Context.PROVIDER_URL, "servicebus.properties");
			Context context = new InitialContext(env);

			ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

			Destination queue = (Destination) context.lookup("EventHub");

			// Create Connection
			Connection connection = cf.createConnection();

			// Create sender-side Session and MessageProducer
			Session sendSession = connection.createSession(false,
					Session.AUTO_ACKNOWLEDGE);
			MessageProducer sender = sendSession.createProducer(queue);

			System.out.println("Press Ctrl-C to stop the sender process");
			System.out.println("Press Enter to start now");
			BufferedReader commandLine = new java.io.BufferedReader(
					new InputStreamReader(System.in));
			commandLine.readLine();

			while (true) {
				sendBytesMessage(sendSession, sender);
				Thread.sleep(200);
			}
		}

		private static void sendBytesMessage(Session sendSession, MessageProducer sender) throws JMSException, UnsupportedEncodingException {
	        BytesMessage message = sendSession.createBytesMessage();
	        message.writeBytes("Test AMQP message from JMS".getBytes("UTF-8"));
	        sender.send(message);
	        System.out.println("Sent message");
	    }



<!-- Images -->
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-java1.png

<!---HONumber=Nov15_HO3-->