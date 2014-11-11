<properties linkid="develop-mobile-how-to-guides/work-with-android-client-library" urlDisplayName="Android Client Library" pageTitle="Working with the Mobile Services Android Client Library" metaKeywords="" description="Learn how to use an Android client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the Android client library for Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Utilisation de la bibliothèque cliente Android pour Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/fr-fr/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/fr-fr/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du client Android pour Azure Mobile Services. Les scénarios traités incluent l'interrogation des données, l'insertion, la mise à jour et la suppression des données, l'authentification des utilisateurs, la gestion des erreurs et la personnalisation du client. Si vous débutez avec Mobile Services, suivez le didacticiel [Démarrage rapide Mobile Services][Démarrage rapide Mobile Services]. Ces didacticiels de démarrage rapide vous aideront à configurer et à créer votre premier service mobile.

Les exemples sont écrits en Java et nécessitent le [Kit de développement logiciel (SDK) Mobile Services][Kit de développement logiciel (SDK) Mobile Services]. Ce didacticiel requiert également le [Kit de développement logiciel (SDK) Android][Kit de développement logiciel (SDK) Android], qui inclut l'IDE (environnement de développement intégré) Eclipse et le plug-in ADT (outils de développement Android). Le Kit de développement logiciel (SDK) Mobile Services prend en charge Android 2.2 ou version ultérieure, mais nous vous recommandons de concevoir votre application avec Android 4.2 ou version ultérieure.

## Sommaire

-   [Présentation de Mobile Services][Présentation de Mobile Services]
-   [Concepts][Concepts]
-   [Configuration et conditions préalables][Configuration et conditions préalables]
-   [Création du client Mobile Services][Création du client Mobile Services]
-   [Création d'une référence de table][Création d'une référence de table]
    -   [Structure de l'API][Structure de l'API]
-   [Interrogation des données à partir d'un service mobile][Interrogation des données à partir d'un service mobile]
    -   [Filtrage des données renvoyées][Filtrage des données renvoyées]
    -   [Tri des données renvoyées][Tri des données renvoyées]
    -   [Renvoi de données dans les pages][Renvoi de données dans les pages]
    -   [Sélection de colonnes spécifiques][Sélection de colonnes spécifiques]
    -   [Concaténation de méthodes de requête][Concaténation de méthodes de requête]
-   [Insertion de données dans un service mobile][Insertion de données dans un service mobile]
-   [Mise à jour des données d'un service mobile][Mise à jour des données d'un service mobile]
-   [Suppression des données d'un service mobile][Suppression des données d'un service mobile]
-   [Recherche d'un élément spécifique][Recherche d'un élément spécifique]
-   [Utilisation de données non typées][Utilisation de données non typées]
-   [Liaison des données dans l'interface utilisateur][Liaison des données dans l'interface utilisateur]
    -   [Définition de la mise en page][Définition de la mise en page]
    -   [Définition de l'adaptateur][Définition de l'adaptateur]
    -   [Utilisation de l'adaptateur][Utilisation de l'adaptateur]
-   [Authentification des utilisateurs][Authentification des utilisateurs]
    -   [Mise en cache des jetons d'authentification][Mise en cache des jetons d'authentification]
-   [Gestion des erreurs][Gestion des erreurs]
-   [Personnalisation du client][Personnalisation du client]
    -   [Personnalisation des en-têtes de requête][Personnalisation des en-têtes de requête]
    -   [Personnalisation de la sérialisation][Personnalisation de la sérialisation]
-   [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

## <a name="setup"></a><span class="short-header">Configuration</span>Configuration et conditions préalables

Nous partons du principe que vous avez créé un service mobile et une table. Pour plus d'informations, consultez la page [Créer une table][Créer une table]. Dans le code utilisé dans cette rubrique, nous partons du principe que la table s'intitule *TodoItem* et contient les colonnes suivantes :

-   id
-   text
-   complete
-   due
-   duration

L'objet côté client typé correspondant est le suivant :

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
        private Date due
        private Integer duration;
    }

Lorsque le schéma dynamique est activé, Azure Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet de la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique][Schéma dynamique].

## <a name="create-client"></a><span class="short-header">Création du client Mobile Services</span> Création du client Mobile Services

Le code suivant permet de créer l'objet [MobileServiceClient][MobileServiceClient] utilisé pour accéder à votre service mobile.

            MobileServiceClient mClient = new MobileServiceClient(
                    "MobileServiceUrl", // Replace with the above Site URL
                    "AppKey",           // replace with the Application Key 
                    this)

Dans le code ci-dessus, remplacez `MobileServiceUrl` et `AppKey` par l'URL et la clé d'application du service mobile, dans cet ordre. Tous deux sont disponibles sur le portail de gestion Azure. Pour y accéder, sélectionnez votre service mobile, puis cliquez sur *Tableau de bord*.

## <a name="instantiating"></a><span class="short-header">Création d'une référence de table</span> Création d'une référence de table

La façon la plus simple d'interroger des données ou de les modifier dans le service mobile est d'utiliser le *modèle de programmation typé*, car Java comporte des types forts (nous aborderons plus tard le modèle *non typé*). Ce modèle permet la sérialisation et la désérialisation transparentes de JSON à l'aide de la bibliothèque [gson][gson] lors de l'envoi des données entre le client et le service mobile : le développeur n'a rien à faire, l'infrastructure gère tout.

La première chose à faire pour interroger des données ou les modifier est de créer un objet [MobileServiceTable][MobileServiceTable] en appelant la méthode **getTable** sur le [**MobileServiceClient**][MobileServiceClient]. Nous allons examiner deux surcharges de cette méthode :

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

Dans le code qui suit, *mClient* est une référence à votre client de service mobile.

La [première surcharge][première surcharge] est utilisée lorsque le nom de la classe et le nom de la table sont identiques :

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

La [deuxième surcharge][deuxième surcharge] est utilisée lorsque le nom de la table est différent du nom du type.

        MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="api"></a>Structure de l'API

Les opérations de table Mobile Services utilisent le modèle de rappel asynchrone. Les méthodes qui utilisent des requêtes et des opérations telles que l'insertion, la mise à jour et la suppression comportent toutes un paramètre qui est un objet de rappel. Cet objet contient toujours une méthode **OnCompleted**. La méthode **onCompleted** inclut un paramètre qui est un objet **Exception**, que vous pouvez tester afin de déterminer la réussite de l'appel de la méthode. Si l'objet **Exception** est vide, l'opération est réussie. Sinon, l'objet **Exception** décrit les raisons de l'échec.

Il existe de nombreux types d'objets de rappel, et celui que vous utilisez dépend de l'opération effectuée (interrogation, modification ou suppression). Les paramètres de la méthode *onCompleted* varient en fonction de l'objet de rappel dont elle fait partie.

## <a name="querying"></a><span class="short-header">Interrogation des données</span> Interrogation des données à partir d'un service mobile

Cette section explique comment émettre des requêtes à destination du service mobile. Les sous-sections décrivent différents aspects tels que le tri, le filtrage et la pagination. Enfin, nous verrons comment concaténer ces différentes opérations.

Le code qui suit renvoie tous les éléments de la table *ToDoItem*.

        mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
                public void onCompleted(List<ToDoItem> result, int count,
                    Exception exception, ServiceFilterResponse response) {
                    if (exception == null) {
                        for (ToDoItem item : result) {
                            Log.i(TAG, "Read object with ID " + item.id);  
                        }
                    }
                }
            });

Les requêtes comme celle-ci utilisent l'objet de rappel [**TableQueryCallback\<E\>**][**TableQueryCallback\<E\>**].

Le paramètre *result* renvoie le jeu de résultats de la requête et le code dans la branche réussie du test *exception* indique comment analyser les lignes.

### <a name="filtering"></a> Filtrage des données renvoyées

Le code qui suit renvoie tous les éléments de la table *ToDoItem* dont le champ *complete* est égal à *false*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

        mToDoTable.where().field("complete").eq(false)
                  .execute(new TableQueryCallback<ToDoItem>() {
                        public void onCompleted(List<ToDoItem> result, 
                                                int count, 
                                                Exception exception,
                                                ServiceFilterResponse response) {
                if (exception == null) {
                    for (ToDoItem item : result) {
                        Log.i(TAG, "Read object with ID " + item.id);  
                    }
                } 
            }
        });

Le filtre commence par un appel de méthode [**where**][**where**] sur la référence de table. Il est suivi par un appel de méthode [**field**][**field**], suivi d'un appel de méthode qui spécifie le prédicat logique. Les méthodes de prédicat possibles sont [**eq**][**eq**], [**ne**][**ne**], [**gt**][**gt**], [**ge**][**ge**], [**lt**][**lt**], [**le**][**le**], etc.

Ces éléments sont suffisants pour comparer des champs de nombre et de chaîne à des valeurs spécifiques. Mais vous pouvez aller bien plus loin.

Vous pouvez par exemple filtrer les dates. Vous pouvez comparer le champ de date en entier, mais aussi des parties de la date, avec les méthodes [**year**][**year**], [**month**][**month**], [**day**][**day**], [**hour**][**hour**], [**minute**][**minute**] et [**second**][**second**]. L'extrait de code qui suit ajoute un filtre pour les éléments dont la date d'échéance *due date* est égale à 2013.

        mToDoTable.where().year("due").eq(2013)

Vous pouvez concevoir de nombreux filtres complexes avec les méthodes [**startsWith**][**startsWith**], [**endsWith**][**endsWith**], [**concat**][**concat**], [**subString**][**subString**], [**indexOf**][**indexOf**], [**replace**][**replace**], [**toLower**][**toLower**], [**toUpper**][**toUpper**], [**trim**][**trim**] et [**length**][**length**]. L'extrait de code qui suit filtre les lignes de la table dans lesquelles la colonne *text* commence par « PRI0 ».

        mToDoTable.where().startsWith("text", "PRI0")

Les champs numériques permettent aussi l'utilisation de filtres plus complexes, avec par exemple les méthodes [**add**][**add**], [**sub**][**sub**], [**mul**][**mul**], [**div**][**div**], [**mod**][**mod**], [**floor**][**floor**], [**ceiling**][**ceiling**] et [**round**][**round**]. L'extrait de code qui suit filtre les lignes de la table dans lesquelles la colonne *duration* est un nombre pair.

        mToDoTable.where().field("duration").mod(2).eq(0)

Vous pouvez combiner les prédicats avec les méthodes [**and**][**and**], [**or**][**or**] et [**not**][**not**]. Cet extrait de code combine deux des exemples ci-dessus.

        mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

Et vous pouvez regrouper et imbriquer les opérateurs logiques, comme dans cet extrait de code :

        mToDoTable.where()
                    .year("due").eq(2013)
                        .and
                    (startsWith("text", "PRI0").or().field("duration").gt(10))

Pour obtenir plus de détails et des exemples de filtres, consultez la page [Explorer la richesse du modèle de requêtes de données client Android Mobile Services][Explorer la richesse du modèle de requêtes de données client Android Mobile Services].

### <a name="sorting"></a> Tri des données renvoyées

Le code qui suit renvoie tous les éléments de la table *ToDoItem* triés par ordre croissant sur le champ *text*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

        mToDoTable.orderBy("text", QueryOrder.Ascending)
            .execute(new TableQueryCallback<ToDoItem>() { 
                /* same implementation as above */ 
            }); 

Le premier paramètre de la méthode [**orderBy**][**orderBy**] est une chaîne égale au nom du champ sur lequel effectuer le tri.

Le second paramètre utilise l'énumération [**QueryOrder**][**QueryOrder**] pour spécifier si le tri doit être croissant ou décroissant.

Notez que si vous filtrez avec la méthode ***where***, celle-ci doit être appelée avant la méthode ***orderBy***.

### <a name="paging"></a> Renvoi de données dans les pages

Le premier exemple présente comment sélectionner les 5 premiers éléments d'une table. Cette requête renvoie les éléments d'une table *ToDoItem*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

        mToDoTable.top(5)
                .execute(new TableQueryCallback<ToDoItem>() {   
                public void onCompleted(List<ToDoItem> result, 
                                        int count,
                                        Exception exception, 
                                        ServiceFilterResponse response) {
                    if (exception == null) {
                        for (ToDoItem item : result) {
                            Log.i(TAG, "Read object with ID " + item.id);  
                        }
                    } 
                }
            });

Ensuite, nous définissons une requête qui ignore les 5 premiers éléments, puis renvoie les 5 suivants.

        mToDoTable.skip(5).top(5)
                .execute(new TableQueryCallback<ToDoItem>() {   
                // implement onCompleted logic here
            });

### <a name="selecting"></a> Sélection de colonnes spécifiques

Le code qui suit illustre comment renvoyer tous les éléments d'une table *ToDoItems*, mais uniquement en affichant les champs *complete* et *text*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

        mToDoTable.select("complete", "text")
                .execute(new TableQueryCallback<ToDoItem>() { 
                    /* same implementation as above */ 
            }); 

Ici, les paramètres associés à la fonction select sont les noms de chaîne des colonnes de table à renvoyer.

La méthode [**select**][**select**] doit suivre les méthodes telles que [**where**][1] et [**orderBy**][2] si elles sont présentes. Elle peut être suivie de méthodes telles que [**top**][**top**].

### <a name="chaining"></a> Concaténation de méthodes de requête

Les méthodes utilisées dans les requêtes de tables de service mobile peuvent être concaténées. Vous pouvez ainsi sélectionner des colonnes spécifiques de lignes filtrées, qui sont triées et paginées. Vous pouvez créer des filtres logiques assez complexes.

Ce qui fait que tout ceci fonctionne est que les méthodes de requête utilisées renvoient des objets [**MobileServiceQuery\<T\>**][**MobileServiceQuery\<T\>**], qui à leur tour font l'objet de méthodes appelées. Pour mettre fin à la série de méthodes et exécuter la requête, appelez la méthode [**execute**][**execute**].

Voici un exemple de code dans lequel *mToDoTable* est une référence à la table du service mobile *ToDoItem*.

        mToDoTable.where().year("due").eq(2013)
                        .and().startsWith("text", "PRI0")
                        .or().field("duration").gt(10)
                    .select("id", "complete", "text", "duration")
                    .orderBy(duration, QueryOrder.Ascending).top(20)                
                    .execute(new TableQueryCallback<ToDoItem>() { 
                        /* code to execute */ 
                });

La principale exigence lors du chaînage de méthodes est que la méthode *where* et les prédicats doivent figurer en premier. Après cela, vous pouvez appeler toutes les méthodes suivantes dans l'ordre qui convient le mieux à votre application.

## <a name="inserting"></a><span class="short-header">Insertion de données</span> Insertion de données dans un service mobile

Le code suivant montre comment insérer de nouvelles lignes dans une table.

Vous créez tout d'abord une instance de la classe *ToDoItem* et définissez ses propriétés.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 
        

Ensuite, vous appelez la méthode [**insert**][**insert**].

        mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
            public void onCompleted(ToDoItem entity, 
                                Exception exception, 
                                ServiceFilterResponse response) {   
                if (exception == null) {
                        Log.i(TAG, "Read object with ID " + entity.id);  
                } 
            }
        });

Pour les opérations **insert**, l'objet de rappel est un [**TableOperationCallback\<ToDoItem\>**][**TableOperationCallback\<ToDoItem\>**].

Le paramètre d'entité de la méthode **onCompleted** contient l'objet qui vient d'être inséré. Le code de réussite montre comment accéder à l'*id* de la ligne insérée.

Mobile Services prend en charge les valeurs de chaîne personnalisées uniques pour l'ID de table. Les applications peuvent ainsi utiliser des valeurs personnalisées, telles que des adresses de messagerie ou des noms d'utilisateur, pour la colonne d'ID d'une table Mobile Services. Par exemple, si vous voulez identifier chaque enregistrement par une adresse électronique, vous pouvez utiliser l'objet JSON suivant.

        ToDoItem mToDoItem = new ToDoItem();
        mToDoItem.id = "myemail@mydomain.com";
        mToDoItem.text = "Test Program";
        mToDoItem.complete = false;
        mToDoItem.duration = 5; 

Si aucune valeur d'ID de chaîne n'est fournie lors de l'insertion de nouveaux enregistrements dans une table, Mobile Services génère une valeur d'ID unique.

La prise en charge des ID de chaîne fournit les avantages suivants aux développeurs :

-   Les ID peuvent être générés sans effectuer d'aller-retour vers la base de données.
-   Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
-   Les valeurs d'ID peuvent mieux s'intégrer à la logique d'une application.

Vous pouvez également utiliser des scripts serveur pour définir des valeurs d'ID. L'exemple de script ci-dessous génère un GUID personnalisé et l'attribue à l'ID d'un nouvel enregistrement. Il est semblable à la valeur d'ID qui serait générée par Mobile Services si vous ne transmettiez pas de valeur pour l'ID d'un enregistrement.

    //Example of generating an id. This is not required since Mobile Services
    //will generate an id if one is not passed in.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

Si une application fournit la valeur d'un ID, Mobile Services la stocke en l'état. Les espaces de début et de fin sont également inclus. Ils ne sont pas supprimés de la valeur.

La valeur d'`id` doit être unique et ne contenir aucun caractère présent dans les ensembles suivants :

-   Caractères de contrôle : [0x0000-0x001F] et [0x007F-0x009F]. Pour plus d'informations, consultez la page [Codes de contrôle ASCII C0 et C1][Codes de contrôle ASCII C0 et C1].
-   Caractères imprimables : **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Les ID « . » et « .. »

Vous pouvez également utiliser des ID d'entier pour vos tables. Pour pouvoir utiliser un ID d'entier, vous devez créer votre table avec la commande `mobile table create` à l'aide de l'option `--integerId`. Cette commande s'utilise avec l'interface de ligne de commande (CLI) pour Azure. Pour plus d'informations sur l'utilisation de l'interface de ligne de commande, consultez la page [Interface de ligne de commande pour la gestion des tables Mobile Services][Interface de ligne de commande pour la gestion des tables Mobile Services].

## <a name="updating"></a><span class="short-header">Mise à jour des données</span> Mise à jour des données d'un service mobile

Le code suivant montre comment mettre à jour les données d'une table. Dans cet exemple, *mToDoItem* est une référence à un élément de la table *ToDoItem* et nous mettons à jour sa propriété *duration*.

        mToDoItem.duration = 5;
        mToDoTable.update(mToDoItem, new TableOperationCallback<ToDoItem>() {
            public void onCompleted(ToDoItem entity, 
                                    Exception exception, 
                                    ServiceFilterResponse response) {
                if (exception == null) {
                        Log.i(TAG, "Read object with ID " + entity.id);  
                } 
            }
        });

Notez que l'objet de rappel et les paramètres de la méthode *onCompleted* sont les mêmes que lors d'une insertion.

## <a name="deleting"></a><span class="short-header">Suppression de données</span> Suppression des données d'un service mobile

Le code suivant montre comment supprimer les données d'une table. Il supprime un élément existant de la table ToDoItem à l'aide d'une référence à cet élément, ici *mToDoItem*.

        mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
            public void onCompleted(Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

Notez que dans le cas de *delete*, l'objet de rappel est une méthode [**TableDeleteCallback**][**TableDeleteCallback**] et la méthode **onCompleted** est légèrement différente, car aucune ligne de la table n'est renvoyée.

Le code qui suit présente une autre manière d'effectuer cette opération. Il supprime un élément existant de la table ToDoItem en spécifiant la valeur dans le champ ID de la ligne à supprimer (qui est ici égal à 37BBF396-11F0-4B39-85C8-B319C729AF6D).

        mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
            public void onCompleted(Exception exception, 
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

## <a name="lookup"></a><span class="short-header">Recherche des données</span> Recherche d'un élément spécifique

Il est possible de rechercher un élément spécifique par son *ID*, à l'inverse de la requête, qui génère une suite d'éléments qui répondent à certains critères. Le code qui suit montre comment effectuer cette recherche, avec l'*ID* = "37BBF396-11F0-4B39-85C8-B319C729AF6D".

        mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
            public void onCompleted(item entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Read object with ID " + entity.id);    
                }
            }
        });

## <a name="untyped"></a><span class="short-header">Utilisation de données non typées</span> Utilisation de données non typées

Le modèle de programmation non typé vous offre un contrôle total de la sérialisation JSON, ce dont vous aurez peut-être besoin dans certains scénarios, par exemple si la table de votre service mobile contient un grand nombre de colonnes et que vous n'avez besoin de faire référence qu'à quelques-unes d'entre elles. Avec le modèle typé, vous devez définir toutes les colonnes de la table du service mobile dans votre classe de données. Mais avec le modèle non typé, vous ne définissez que les colonnes dont vous avez besoin.

Comme pour le modèle typé, vous commencez par obtenir une référence de table, mais il s'agit dans ce cas d'un objet [MobileServicesJsonTable][MobileServicesJsonTable]. Vous obtenez cette référence en appelant la méthode [getTable()][getTable()] sur une instance du client Mobile Services.

Utilisez la surcharge suivante de cette méthode, qui est utilisée avec les modèles de programmation JSON non typés :

        public class MobileServiceClient {
            public MobileServiceJsonTable getTable(String name);
        }

La plupart des appels d'API permettant d'accéder aux données sont similaires à ceux des appels de programmation typés. La principale différence est que dans le modèle non typé, vous appelez des méthodes dans l'objet **MobileServiceJsonTable** plutôt que dans l'objet **MobileServiceTable**. L'utilisation de l'objet de rappel et de la méthode **onCompleted** est très similaire à celle du modèle typé.

### <a name="json_instance"></a> Création d'une instance de table non typée

Une fois que vous avez créé une instance du client Mobile Services (ici la variable *mClient*), vous créez une instance de **MobileServiceJsonTable**, avec le code qui suit.

        MobileServiceJsonTable mTable = mClient.getTable("ToDoItem");

Une fois que vous avez créé une instance de **MobileServiceJsonTable**, vous pouvez appeler presque toutes les méthodes utilisables avec le modèle de programmation typé. Dans certains cas, les méthodes prennent un paramètre non typé, comme nous allons le voir dans les exemples qui suivent.

### <a name="json_insert"></a> Insertion dans une table non typée

Le code suivant vous explique comment effectuer une insertion. La première étape est de créer un objet [**JsonObject**][**JsonObject**], qui fait partie de la bibliothèque [gson][gson].

        JsonObject task = new JsonObject();
        task.addProperty("text", "Wake up");
        task.addProperty("complete", false);
        task.addProperty("duration", 5);

L'étape suivante est l'insertion de l'objet. La fonction de rappel transmise à la méthode [**insert**][3] est une instance de la classe [**TableJsonOperationCallback**][**TableJsonOperationCallback**]. Notez que le premier paramètre de la méthode *onCompleted* est un objet JsonObject.

        mTable.insert(task, new TableJsonOperationCallback() {
            public void onCompleted(JsonObject jsonObject, 
                                    Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object inserted with ID " + 
                jsonObject.getAsJsonPrimitive("id").getAsString());
                }
            }
        });

Notez comment nous obtenons l'ID de l'objet inséré avec cet appel de méthode :

                jsonObject.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a> Suppression dans une table non typée

Le code qui suit montre comment supprimer une instance, dans ce cas la même instance de l'objet **JsonObject** créé dans l'exemple *insert* précédent. Notez que l'objet de rappel **TableDeleteCallback** est identique à celui utilisé dans le modèle de programmation typé et que la méthode **onCompleted** comporte une autre signature que cette utilisée dans l'exemple **insert**.

        mTable.delete(task, new TableDeleteCallback() {
            public void onCompleted(Exception exception, 
                                    ServiceFilterResponse response) {
                if(exception == null){
                    Log.i(TAG, "Object deleted");
                }
            }
        });

Vous pouvez aussi directement supprimer une instance avec son ID :

        mTable.delete(task.getAsJsonPrimitive("id").getAsString(), ...)

### <a name="json_get"></a> Récupération de toutes les lignes d'une table non typée

Le code suivant montre comment récupérer toute une table. Notez que le modèle de programmation non typé utilise un autre objet de rappel : [**TableJsonQueryCallback**][**TableJsonQueryCallback**].

        mTable.execute(new TableJsonQueryCallback() {
            public void onCompleted(JsonElement result, 
                                    int count, 
                                    Exception exception,
                                    ServiceFilterResponse response) {
                if(exception == null){
                    JsonArray results = result.getAsJsonArray();
                    for(JsonElement item : results){
                        Log.i(TAG, "Read object with ID " + 
                    item.getAsJsonObject().getAsJsonPrimitive("id").getAsInt());
                    }
                }
            }
        });

Vous pouvez filtrer, trier et paginer en concaténant des méthodes portant le même nom que celles utilisées dans le modèle de programmation typé.

## <a name="binding"></a><span class="short-header">Liaison des données</span> Liaison des données dans l'interface utilisateur

La liaison des données nécessite trois composants :

-   la source de données ;
-   la mise en page à l'écran ;
-   l'adaptateur qui lie ces deux éléments.

Dans notre exemple de code, nous renvoyons les données de la table de service mobile *ToDoItem* dans un tableau. Il s'agit là d'un cas de figure très courant pour les applications de données : les requêtes de base de données renvoient une suite de lignes que le client récupère dans une liste ou un tableau. Dans cet exemple, le tableau est la source des données.

Ce code spécifie une mise en page à l'écran qui définit la façon dont les données seront affichées sur l'appareil.

Les deux sont liés par un adaptateur, qui dans ce code est une extension de la classe *ArrayAdapter\<ToDoItem\>*.

### <a name="layout"></a> Définition de la mise en page

La mise en page est définie par plusieurs éléments de code XML. En nous basant sur une mise en page existante, nous partons du principe que le code qui suit représente la vue **ListView** que nous souhaitons remplir avec les données du serveur.

        <ListView
            android:id="@+id/listViewToDo"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            tools:listitem="@layout/row_list_to_do" >
        </ListView>

Dans ce code, l'attribut *listitem* spécifie l'ID de la mise en page de chaque ligne dans la liste. Voici ce code, qui spécifie une case à cocher et le texte associé. Ce contenu est créé pour chaque élément de la liste. Pour une mise en page plus complexe, d'autres champs doivent être définis dans l'affichage. Le code se trouve dans le fichier *row\_list\_to\_do.xml*.

        <?xml version="1.0" encoding="utf-8"?>
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="horizontal">           
            <CheckBox
                android:id="@+id/checkToDoItem"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/checkbox_text" />
        </LinearLayout>
        

### <a name="adapter"></a> Définition de l'adaptateur

Comme la source de données de notre vue est un tableau de *ToDoItem*, nous créons une sous-classe de notre adaptateur à partir de la classe *ArrayAdapter\<ToDoItem\>*. Cette sous-classe produit une vue pour chaque élément *ToDoItem* utilisant la mise en page *row\_list\_to\_do*.

Dans notre code, nous définissons la classe suivante, qui est une extension de la classe *ArrayAdapter\<E\>* :

        public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Vous devez ignorer la méthode *getView* de l'adaptateur. Cet exemple de code présente une des manières d'effectuer cette opération : les détails varient selon les applications.

    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        return row;
    }

Nous créons une instance de cette classe dans notre activité, comme suit :

        ToDoItemAdapter mAdapter;
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Notez que le deuxième paramètre du constructeur ToDoItemAdapter est une référence à la mise en page. L'appel au constructeur est suivi du code ci-dessous, qui obtient tout d'abord une référence à **ListView**, puis appelle ensuite *setAdapter* pour se configurer lui-même pour utiliser l'adaptateur que nous venons de créer :

        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

### <a name="use-adapter"></a> Utilisation de l'adaptateur

Vous êtes désormais prêt à utiliser la liaison des données. Le code qui suit montre comment obtenir les éléments dans la table du service mobile, nettoyer l'adaptateur, puis appeler la méthode *add* de l'adaptateur pour y placer les éléments renvoyés.

        mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
            public void onCompleted(List<ToDoItem> result, int count, Exception exception, ServiceFilterResponse response) {
                if (exception == null) {
                    mAdapter.clear();
                    for (ToDoItem item : result) {
                        mAdapter.add(item);
                    }
                } 
            }
        });

Vous devez également appeler l'adaptateur à chaque fois que vous modifiez la table *ToDoItem* si vous voulez afficher les résultats de cette modification. Comme les modifications se font enregistrement par enregistrement, vous ne composez qu'avec une seule ligne, et non une collection. Lorsque vous insérez un élément, vous appelez la méthode *add* de l'adaptateur et lorsque vous supprimez un élément, vous appelez la méthode *remove*.

## <a name="authentication"></a><span class="short-header">Authentification</span> Authentification des utilisateurs

Mobile Services permet aux utilisateurs d'applications de s'authentifier par l'intermédiaire de divers fournisseurs d'identité externes : Facebook, Google, Compte Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'identité des utilisateurs authentifiés pour implémenter des règles d'autorisation dans les scripts serveur. Pour plus d'informations, consultez la page [Prise en main de l'authentification][Prise en main de l'authentification].

Deux flux d'authentification sont pris en charge : un *flux serveur* et un *flux client*. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) propres aux appareils et aux fournisseurs.

Trois étapes sont nécessaires pour activer l'authentification dans votre application :

1.  inscription de votre application pour l'authentification auprès d'un fournisseur et configuration de Mobile Services ;
2.  restriction des autorisations de table aux utilisateurs authentifiés uniquement ;
3.  Ajout de code d'authentification à votre application

Mobile Services prend en charge les fournisseurs d'identité suivants, utilisables pour l'authentification des utilisateurs :

-   Compte Microsoft
-   Facebook
-   Twitter
-   Google
-   Azure Active Directory

Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'ID d'un utilisateur authentifié pour modifier des demandes.

Ces deux premières tâches sont effectuées via le [portail de gestion Azure][portail de gestion Azure]. Pour plus d'informations, consultez la page [Prise en main de l'authentification][Prise en main de l'authentification].

### <a name="caching"></a> Ajout de code d'authentification à votre application

1.  Ajoutez les instructions d'importation suivantes au fichier d'activités de votre application.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Dans la méthode **onCreate** de la classe d'activité, ajoutez la ligne de code qui suit après le code qui crée l'objet `MobileServiceClient` : nous partons du principe que la référence à l'objet `MobileServiceClient` est *mClient*.

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {
                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {  
                            if (exception == null) {
                                /* User now logged in, you can get their identity via user.getUserId() */ 
                            } else {
                                /* Login error */
                            }
                        }
                    });

    Ce code authentifie l'utilisateur à l'aide de la connexion Google. Une boîte de dialogue affiche l'identifiant de l'utilisateur authentifié. Vous ne pouvez pas poursuivre sans authentification positive.

    <div class="dev-callout"><b>Remarque</b>
<p>Si vous utilisez un fournisseur d'identit&eacute; diff&eacute;rent de Google, remplacez la valeur transmise &agrave; la m&eacute;thode <strong>login</strong> ci-dessus par l'une des valeurs suivantes&nbsp;: <i>MicrosoftAccount</i>, <i>Facebook</i>, <i>Twitter</i> ou <i>WindowsAzureActiveDirectory</i>.</p>
</div>

3.  Lorsque vous exécutez l'application, connectez-vous avec le fournisseur d'identité choisi.

### <a name="caching"></a> Mise en cache des jetons d'authentification

Cette section vous montre comment mettre en cache un jeton d'authentification. Cela permet d'éviter que les utilisateurs s'identifient à nouveau si l'application est mise en hibernation alors que le jeton est encore valable.

Pour cela, vous devez stocker localement l'ID utilisateur et le jeton d'authentification sur l'appareil. Au prochain démarrage de l'application, vous vérifiez le cache, et si ces valeurs sont présentes, vous pouvez ignorer la procédure d'ouverture de session et rafraîchir le client avec ces données. Mais ces données sont sensibles et elles doivent être stockées sous forme chiffrée au cas où le téléphone serait volé.

L'extrait de code suivant montre comment obtenir un jeton pour se connecter à un compte Microsoft. Si le cache est identifié, le jeton est alors mis en cache et rechargé.

    private void authenticate() {
        if (LoadCache())
        {
            createTable();
        }
        else
        {
            // Login using the provider.
            mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
                    new UserAuthenticationCallback() {
                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {
                            if (exception == null) {
                                createTable();
                                cacheUser(mClient.getCurrentUser());
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }
    }   


    private boolean LoadCache()
    {
        SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
        String tmp1 = prefs.getString("tmp1", "undefined"); 
        if (tmp1 == "undefined")
            return false;
        String tmp2 = prefs.getString("tmp2", "undefined"); 
        if (tmp2 == "undefined")
            return false;
        MobileServiceUser user = new MobileServiceUser(tmp1);
        user.setAuthenticationToken(tmp2);
        mClient.setCurrentUser(user);       
        return true;
    }


    private void cacheUser(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString("tmp1", user.getUserId());
        editor.putString("tmp2", user.getAuthenticationToken());
        editor.commit();
    }

Que se passe-t-il si votre jeton expire ? Dans ce cas, lorsque vous l'utilisez pour vous connecter, vous obtenez un message *401 de connexion non autorisée*. L'utilisateur doit alors se connecter pour obtenir de nouveaux jetons. Vous pouvez éviter d'écrire du code pour gérer cette opération partout où votre application appelle Mobile Services en utilisant des filtres, ce qui vous permet d'intercepter les appels envoyés et les réponses de Mobile Services. Le code de filtre teste ensuite la réponse pour une erreur 401, déclenche le processus de connexion si nécessaire, puis reprend la demande qui a généré l'erreur 401.

## <a name="errors"></a><span class="short-header">Gestion des erreurs</span> Gestion des erreurs

Vous pouvez consulter un exemple de validation et de gestion des erreurs [ici][ici]. Il met en œuvre la validation via des scripts serveur qui renvoient des exceptions en cas d'erreur, ainsi qu'un code client qui gère les exceptions.

Une autre possibilité est de fournir un gestionnaire d'événements *global*. Le code qui accède à la table du service mobile a recours à trois objets de rappel différents :

-   **TableQueryCallback** / **TableQueryJsonCallback**
-   **TableOperationCallback** / **TableJsonOperationCallback**
-   **TableDeleteCallback**

Chacun de ces objets a une méthode **OnCompleted** dans laquelle le deuxième paramètre est un objet **java.lang.Exception**. Vous pouvez créer une sous-classe de ces objets de rappel et mettre en œuvre votre propre méthode **onCompleted** qui vérifie si le paramètre d'exception est vide. Si c'est le cas et qu'il n'y a pas d'erreur, vous pouvez juste appeler **super.OnCompleted()**.

Si l'objet **Exception** n'est pas vide, gérez l'erreur en affichant des informations plus détaillées à son sujet. L'extrait de code qui suit présente une manière d'afficher plus de détails.

        String msg = exception.getCause().getMessage();

Le développeur peut maintenant utiliser les rappels de sous-classe et ne plus se soucier de la vérification de l'exception, tout étant géré de façon centralisée (\#2) pour toutes les instances du rappel.

## <a name="customizing"></a><span class="short-header">Personnalisation du client</span> Personnalisation du client

### <a name="headers"></a> Personnalisation des en-têtes de requête

Vous pouvez joindre un en-tête personnalisé à chaque requête sortante. Pour ce faire, vous pouvez configurer un ServiceFilter de la façon suivante :

        client = client.withFilter(new ServiceFilter() {
        
            @Override
            public void handleRequest(ServiceFilterRequest request,
                    NextServiceFilterCallback nextServiceFilterCallback,
                    ServiceFilterResponseCallback responseCallback) {
                request.addHeader("My-Header", "Value");      
                nextServiceFilterCallback.onNext(request, responseCallback);
            }
        });

### <a name="serialization"></a> Personnalisation de la sérialisation

Par défaut, Mobile Services part du principe que les noms de table, les noms de colonnes et les types de données sur le serveur correspondent exactement à ce qui se trouve sur le client. Mais pour diverses raisons, les noms sur le serveur et sur le client peuvent ne pas correspondre. Un bon exemple peut être si vous voulez modifier un client existant afin qu'il utilise Azure Mobile Services plutôt que le produit d'un concurrent.

Vous pouvez effectuer les personnalisations suivantes :

-   Les noms de colonnes utilisés dans le service mobile ne correspondent pas à ceux que vous utilisez dans le client
-   Utiliser une table de service mobile qui porte un autre nom que celui de la classe sur laquelle elle est mappée dans le client
-   Activer la mise en majuscules automatique des propriétés
-   Ajouter des propriétés complexes à un objet

### <a name="columns"></a> Mappage de noms de client et de serveur différents

Supposons que le code de votre client Java utilise des noms de style Java standard pour les propriétés d'objets *ToDoItem*, comme ce qui suit.

-   mId
-   mText
-   mComplete
-   mDuration

Vous devez sérialiser les noms du client en noms JSON correspondant aux noms des colonnes de la table *ToDoItem* sur le serveur. Le code qui suit, qui utilise la bibliothèque [gson][gson], effectue cette sérialisation.

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a> Mappage de noms de tables différents entre client et service mobile

Le mappage du nom de table client vers un autre nom de table de service mobile est simple. Il suffit d'utiliser un des remplacements de la fonction
[getTable()][deuxième surcharge], comme dans le code qui suit.

        mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a> Automatisation du mappage des noms de colonnes

Le mappage des noms de colonnes pour une table avec peu de colonnes n'est pas compliqué, comme nous l'avons vu dans la section précédente. Mais supposons que notre table contienne un grand nombre de colonnes, par exemple 20 ou 30. Nous pouvons alors utiliser l'API [gson][gson] et spécifier une stratégie de conversion à appliquer à toutes les colonnes, ce qui nous évite d'avoir à annoter tous les noms de colonnes.

Pour cela, nous utilisons la bibliothèque [gson][gson] que la bibliothèque cliente Android utilise en arrière-plan pour sérialiser les objets Java en données JSON, qui sont ensuite envoyées à Azure Mobile Services.

Le code qui suit utilise la méthode *setFieldNamingStrategy()*, dans laquelle nous définissons une méthode *FieldNamingStrategy()*. Cette méthode indique de supprimer le premier caractère (un « m »), puis de mettre le caractère suivant en minuscule, ce pour tous les noms de champ. Ce code permet aussi d'obtenir une impression lisible du résultat JSON.

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            })
            .setPrettyPrinting());

Ce code doit être exécuté avant tout autre appel à des méthodes sur l'objet client Mobile Services.

### <a name="complex"></a> Stockage d'un objet ou d'une propriété de tableau dans une table

Jusqu'ici, tous nos exemples de sérialisation impliquaient des types primitifs comme les entiers et les chaînes, qui sont facilement sérialisés en JSON et dans la table du service mobile. Supposons que nous voulions ajouter un objet complexe à notre type de client, objet qui n'est pas automatiquement sérialisé vers JSON et la table. Par exemple, nous pourrions ajouter un tableau de chaînes à l'objet client. Nous devons alors spécifier comment effectuer la sérialisation et comment stocker le tableau dans la table du service mobile.

Pour voir un exemple, consultez le billet de blog [Personnalisation de la sérialisation avec la bibliothèque][Personnalisation de la sérialisation avec la bibliothèque][gson][gson] dans le client Android Mobile Services</a>.

Cette méthode générale peut être utilisée dès lors que nous sommes en présence d'un objet complexe, qui ne peut pas être sérialisé de façon automatique vers JSON et la table du service mobile.

## <a name="next-steps"></a>Étapes suivantes

La référence Javadocs pour l'API cliente Android se trouve [][]<http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html></a>

  [Démarrage rapide Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started-android/
  [Présentation de Mobile Services]: #what-is
  [Concepts]: #concepts
  [Configuration et conditions préalables]: #setup
  [Création du client Mobile Services]: #create-client
  [Création d'une référence de table]: #instantiating
  [Structure de l'API]: #api
  [Interrogation des données à partir d'un service mobile]: #querying
  [Filtrage des données renvoyées]: #filtering
  [Tri des données renvoyées]: #sorting
  [Renvoi de données dans les pages]: #paging
  [Sélection de colonnes spécifiques]: #selecting
  [Concaténation de méthodes de requête]: #chaining
  [Insertion de données dans un service mobile]: #inserting
  [Mise à jour des données d'un service mobile]: #updating
  [Suppression des données d'un service mobile]: #deleting
  [Recherche d'un élément spécifique]: #lookup
  [Utilisation de données non typées]: #untyped
  [Liaison des données dans l'interface utilisateur]: #binding
  [Définition de la mise en page]: #layout
  [Définition de l'adaptateur]: #adapter
  [Utilisation de l'adaptateur]: #use-adapter
  [Authentification des utilisateurs]: #authentication
  [Mise en cache des jetons d'authentification]: #caching
  [Gestion des erreurs]: #errors
  [Personnalisation du client]: #customizing
  [Personnalisation des en-têtes de requête]: #headers
  [Personnalisation de la sérialisation]: #serialization
  [Étapes suivantes]: #next-steps
  [mobile-services-concepts]: ../includes/mobile-services-concepts.md
  [Créer une table]: http://go.microsoft.com/fwlink/p/?LinkId=298592
  [Schéma dynamique]: http://go.microsoft.com/fwlink/p/?LinkId=296271
  [MobileServiceClient]: http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
  [gson]: %20http://go.microsoft.com/fwlink/p/?LinkId=290801
  [MobileServiceTable]: http://go.microsoft.com/fwlink/p/?LinkId=296835
  [première surcharge]: http://go.microsoft.com/fwlink/p/?LinkId=296839
  [deuxième surcharge]: http://go.microsoft.com/fwlink/p/?LinkId=296840
  [**eq**]: http://go.microsoft.com/fwlink/p/?LinkId=298461
  [**ne**]: http://go.microsoft.com/fwlink/p/?LinkId=298462
  [**ge**]: http://go.microsoft.com/fwlink/p/?LinkId=298464
  [**le**]: http://go.microsoft.com/fwlink/p/?LinkId=298466
  [Explorer la richesse du modèle de requêtes de données client Android Mobile Services]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
  [1]: http://go.microsoft.com/fwlink/p/?LinkId=296296
  [2]: http://go.microsoft.com/fwlink/p/?LinkId=296313
  [Codes de contrôle ASCII C0 et C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
  [Interface de ligne de commande pour la gestion des tables Mobile Services]: http://www.windowsazure.com/fr-fr/manage/linux/other-resources/command-line-tools/#Mobile_Tables
  [MobileServicesJsonTable]: http://go.microsoft.com/fwlink/p/?LinkId=298733
  [3]: http://go.microsoft.com/fwlink/p/?LinkId=298535
  [Prise en main de l'authentification]: http://go.microsoft.com/fwlink/p/?LinkId=296316
  [portail de gestion Azure]: https://manage.windowsazure.com/
  [ici]: https://www.windowsazure.com/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
  [Personnalisation de la sérialisation avec la bibliothèque]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
  []: http://go.microsoft.com/fwlink/p/?LinkId=298735 "ici"
