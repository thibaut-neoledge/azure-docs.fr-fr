Vous pouvez maintenant utiliser l’Explorateur de données pour créer une table et ajouter des données à votre base de données. 

1. Dans le portail Azure, dans le menu de navigation, cliquez sur **Explorateur de données** (version d’évaluation). 
2. Dans le panneau Explorateur de données, cliquez sur **Nouvelle table**, puis renseignez la page à l’aide des informations suivantes.

    ![Explorateur de données dans le portail Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

    Paramètre|Valeur suggérée|Description
    ---|---|---
    ID de la table|sample-database|L’ID de votre nouvelle table. Les noms de tables sont soumis aux mêmes exigences de nombre de caractères que les ID de bases de données. Les noms de base de données doivent inclure entre 1 et 255 caractères et ne peuvent pas contenir `/ \ # ?` ni d’espace de fin.
    Capacité de stockage| 10 Go|Conservez la valeur par défaut. Il s’agit de la capacité de stockage de la base de données.
    Débit|400 unités de requête|Conservez la valeur par défaut. Vous pourrez augmenter le [débit](../articles/cosmos-db/request-units.md) ultérieurement si vous souhaitez réduire la latence.
    RU/m|Off|Utilisez la valeur par défaut. Vous pouvez activer la fonctionnalité [RU/m](../articles/cosmos-db/request-units-per-minute.md) ultérieurement si vous avez besoin de gérer des charges de travail imprévisibles.

3. Une fois le formulaire rempli, cliquez sur **OK**.