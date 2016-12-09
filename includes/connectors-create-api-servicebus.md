### <a name="prerequisites"></a>Composants requis
Vous devez disposer d’un compte [Service Bus](https://azure.microsoft.com/services/service-bus/).  

Pour pouvoir utiliser votre compte Azure Service Bus dans une application logique, vous devez autoriser l’application à se connecter à votre compte. Heureusement, cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure.  

Pour autoriser votre application logique à se connecter à votre compte Service Bus, procédez comme suit :  

1. Pour créer une connexion à Service Bus, dans le concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante. Puis entrez **service bus** dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous souhaitez utiliser.  
    ![Image de connexion à Service Bus 1](./media/connectors-create-api-servicebus/servicebus-1.png)  
2. Si vous n’avez créé aucune connexion à Service Bus auparavant, vous êtes invité à indiquer vos informations d’identification Service Bus. Ces informations d’identification servent à autoriser votre application logique à se connecter et à accéder aux données de votre compte Service Bus. Le connecteur de Service Bus a besoin de la chaîne de connexion pour l’espace de noms Service Bus. Il nécessite également des autorisations de **gestion**. Un bon moyen de savoir si votre chaîne de connexion est pour l’espace de noms ou une entité spécifique est de voir si elle contient le paramètre `EntityPath`. Si tel est le cas, ce n’est pas la chaîne de connexion appropriée pour une application logique.  
    ![Chaîne de connexion à Service Bus](./media/connectors-create-api-servicebus/connectionstring.png)
3. Une fois que vous avez reçu la chaîne de connexion pour l’espace de noms, vous pouvez l’utiliser pour la connexion à l’API dans les applications logiques.  
    ![Image de connexion à Service Bus 2](./media/connectors-create-api-servicebus/servicebus-2.png)  
4. Notez que la connexion a été créée et que vous pouvez désormais poursuivre la procédure dans votre application logique.  
    ![Image de connexion à Service Bus 3](./media/connectors-create-api-servicebus/servicebus-3.png)   



<!--HONumber=Nov16_HO3-->


