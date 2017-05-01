Cette condition évaluera le champ d’adresse de messagerie de chaque nouveau prospect Salesforce. Si l’adresse de messagerie contient *amazon.com*, le résultat de la condition est *True*.

1. Sélectionnez **+ Nouvelle étape**.  
   ![Image de condition Salesforce 1](./media/connectors-create-api-salesforce/condition-1.png)   
2. Sélectionnez **Ajouter une condition**.    
   ![Image de condition Salesforce 2](./media/connectors-create-api-salesforce/condition-2.png)  
3. Sélectionnez **Choisir une valeur**.    
   ![Image de condition Salesforce 3](./media/connectors-create-api-salesforce/condition-3.png)  
4. Sélectionnez le jeton *E-mail* à partir du prospect du déclencheur.    
   ![Image de condition Salesforce 4](./media/connectors-create-api-salesforce/condition-4.png)  
5. Sélectionnez *Contient*.      
   ![Image de condition Salesforce 5](./media/connectors-create-api-salesforce/condition-5.png)  
6. Sélectionnez **Choisir une valeur** en bas du contrôle.     
   ![Image de condition Salesforce 6](./media/connectors-create-api-salesforce/condition-6.png)  
7. Saisissez *amazon.com* en tant que valeur pour laquelle vous souhaitez évaluer l’adresse de messagerie du nouveau prospect. Si l’adresse de messagerie contient *amazon.com*, la condition correspond à *True* et les autres étapes de votre application logique peuvent s’exécuter.    
   ![Image de condition Salesforce 7](./media/connectors-create-api-salesforce/condition-7.png)  
8. Enregistrez vos applications logiques.  

