### Composants requis
- Accès à une base de données SQL Server

Pour pouvoir utiliser Mashup SQL dans une application logique, vous devez autoriser celle-ci à se connecter à votre compte Mashup SQL. Heureusement, cette opération est très simple à effectuer dans votre application logique sur le portail Azure.

Pour autoriser votre application logique à se connecter à votre compte Mashup SQL, procédez comme suit :

1. Pour créer une connexion à Mashup SQL, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *Mashup SQL* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser : ![Mashup SQL étape 1](./media/connectors-create-api-mashupsql/mashupsql-1.png)
2. Si vous n’avez créé aucune connexion à Mashup SQL auparavant, vous êtes invité à indiquer vos informations d’identification Mashup SQL. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte Mashup SQL : ![Mashup SQL étape 2](./media/connectors-create-api-mashupsql/mashupsql-2.png)
5. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique : ![Mashup SQL étape 3](./media/connectors-create-api-mashupsql/mashupsql-3.png)