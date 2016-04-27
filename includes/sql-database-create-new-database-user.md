## Création d'un utilisateur de base de données à l'aide de SSMS

Les étapes ci-dessous supposent que vous utilisez SSMS, que vous êtes connecté à la base de données SQL dans l'Explorateur d'objets, et connecté au serveur logique de votre base de données SQL en tant qu’administrateur principal au niveau du serveur ou avec un compte d'utilisateur disposant des privilèges pour créer un utilisateur. En outre, les étapes ci-dessous supposent qu'il existe une base de données utilisateur dans laquelle vous souhaitez créer un compte d'utilisateur.

1. Dans l'Explorateur d'objets, développez le nœud Bases de données et sélectionnez la base de données dans laquelle vous souhaitez créer un compte utilisateur.

     ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Cliquez avec le bouton droit sur la base de données sélectionnée, puis cliquez sur **Requête**.

     ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. Dans la fenêtre de la requête, modifiez et utilisez l'instruction Transact-SQL suivante pour créer un utilisateur contenu dans votre base de données utilisateur.

    ```CREATE USER user1 WITH PASSWORD ='p@ssw0rd1';

     ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-3.png)

<!---HONumber=AcomDC_0420_2016-->