## Création d'un utilisateur de base de données à l'aide de SSMS

Les étapes ci-dessous supposent que vous utilisez SSMS, que vous êtes connecté à la base de données SQL dans l'Explorateur d'objets, et connecté au serveur logique de votre base de données SQL en tant qu’administrateur principal au niveau du serveur ou avec un compte d'utilisateur disposant des privilèges pour accorder des autorisations aux utilisateurs. En outre, les étapes ci-dessous supposent qu'un utilisateur existe dans la base de données à laquelle vous souhaitez accorder des autorisations dbo.

1. Dans l'Explorateur d'objets, développez le nœud Bases de données et sélectionnez la base de données avec l'utilisateur auquel vous souhaitez accorder des autorisations dbo.

     ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Cliquez avec le bouton droit sur la base de données sélectionnée, puis cliquez sur **Requête**.

     ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. Dans la fenêtre de la requête, modifiez et utilisez l'instruction Transact-SQL suivante pour accorder des autorisations dbo à un utilisateur spécifié.

    '''ALTER ROLE db\_owner ADD MEMBER user1;

     ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!---HONumber=AcomDC_0420_2016-->