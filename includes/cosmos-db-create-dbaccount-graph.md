1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le volet de gauche, cliquez sur **Nouveau**, sur **Bases de données**, puis sur **Azure Cosmos DB**.
   
   ![Volet Bases de données du Portail Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. Dans le panneau **Nouveau compte**, indiquez la configuration souhaitée pour le compte Azure Cosmos DB. 

    Grâce à Azure Cosmos DB, vous pouvez choisir un des quatre modèles de programmation : Gremlin (graphique), MongoDB, SQL (DocumentDB) et Table (clé-valeur).  
       
    Dans le cadre de cet article de démarrage rapide, nous utilisons l’API Graph pour notre programmation. Vous devez donc choisir **Gremlin (graphique)** lors du remplissage du formulaire. Si vous disposez de données de document provenant d’une application du catalogue, de données clé/valeur (table) ou de données ayant fait l’objet d’une migration à partir d’une application MongoDB, notez qu’Azure Cosmos DB peut fournir une plateforme de service de base de données hautement disponible et distribuée dans le monde entier pour toutes vos applications essentielles.

    Dans le panneau **Nouveau compte**, renseignez les différents champs en vous appuyant sur les informations de la capture d’écran suivante. Étant donné que vos valeurs ne correspondront pas à celles de la capture d’écran, veillez à choisir des valeurs uniques lorsque vous configurez votre compte. 
 
    ![Panneau Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-2.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID|*Valeur unique*|Nom unique que vous choisissez pour identifier le compte Azure Cosmos DB. Étant donné que *documents.azure.com* est ajouté à l’ID que vous fournissez pour créer votre URI, utilisez un ID unique mais identifiable. L’ID ne peut contenir que des lettres minuscules, des chiffres et le caractère trait d’union (-), et doit comporter entre 3 et 50 caractères.
    API|Gremlin (graphique)|Nous utilisons [l’API Graph](../articles/cosmos-db/graph-introduction.md) plus loin dans cet article.|
    Abonnement|*Votre abonnement*|L’abonnement Azure que vous souhaitez utiliser pour le compte Azure Cosmos DB. 
    Groupe de ressources|*La même valeur que l’ID*|Le nouveau nom de groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID. 
    Lieu|*La région la plus proche de vos utilisateurs*|La zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Choisissez l’emplacement le plus proche de vos utilisateurs afin de leur donner l’accès le plus rapide aux données.

4. Cliquez sur **Créer** pour créer le compte.
5. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

    ![Notification du début de déploiement](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Une fois le déploiement terminé, ouvrez le nouveau compte à partir de la vignette **Toutes les ressources**. 

    ![Compte DocumentDB sur la mosaïque Toutes les ressources](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-all-resources.png)