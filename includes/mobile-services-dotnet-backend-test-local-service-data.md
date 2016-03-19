Dans cette section, vous allez utiliser Visual Studio pour héberger le service mobile en local sur votre station de travail de développement dans IIS Express. Vous testerez ensuite l'application et le service principal.


1. Dans Visual Studio, appuyez sur la touche F7 ou cliquez sur **Générer la solution** dans le menu **Générer** pour générer à la fois l'application Windows Store et le service mobile. Vérifiez que les deux projets ont été générés sans erreurs dans la fenêtre Sortie de Visual Studio

2. Dans Visual Studio, appuyez sur la touche F5 ou cliquez sur **Démarrer le débogage** dans le menu **Déboguer** pour exécuter l'application et héberger le service mobile localement dans IIS Express.

 
3. Entrez un texte pour le nouvel élément todoitem. Cliquez ensuite sur **Enregistrer**. Un nouvel élément TodoItem est inséré dans la base de données créée par le service mobile hébergé localement dans IIS Express.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png)

4. Cliquez sur la case à cocher d'un des éléments pour le marquer comme terminé.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png)

5. Dans Visual Studio, vous pouvez examiner les modifications dans la base de données créée pour le service principal en ouvrant l’Explorateur de serveurs et en développant les Connexions de données. Cliquez avec le bouton droit sur la table TodoItems située sous **MS\_TableConnectionString** et cliquez sur **Afficher les données de la table.**

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png)

<!---HONumber=Oct15_HO3-->