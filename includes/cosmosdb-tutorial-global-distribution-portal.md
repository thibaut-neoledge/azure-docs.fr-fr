
Vous pouvez découvrir la diffusion mondiale de Azure Cosmos DB dans cette vidéo Azure Friday avec Scott Hanselman et Karthik Raman, responsable principal de l’ingénierie.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Cosmos DB](../articles/documentdb/documentdb-distribute-data-globally.md).

## <a id="addregion"></a>Ajouter des régions de bases de donnée mondiales à l’aide du portail Azure
Azure Cosmos DB est disponible dans toutes les [régions Azure][azureregions] à travers le monde. Après avoir sélectionné le niveau de cohérence par défaut pour votre compte de base de données, vous pouvez associer une ou plusieurs régions (en fonction de votre choix de niveau de cohérence par défaut et de vos besoins de distribution mondiale).

1. Dans la barre à gauche du [portail Azure](https://portal.azure.com/), cliquez sur **Azure Cosmos DB**.
2. Dans le panneau **Azure Cosmos DB**, sélectionnez le compte de base de données à modifier.
3. Dans le panneau du compte, cliquez sur **Répliquer les données globalement** à partir du menu.
4. Dans le panneau **Répliquer les données globalement**, sélectionnez les régions à ajouter ou à supprimer en cliquant sur des régions sur la carte, puis cliquez sur **Enregistrer**. L’ajout de régions est payant. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/) ou l’article [Distribution mondiale des données avec DocumentDB](../articles/documentdb/documentdb-distribute-data-globally.md).
   
    ![Cliquez sur les régions dans la carte pour les ajouter ou les supprimer.][1]
    
Une fois que vous ajoutez une deuxième région, l’option **basculement manuel** est activée sur le panneau **Répliquer les données localement** dans le portail. Vous pouvez utiliser cette option pour tester le processus de basculement ou modifier la région principale d’écriture. Une fois que vous ajoutez une troisième région, l’option **Priorités de basculement** est activée sur le même panneau afin que vous puissiez modifier l’ordre de basculement pour les lectures.  

### <a name="selecting-global-database-regions"></a>Sélection de régions de base de données mondiale
Il existe deux scénarios courants pour la configuration de deux ou plusieurs régions :

1. La fourniture aux utilisateurs finaux d’un accès à faible latence aux données, où qu’ils se trouvent dans le monde
2. L’ajout d’une résilience régionale pour la continuité des activités et la récupération d’urgence (BCDR)

Pour fournir une faible latence aux utilisateurs finaux, il est recommandé de déployer l’application et d’ajouter Azure Cosmos DB dans les régions correspondant à la localisation géographique des utilisateurs de l’application.

Pour BCDR, nous vous recommandons d’ajouter les régions en fonction des paires de régions décrites dans l’article [Continuité des activités et récupération d’urgence (BCDR) : régions jumelées d’Azure][bcdr].

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmosdb-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmosdb-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmosdb-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/documentdb/
