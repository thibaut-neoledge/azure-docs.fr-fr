#### <a name="prerequisites"></a>Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte [Office 365](https://office365.com)  

Avant d’utiliser votre compte Office 365 dans une application logique, autorisez l’application logique à se connecter à votre compte Office 365. Cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure.  

Pour autoriser votre application logique à se connecter à votre compte Office 365, procédez comme suit :

1. Créez une application logique. Dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez « office 365 » dans la zone de recherche. Sélectionnez l’un des déclencheurs ou actions :  
    ![Étape de création de la connexion à Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Si vous n’avez créé aucune connexion à Office 365 auparavant, vous êtes invité à vous connecter avec vos informations d’identification Office 365 :  
    ![étape de création de la connexion à Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Sélectionnez **Connexion**, puis indiquez votre nom d’utilisateur et votre mot de passe. Sélectionnez **Connexion** :  
    ![Étape de création de la connexion à Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Ces informations d’identification servent à autoriser votre application logique à se connecter et à accéder à votre compte Office 365. 
4. Vous pouvez voir que la connexion a été créée. Maintenant, effectuez les autres étapes dans votre application logique :   
    ![étape de création de la connexion à Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

