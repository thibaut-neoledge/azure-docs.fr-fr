## <a name="connect-to-outlookcom"></a>Connexion à Outlook.com
### <a name="prerequisites"></a>Composants requis
* Un compte Outlook.com

Pour pouvoir utiliser votre compte Outlook.com dans une application logique, vous devez autoriser l’application à se connecter à votre compte. Heureusement, cette opération est très simple à effectuer dans votre application logique sur le portail Azure. 

Pour autoriser votre application logique à se connecter à votre compte Outlook.com, procédez comme suit :

1. Toutes les applications logiques doivent être démarrées par un déclencheur. Donc, une fois votre application logique créée, le concepteur s’ouvre et affiche la liste des déclencheurs que vous pouvez utiliser pour démarrer votre application logique :
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Dans la zone de recherche, entrez « outlook ». Remarquez que la liste est filtrée de façon à afficher tous les déclencheurs dont le nom contient « Outlook » : ![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Sélectionnez **Office 365 Outlook - En cas de nouveau message**.   
   Si vous n’avez créé aucune connexion à Outlook auparavant, vous êtes invité à indiquer vos informations d’identification Outlook.com. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte Outlook.com : ![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Indiquez vos informations d’identification pour Outlook et connectez-vous : ![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   Vous avez terminé. Vous venez de créer une connexion à Outlook. Cette connexion sera disponible dans toutes les applications logiques que vous créerez.



<!--HONumber=Nov16_HO3-->


