1. Dans une nouvelle fenêtre, connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le volet de gauche, cliquez sur **Nouveau**, sur **Bases de données**, puis sur **Azure Cosmos DB**.
   
   ![Volet Bases de données du portail Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Dans le panneau **Nouveau compte**, indiquez la configuration souhaitée pour le compte Azure Cosmos DB. 

    Grâce à Azure Cosmos DB, vous pouvez choisir un des quatre modèles de programmation : Gremlin (graphique), MongoDB, SQL (DocumentDB) et Table (clé-valeur). 
    
    Dans ce guide de démarrage rapide, vous utilisons l’API Document DB pour la programmation. De ce fait, vous devez indiquer **SQL (DocumentDB)** dans le formulaire. Toutefois, si vous disposez de données graphiques pour une application de réseau social, des données clé/valeur (table) ou des données migrées depuis une application MongoDB, sachez qu’Azure Cosmos DB peut fournir une plate-forme de service de base de données hautement disponible et distribuée dans le monde entier pour toutes vos applications stratégiques.

    Remplissez les champs du panneau **Nouveau compte** en vous appuyant sur les informations de la capture d’écran suivante. Lorsque vous configurez votre compte, choisissez des valeurs uniques, qui ne correspondent pas à celles qui figurent dans la capture d’écran. 
 
    ![Nouveau panneau Azure Cosmos DB](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID|*Valeur unique*|Nom unique permettant d’identifier votre compte Azure Cosmos DB. La chaîne *documents.azure.com* est ajoutée à l’ID que vous fournissez pour créer votre URI ; aussi, utilisez un ID unique, mais identifiable. L’ID peut contenir uniquement des lettres minuscules, des chiffres et le caractère de trait d’union (-). Il doit inclure entre 3 et 50 caractères.
    API|SQL (DocumentDB)|Nous allons utiliser [l’API DocumentDB](../articles/documentdb/documentdb-introduction.md) pour notre programmation un peu plus loin dans cet article.|
    Abonnement|*Votre abonnement*|L’abonnement Azure que vous souhaitez utiliser pour votre compte Azure Cosmos DB. 
    Groupe de ressources|*La même valeur que l’ID*|Nouveau nom du groupe de ressources pour votre compte. Pour plus de simplicité, vous pouvez utiliser le même nom que votre ID. 
    Lieu|*La région la plus proche de vos utilisateurs*|La zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Choisissez l’emplacement le plus proche de vos utilisateurs, afin de permettre d’accéder aussi rapidement que possible aux données.
4. Cliquez sur **Créer** pour créer le compte.
5. Dans la barre d’outils supérieure, cliquez sur **Notifications** pour surveiller le processus de déploiement.

    ![Volet Notifications du portail Azure](./media/cosmos-db-create-dbaccount-graph/azure-documentdb-nosql-notification.png)

6.  Lorsque le déploiement est terminé, ouvrez le nouveau compte à partir de la mosaïque **Toutes les ressources**. 

    ![Compte DocumentDB apparaissant sur la mosaïque Toutes les ressources](./media/cosmos-db-create-dbaccount/all-resources.png)
 
