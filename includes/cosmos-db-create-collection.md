Vous pouvez maintenant utiliser l’Explorateur de données pour créer une collection et ajouter des données à votre base de données. 

1. Dans le portail Azure, dans le menu de navigation, cliquez sur **Explorateur de données**. 
2. Dans le panneau Explorateur de données, cliquez sur **Nouvelle collection**, puis renseignez la page à l’aide des informations suivantes.

    ![Explorateur de données dans le portail Azure](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID de base de données|Éléments|L’ID de votre nouvelle base de données. Les noms de base de données doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?` ni d’espace de fin.
    ID de la collection|ToDoList|L’ID de votre nouvelle collection. Les noms de collection sont soumis aux mêmes exigences de nombre de caractères que les ID de base de données.
    Capacité de stockage| Fixe (10 Go)|Conservez la valeur par défaut. Il s’agit de la capacité de stockage de la base de données.
    Débit|400 unités de requête|Conservez la valeur par défaut. Vous pourrez augmenter le débit ultérieurement si vous souhaitez réduire la latence.
    Clé de partition|/userid|Clé de partition qui distribuera les données uniformément sur chaque partition. Il est important de sélectionner la clé de partition correcte pour obtenir une collection performante. Pour plus d’informations sur ce point, consultez [Conception du partitionnement](../articles/cosmos-db/partition-data.md#designing-for-partitioning).    



3. Une fois le formulaire rempli, cliquez sur **OK**.