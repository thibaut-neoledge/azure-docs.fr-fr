### Composants requis

- Un compte [ServiceBus](https://azure.microsoft.com/services/service-bus/)  


Avant de pouvoir utiliser votre compte ServiceBus dans une application logique, vous devez autoriser l’application logique à se connecter à votre compte ServiceBus. Heureusement, vous pouvez faire cela facilement à partir de votre application logique sur le portail Azure.

Pour autoriser votre application logique à se connecter à votre compte ServiceBus, procédez comme suit :
1. Pour créer une connexion à ServiceBus, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *ServiceBus* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous voulez utiliser : ![](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Si vous n’avez créé aucune connexion à ServiceBus auparavant, vous êtes invité à indiquer vos informations d’identification ServiceBus. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte ServiceBus : ![](./media/connectors-create-api-servicebus/servicebus-2.png)  
3. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique : ![](./media/connectors-create-api-servicebus/servicebus-3.png)   

<!---HONumber=AcomDC_0525_2016-->