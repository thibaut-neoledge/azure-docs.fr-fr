Vous pouvez désormais utiliser l’outil Explorateur de données dans le portail Azure pour créer une base de données et une collection. 

1. Dans le menu de navigation de gauche du portail Azure, cliquez sur **Explorateur de données** (version d’évaluation). 

2. Dans le panneau **Explorateur de données** (version d’évaluation), cliquez sur **Nouvelle collection**, puis fournissez les informations suivantes :

    ![Panneau Explorateur de données du Portail Azure](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID de base de données|Tâches|Nom de votre nouvelle base de données. Les noms de base de données doivent comporter entre 1 et 255 caractères, et ne peuvent pas contenir les caractères /, \\, # ou ?, ni d’espace de fin.
    ID de la collection|Éléments|Nom de votre nouvelle collection. Les noms de collection sont soumis aux mêmes exigences de caractères que les ID de base de données.
    Capacité de stockage| Fixe (10 Go)|Utilisez la valeur par défaut. Cette valeur correspond à la capacité de stockage de la base de données.
    Débit|400 RU|Utilisez la valeur par défaut. Si vous souhaitez réduire la latence, vous pourrez augmenter le débit par la suite.
    RU/m|Off|Conservez la valeur par défaut.
    Clé de partition|/category|Clé de partition qui distribue les données uniformément à chaque partition. Il est important de sélectionner la clé de partition correcte pour obtenir une collection performante. Pour plus d’informations, consultez la section [Conception du partitionnement](../articles/cosmos-db/partition-data.md#designing-for-partitioning).    
3. Une fois le formulaire rempli, cliquez sur **OK**.

L’Explorateur de données affiche la base de données et la collection que vous venez de créer. 