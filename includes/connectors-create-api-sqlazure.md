### Configuration requise

- Un compte [SQL Azure](https://www.microsoft.com/sql)  


Avant de pouvoir utiliser votre compte SQL Azure dans une application logique, vous devez autoriser l’application logique à se connecter à votre compte SQL Azure. Heureusement, vous pouvez faire cela facilement à partir de votre application logique sur le portail Azure.

Pour autoriser votre application logique à se connecter à votre compte SQL Azure, procédez comme suit :
1. Pour créer une connexion à SQL Azure, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *SQL Azure* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser : ![étape de création de la connexion à SQL Azure](./media/connectors-create-api-sql/sql-1.png)  
2. Si vous n’avez créé aucune connexion à SQL Azure auparavant, vous êtes invité à indiquer vos informations d’identification SQL Azure. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte SQL Azure : ![étape de création de la connexion à SQL Azure](./media/connectors-create-api-sql/sql-2.png)  
3. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique : ![étape de création de la connexion à SQL Azure](./media/connectors-create-api-sql/sql-3.png)  

<!---HONumber=AcomDC_0525_2016-->