<properties
	pageTitle="Utilisation de la bibliothèque cliente Android pour Mobile Services"
	description="Découvrez comment utiliser un client Android pour Azure Mobile Services."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>


# Utilisation de la bibliothèque cliente Android pour Mobile Services

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Pour la version Mobile Apps équivalente de cette rubrique, consultez l’article [Utilisation de la bibliothèque cliente Android pour Mobile Apps](../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md).
 
Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du client Android pour Azure Mobile Services. Les scénarios traités incluent l'interrogation des données, l'insertion, la mise à jour et la suppression des données, l'authentification des utilisateurs, la gestion des erreurs et la personnalisation du client.

Si vous débutez avec Mobile Services, suivez le didacticiel de démarrage rapide [Prise en main de Mobile Services]. L’achèvement de ce didacticiel permet de s’assurer que vous avez installé Android Studio. Il vous permettra de configurer votre compte, de créer votre premier service mobile et d’installer le Kit de développement logiciel (SDK) Mobile Services, qui prend en charge Android 2.2 ou une version ultérieure, mais nous vous recommandons de concevoir votre application avec Android 4.2 ou une version ultérieure.

La référence Javadocs pour la bibliothèque de l’API cliente Android se trouve [ici](http://go.microsoft.com/fwlink/p/?LinkId=298735).

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Configuration et conditions préalables

Nous partons du principe que vous avez créé un service mobile et une table. Pour plus d'informations, consultez la page [Créer une table](http://go.microsoft.com/fwlink/p/?LinkId=298592). Dans le code utilisé dans cette rubrique, nous partons du principe que la table s'intitule *TodoItem* et contient les colonnes suivantes :

- id
- text
- terminé

L'objet côté client typé correspondant est le suivant :

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Lorsque le schéma dynamique est activé, Azure Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet de la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique](http://go.microsoft.com/fwlink/p/?LinkId=296271).

##<a name="create-client"></a>Procédure : création du client Mobile Services
Le code suivant permet de créer l'objet [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) utilisé pour accéder à votre service mobile. Le code est inséré dans la méthode `onCreate` de la classe Activity spécifiée dans *AndroidManifest.xml* comme action **MAIN** et catégorie **LAUNCHER**.

		MobileServiceClient mClient = new MobileServiceClient(
				"MobileServiceUrl", // Replace with the above Site URL
				"AppKey", 			// replace with the Application Key
				this)

Dans le code ci-dessus, remplacez `MobileServiceUrl` et `AppKey` par l'URL et la clé d'application du service mobile, dans cet ordre. Tous deux sont disponibles sur le portail Azure Classic. Pour y accéder, sélectionnez votre service mobile, puis cliquez sur *Tableau de bord*.

##<a name="instantiating"></a>Procédure : création d'une référence de table

La façon la plus simple d'interroger des données ou de les modifier dans le service mobile est d'utiliser le *modèle de programmation typé*, car Java comporte des types forts (nous aborderons plus tard le modèle *non typé*). Ce modèle fournit une sérialisation et une désérialisation transparentes de JSON en utilisant la bibliothèque [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) pendant l’envoi des données entre le client et le service mobile : le développeur n’a rien à faire, tout est géré par l’infrastructure.

La première chose à faire pour interroger des données ou les modifier est de créer un objet [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) en appelant la méthode **getTable** sur le [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Nous allons examiner deux surcharges de cette méthode :

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

Dans le code qui suit, *mClient* est une référence à votre client de service mobile.

La [première surcharge](http://go.microsoft.com/fwlink/p/?LinkId=296839) est utilisée lorsque le nom de la classe et le nom de la table sont identiques :

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


La [deuxième surcharge](http://go.microsoft.com/fwlink/p/?LinkId=296840) est utilisée lorsque le nom de la table est différent du nom du type.

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

## <a name="api"></a>Structure de l'API

Depuis la version 2.0 de la bibliothèque cliente, les opérations de table des services mobiles utilisent les objets [Future](http://developer.android.com/reference/java/util/concurrent/Future.html) et [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) dans toutes les opérations asynchrones, comme les méthodes impliquant des requêtes et des opérations telles que les insertions, les mises à jour et les suppressions. Cela permet d’effectuer plus facilement plusieurs opérations (sur un thread d’arrière-plan) sans avoir à gérer plusieurs rappels imbriqués.


##<a name="querying"></a>Procédure : interrogation des données à partir d'un service mobile

Cette section explique comment émettre des requêtes à destination du service mobile. Les sous-sections décrivent différents aspects tels que le tri, le filtrage et la pagination. Enfin, nous verrons comment concaténer ces différentes opérations.

### <a name="showAll"></a>Procédure de renvoi de tous les éléments d’une table

Le code qui suit renvoie tous les éléments de la table *ToDoItem*. Il les affiche dans l’interface utilisateur en ajoutant les éléments à un adaptateur. Ce code est similaire à celui qui se trouve dans le didacticiel de démarrage rapide [Prise en main de Mobile Services].

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {

					final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
               } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
               }
               return result;
            }
		}.execute();


Les requêtes comme celle-ci utilisent l’objet [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html).

La variable *result* renvoie le jeu de résultats de la requête et le code suivant l’instruction `mToDoTable.execute().get()` indique comment afficher chaque ligne.


### <a name="filtering"></a>Procédure de filtrage des données renvoyées

Le code qui suit renvoie tous les éléments de la table *ToDoItem* dont le champ *complete* est égal à *false*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result =
						mToDoTable.where().field("complete").eq(false).execute().get();
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);
					}
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();



Le filtre commence par un appel de méthode [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) sur la référence de table. Il est suivi par un appel de méthode [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869), suivi d'un appel de méthode qui spécifie le prédicat logique. Les méthodes de prédicat possibles sont [**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466), etc.

Ces éléments sont suffisants pour comparer des champs de nombre et de chaîne à des valeurs spécifiques. Mais vous pouvez aller bien plus loin.

Vous pouvez par exemple filtrer les dates. Vous pouvez comparer le champ de date en entier, mais aussi des parties de la date, avec les méthodes [**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) et [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472). L'extrait de code qui suit ajoute un filtre pour les éléments dont la date d'échéance *due date* est égale à 2013.

		mToDoTable.where().year("due").eq(2013).execute().get();

Vous pouvez concevoir de nombreux filtres complexes avec les méthodes [**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495) et [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496). L'extrait de code qui suit filtre les lignes de la table dans lesquelles la colonne *text* commence par « PRI0 ».

		mToDoTable.where().startsWith("text", "PRI0").execute().get();

Les champs numériques permettent aussi l'utilisation de filtres plus complexes, avec par exemple les méthodes [**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497), [**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506) et [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507). L'extrait de code qui suit filtre les lignes de la table dans lesquelles la colonne *duration* est un nombre pair.

		mToDoTable.where().field("duration").mod(2).eq(0).execute().get();


Vous pouvez combiner les prédicats avec les méthodes [**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) et [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515). Cet extrait de code combine deux des exemples ci-dessus.

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
					.execute().get();

Et vous pouvez regrouper et imbriquer les opérateurs logiques, comme dans cet extrait de code :

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))
					.execute().get();

Pour obtenir plus de détails et des exemples de filtres, consultez la page [Explorer la richesse du modèle de requêtes de données client Android Mobile Services](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Procédure de tri des données renvoyées

Le code qui suit renvoie tous les éléments de la table *ToDoItem* triés par ordre croissant sur le champ *text*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

		mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Le premier paramètre de la méthode [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) est une chaîne égale au nom du champ sur lequel effectuer le tri.

Le second paramètre utilise l'énumération [**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) pour spécifier si le tri doit être croissant ou décroissant.

Notez que si vous filtrez avec la méthode ***where***, celle-ci doit être appelée avant la méthode ***orderBy***.

### <a name="paging"></a>Procédure de renvoi de données dans les pages

Le premier exemple présente comment sélectionner les 5 premiers éléments d'une table. Cette requête renvoie les éléments d'une table *ToDoItem*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

       final MobileServiceList<ToDoItem> result = mToDoTable.top(5).execute().get();


Ensuite, nous définissons une requête qui ignore les 5 premiers éléments, puis renvoie les 5 suivants.

		mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Procédure de sélection de colonnes spécifiques

Le code qui suit illustre comment renvoyer tous les éléments d'une table *ToDoItems*, mais uniquement en affichant les champs *complete* et *text*. *mToDoTable* est la référence à la table de service mobile créée précédemment.

		mToDoTable.select("complete", "text").execute().get();


Ici, les paramètres associés à la fonction select sont les noms de chaîne des colonnes de table à renvoyer.

La méthode [**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) doit suivre les méthodes telles que [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) et [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313) si elles sont présentes. Elle peut être suivie de méthodes telles que [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731).

### <a name="chaining"></a>Procédure de concaténation de méthodes de requête

Les méthodes utilisées dans les requêtes de tables de service mobile peuvent être concaténées. Vous pouvez ainsi sélectionner des colonnes spécifiques de lignes filtrées, qui sont triées et paginées. Vous pouvez créer des filtres logiques assez complexes.

Tout ceci fonctionne grâce aux méthodes de requête utilisées qui renvoient des objets [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551), qui à leur tour font l'objet de méthodes appelées. Pour mettre fin à la série de méthodes et exécuter la requête, appelez la méthode [**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554).

Voici un exemple de code dans lequel *mToDoTable* est une référence à la table du service mobile *ToDoItem*.

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)
					.execute().get();

La principale exigence lors du chaînage de méthodes est que la méthode *where* et les prédicats doivent figurer en premier. Après cela, vous pouvez appeler toutes les méthodes suivantes dans l'ordre qui convient le mieux à votre application.


##<a name="inserting"></a>Procédure : insertion de données dans un service mobile

Le code suivant montre comment insérer une nouvelle ligne dans une table.

Vous créez tout d'abord une instance de la classe *ToDoItem* et définissez ses propriétés.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

 Ensuite, vous exécutez le code suivant :

		// Insert the new item
	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();


Ce code insère le nouvel élément et l’ajoute à l’adaptateur afin qu’il l’affiche dans l’interface utilisateur.

Mobile Services prend en charge des valeurs de chaîne personnalisée uniques pour l’ID de la table. Cela permet aux applications d’utiliser des valeurs personnalisées telles que les adresses e-mail ou des noms d’utilisateur pour la colonne ID d’une table Mobile Services. Par exemple, si vous voulez identifier chaque enregistrement par une adresse électronique, vous pouvez utiliser l'objet JSON suivant.

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;

Si aucune valeur d'ID de chaîne n'est fournie lors de l'insertion de nouveaux enregistrements dans une table, Mobile Services génère une valeur d'ID unique.

La prise en charge des ID de chaîne fournit les avantages suivants aux développeurs :

+ Les ID peuvent être générés sans effectuer d'aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d'ID peuvent mieux s'intégrer à la logique d'une application.

Vous pouvez également utiliser des scripts serveur pour définir des valeurs d'ID. L’exemple de script ci-dessous génère un GUID personnalisé et lui attribue un nouvel ID d’enregistrement. Il est similaire à la valeur d’ID que génère Mobiles Services si vous n’avez pas transmis de valeur pour un ID d’enregistrement.

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

La valeur pour le `id` doit être unique et ne contenir aucun caractère présent dans les ensembles suivants :

+ Caractères de contrôle : [0x0000-0x001F] et [0x007F-0x009F]. Pour plus d'informations, consultez la page [Codes de contrôle ASCII C0 et C1].
+  Caractères imprimables : **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\** (0x005C), **`** (0x0060)
+  Les ID « . » et « .. »

Vous pouvez également utiliser des ID d'entier pour vos tables. Pour pouvoir utiliser un ID d'entier, vous devez créer votre table avec la commande `mobile table create` à l'aide de l'option `--integerId`. Cette commande s'utilise avec l'interface de ligne de commande (CLI) pour Azure. Pour plus d’informations sur l’utilisation de l’interface de ligne de commande, consultez la page [Interface de ligne de commande pour la gestion des tables Mobile Services].


##<a name="updating"></a>Mise à jour des données dans un service mobile

Le code suivant montre comment mettre à jour les données d'une table. Dans cet exemple, l’*élément* est une référence à une ligne dans la table *ToDoItem*, qui a été modifiée. La méthode suivante met à jour la table et l’adaptateur d’interface utilisateur.

	private void updateItem(final ToDoItem item) {
	    if (mClient == null) {
	        return;
	    }

	    new AsyncTask<Void, Void, Void>() {

	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
	}

##<a name="deleting"></a>Procédure : suppression de données dans un service mobile

Le code suivant montre comment supprimer les données d'une table. Il supprime un élément existant de la table ToDoItem avec la case à cocher **Terminé** activée dans l’interface utilisateur.

	public void checkItem(final ToDoItem item) {
		if (mClient == null) {
			return;
		}

		// Set the item as completed and update it in the table
		item.setComplete(true);

		new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(item);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            if (item.isComplete()) {
                                mAdapter.remove(item);
                            }
                            refreshItemsFromTable();
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
	}


Le code qui suit présente une autre manière d'effectuer cette opération. Il supprime un élément existant de la table ToDoItem en spécifiant la valeur dans le champ ID de la ligne à supprimer (qui est ici égal à 2FA404AB-E458-44CD-BC1B-3BC847EF0902). Dans une application réelle, vous devez chercher l’ID et le transmettre en tant que variable. Ici, pour simplifier les tests, vous pouvez accéder à votre service dans le portail Azure Classic, cliquer sur **Données** et copier un ID que vous souhaitez tester.

    public void deleteItem(View view) {

        final String ID = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mToDoTable.delete(ID);
                    runOnUiThread(new Runnable() {
                        public void run() {
                            refreshItemsFromTable();
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="lookup"></a>Recherche d’un élément spécifique
Il est possible de rechercher un élément spécifique par son *ID*, à l'inverse de la requête, qui génère une suite d'éléments qui répondent à certains critères. Le code qui suit montre comment effectuer cette recherche, avec une valeur *id* de `0380BAFB-BCFF-443C-B7D5-30199F730335`. Dans une application réelle, vous devez chercher l’ID et le transmettre en tant que variable. Ici, pour simplifier les tests, vous pouvez accéder à votre service dans le portail Azure Classic, cliquer sur l’onglet **Données** et copier un ID que vous souhaitez tester.

    /**
     * Lookup specific item from table and UI
     */
    public void lookup(View view) {

        final String ID = "0380BAFB-BCFF-443C-B7D5-30199F730335";
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final ToDoItem result = mToDoTable.lookUp(ID).get();
                    runOnUiThread(new Runnable() {
                        public void run() {
                            mAdapter.clear();
                            mAdapter.add(result);
                        }
               });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

##<a name="untyped"></a>Procédure : utilisation de données non typées

Le modèle de programmation non typé vous offre un contrôle total de la sérialisation JSON, ce dont vous aurez peut-être besoin dans certains scénarios, par exemple si la table de votre service mobile contient un grand nombre de colonnes et que vous n'avez besoin de faire référence qu'à quelques-unes d'entre elles. Avec le modèle typé, vous devez définir toutes les colonnes de la table du service mobile dans votre classe de données. Mais avec le modèle non typé, vous ne définissez que les colonnes dont vous avez besoin.

La plupart des appels d'API permettant d'accéder aux données sont similaires à ceux des appels de programmation typés. La principale différence est que dans le modèle non typé, vous appelez des méthodes dans l'objet **MobileServiceJsonTable** plutôt que dans l'objet **MobileServiceTable**.


### <a name="json_instance"></a>Procédure de création d'une instance de table non typée

Comme pour le modèle typé, vous commencez par obtenir une référence de table, mais il s'agit dans ce cas d'un objet [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733). Vous obtenez cette référence en appelant la méthode [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) sur une instance du client Mobile Services.

Tout d’abord, vous définissez la variable :

    /**
     * Mobile Service Json Table used to access untyped data
     */
    private MobileServiceJsonTable mJsonToDoTable;



Une fois que vous avez créé une instance du client Mobile Services dans la méthode **onCreate** (ici la variable *mClient*), vous créez une instance de **MobileServiceJsonTable**, avec le code qui suit.


            // Get the Mobile Service Json Table to use
            mJsonToDoTable = mClient.getTable("ToDoItem");

Une fois que vous avez créé une instance de **MobileServiceJsonTable**, vous pouvez appeler presque toutes les méthodes utilisables avec le modèle de programmation typé. Dans certains cas, les méthodes prennent un paramètre non typé, comme nous allons le voir dans les exemples qui suivent.

### <a name="json_insert"></a>Procédure d’insertion dans une table non typée

Le code suivant vous explique comment effectuer une insertion. La première étape est de créer un objet [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), qui fait partie de la bibliothèque <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>.

		JsonObject item = new JsonObject();
		item.addProperty("text", "Wake up");
		item.addProperty("complete", false);

L'étape suivante est l'insertion de l'objet. La fonction de rappel transmise à la méthode [**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) est une instance de la classe [**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532). Notez que le paramètre de la méthode *insert* est un objet JsonObject.

        // Insert the new item
        new AsyncTask<Void, Void, Void>() {

            @Override
            protected Void doInBackground(Void... params) {
                try {
                    mJsonToDoTable.insert(item).get();
                    refreshItemsFromTable();
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();


Si vous avez besoin d’obtenir l’ID de l’objet inséré, utilisez cet appel de méthode :

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Procédure de suppression dans une table non typée

Le code qui suit montre comment supprimer une instance, dans ce cas la même instance de l'objet **JsonObject** créé dans l'exemple *insert* précédent. Notez que le code est identique à la casse typée, mais la méthode a une signature différente puisqu’elle fait référence à un objet **JsonObject**.


         mToDoTable.delete(item);


Vous pouvez aussi directement supprimer une instance avec son ID :

		 mToDoTable.delete(ID);



### <a name="json_get"></a>Procédure de récupération de toutes les lignes d'une table non typée

Le code suivant montre comment récupérer toute une table. Étant donné que vous utilisez un tableau JSON, vous pouvez extraire uniquement certaines colonnes de la table, de manière sélective.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Vous pouvez filtrer, trier et paginer en concaténant des méthodes portant le même nom que celles utilisées dans le modèle de programmation typé.


##<a name="binding"></a>Liaison des données dans l’interface utilisateur

La liaison des données nécessite trois composants :

- la source de données ;
- la mise en page à l'écran ;
- l'adaptateur qui lie ces deux éléments.

Dans notre exemple de code, nous renvoyons les données de la table de service mobile *ToDoItem* dans un tableau. Il s'agit d'un modèle très courant pour les applications de données : les requêtes de base de données retournent généralement une collection de lignes que le client récupère dans une liste ou un tableau. Dans cet exemple, le tableau est la source des données.

Ce code spécifie une mise en page à l'écran qui définit la façon dont les données seront affichées sur l'appareil.

Les deux sont liés par un adaptateur, qui dans ce code est une extension de la classe *ArrayAdapter&lt;ToDoItem&gt;*.

### <a name="layout"></a>Procédure de définition de la mise en page

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


### <a name="adapter"></a>Procédure de définition de l’adaptateur

Comme la source de données de notre vue est un tableau de *ToDoItem*, nous créons une sous-classe de notre adaptateur à partir de la classe *ArrayAdapter&lt;ToDoItem&gt;*. Cette sous-classe produit une vue pour chaque élément *ToDoItem* utilisant la mise en page *row\_list\_to\_do*.

Dans notre code, nous définissons la classe suivante, qui est une extension de la classe *ArrayAdapter&lt;E&gt;* :

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Vous devez ignorer la méthode *getView* de l'adaptateur. À cet effet, vous pouvez utiliser cet exemple de code : les détails varient en fonction de votre application.

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

Nous créons une instance de cette classe dans notre activité, comme suit :

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Notez que le deuxième paramètre du constructeur ToDoItemAdapter est une référence à la mise en page. L'appel au constructeur est suivi du code ci-dessous, qui obtient tout d'abord une référence à **ListView**, puis appelle ensuite *setAdapter* pour se configurer lui-même pour utiliser l'adaptateur que nous venons de créer :

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>Procédure d’utilisation de l'adaptateur

Vous êtes désormais prêt à utiliser la liaison des données. Le code qui suit montre comment obtenir les éléments dans la table du service mobile, nettoyer l'adaptateur, puis appeler la méthode *add* de l'adaptateur pour y placer les éléments renvoyés.

    public void showAll(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final MobileServiceList<ToDoItem> result = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Vous devez également appeler l'adaptateur à chaque fois que vous modifiez la table *ToDoItem* si vous voulez afficher les résultats de cette modification. Comme les modifications se font enregistrement par enregistrement, vous ne composez qu'avec une seule ligne, et non une collection. Lorsque vous insérez un élément, vous appelez la méthode *add* de l'adaptateur et lorsque vous supprimez un élément, vous appelez la méthode *remove*.

##<a name="custom-api"></a>Procédure : appel d'une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON. Pour obtenir un exemple montrant comment créer une API personnalisée dans votre service mobile, consultez [Procédure : définition d’un point de terminaison dans une API personnalisée](mobile-services-dotnet-backend-define-custom-api.md).

[AZURE.INCLUDE [mobile-services-android-call-custom-api](../../includes/mobile-services-android-call-custom-api.md)]


##<a name="authentication"></a>Procédure : authentification des utilisateurs

Mobile Services prend en charge l'authentification et l'autorisation des utilisateurs d'applications via divers fournisseurs d'identité externes : Facebook, Google, Microsoft Account, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour implémenter des règles d’autorisation dans votre serveur principal. Pour plus d'informations, consultez la page [Prise en main de l'authentification](http://go.microsoft.com/fwlink/p/?LinkId=296316).

Deux flux d'authentification sont pris en charge : un flux *serveur* et un flux *client*. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) propres aux appareils et aux fournisseurs.

Trois étapes sont nécessaires pour activer l'authentification dans votre application :

- inscription de votre application pour l'authentification auprès d'un fournisseur et configuration de Mobile Services ;
- restriction des autorisations de table aux utilisateurs authentifiés uniquement ;
- Ajout de code d'authentification à votre application


Mobile Services prend en charge les fournisseurs d'identité suivants, utilisables pour l'authentification des utilisateurs :

- Compte Microsoft
- Facebook
- Twitter
- Google
- Azure Active Directory

Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l'ID d'un utilisateur authentifié pour modifier des demandes.

Ces deux premières tâches sont effectuées via le [portail Azure Classic](https://manage.windowsazure.com/). Pour plus d'informations, consultez la page [Prise en main de l'authentification](http://go.microsoft.com/fwlink/p/?LinkId=296316).

### <a name="caching"></a>Procédure d’ajout de code d’authentification à votre application

1.  Ajoutez les instructions d'importation suivantes au fichier d'activités de votre application.

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. Dans la méthode **onCreate** de la classe d’activité, ajoutez la ligne de code suivante après le code qui crée l’objet `MobileServiceClient` : nous supposons que la référence à l’objet `MobileServiceClient` est *mClient*.

	    // Login using the Google provider.

		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();
    		}
    	});

    Ce code authentifie l'utilisateur à l'aide de la connexion Google. Une boîte de dialogue affiche l'identifiant de l'utilisateur authentifié. Vous ne pouvez pas poursuivre sans authentification positive.

    > [AZURE.NOTE] Si vous utilisez un autre fournisseur d'identité que Google, remplacez la valeur passée à la méthode **login** ci-dessus par l’une des valeurs suivantes : _MicrosoftAccount_, _Facebook_, _Twitter_ ou _windowsAzureActiveDirectory_.


3. Lorsque vous exécutez l'application, connectez-vous avec le fournisseur d'identité choisi.


### <a name="caching"></a>Procédure de mise en cache des jetons d'authentification

Cette section vous montre comment mettre en cache un jeton d'authentification. Cela permet d'éviter que les utilisateurs s'identifient à nouveau si l'application est mise en hibernation alors que le jeton est encore valable.

Pour cela, vous devez stocker localement l'ID utilisateur et le jeton d'authentification sur l'appareil. Au prochain démarrage de l'application, vous vérifiez le cache, et si ces valeurs sont présentes, vous pouvez ignorer la procédure d'ouverture de session et rafraîchir le client avec ces données. Mais ces données sont sensibles et elles doivent être stockées sous forme chiffrée au cas où le téléphone serait volé.

L'extrait de code suivant montre comment obtenir un jeton pour se connecter à un compte Microsoft. Le jeton est mis en cache et rechargé si le cache est trouvé.

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
			    // Login using the Google provider.
				ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

		    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		    		@Override
		    		public void onFailure(Throwable exc) {
		    			createAndShowDialog("You must log in. Login Required", "Error");
		    		}
		    		@Override
		    		public void onSuccess(MobileServiceUser user) {
		    			createAndShowDialog(String.format(
		                        "You are now logged in - %1$2s",
		                        user.getUserId()), "Success");
		    			cacheUserToken(mClient.getCurrentUser());
		    			createTable();
		    		}
		    	});		}
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


Que se passe-t-il si votre jeton expire ? Dans ce cas, lorsque vous l'utilisez pour vous connecter, vous obtenez un message *401 de connexion non autorisée*. L'utilisateur doit alors se connecter pour obtenir de nouveaux jetons. Vous pouvez éviter d’écrire du code pour gérer cette opération partout où votre application appelle votre service mobile en utilisant des filtres, ce qui vous permet d'intercepter les appels envoyés à Mobile Services ainsi que les réponses émises par ce service. Le code de filtre teste ensuite la réponse pour une erreur 401, déclenche le processus de connexion si nécessaire, puis reprend la demande qui a généré l’erreur 401.


##<a name="customizing"></a>Procédure : personnalisation du client

Plusieurs méthodes permettent de personnaliser le comportement par défaut du client Mobile Services.

### <a name="headers"></a>Procédure de personnalisation des en-têtes de requête

Vous pouvez joindre un en-tête personnalisé à chaque demande sortante. Pour ce faire, vous pouvez configurer un **ServiceFilter** de la façon suivante :

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Procédure de personnalisation de la sérialisation

Par défaut, Mobile Services part du principe que les noms de table, les noms de colonnes et les types de données sur le serveur correspondent exactement à ce qui se trouve sur le client. Mais pour diverses raisons, les noms sur le serveur et sur le client peuvent ne pas correspondre. Vous pouvez, par exemple, modifier un client existant pour qu’il utilise Mobile Services plutôt que le produit d’un concurrent.

Vous pouvez effectuer les personnalisations suivantes :

- Les noms de colonnes utilisés dans la table du service mobile ne correspondent pas à ceux que vous utilisez dans le client
- Utiliser une table de service mobile qui porte un autre nom que celui de la classe sur laquelle elle est mappée dans le client
- Activer la mise en majuscules automatique des propriétés
- Ajouter des propriétés complexes à un objet

### <a name="columns"></a>Procédure de mappage de noms de client et de serveur différents

Supposons que le code de votre client Java utilise des noms de style Java standard pour les propriétés d'objets *ToDoItem*, comme ce qui suit.

- mId
- mText
- mComplete
- mDuration


Vous devez sérialiser les noms du client en noms JSON correspondant aux noms des colonnes de la table *ToDoItem* sur le serveur. Le code qui suit, qui utilise la bibliothèque <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a>, effectue cette sérialisation.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Procédure de mappage de noms de table différents entre le client et les services mobiles

Le mappage du nom de table client vers un autre nom de table de services mobiles est simple. Il suffit d'utiliser un des remplacements de la fonction <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>, comme dans le code qui suit.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### </a>Procédure d’automatisation du mappage des noms de colonnes<a name="conversions">

Le mappage des noms de colonnes pour une table avec peu de colonnes n'est pas compliqué, comme nous l'avons vu dans la section précédente. Mais supposons que notre table contient un grand nombre de colonnes, par exemple 20 ou 30. Il s'avère que nous pouvons appeler l'API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> et spécifier une stratégie de conversion qui s'applique à chaque colonne, ce qui évite d'avoir à annoter chaque nom de colonne individuellement.

Pour cela, nous utilisons la bibliothèque <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> que la bibliothèque cliente Android utilise en arrière-plan pour sérialiser les objets Java en données JSON, qui sont ensuite envoyées à Azure Mobile Services.

Le code qui suit utilise la méthode *setFieldNamingStrategy()*, dans laquelle nous définissons une méthode *FieldNamingStrategy()*. Cette méthode indique de supprimer le premier caractère (un « m »), puis de mettre le caractère suivant en minuscule, ce pour tous les noms de champ. Ce code permet aussi d'obtenir une impression lisible du résultat JSON.

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

### <a name="complex"></a>Procédure de stockage d'un objet ou d'une propriété de tableau dans une table

Jusqu'ici, tous nos exemples de sérialisation impliquaient des types primitifs comme les entiers et les chaînes, qui sont facilement sérialisés en JSON et dans la table du service mobile. Supposons que nous voulions ajouter un objet complexe à notre type de client, objet qui n'est pas automatiquement sérialisé vers JSON et la table. Par exemple, nous pourrions ajouter un tableau de chaînes à l'objet client. Nous devons alors spécifier comment effectuer la sérialisation et comment stocker le tableau dans la table du service mobile.

Pour voir un exemple, consultez le billet de blog <a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Personnalisation de la sérialisation avec la bibliothèque <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> dans le client </a> Android Mobile Services.

Cette méthode générale peut être utilisée dès lors que nous sommes en présence d'un objet complexe, qui ne peut pas être sérialisé de façon automatique vers JSON et la table du service mobile.

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Return all Items]: #showAll
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-android-get-started.md
[Codes de contrôle ASCII C0 et C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set

<!------HONumber=AcomDC_0309_2016-->