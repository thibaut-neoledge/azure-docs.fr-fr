Les étapes suivantes montrent comment se connecter à l’instance SQL Server sur Internet à l’aide de SQL Server Management Studio (SSMS). Toutefois, les mêmes étapes s’appliquent pour rendre votre machine virtuelle SQL Server accessible pour vos applications exécutées en local et dans Azure.

Avant de pouvoir vous connecter à l’instance de SQL Server à partir d’une autre machine virtuelle ou d’Internet, vous devez effectuer les tâches suivantes, comme indiqué dans les sections ci-dessous :

- [Créer un point de terminaison TCP pour la machine virtuelle](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Ouvrir des ports TCP dans le pare-feu Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurer SQL Server pour l’écoute du protocole TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurer SQL Server pour l’authentification en mode mixte](#configure-sql-server-for-mixed-mode-authentication)
- [Créer des connexions d’authentification SQL Server](#create-sql-server-authentication-logins)
- [Déterminer le nom DNS de la machine virtuelle](#determine-the-dns-name-of-the-virtual-machine)
- [Se connecter au moteur de base de données à partir d’un autre ordinateur](#connect-to-the-database-engine-from-another-computer)

Le chemin de connexion est résumé dans le schéma suivant :

![Connexion à une machine virtuelle SQL Server](./media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

### Création d'un point de terminaison TCP pour la machine virtuelle

Pour accéder à SQL Server depuis Internet, la machine virtuelle doit avoir un point de terminaison pour écouter les communications TCP entrantes. Dans cette étape de configuration Azure, le trafic du port TCP entrant est dirigé vers un port TCP accessible à la machine virtuelle.

>[AZURE.NOTE]Si vous vous connectez dans le même service cloud ou réseau virtuel, vous n’avez pas besoin de créer un point de terminaison accessible publiquement. Dans ce cas, vous pouvez passer à l’étape suivante. Pour plus d'informations, consultez [Scénarios de connexion](../articles/virtual-machines/virtual-machines-sql-server-connectivity.md#connection-scenarios).

1. Dans le portail de gestion Azure, cliquez sur **VIRTUAL MACHINES**.
	
2. Cliquez sur la machine virtuelle que vous venez de créer. Les informations relatives à la machine virtuelle sont affichées.
	
3. En haut de la page, sélectionnez la page **POINTS DE TERMINAISON**, puis en bas de la page, cliquez sur **AJOUTER**.
	
4. Dans la page **Ajouter un point de terminaison à la machine virtuelle**, cliquez sur **Ajouter un point de terminaison autonome**, puis sur la flèche Suivant.
	
5. Sur la page **Specify the details of the endpoint**, entrez les informations suivantes.

	- Dans la zone **NAME**, entrez un nom pour le point de terminaison.
	- Dans la zone **PROTOCOL**, sélectionnez **TCP**. Vous pouvez taper la valeur **57500** dans la zone **PORT PUBLIC**. De la même façon, vous pouvez indiquer le port d’écoute par défaut de SQL Server **1433** dans la zone **Port privé**. Notez que plusieurs organisations sélectionnent différents numéros de ports pour éviter les attaques de sécurité. 

6. Cliquez sur la coche pour continuer. Le point de terminaison est créé.

### Ouverture de ports TCP dans le pare-feu Windows pour l'instance par défaut du moteur de base de données

1. Connectez-vous à la machine virtuelle via le Bureau à distance Windows. Une fois connecté, sur l’écran d’accueil, tapez **WF.msc**, puis appuyez sur ENTRÉE. 

	![Démarrer le programme du pare-feu](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
2. Dans le **Pare-feu Windows avec fonctions avancées de sécurité**, dans le volet gauche, cliquez avec le bouton droit sur **Règles de trafic entrant**, puis sur **Nouvelle règle** dans le volet d'action.

	![Nouvelle règle](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)

3. Dans la boîte de dialogue de l’**Assistant Nouvelle règle de trafic entrant**, sous **Type de règle**, sélectionnez **Port**, puis cliquez sur **Suivant**.

4. Dans la boîte de dialogue **Protocole et ports**, utilisez le paramètre par défaut **TCP**. Dans la boîte de dialogue **Ports locaux spécifiques**, entrez le numéro de port de l’instance du moteur de base de données (**1433** pour l’instance par défaut, ou le numéro que vous avez choisi pour le port privé à l’étape du point de terminaison).

	![Port TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)

5. Cliquez sur **Suivant**.

6. Dans la boîte de dialogue **Action**, sélectionnez **Autoriser la connexion**, puis cliquez sur **Suivant**.

	**Remarque relative à la sécurité** : la sélection de l’option **Autoriser la connexion si elle est sécurisée** peut renforcer la sécurité. Sélectionnez cette option si vous voulez configurer des options de sécurité supplémentaires dans votre environnement.

	![Autoriser les connexions](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)

7. Dans la boîte de dialogue **Profil**, sélectionnez **Public**, **Privé** et **Domaine**. Cliquez ensuite sur **Suivant**.

    **Remarque relative à la sécurité** : la sélection de l’option **Public** autorise l’accès via Internet. Lorsque cela est possible, sélectionnez un profil plus restrictif.

	![Profil public](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)

8. Dans la boîte de dialogue **Nom**, entrez un nom et une description pour cette règle, puis cliquez sur **Terminer**.

	![Nom de la règle](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Le cas échéant, ouvrez des ports supplémentaires pour les autres composants. Pour plus d'informations, consultez la page [Configurer le Pare-feu Windows pour autoriser l'accès à SQL Server](http://msdn.microsoft.com/library/cc646023.aspx).


### Configuration de SQL Server pour écouter le protocole TCP

1. Lorsque vous êtes connecté à la machine virtuelle, sur la page de démarrage, tapez **Gestionnaire de configuration SQL Server** et appuyez sur ENTRÉE.
	
	![Ouvrir le gestionnaire de configuration SQL Server](./media/virtual-machines-sql-server-connection-steps/9Click-SSCM.png)

2. Dans le Gestionnaire de configuration SQL Server, dans le volet de la console, développez **Configuration du réseau SQL Server**.

3. Dans le volet de la console, cliquez sur **Protocoles pour MSSQLSERVER** (le nom d’instance par défaut). Dans le volet d’informations, cliquez avec le bouton droit sur TCP qui doit être activé pour les images de la galerie par défaut. Pour vos images personnalisées, cliquez sur **Activer** (si l'état est Désactivé).

	![Activer TCP](./media/virtual-machines-sql-server-connection-steps/10Enable-TCP.png)

5. Dans le volet de la console, cliquez sur **Services SQL Server** Dans le volet d'informations, cliquez avec le bouton droit sur **SQL Server (_nom de l'instance_)** (l'instance par défaut est **SQL Server (MSSQLSERVER)**), puis cliquez sur **Redémarrer**, pour arrêter et redémarrer l'instance de SQL Server.

	![Redémarrer le moteur de base de données](./media/virtual-machines-sql-server-connection-steps/11Restart.png)

7. Fermez le Gestionnaire de configuration de SQL Server.

Pour plus d'informations sur l'activation des protocoles pour le moteur de base de données SQL Server, consultez la page [Activer ou désactiver un protocole réseau de serveur](http://msdn.microsoft.com/library/ms191294.aspx).

### Configuration de SQL Server pour l'authentification en mode mixte

Le moteur de base de données de SQL Server ne peut pas utiliser l'authentification Windows sans un environnement de domaine. Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, configurez SQL Server pour l'authentification en mode mixte qui permet l’authentification SQL Server et l’authentification Windows

>[AZURE.NOTE](il n’est pas nécessaire de configurer l’authentification en mode mixte si vous avez configuré un réseau virtuel Azure avec un environnement de domaine configuré).

1. Lorsque vous êtes connecté à la machine virtuelle, sur la page de démarrage, tapez **SQL Server 2014 Management Studio** et cliquez sur l’icône sélectionnée.

	![Démarrer SSMS](./media/virtual-machines-sql-server-connection-steps/18Start-SSMS.png)

	Lorsque vous ouvrez Management Studio pour la première fois, il doit créer l’environnement Management Studio pour les utilisateurs. Cette opération peut prendre du temps.

2. Management Studio affiche la boîte de dialogue **Se connecter au serveur**. Dans la zone **Nom du serveur**, entrez le nom de la machine virtuelle à connecter au moteur de base de données avec Object Explorer (au lieu d'entrer le nom de la machine virtuelle, vous pouvez également utiliser **(local)** ou un simple point pour le **nom du serveur**). Sélectionnez **Authentification Windows** et laissez **_your\_VM\_name_\\your\_local\_administrator** dans la zone **Nom d'utilisateur**. Cliquez sur **Connecter**.

	![Se connecter au serveur](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)

3. Dans SQL Server Management Studio Object Explorer, cliquez avec le bouton droit sur le nom de l'instance de SQL Server (le nom de la machine virtuelle), puis cliquez sur **Propriétés**.

	![Propriétés de serveur](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)

4. Sur la page **Sécurité**, sous **Authentification du serveur**, sélectionnez **Mode d'authentification SQL Server et Windows**, puis cliquez sur **OK**.

	![Sélectionner le mode d'authentification](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)

5. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **OK** pour confirmer l'obligation de redémarrer SQL Server.

6. Dans Object Explorer, cliquez avec le bouton droit sur votre serveur, puis cliquez sur **Redémarrer** (si SQL Server Agent est exécuté, il doit également être redémarré).

	![Redémarrer](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)

7. Dans la boîte de dialogue SQL Server Management Studio, cliquez sur **Oui** pour confirmer que vous voulez redémarrer SQL Server.

### Création de connexions d'authentification SQL Server

Pour vous connecter au moteur de base de données à partir d'un autre ordinateur, vous devez créer au moins une connexion d'authentification SQL Server.

1. Dans SQL Server Management Studio Object Explorer, développez le dossier de l'instance de serveur dans laquelle vous voulez créer la connexion.

2. Cliquez avec le bouton droit sur le dossier **Sécurité**, pointez sur **Nouveau**, puis sélectionnez **Connexion...**.

	![Nouvelle connexion](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)

3. Dans la boîte de dialogue **Nouvelle connexion**, sur la page **Général**, entrez le nom du nouvel utilisateur dans la zone **Nom de connexion**.

4. Sélectionnez **Authentification SQL Server**.

5. Dans la zone **Mot de passe**, entrez un mot de passe pour le nouvel utilisateur. Entrez de nouveau ce mot de passe dans la zone **Confirmer le mot de passe**.

6. Pour renforcer la complexité et la sécurité des options de stratégie de mot de passe, sélectionnez **Conserver la stratégie de mot de passe** (recommandé). Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

7. Pour conserver les options de stratégie de mot de passe après expiration, sélectionnez **Conserver l'expiration du mot de passe** (recommandé). La stratégie de conservation de mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

8. Pour forcer l'utilisateur à créer un mot de passe après sa première connexion, sélectionnez **L'utilisateur doit changer de mot de passe à la prochaine connexion** (recommandé si cette connexion est destinée à une autre personne. Si la connexion est destinée à votre propre usage, ne sélectionnez pas cette option). L'option Conserver l'expiration du mot de passe doit être sélectionnée pour activer cette case à cocher. Il s'agit d'une option par défaut lorsque l'authentification SQL Server est sélectionnée.

9. Dans la liste **Base de données par défaut**, sélectionnez une base de données par défaut pour la connexion. Par défaut, cette option est réglée sur **principale**. Si vous n'avez pas encore créé de base de données d'utilisateur, gardez la valeur par défaut **master**.

10. Dans la liste **Langue par défaut**, conservez la valeur **par défaut**.
    
	![Propriétés de connexion](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)

11. S’il s’agit de votre première connexion, vous pouvez désigner cette connexion en tant qu’administrateur SQL Server. Si cela est le cas, sur la page **Rôles du serveur**, activez la case à cocher **administrateur système**.

	**Remarque relative à la sécurité** : les membres du rôle serveur fixe administrateur système contrôlent complètement le moteur de base de données. Vous devez limiter soigneusement l'appartenance à ce rôle.

	![administrateur système](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)

12. Cliquez sur OK.

Pour plus d'informations sur les connexions SQL Server, consultez la page [Créer un compte de connexion](http://msdn.microsoft.com/library/aa337562.aspx).

### Détermination du nom DNS de la machine virtuelle

Pour vous connecter au moteur de base de données SQL Server à partir d'un autre ordinateur, vous devez connaître le nom DNS de la machine virtuelle. Il s'agit du nom utilisé par Internet pour identifier une machine virtuelle. Vous pouvez utiliser l'adresse IP, mais celle-ci peut être modifiée lorsqu'Azure déplace des ressources pour des raisons de redondance ou de maintenance. Le nom DNS reste stable, car il peut être redirigé vers une nouvelle adresse IP.

1. Dans le portail de gestion Azure (ou à partir de l'étape précédente), sélectionnez **VIRTUAL MACHINES**. 

2. Sur la page **INSTANCES DE MACHINE VIRTUELLE**, dans la colonne **Aperçu rapide**, recherchez et copiez le nom DNS de la machine virtuelle.

	![Nom DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)
	

### Connexion au moteur de base de données à partir d'un autre ordinateur
 
1. Sur un ordinateur connecté à Internet, ouvrez SQL Server Management Studio.
2. Dans la boîte de dialogue **Se connecter au serveur** ou **Se connecter au moteur de base de données**, dans la zone **Nom du serveur**, entrez le nom DNS de la machine virtuelle (déterminé pendant la tâche précédente) et le numéro d’un port de point de terminaison public au format *NomDNS,NuméroPort*, par exemple **tutorialtestVM.cloudapp.net,57500**. Pour obtenir le numéro de port, connectez-vous au portail de gestion Azure et cherchez la machine virtuelle. Dans le tableau de bord, cliquez sur **POINTS DE TERMINAISON** et utilisez le **PORT PUBLIC** affecté à **MSSQL**. ![Port public](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Dans la zone **Authentification**, sélectionnez **Authentification SQL Server**.
5. Dans la zone **Connexion**, entrez le nom d'une connexion créée lors d'une tâche précédente.
6. Dans la zone **Mot de passe**, entrez le mot de passe de connexion que vous avez créé lors d'une tâche précédente.
7. Cliquez sur **Connecter**.

	![Connexion en utilisant SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)

<!---HONumber=Oct15_HO3-->