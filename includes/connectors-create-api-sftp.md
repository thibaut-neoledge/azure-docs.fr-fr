### <a name="prerequisites"></a>Composants requis
* Un compte [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  

Pour pouvoir utiliser votre compte SFTP dans une application logique, vous devez autoriser l’application à se connecter à votre compte. Heureusement, cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure.  

Pour autoriser votre application logique à se connecter à votre compte SFTP, procédez comme suit :  

1. Pour créer une connexion à SFTP, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *SFTP* dans la zone de recherche. Sélectionnez le déclencheur **SFTP - Lors de l’ajout ou de la modification d’un fichier** :  
   ![Image de connexion SFTP en ligne 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Si vous n’avez créé aucune connexion à SFTP auparavant, vous êtes invité à indiquer vos informations d’identification SFTP. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte SFTP :  
   ![Image de connexion SFTP en ligne 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Notez que la connexion a été créée et que vous pouvez désormais poursuivre la procédure dans votre application logique :   
   ![Image de connexion SFTP en ligne 3](./media/connectors-create-api-sftp/sftp-3.png) 



<!--HONumber=Nov16_HO3-->


