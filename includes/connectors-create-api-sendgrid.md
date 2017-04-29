### <a name="prerequisites"></a>Composants requis
* Un compte [SendGrid](https://www.SendGrid.com/) 

Pour pouvoir utiliser votre compte SendGrid dans une application logique, vous devez autoriser l’application à se connecter à votre compte. Heureusement, cette opération est très simple à effectuer dans votre application logique sur le portail Azure. 

Pour autoriser votre application logique à se connecter à votre compte SendGrid, procédez comme suit :

1. Pour créer une connexion à SendGrid, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *SendGrid* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser :   
   ![SendGrid étape 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Si vous n’avez créé aucune connexion à SendGrid auparavant, vous êtes invité à indiquer vos informations d’identification SendGrid. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte SendGrid :  
   ![SendGrid étape 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique :   
   ![SendGrid étape 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

