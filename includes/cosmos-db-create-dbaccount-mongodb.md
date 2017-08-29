1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le volet de gauche, cliquez sur **Nouveau** et sur **Bases de données**, puis sous **Azure Cosmos DB**, cliquez sur **Créer**.
   
   ![Capture d’écran du portail Azure, mettant en surbrillance l’option Plus de services, et Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. Dans le panneau **Nouveau compte**, indiquez la configuration souhaitée pour le compte Azure Cosmos DB. 

    Grâce à Azure Cosmos DB, vous pouvez choisir un des quatre modèles de programmation : Gremlin (graphique), MongoDB, SQL (DocumentDB) et Table (clé-valeur). 
       
    Dans ce guide de démarrage rapide, nous allons à nouveau programmer l’API MongoDB, vous choisirez donc **MongoDB** pendant que vous remplissez le formulaire. Cependant, si vous avez des données graphiques pour une application de réseau social, des données de document à partir d’une application du catalogue, ou des données clé/valeur (table), sachez qu’Azure Cosmos DB peut fournir une plate-forme de service de base de données hautement disponible et distribuée dans le monde entier pour toutes vos applications essentielles.

    Remplissez le panneau **Nouveau compte** à l’aide des informations dans la table en tant que guide.
 
    ![Capture d’écran du nouveau Panneau Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)
   
    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID|*Valeur unique*|Un nom unique que vous choisissez pour identifier le compte Azure Cosmos DB. *documents.azure.com* est ajouté à l’ID que vous fournissez pour créer votre URI ; aussi, utilisez un ID unique mais identifiable. Cet ID ne peut contenir que des minuscules, des chiffres et le caractère « - », et il doit comporter entre 3 et 50 caractères.
    API|MongoDB|Nous allons programmer [l’API MongoDB](../articles/documentdb/documentdb-protocol-mongodb.md) plus loin dans cet article.|
    Abonnement|*Votre abonnement*|L’abonnement Azure que vous souhaitez utiliser pour le compte Azure Cosmos DB. 
    Groupe de ressources|*La même valeur que l’ID*|Le nouveau nom de groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID. 
    Lieu|*La région la plus proche de vos utilisateurs*|La zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Choisissez l’emplacement le plus proche de vos utilisateurs afin de leur donner l’accès le plus rapide aux données.

4. Cliquez sur **Créer** pour créer le compte.
5. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

    ![Notification du début de déploiement](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  Lorsque le déploiement est terminé, ouvrez le nouveau compte à partir de la mosaïque Toutes les ressources. 

    ![Compte DocumentDB sur la mosaïque Toutes les ressources](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)
