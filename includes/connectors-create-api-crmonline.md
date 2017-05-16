#### <a name="prerequisites"></a>Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Avant d’utiliser votre compte Dynamics dans une application logique, autorisez celle-ci à se connecter à votre compte CRM Online. Cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure. 

Pour autoriser votre application logique à se connecter à votre compte CRM Online, procédez comme suit :

1. Créez une application logique. Dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez « dynamics » dans la zone de recherche. Sélectionnez l’un des déclencheurs ou actions :  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Si vous n’avez créé aucune connexion à Dynamics, vous êtes invité à vous connecter avec vos informations d’identification Dynamics :  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Sélectionnez **Connexion**, puis indiquez votre nom d’utilisateur et votre mot de passe. Sélectionnez **Connexion**. 
   
    Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte Dynamics. 
4. Vous pouvez voir que la connexion a été créée. Maintenant, effectuez les autres étapes dans votre application logique :  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

