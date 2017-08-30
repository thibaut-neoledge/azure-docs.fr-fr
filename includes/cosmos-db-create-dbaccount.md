1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le volet de gauche, cliquez sur **Nouveau**, sur **Bases de données**, puis sous **Azure Cosmos DB**, cliquez sur **Créer**.
   
   ![Volet Bases de données du portail Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Dans le panneau **Nouveau compte**, indiquez la configuration souhaitée pour ce compte Azure Cosmos DB. 

    Grâce à Azure Cosmos DB, vous pouvez choisir l’un des quatre modèles de programmation : Gremlin (graphique), MongoDB, SQL (DocumentDB) et Table (clé-valeur). Chacun de ces modèles requiert actuellement un compte distinct.
    
    Dans ce guide de démarrage rapide, vous utilisons l’API Document DB pour la programmation. De ce fait, vous devez indiquer **SQL (DocumentDB)** dans le formulaire. Si vous disposez de données graphiques pour une application de réseau social, de données clé/valeur (table) ou de données ayant fait l’objet d’une migration à partir d’une application MongoDB, notez qu’Azure Cosmos DB peut fournir une plateforme de service de base de données hautement disponible et distribuée dans le monde entier pour toutes vos applications stratégiques.

    Renseignez les champs du panneau **Nouveau compte** en vous appuyant sur les informations de la capture d’écran ci-après. Vos valeurs peuvent différer de celles figurant dans la capture d’écran.
 
    ![Panneau de nouveau compte pour Azure Cosmos DB](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID|*Valeur unique*|Nom unique identifiant ce compte Azure Cosmos DB. Étant donné que *documents.azure.com* est ajouté à l’ID que vous fournissez pour créer votre URI, utilisez un ID unique mais identifiable. L’ID ne peut contenir que des lettres minuscules, des chiffres et le caractère de trait d’union (-), et doit comporter entre 3 et 50 caractères.
    API|SQL (DocumentDB)|Nous allons utiliser [l’API DocumentDB](../articles/documentdb/documentdb-introduction.md) pour notre programmation un peu plus loin dans cet article.|
    Abonnement|*Votre abonnement*|Abonnement Azure que vous souhaitez utiliser pour ce compte Azure Cosmos DB. 
    Groupe de ressources|*La même valeur que l’ID*|Nouveau nom du groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID. 
    Lieu|*La région la plus proche de vos utilisateurs*|La zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Choisissez l’emplacement le plus proche de vos utilisateurs, afin de permettre d’accéder aussi rapidement que possible aux données.
4. Cliquez sur **Créer** pour créer le compte.
5. Dans la barre d’outils supérieure, cliquez sur l’icône **Notifications** ![Icône Notifications](./media/cosmos-db-create-dbaccount/notification-icon.png) pour surveiller le processus de déploiement.

    ![Volet Notifications du portail Azure](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Lorsque la fenêtre Notifications indique que le déploiement a réussi, fermez la fenêtre et ouvrez le nouveau compte à partir de la vignette **Toutes les ressources** du tableau de bord. 

    ![Compte DocumentDB apparaissant sur la mosaïque Toutes les ressources](./media/cosmos-db-create-dbaccount/all-resources.png)
 
