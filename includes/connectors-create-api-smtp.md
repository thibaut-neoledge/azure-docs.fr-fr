### Composants requis

- Un compte [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  


Avant de pouvoir utiliser votre compte SMTP dans une application logique, vous devez autoriser l’application logique à se connecter à votre compte SMTP. Heureusement, vous pouvez faire cela facilement à partir de votre application logique sur le portail Azure.  

Pour autoriser votre application logique à se connecter à votre compte SMTP, procédez comme suit :  
1. Pour créer une connexion à SMTP, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *SMTP* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser :  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Si vous n’avez créé aucune connexion à SMTP auparavant, vous êtes invité à indiquer vos informations d’identification SMTP. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte SMTP :  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique :  
![](./media/connectors-create-api-smtp/smtp-3.png)  

<!---HONumber=AcomDC_0727_2016-->
