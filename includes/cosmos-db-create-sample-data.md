Vous pouvez maintenant ajouter des données à votre nouvelle collection grâce à l’Explorateur de données.

1. Dans l’Explorateur de données, la nouvelle base de données apparaît dans le volet Collections. Développez la base de données **Éléments**, développez la collection **ToDoList**, cliquez sur **Documents**, puis cliquez sur **Nouveaux documents**. 

   ![Créer des documents dans l’Explorateur de données, dans le portail Azure](./media/cosmos-db-create-sample-data/azure-cosmosdb-data-explorer-emulator-new-document.png)
  
2. Maintenant, ajoutez de nouveaux documents à la collection avec la structure suivante, en insérant des valeurs uniques pour l’ID de chaque document et en modifiant les autres propriétés à votre convenance. Vos nouveaux documents peuvent avoir la structure de votre choix car Azure Cosmos DB n’impose aucun schéma pour vos données.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries."
     }
     ```

     Vous pouvez désormais utiliser des requêtes dans l’Explorateur de données pour récupérer vos données. Par défaut, l’Explorateur de données utilise SELECT * FROM c pour récupérer tous les documents dans la collection, mais vous pouvez le remplacer par SELECT * FROM c ORDER BY c.name ASC, afin d’afficher tous les documents par ordre alphabétique d’après la propriété Name. 
 
     Vous pouvez également utiliser l’Explorateur de données pour créer des procédures stockées, des fonctions définies par l'utilisateur et des déclencheurs afin d’exécuter la logique métier côté serveur. L’Explorateur de données affiche tous les accès aux données par programmation intégrés disponibles dans l’API, mais permet d’accéder facilement à vos données dans le portail Azure.