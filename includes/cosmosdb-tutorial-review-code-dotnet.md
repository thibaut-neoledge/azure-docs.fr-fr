Passons rapidement en revue ce qui se passe dans l’application. Ouvrez le fichier DocumentDBRepository.cs, et vous découvrirez que ces lignes de code créent les ressources DocumentDB. 

* Le DocumentClient est initialisé.

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);`
    ```

* Une nouvelle base de données est créée.

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* Une nouvelle collection est créée.

    ```csharp
    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri(DatabaseId),
        new DocumentCollection { Id = CollectionId },
        new RequestOptions { OfferThroughput = 1000 });
    ```
