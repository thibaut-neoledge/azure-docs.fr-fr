1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le volet de gauche, cliquez sur **Nouveau**et sur **Bases de données**, puis sous **Azure Cosmos DB**, cliquez sur **Créer**.
   
   ![Volet Bases de données du Portail Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Dans le panneau **Nouveau compte**, indiquez la configuration souhaitée pour ce compte Azure Cosmos DB. 

    Grâce à Azure Cosmos DB, vous pouvez choisir l’un des quatre modèles de programmation : Gremlin (graphique), MongoDB, SQL (DocumentDB) et Table (clé-valeur). Chacun de ces modèles requiert actuellement un compte distinct.
       
    Dans le cadre de cet article de démarrage rapide, nous utilisons l’API Graph pour notre programmation. Vous devez donc choisir **Gremlin (graphique)** lors du remplissage du formulaire. Si vous disposez de données de document provenant d’une application du catalogue, de données clé/valeur (table) ou de données ayant fait l’objet d’une migration à partir d’une application MongoDB, notez qu’Azure Cosmos DB peut fournir une plateforme de service de base de données hautement disponible et distribuée dans le monde entier pour toutes vos applications essentielles.

    Renseignez les champs du panneau **Nouveau compte** en vous appuyant sur les informations de la capture d’écran ci-après. Vos valeurs peuvent différer de celles figurant dans la capture d’écran.
 
    ![Panneau de nouveau compte pour Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID|*Valeur unique*|Nom unique identifiant ce compte Azure Cosmos DB. Étant donné que *documents.azure.com* est ajouté à l’ID que vous fournissez pour créer votre URI, utilisez un ID unique mais identifiable. L’ID ne peut contenir que des lettres minuscules, des chiffres et le caractère trait d’union (-), et doit comporter entre 3 et 50 caractères.
    API|Gremlin (graphique)|Nous utilisons [l’API Graph](../articles/cosmos-db/graph-introduction.md) plus loin dans cet article.|
    Abonnement|*Votre abonnement*|Abonnement Azure que vous souhaitez utiliser pour ce compte Azure Cosmos DB. 
    Groupe de ressources|*La même valeur que l’ID*|Le nouveau nom de groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID. 
    Lieu|*La région la plus proche de vos utilisateurs*|La zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Choisissez l’emplacement le plus proche de vos utilisateurs afin de leur donner l’accès le plus rapide aux données.

4. Cliquez sur **Créer** pour créer le compte.
5. Dans la barre d’outils supérieure, cliquez sur l’icône **Notifications** ![Icône Notifications](./media/cosmos-db-create-dbaccount-graph/notification-icon.png) pour surveiller le processus de déploiement.

    ![Volet Notifications du portail Azure](./media/cosmos-db-create-dbaccount-graph/notification.png)

6.  Lorsque la fenêtre Notifications indique que le déploiement a réussi, fermez la fenêtre et ouvrez le nouveau compte à partir de la vignette **Toutes les ressources** du tableau de bord. 

    ![Compte DocumentDB sur la mosaïque Toutes les ressources](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)