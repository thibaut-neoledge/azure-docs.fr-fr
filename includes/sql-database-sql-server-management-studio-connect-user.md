## Connexion à une base de données SQL Azure en tant qu’utilisateur

Utilisez les étapes suivantes pour vous connecter à une base de données SQL Azure avec SSMS en tant qu'utilisateur.

1. Tapez « Microsoft SQL Server Management Studio » dans la zone de recherche de Windows, puis cliquez sur l’application pour ordinateur pour démarrer SSMS.

2. Dans la fenêtre Se connecter au serveur, entrez les valeurs suivantes :

- **Type de serveur** : la valeur par défaut est le moteur de base de données ; ne modifiez pas cette valeur.
 - **Nom du serveur** : entrez le nom du serveur qui héberge votre base de données SQL selon le format *&lt;nomserveur>*.**database.windows.net**
 - **Type d'authentification** : si vous débutez, sélectionnez l'authentification SQL. Si vous avez activé Active Directory pour votre serveur logique de base de données SQL, vous pouvez sélectionner l'authentification par mot de passe Active Directory ou l'authentification intégrée Active Directory.
 - **Nom d'utilisateur** : si vous avez sélectionné l'authentification SQL ou l'authentification par mot de passe Active Directory, entrez le nom d'un utilisateur ayant accès à une base de données sur le serveur.
 - **Mot de passe** : si vous avez sélectionné l'authentification SQL ou l'authentification par mot de passe Active Directory, entrez le mot de passe pour l'utilisateur spécifié.
   
       ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-1.png)

3. Cliquez sur **Options** pour spécifier la base de données à laquelle vous souhaitez vous connecter.

      ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-2.png)
 
4. Dans **Se connecter à la base de données**, sélectionnez la base de données à laquelle vous souhaitez vous connecter.

     ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-3.png)

5. Cliquez sur **Connecter**.
 
6. Si l’adresse IP de votre client n'a pas accès au serveur logique de la base de données SQL, vous devrez vous connecter à un compte Azure et créer une règle de pare-feu au niveau du serveur. Si vous êtes un administrateur d'abonnement Azure, cliquez sur **Se connecter** pour créer une règle de pare-feu au niveau du serveur. Sinon, demandez à un administrateur de créer une règle de pare-feu au niveau du serveur ou une règle de pare-feu au niveau de la base de données pour la base de données à laquelle vous essayez de vous connecter.
 
      ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-4.png)
 
7. Si vos informations d'identification vous autorisent à accéder à la base de données, l’Explorateur d'objets s'ouvre et vous pouvez désormais effectuer des tâches administratives ou interroger des données, en fonction des autorisations de l'utilisateur.
  
      ![SQL Server Management Studio : se connecter à un serveur de base de données SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-5.png)
      
 
 ## Dépannage des échecs de connexion

Les échecs de connexion les plus courants sont dus à des erreurs dans le nom du serveur (rappelez-vous que <*nom\_serveur*> est le nom du serveur logique et non celui de la base de données), dans le nom d’utilisateur ou dans le mot de passe, ou quand le serveur n’autorise pas les connexions pour des raisons de sécurité.

<!---HONumber=AcomDC_0420_2016-->