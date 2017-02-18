## <a name="a-namecreate-clientacreate-a-client-connection"></a><a name="create-client"></a>Créer une connexion cliente
Créez une connexion cliente en créant un objet `WindowsAzure.MobileServiceClient` .  Remplacez `appUrl` par l’URL de votre application mobile.

```
var client = WindowsAzure.MobileServiceClient(appUrl);
```

## <a name="a-nametable-referenceawork-with-tables"></a><a name="table-reference"></a>Utilisation des tables
Pour accéder aux données ou les mettre à jour, créez une référence à la table principale. Remplacez `tableName` par le nom de votre table.

```
var table = client.getTable(tableName);
```

Une fois que vous disposez d’une référence de table, vous pouvez continuer à utiliser votre table :

* [Interroger une table](#querying)
  * [Filtrage des données](#table-filter)
  * [Pagination des données](#table-paging)
  * [Tri des données](#sorting-data)
* [Insertion de données](#inserting)
* [Modification des données](#modifying)
* [Suppression de données](#deleting)

### <a name="a-namequeryingahow-to-query-a-table-reference"></a><a name="querying"></a>Procédure : interrogation d’une référence de table
Une fois que vous disposez d’une référence de table, vous pouvez l’utiliser pour rechercher des données sur le serveur.  Les requêtes sont effectuées dans un langage de type LINQ.
Pour retourner toutes les données de la table, utilisez le code suivant :

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

La fonction success est appelée avec les résultats.  Ne recourez pas à `for (var i in results)` dans la fonction success, car cette action entraîne une itération sur les informations contenues dans les résultats quand d’autres fonctions de requête (telles que `.includeTotalCount()`) sont utilisées.

Pour plus d’informations sur la syntaxe de requête, consultez la [documentation de l’objet Query].

#### <a name="a-nametable-filterafiltering-data-on-the-server"></a><a name="table-filter"></a>Filtrage des données sur le serveur
Vous pouvez utiliser une clause `where` sur la référence de table :

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Vous pouvez également utiliser une fonction qui filtre l’objet.  Dans ce cas, la variable `this` est affectée à l’objet en cours de filtrage.  Le code suivant est équivalent à l’exemple précédent sur le plan fonctionnel :

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

#### <a name="a-nametable-pagingapaging-through-data"></a><a name="table-paging"></a>Pagination des données
Utilisez les méthodes `take()` et `skip()`.  Par exemple, si vous souhaitez fractionner la table en enregistrements de 100 lignes :

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

La méthode `.includeTotalCount()` est utilisée pour ajouter un champ totalCount à l’objet results.  Le champ totalCount est rempli avec le nombre total d’enregistrements qui est retourné si aucune pagination n’est utilisée.

Vous pouvez ensuite utiliser la variable pages et des boutons d’interface utilisateur pour fournir une liste de pages ; utilisez `loadPage()` pour charger les nouveaux enregistrements pour chaque page.  Implémentez la mise en cache pour accélérer l’accès aux enregistrements qui ont déjà été chargés.

#### <a name="a-namesorting-dataahow-to-return-sorted-data"></a><a name="sorting-data"></a>Procédure : renvoi de données triées
Utilisez les méthodes de requête `.orderBy()` ou `.orderByDescending()` :

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Pour plus d’informations sur l’objet Query, consultez la [documentation de l’objet Query].

### <a name="a-nameinsertingahow-to-insert-data"></a><a name="inserting"></a>Procédure : insertion de données
Créez un objet JavaScript avec la date appropriée et appelez `table.insert()` de façon asynchrone :

```javascript
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Une fois l’insertion correctement effectuée, l’élément inséré est retourné avec les champs supplémentaires qui sont nécessaires pour les opérations de synchronisation.  Mettez à jour votre propre cache avec ces informations en vue des mises à jour ultérieures.

Le Kit de développement logiciel (SDK) de serveur Node.js Azure Mobile Apps prend en charge le schéma dynamique à des fins de développement.  Le schéma dynamique vous permet d’ajouter des colonnes à la table en les spécifiant dans une opération d’insertion ou de mise à jour.  Nous vous recommandons de désactiver le schéma dynamique avant de déplacer votre application vers un environnement de production.

### <a name="a-namemodifyingahow-to-modify-data"></a><a name="modifying"></a>Procédure : modification des données
Comme dans le cas de la méthode `.insert()`, vous devez créer un objet de mise à jour, puis appeler `.update()`.  L’objet de mise à jour doit contenir l’ID de l’enregistrement à mettre à jour, obtenu au moment de la lecture de l’enregistrement ou de l’appel de `.insert()`.

```javascript
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

### <a name="a-namedeletingahow-to-delete-data"></a><a name="deleting"></a>Procédure : suppression de données
Pour supprimer un enregistrement, appelez la méthode `.del()`.  Transmettez l’ID d’une référence d’objet :

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```


<!--HONumber=Feb17_HO1-->


