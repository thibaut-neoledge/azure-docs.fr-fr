<a id="log-in-to-the-azure-portal" class="xliff"></a>

## Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

<a id="create-a-blank-sql-database-using-the-azure-portal" class="xliff"></a>

## Créer une base de données SQL vide grâce au portail Azure

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](../articles/sql-database/sql-database-service-tiers.md). La base de données est créée dans un [groupe de ressources Azure](../articles/azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](../articles/sql-database/sql-database-features.md). 

Pour créer une base de données SQL vide, suivez la procédure suivante. 

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données SQL** dans la page **Bases de données**. 

   ![créer une base de données vide](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

3. Remplissez le formulaire de base de données SQL avec les informations suivantes, comme indiqué dans l’illustration précédente :   

   | Paramètre | Valeur suggérée | Description |
   | --------| --------------- | ----------- | 
   | **Nom de la base de données** | mySampleDatabase | Pour les noms de base de données valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données). | 
   | **Abonnement** | Votre abonnement  | Pour plus d’informations sur vos abonnements, consultez [Abonnements](https://account.windowsazure.com/Subscriptions). |
   | **Groupe de ressources** | myResourceGroup | Pour les noms de groupe de ressources valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). |
   | **Sélectionner une source** | Base de données vide | Indique qu’une base de données vide doit être créée. |
   ||||

4. Cliquez sur **Serveur** pour créer et configurer un serveur pour votre nouvelle base de données. Remplissez le **formulaire de nouveau serveur** avec les informations suivantes : 

   | Paramètre | Valeur suggérée | Description |
   | --------| --------------- | ----------- | 
   | **Nom du serveur** | Nom globalement unique. | Pour les noms de serveur valides, consultez [Naming conventions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Conventions d’affectation de nom). | 
   | **Connexion d’administrateur du serveur** | Nom valide. | Pour les noms de connexion valides, consultez [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificateurs de base de données).|
   | **Mot de passe** | Mot de passe valide. | Votre mot de passe doit comporter au moins 8 caractères et contenir des caractères appartenant à trois des catégories suivantes : majuscules, minuscules, chiffres et caractères non alphanumériques. |
   | **Emplacement** | Emplacement valide. | Pour plus d’informations sur les régions, consultez [Régions Azure](https://azure.microsoft.com/regions/). |
   ||||

   ![create database-server](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

5. Cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances pour votre nouvelle base de données. Pour ce didacticiel, sélectionnez **20 DTU** et **250** Go de stockage.

   ![create database-s1](../articles/sql-database/media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Cliquez sur **Apply**.  

8. Sélectionnez un **classement** pour la base de données vide (pour ce didacticiel, utilisez la valeur par défaut). Pour en savoir plus sur les classements, voir [Classements](https://docs.microsoft.com/sql/t-sql/statements/collations)

9. Cliquez sur **Créer** pour approvisionner la base de données. L’approvisionnement prend environ une minute et demie. 

10. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

   ![notification](../articles/sql-database/media/sql-database-get-started-portal/notification.png)

<a id="create-a-server-level-firewall-rule-using-the-azure-portal" class="xliff"></a>

## Créer une règle de pare-feu au niveau du serveur à l’aide du portail Azure

Le service SQL Database crée un pare-feu au niveau du serveur. Initialement, le pare-feu empêche les outils externes et les applications de se connecter au serveur ou à n’importe quelles bases de données sur le serveur. Les connexions sont autorisées après qu’une règle de pare-feu a été créée pour ouvrir des adresses IP spécifiques. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur SQL Database](../articles/sql-database/sql-database-firewall-configure.md) pour l’adresse IP de votre client, et pour permettre la connectivité externe via le pare-feu SQL Database pour votre adresse IP uniquement. 


> [!NOTE]
> Azure SQL Database communique par le biais du port 1433. Vous pouvez vous connecter à SQL Database uniquement après que le pare-feu de votre réseau a autorisé le trafic sortant via le port 1433.


1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche, puis cliquez sur **mySampleDatabase** sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170313.database.windows.net**) et fournit des options pour poursuivre la configuration. Copiez ce nom de serveur complet pour une utilisation ultérieure.

   > [!IMPORTANT]
   > Vous avez besoin du nom complet du serveur pour vous connecter à votre serveur et à ses bases de données dans les guides de démarrage rapide suivants.
   > 

   ![nom du serveur](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 

2. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré sur l’image précédente. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

   ![règle de pare-feu de serveur](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png) 


3. Dans la barre d’outils, cliquez sur **Ajouter une adresse IP cliente** afin d’ajouter votre adresse IP actuelle à une nouvelle règle de pare-feu. Une règle de pare-feu peut ouvrir le port 1433 pour une seule adresse IP ou une plage d’adresses IP.

4. Cliquez sur **Save**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle et ouvre le port 1433 sur le serveur logique.

   ![définir la règle de pare-feu de serveur](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Cliquez sur **OK**, puis fermez la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter au serveur Azure SQL Database et à ses bases de données à l’aide d’un outil tel que SQL Server Management Studio (SSMS). La connexion s’effectue à partir de cette adresse IP. Elle utilise le compte Administrateur du serveur créé précédemment.


> [!IMPORTANT]
> Par défaut, l’accès via le pare-feu SQL Database est activé pour tous les services Azure. Cliquez sur **ÉTEINT** sur cette page pour le désactiver pour tous les services Azure.


<a id="get-connection-string-values-using-the-azure-portal" class="xliff"></a>

## Obtenir la connexion des valeurs de chaîne à l’aide du portail Azure

Obtenez le nom de serveur complet de votre serveur Azure SQL Database dans le portail Azure. Utilisez le nom de serveur complet pour vous connecter à votre serveur avec SQL Server Management Studio.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 

3. Dans le volet **Essentials** de la page du portail Azure pour votre base de données, recherchez et copiez le **nom du serveur**.

   ![informations de connexion](../articles/sql-database/media/sql-database-get-started-portal/server-name.png) 
