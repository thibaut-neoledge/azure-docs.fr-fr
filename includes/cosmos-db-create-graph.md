Vous pouvez désormais utiliser l’outil Explorateur de données dans le portail Azure pour créer une base de données de graphiques. 

1. Dans le menu de navigation de gauche du portail Azure, cliquez sur **Explorateur de données** (version d’évaluation). 
2. Dans le panneau **Explorateur de données (préversion)**, cliquez sur **Nouveau graphique**, puis renseignez la page à l’aide des informations suivantes.

    ![Explorateur de données dans le portail Azure](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID de base de données|sample-database|L’ID de votre nouvelle base de données. Les noms de base de données doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?` ni d’espace de fin.
    ID de graphique|sample-graph|L’ID de votre nouveau graphique. Les noms de graphiques sont soumis aux mêmes exigences de nombre de caractères que les ID de bases de données.
    Capacité de stockage| 10 Go|Conservez la valeur par défaut. Il s’agit de la capacité de stockage de la base de données.
    Débit|400 unités de requête|Conservez la valeur par défaut. Vous pourrez augmenter le débit ultérieurement si vous souhaitez réduire la latence.
    RU/m|Off|Conservez la valeur par défaut. 
    Clé de partition|/userid|Clé de partition qui distribuera les données uniformément sur chaque partition. Il est important de sélectionner la clé de partition correcte pour obtenir un graphique performant. Pour plus d’informations sur ce point, consultez [Conception du partitionnement](../articles/cosmos-db/partition-data.md#designing-for-partitioning).

3. Une fois le formulaire rempli, cliquez sur **OK**.