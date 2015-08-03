
Cette section explique comment installer SQL Server Express, activer TCP/IP, définir un port statique et créer une base de données que vous pouvez utiliser avec les connexions hybrides.

###Installation de SQL Server Express

Pour utiliser une base de données SQL Server ou SQL Server Express locale avec une connexion hybride, TCP/IP doit être activé sur un port statique. Les instances par défaut dans SQL Server utilisent le port statique 1433, mais pas les instances nommées. Pour cette raison, nous allons installer l’instance par défaut. Si vous avez déjà installé l’instance par défaut de SQL Server Express, vous pouvez ignorer cette section.

1. Pour installer SQL Server Express, exécutez le fichier **SQLEXPRWT_x64_ENU.exe** ou **SQLEXPR_x86_ENU.exe** que vous avez téléchargé. Le Centre d'installation SQL Server s'affiche.
	
2. Sélectionnez **Nouvelle installation autonome SQL Server ou ajout de fonctionnalités à une installation existante** et suivez les instructions en acceptant les valeurs et paramètres par défaut, jusqu’à ce que la page **Configuration de l’instance** s’affiche.
	
3. Sur la page **Configuration de l’instance**, sélectionnez **Instance par défaut**, puis acceptez les paramètres par défaut sur la page **Configuration du serveur**.

	>[AZURE.NOTE]Si vous avez déjà installé une instance par défaut de SQL Server, vous pouvez ignorer la section suivante et utiliser cette instance avec les connexions hybrides.
	
5. Sur la page **Configuration du moteur de base de données**, sous **Mode d’authentification**, sélectionnez **Mode mixte (authentification SQL Server et authentification Windows)** et indiquez un mot de passe sécurisé pour le compte Administrateur intégré **sa**.
	
	Dans ce didacticiel, vous allez utiliser l'authentification SQL Server. N'oubliez pas votre mot de passe, car vous en aurez besoin ultérieurement.
	
6. Suivez les autres étapes de l’Assistant pour terminer l’installation.

###Activation de TCP/IP et définition d’un port statique

Dans cette section, le Gestionnaire de configuration SQL Server, qui a été installé en même temps que SQL Server Express, est utilisé pour activer TCP/IP et définir une adresse IP statique.

1. Suivez la procédure décrite à la page [Activer le protocole réseau TCP/IP pour SQL Server](http://technet.microsoft.com/library/hh231672%28v=sql.110%29.aspx) pour activer l’accès TCP/IP à l’instance.

2. (Facultatif) Si vous n’êtes pas en mesure d’utiliser l’instance par défaut, vous devez suivre la procédure décrite à la page [Configurer un serveur pour écouter un port TCP spécifique](https://msdn.microsoft.com/library/ms177440.aspx) afin de définir un port statique pour l’instance. Si vous effectuez cette étape, vous devez vous connecter à l’aide du nouveau port que vous définissez et non du port 1433.

3. (Facultatif) Si nécessaire, ajoutez des exceptions au pare-feu pour autoriser l’accès à distance au processus SQL Server (sqlservr.exe).

###Création d’une nouvelle base de données dans l’instance SQL Server locale

1. Dans SQL Server Management Studio, connectez-vous à l'instance SQL Server que vous venez d'installer. (Si la boîte de dialogue **Se connecter au serveur** ne s’affiche pas automatiquement, accédez à l’**Explorateur d’objets** dans le volet gauche, cliquez sur **Se connecter**, puis sur **Moteur de base de données**.) 	

	![Se connecter au serveur](./media/hybrid-connections-create-on-premises-database/A04SSMSConnectToServer.png)
	
	Dans la zone **Type de serveur**, choisissez **Moteur de base de données**. Dans la zone **Nom du serveur**, saisissez **localhost** ou le nom de l’ordinateur sur lequel vous avez installé SQL Server. Sélectionnez **Authentification SQL Server**, puis indiquez le mot de passe que vous avez créé précédemment pour le compte de connexion sa.
	
2. Pour créer une base de données avec SQL Server Management Studio, cliquez avec le bouton droit sur **Bases de données** dans l'Explorateur d'objets, puis cliquez sur **Nouvelle base de données**.
	
3. Dans la boîte de dialogue **Nouvelle base de données**, tapez `OnPremisesDB`, puis cliquez sur **OK**.
	
4. Dans l’Explorateur d’objets, si vous développez **Bases de données**, vous pouvez constater que la base de données a été créée.

###Création d’un nouveau compte de connexion SQL Server et définition des autorisations

Pour terminer, vous allez créer un compte de connexion SQL Server avec des autorisations limitées. Votre service Azure se connectera à l’instance SQL Server locale à l’aide de ce compte de connexion et non du compte de connexion sa intégré, qui dispose des autorisations maximales sur le serveur.

1. Dans l’Explorateur d’objets SQL Server Management Studio, cliquez avec le bouton droit sur la base de données **OnPremisesDB**, puis cliquez sur **Nouvelle requête**.

2.  Copiez et collez la requête TSQL suivante dans la fenêtre de requête.

		USE [master]
		GO
		
		/* Replace the PASSWORD in the following statement with a secure password. 
		   If you save this script, make sure that you secure the file to 
		   securely maintain the password. */ 
		CREATE LOGIN [HybridConnectionLogin] WITH PASSWORD=N'<**secure_password**>', 
			DEFAULT_DATABASE=[OnPremisesDB], DEFAULT_LANGUAGE=[us_english], 
			CHECK_EXPIRATION=OFF, CHECK_POLICY=ON
		GO
	
		USE [OnPremisesDB]
		GO
	
		CREATE USER [HybridConnectionLogin] FOR LOGIN [HybridConnectionLogin] 
		WITH DEFAULT_SCHEMA=[dbo]
		GO

		GRANT CONNECT TO [HybridConnectionLogin]
		GRANT CREATE TABLE TO [HybridConnectionLogin]
		GRANT CREATE SCHEMA TO [HybridConnectionLogin]
		GO  
   
3. Dans le script ci-dessus, remplacez la chaîne `<**secure_password**>` par un mot de passe sécurisé pour le nouveau *HybridConnectionsLogin*.

4. **Exécutez** la requête pour créer un compte de connexion et accorder les autorisations requises dans la base de données locale.

<!---HONumber=July15_HO4-->