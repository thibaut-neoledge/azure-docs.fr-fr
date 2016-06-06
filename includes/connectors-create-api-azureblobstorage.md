### Composants requis

- Compte de [stockage d’objets blob Azure](https://azure.microsoft.com/documentation/services/storage/)  


Avant de pouvoir utiliser votre compte de stockage d’objets blob dans une application logique, vous devez autoriser l’application logique à se connecter à votre compte de stockage d’objets blob Azure. Heureusement, vous pouvez faire cela facilement à partir de votre application logique sur le portail Azure.

Pour autoriser votre application logique à se connecter à votre compte de stockage d’objets blob Azure, procédez comme suit :
1. Pour créer une connexion au stockage d’objets blob Azure, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *stockage d’objets blob Azure* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser : ![Étape de création de la connexion au stockage d’objets blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Si vous n’avez créé aucune connexion à au stockage d’objets blob Azure auparavant, vous êtes invité à indiquer vos informations d’identification au stockage d’objets blob Azure. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte de stockage d’objets blob Azure : ![Étape de création de la connexion au stockage d’objets blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-2.png)  
3. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique : ![Étape de création de la connexion au stockage d’objets blob Azure](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

<!---HONumber=AcomDC_0525_2016-->