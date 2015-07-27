#Création d’une base de données MongoDB dans Azure

Cet article vous montre comment utiliser [MongoLab] pour créer une base de données MongoDB à partir d’[Azure Marketplace]. [MongoLab] est un fournisseur de services MongoDB-as-a-service permettant d’exécuter et de gérer des bases de données MongoDB dans les centres de données Azure et de s’y connecter à partir d’une application.

Pour créer une base de données MongoDB à partir de l’[Azure Store], procédez comme suit :

1. Connectez-vous au [portail de gestion Azure][portal].
2. Cliquez sur **+NEW** en bas de la page, puis sélectionnez **STORE**.

	![Sélectionner un module complémentaire dans le marketplace](./media/create-mongolab-mongodb/select-store.png)

3. Sélectionnez **MongoLab**, puis cliquez sur la flèche en bas du cadre.

	![Sélectionner MongoLab](./media/create-mongolab-mongodb/select-mongo-db.png)

4. Indiquez le nom de la base de données, sélectionnez une région, puis cliquez sur la flèche en bas du cadre.

	![Acheter une base de données MongoLab dans le marketplace](./media/create-mongolab-mongodb/purchase-mongodb.png)

5. Cliquez sur la coche pour effectuer l’achat.

	![Passer en revue et terminer l'achat](./media/create-mongolab-mongodb/complete-mongolab-purchase.png)

6. Une fois la base de données créée, vous pouvez la gérer à partir de l’onglet **ADD-ONS** du portail de gestion.

	![Gérer une base de données MongoLab dans le portail Azure](./media/create-mongolab-mongodb/manage-mongolab-add-on.png)

7. Pour obtenir les informations de connexion à la base de données, cliquez sur **CONNECTION INFO** en bas de la page (comme illustré ci-dessus).

	![Informations de connexion MongoLab](./media/create-mongolab-mongodb/mongolab-conn-info.png)

[MongoLab]: https://mongolab.com/home
[waws]: /manage/services/web-sites/
[Azure Store]: ../articles/overview.md
[Azure Marketplace]: ../articles/overview.md
[portal]: http://windows.azure.com/

<!---HONumber=July15_HO3-->