### <a name="prerequisites"></a>Composants requis
* Un compte Twilio
* Un numéro de téléphone Twilio vérifié pouvant recevoir des SMS
* Un numéro de téléphone Twilio vérifié pouvant envoyer des SMS

> [!NOTE]
> Si vous utilisez un compte d’évaluation Twilio, vous ne pouvez envoyer des SMS qu’à des numéros de téléphone **vérifiés**.  
> 
> 

Pour pouvoir utiliser votre compte Twilio dans une application logique, vous devez autoriser l’application à se connecter à votre compte. Heureusement, cette opération est très simple à effectuer dans votre application logique sur le portail Azure. 

Pour autoriser votre application logique à se connecter à votre compte Twilio, procédez comme suit :

1. Pour créer une connexion à Twilio, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *Twilio* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser :   
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Si vous n’avez créé aucune connexion à Twilio auparavant, vous êtes invité à indiquer vos informations d’identification Twilio. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte Twilio :  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Vous aurez besoin de **l’ID du compte Twilio** et du **jeton d’accès Twilio** figurant dans le tableau de bord de Twilio. Donc, connectez-vous à votre compte Twilio pour obtenir ces deux informations :  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio et les applications logiques utilisent des noms différents pour identifier ces deux informations. Voici comment vous devez les mapper dans la boîte de dialogue Logic Apps : ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Cliquez sur le bouton **Créer une connexion** :  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique :   
   ![](./media/connectors-create-api-twilio/twilio-5.png)

