## Envoi de messages vers Event Hubs
La bibliothèque cliente Java pour Event Hubs est disponible pour une utilisation dans les projets Maven à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Elle peut être référencée à l’aide de la déclaration de dépendance suivante dans votre fichier de projet Maven :

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```

Pour différents types d’environnements de génération, vous pouvez obtenir explicitement les fichiers JAR les plus récents à partir du [référentiel central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) ou [du point de distribution de version sur GitHub](https://github.com/Azure/azure-event-hubs/releases).

Pour une publication d’événement plus simple, importez le package *com.microsoft.azure.eventhubs* pour les classes clientes Event Hubs et le package *com.microsoft.azure.servicebus* pour les classes d’utilitaire, telles que les exceptions courantes qui sont partagées avec le client de messagerie Azure Service Bus.

Pour l’exemple suivant, créez tout d’abord un nouveau projet Maven pour une application de console/shell dans votre environnement de développement Java favori. La classe sera appelée ```Send```.

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Remplacez l’espace de noms et les noms du concentrateur d’événements par les valeurs utilisées lorsque vous avez créé le concentrateur d’événements.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Ensuite, créez un événement unique en transformant une chaîne dans son encodage UTF-8 octets. Nous créons ensuite une nouvelle instance cliente Event Hubs à partir de la chaîne de connexion et nous envoyons le message.

``` Java 

    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);

    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

<!---HONumber=AcomDC_0907_2016-->