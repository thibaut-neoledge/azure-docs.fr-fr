### <a name="prerequisites"></a>Composants requis
* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Une [base de données SQL Azure](../articles/sql-database/sql-database-get-started.md), ainsi que ses informations de connexion (nom du serveur, nom de la base de données et nom d’utilisateur/mot de passe). Ces informations sont incluses dans la chaîne de connexion de base de données SQL :
  
    Server=tcp:*nom_SQL_Server*.database.windows.net,1433;Initial Catalog=*nom_BD_SQL*;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
  
    Pour en savoir plus, consultez [Bases de données SQL Azure](https://azure.microsoft.com/services/sql-database).

> [!NOTE]
> Lorsque vous créez une base de données SQL Azure, vous pouvez également créer des exemples de bases de données inclus avec SQL. 
> 
> 

Avant d’utiliser votre base de données SQL Azure dans une application logique, connectez-vous à votre base de données SQL. Cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure.  

Connectez-vous à votre base de données SQL Azure en suivant les étapes ci-dessous :  

1. Créez une application logique. Dans le concepteur d’applications logiques, ajoutez un déclencheur, puis ajoutez une action. Sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis saisissez « sql » dans la zone de recherche. Sélectionnez une des actions :  
   
    ![étape de création de la connexion à SQL Azure](./media/connectors-create-api-sqlazure/sql-actions.png)
2. Si vous n’avez pas encore créé de connexion à la base de données SQL, vous êtes invité à saisir les informations de connexion :  
   
    ![étape de création de la connexion à SQL Azure](./media/connectors-create-api-sqlazure/connection-details.png) 
3. Entrez les détails de la base de données SQL. Les propriétés marquées d’un astérisque sont obligatoires.
   
   | Propriété | Détails |
   | --- | --- |
   | Se connecter via la passerelle |Laissez cette option désactivée. Elle est utilisée si vous vous connectez à un serveur SQL en local. |
   | Nom de connexion * |Entrez un nom pour votre connexion. |
   | Nom du serveur SQL Server * |Entrez le nom du serveur qui se présente sous la forme *nom_serveur.base_données.windows.net*. Le nom du serveur est indiqué dans les propriétés de la base de données SQL sur le portail Azure ainsi que dans la chaîne de connexion. |
   | Nom de la base de données SQL * |Entrez le nom que vous avez donné à votre base de données SQL. Il est indiqué dans les propriétés de la base de données SQL dans la chaîne de connexion : Initial Catalog=*nom_BD_SQL*. |
   | Nom d’utilisateur * |Entrez le nom d’utilisateur que vous avez indiqué lors de la création de la base de données SQL. Il est indiqué dans les propriétés de la base de données SQL sur le portail Azure. |
   | Mot de passe * |Entrez le mot de passe que vous avez indiqué lors de la création de la base de données SQL. |
   
    Ces informations d’identification servent à autoriser votre application logique à se connecter et à accéder à vos données SQL. Une fois complets, les détails de votre connexion se présentent comme suit :  
   
    ![étape de création de la connexion à SQL Azure](./media/connectors-create-api-sqlazure/sample-connection.png) 
4. Sélectionnez **Créer**. 
5. Vous pouvez voir que la connexion a été créée. Maintenant, effectuez les autres étapes dans votre application logique : 
   
    ![étape de création de la connexion à SQL Azure](./media/connectors-create-api-sqlazure/table.png)

