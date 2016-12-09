Dans cette procédure pas à pas, vous allez apprendre à utiliser le déclencheur **Salesforce - Création d’un objet** pour initier un flux de travail logique application lorsqu’un prospect est créé dans Salesforce.

> [!NOTE]
> Vous serez invité à vous connecter à votre compte Salesforce si vous n’avez pas encore créé une *connexion* à Salesforce.  
> 
> 

1. Saisissez *salesforce* dans la zone de recherche sur le concepteur d’applications logiques, puis sélectionnez le déclencheur **Salesforce - Création d’un objet**.  
   ![Image de déclencheur Salesforce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. Le contrôle **Lors de la création d’un objet** s’affiche.  
   ![Image de déclencheur Salesforce 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Sélectionnez le **Type d’objet** puis sélectionnez *Prospect* à partir de la liste d’objets. Lors de cette étape, vous indiquez que vous créez un déclencheur qui informe votre application logique de la création d’un prospect dans Salesforce.   
   ![Image de déclencheur Salesforce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. Vous avez terminé. Vous avez créé le déclencheur. Toutefois, vous devez créer au moins une action pour en faire une application logique valide.    
   ![Image de déclencheur Salesforce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

À ce stade, votre application logique a été configurée avec un déclencheur qui lance une série d’autres déclencheurs et actions dans le flux de travail lorsqu’un nouvel élément est créé dans Salesforce.  



<!--HONumber=Nov16_HO3-->


