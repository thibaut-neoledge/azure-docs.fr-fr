<properties
	pageTitle="Utilisation de la bibliothèque cliente Android Mobile Apps"
	description="Utilisation du kit de développement logiciel (SDK) Android pour Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="adrianha"/>


# Utilisation de la bibliothèque cliente Android pour Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous montre comment utiliser le Kit de développement logiciel (SDK) du client Android pour Mobile Apps afin d’implémenter des scénarios courants tels que :

- Interrogation des données (insertion, mise à jour et suppression).
- Authentification.
- Gestion des erreurs.
- Personnalisation du client.

Il propose également une exploration détaillée du code de client couramment utilisé dans la plupart des applications mobiles.

Ce guide est axé sur le kit de développement logiciel Android côté client. Pour plus d’informations sur les kits de développement côté serveur pour Mobile Apps, consultez les articles [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps][10] ou [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps][11].

## Documentation de référence

La [référence de l’API Javadocs][12] pour la bibliothèque cliente Android se trouve sur GitHub.

## Plateformes prises en charge

Le Kit de développement logiciel (SDK) Android pour Azure Mobile prend en charge les niveaux d’API 19 à 24 (KitKat jusqu’à Nougat).

L’authentification « serveur flux » utilise un mode d’affichage WebView pour l’interface utilisateur présentée. Si l’appareil n’est pas en mesure de présenter une interface utilisateur WebView, d’autres méthodes d’authentification sont alors nécessaires, ce qui sort du cadre de ce produit. Ce SDK ne convient pas au type Watch ou d’autres appareils restreints similaires.

## Configuration et conditions préalables

Terminez le [didacticiel de démarrage rapide Mobile Apps](app-service-mobile-android-get-started.md). Cette tâche garantit que toutes les conditions préalables au développement d’Azure Mobile Apps ont été remplies. Le didacticiel de démarrage rapide vous aide également à configurer et à créer votre premier backend Mobile App.

Si vous décidez de ne pas suivre le didacticiel de démarrage rapide, effectuez les tâches suivantes :

- [créer un backend Mobile Apps][13] à utiliser avec votre application Android ;
- dans Android Studio, [mettre à jour les fichiers de construction Gradle](#gradle-build) ;
- [activer les autorisations Internet](#enable-internet).

###<a name="gradle-build"></a>Mise à jour du fichier de construction Gradle

Modifiez les deux fichiers **build.gradle** :

1. ajoutez ce code au fichier **build.gradle** de niveau *Project* à l’intérieur de la balise *buildscript* :

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. Ajoutez ce code au fichier **build.gradle** de niveau *Module app* à l’intérieur de la balise *dependencies* :

		compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    La version la plus récente est la 3.1.0. Les versions prises en charge sont répertoriées [ici][14].

###<a name="enable-internet"></a>activer les autorisations Internet.

Pour accéder à Azure, l’autorisation INTERNET doit être activée sur votre application. Si ce n’est pas déjà fait, ajoutez la ligne de code suivante à votre fichier **AndroidManifest.xml** :

	<uses-permission android:name="android.permission.INTERNET" />

## Présentation approfondie des principes de base

Cette section présente une partie du code de l’application de démarrage rapide qui exécute Azure Mobile Apps.

###<a name="data-object"></a>Définition des classes de données client

Pour accéder aux données à partir des tables SQL Azure, définissez des classes de données client qui correspondent aux tables du backend Mobile Apps. Les exemples de cette rubrique reposent sur une table nommée **ToDoItem**, qui contient les colonnes suivantes :

- id
- texte
- terminé

L’objet côté client typé correspondant :

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Le code réside dans un fichier appelé **ToDoItem.java**.

Si votre table SQL Azure contient davantage de colonnes, vous devez ajouter les champs correspondants à cette classe. Pour exemple, si l’objet de transfert de données (DTO, data transfer object) comporte une colonne d’entiers Priority, vous pouvez ajouter ce champ, ainsi que ses méthodes getter et setter :

	private Integer priority;

	/**
	* Returns the item priority
	*/
	public Integer getPriority() {
	    return mPriority;
	}
	
	/**
	* Sets the item priority
	*
	* @param priority
	*            priority to set
	*/
	public final void setPriority(Integer priority) {
	    mPriority = priority;
	}

Pour savoir comment créer des tables supplémentaires dans votre backend Mobile Apps, consultez [Définir un contrôleur de table][15] \(backend .NET) ou [Procédure : définir des tables à l’aide d’un schéma dynamique][16] \(backend Node.js). Pour un backend Node.js, vous pouvez également utiliser les **Easy Tables** dans le [portail Azure].

###<a name="create-client"></a>Création du contexte client

Le code suivant permet de créer l’objet **MobileServiceClient** utilisé pour accéder à votre backend Mobile Apps. Le code est inséré dans la méthode `onCreate` de la classe **Activity** spécifiée dans le fichier *AndroidManifest.xml* comme action **MAIN** dans la catégorie **LAUNCHER**. Dans le code de démarrage rapide, il est placé dans le fichier **ToDoActivity.java**.

		MobileServiceClient mClient = new MobileServiceClient(
			"MobileAppUrl", // Replace with the Site URL
			this)

Dans ce code, remplacez `MobileAppUrl` par l’URL de votre backend Mobile Apps, qui se trouve dans le panneau de votre backend Mobile Apps du [portail Azure]. Pour pouvoir compiler cette ligne de code, vous devez également ajouter l’instruction **import** suivante :

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Procédure : création d'une référence de table

La façon la plus simple d’interroger des données ou de les modifier dans le backend consiste à utiliser le *modèle de programmation typé*, car Java comporte des types forts. Ce modèle fournit une sérialisation et une désérialisation transparentes de JSON en utilisant la bibliothèque [gson][3] pendant l’envoi des données entre les objets client et les tables dans le backend Azure SQL.

Pour accéder à une table, vous devez d’abord créer un objet [MobileServiceTable][8] en appelant la méthode **getTable** sur le [MobileServiceClient][9]. Cette méthode comporte deux surcharges :

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

Dans le code suivant, **mClient** est une référence à votre objet MobileServiceClient. La première surcharge est utilisée quand le nom de la classe et le nom de la table sont identiques. Elle est reprise dans le didacticiel de démarrage rapide :

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

La deuxième surcharge est utilisée quand le nom de la table est différent du nom de la classe : le premier paramètre correspond au nom de la table.

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Liaison des données dans l’interface utilisateur

La liaison des données nécessite trois composants :

- la source de données ;
- la mise en page à l’écran ;
- l’adaptateur qui lie ces deux éléments.

Dans notre exemple de code, nous renvoyons les données de la table SQL Azure Mobile Apps **ToDoItem** dans un tableau. Cette activité est un cas de figure très courant pour les applications de données. Les requêtes de base de données renvoient souvent une suite de lignes que le client récupère dans une liste ou un tableau. Dans cet exemple, le tableau est la source des données.

Ce code spécifie une mise en page à l'écran qui définit la façon dont les données sont affichées sur l'appareil. Les deux sont liés par un adaptateur, qui dans ce code est une extension de la classe **ArrayAdapter&lt;ToDoItem&gt;**.

#### <a name="layout"></a>Procédure de définition de la mise en page

La mise en page est définie par plusieurs éléments de code XML. En nous basant sur une mise en page existante, le code qui suit représente la vue **ListView** que nous souhaitons remplir avec les données du serveur.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

Dans le code précédent, l'attribut *listitem* spécifie l'ID de la mise en page de chaque ligne dans la liste. Ce code spécifie une case à cocher ainsi que le texte associé, et il est instancié une fois pour chaque élément dans la liste. Cette mise en page n’affiche pas le champ **id** ; une mise en page plus complexe spécifierait d’autres champs dans l’affichage. Le code se trouve dans le fichier **row\_list\_to\_do.xml**.

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


#### <a name="adapter"></a>Procédure de définition de l’adaptateur

Comme la source de données de notre vue est un tableau de **ToDoItem**, nous créons une sous-classe de notre adaptateur à partir de la classe **ArrayAdapter&lt;ToDoItem&gt;**. Cette sous-classe produit une vue pour chaque élément **ToDoItem** utilisant la mise en page **row\_list\_to\_do**.

Dans notre code, nous définissons la classe suivante, qui est une extension de la classe **ArrayAdapter&lt;E&gt;** :

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Ignorez la méthode **getView** de l'adaptateur. Par exemple :

    @Override
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

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

Nous créons une instance de cette classe dans notre activité, comme suit :

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Le deuxième paramètre du constructeur ToDoItemAdapter est une référence à la mise en page. Nous pouvons maintenant instancier l’élément **ListView** et attribuez l’adaptateur à **ListView**.

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>Structure de l'API

Les opérations de table Mobile Apps et les appels d’API personnalisées sont asynchrones. Utilisez les objets [Future] et [AsyncTask] pour les méthodes asynchrones impliquant des requêtes, des insertions, des mises à jour et des suppressions. Cela permet d’effectuer plus facilement plusieurs opérations sur un thread d’arrière-plan sans avoir à gérer plusieurs rappels imbriqués.

Pour obtenir un exemple, ouvrez le fichier **ToDoActivity.java** dans le projet de démarrage rapide Android à partir du [portail Azure].

#### <a name="use-adapter"></a>Procédure d’utilisation de l'adaptateur

Vous êtes désormais prêt à utiliser la liaison des données. Le code suivant montre comment obtenir les éléments du tableau et remplit l’adaptateur local en utilisant les éléments renvoyés.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

Appelez l’adaptateur chaque fois que vous modifiez le tableau **ToDoItem**. Comme les modifications se font enregistrement par enregistrement, vous ne gérez qu’une seule ligne, et non une collection. Lorsque vous insérez un élément, appelez la méthode **add** de l'adaptateur et lorsque vous supprimez un élément, appelez la méthode **remove**.

##<a name="querying"></a>Procédure : interrogation des données à partir de votre back-end Mobile Apps

Cette section explique comment émettre des requêtes à destination du backend Mobile Apps, qui inclut les tâches suivantes :

- [Retourner tous les éléments]
- [Filtrer les données renvoyées]
- [Trier les données renvoyées]
- [Renvoyer les données de pages]
- [Sélectionner des colonnes spécifiques]
- [Concaténation de méthodes de requête](#chaining)

### <a name="showAll"></a>Procédure de renvoi de tous les éléments d’une table

La requête suivante renvoie tous les éléments de la table **ToDoItem**.

	List<ToDoItem> results = mToDoTable.execute().get();

La variable *results* renvoie l’ensemble de résultats de la requête sous forme de liste.

### <a name="filtering"></a>Procédure : filtrage des données renvoyées

L’exécution de la requête suivante retourne tous les éléments de la table **ToDoItem** dont le paramètre **complete** est égal à **false**.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

**mToDoTable** est la référence à la table des services mobiles que vous venez de créer.

Définissez un filtre en appelant la méthode **where** sur la référence de table. La méthode **where** est suivie d’une méthode **field**, suivie d’une méthode qui spécifie le prédicat logique. Méthodes de prédicat possibles : **eq** (égal à), **ne** (différent de), **gt** (supérieur à), **ge** (supérieur ou égal à), **lt** (inférieur à), **le** (inférieur ou égal à). Ces méthodes vous permettent de comparer les champs de nombre et de chaîne à des valeurs spécifiques.

Vous pouvez activer des filtres sur les dates. Les méthodes suivantes vous permettent de comparer le champ de date complet ou des parties de la date : **year**, **month**, **day**, **hour**, **minute** et **second**. L’exemple suivant ajoute un filtre pour les éléments dont la date d’échéance *due date* est égale à 2013.

	mToDoTable.where().year("due").eq(2013).execute().get();

Les méthodes suivantes prennent en charge des filtres complexes sur les champs de chaîne : **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** et **length**. L’exemple suivant filtre les lignes de la table dans lesquelles la colonne *text* commence par « PRI0 ».

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

Les méthodes d’opérateur suivantes sont prises en charge sur les champs numériques : **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** et **round**. L’exemple suivant filtre les lignes de la table dans lesquelles la colonne **duration** est un nombre pair.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Vous pouvez combiner les prédicats avec les méthodes logiques **and**, **or** et **not**. L’exemple suivant combine deux des exemples précédents.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

Regrouper et imbriquer des opérateurs logiques :

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

Pour obtenir plus de détails et des exemples de filtres, consultez la page [Exploring the richness of the Android client query model](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (Exploration de la richesse du modèle de requête client Android).

### <a name="sorting"></a>Procédure : tri des données renvoyées

Le code qui suit renvoie tous les éléments de la table **ToDoItem** triés par ordre croissant sur le champ *text*. *mToDoTable* est la référence à la table de back-end créée précédemment :

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Le premier paramètre de la méthode **orderBy** est une chaîne équivalente au nom du champ sur lequel effectuer le tri. Le second paramètre utilise l’énumération **QueryOrder** pour spécifier si le tri doit être croissant ou décroissant. Si vous filtrez avec la méthode ***where***, la méthode ***where*** doit être appelée avant la méthode ***orderBy***.

### <a name="paging"></a>Procédure de renvoi de données dans les pages

Le premier exemple présente comment sélectionner les cinq premiers éléments d'une table. Cette requête renvoie les éléments d’une table **ToDoItem**. **mToDoTable** est la référence à la table de back-end créée précédemment :

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Voici maintenant une requête qui ignore les cinq premiers éléments, puis renvoie les cinq suivants :

	mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Procédure : sélection de colonnes spécifiques

Le code qui suit illustre comment renvoyer tous les éléments d’une table **ToDoItems**, mais uniquement en affichant les champs **complete** et **text**. **mToDoTable** est la référence à la table de back-end créée précédemment.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Les paramètres associés à la fonction select sont les noms de chaîne des colonnes de table à renvoyer.

La méthode **select** doit suivre les méthodes telles que **where** et **orderBy**. Elle peut être suivie de méthodes de pagination telles que **top**.

### <a name="chaining"></a>Procédure de concaténation de méthodes de requête

Les méthodes utilisées dans les requêtes de tables de backend peuvent être concaténées. La concaténation des méthodes de requêtes vous permet de sélectionner des colonnes spécifiques de lignes filtrées, qui sont triées et paginées. Vous pouvez créer des filtres logiques complexes. Chaque méthode de requête retourne un objet de requête. Pour mettre fin à la série de méthodes et exécuter la requête, appelez la méthode **execute**. Par exemple :

	mToDoTable.where()
        .year("due").eq(2013)
		.and().startsWith("text", "PRI0")
		.or().field("duration").gt(10)
		.orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
		.execute().get();

Les méthodes de requête concaténées doivent être classées comme suit :

1. Filtrage des méthodes (**where**).
2. Tri des méthodes (**orderBy**).
3. Sélection des méthodes (**select**).
4. pagination des méthodes (**skip** et **top**).

##<a name="inserting"></a>Procédure : insertion de données dans le back-end

Créez une instance de la classe *ToDoItem* et définissez ses propriétés.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

Utilisez ensuite **insert()** pour insérer un objet :

	ToDoItem entity = mToDoTable.insert(item).get();

L’entité renvoyée correspond aux données insérées dans la table principale, incluant l’ID et toutes les autres valeurs définies sur le backend.

Les tables Mobile Apps nécessitent une colonne de clé primaire nommée **id**. Par défaut, cette colonne est une chaîne. La valeur par défaut de la colonne ID est un GUID. Vous pouvez fournir d’autres valeurs uniques, telles que des adresses de messagerie ou des noms d’utilisateurs. Lorsqu’aucune valeur d’ID de chaîne n’est fournie pour un enregistrement inséré, le backend génère une nouvelle valeur GUID.

Les valeurs d’ID de chaîne offrent les avantages suivants :

+ Les ID peuvent être générés sans effectuer d’aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d’ID s’intègrent mieux à la logique d’une application.

Les valeurs d’ID de chaîne sont **obligatoires** pour la prise en charge de la synchronisation hors connexion.

##<a name="updating"></a>Mise à jour des données dans une application mobile

Pour mettre à jour les données d’une table, transmettez le nouvel objet à la méthode **update()**.

    mToDoTable.update(item).get();

Dans cet exemple, l’*élément* est une référence à une ligne dans la table *ToDoItem*, qui a été modifiée. La ligne avec le même **id** est mise à jour.

##<a name="deleting"></a>Suppression de données dans une application mobile

Le code suivant montre comment supprimer les données d’une table en spécifiant l’objet de données.

	mToDoTable.delete(item);

Vous pouvez également supprimer un élément en spécifiant le champ **id** de la ligne à supprimer.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);

##<a name="lookup"></a>Recherche d’un élément spécifique

Recherchez un élément avec un champ **id** spécifique avec la méthode **lookUp()** :

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>Procédure : utilisation de données non typées

Le modèle de programmation non typé vous offre un contrôle total de la sérialisation JSON. Il existe certains scénarios courants où vous pouvez utiliser un modèle de programmation non typé. Par exemple, si la table de votre backend contient un grand nombre de colonnes et que vous n’avez besoin de faire référence qu’à quelques-unes d’entre elles. Avec le modèle typé, vous devez définir toutes les colonnes de la table Mobile Apps dans votre classe de données.

La plupart des appels d'API permettant d'accéder aux données sont similaires à ceux des appels de programmation typés. La principale différence est que dans le modèle non typé, vous appelez des méthodes dans l'objet **MobileServiceJsonTable** plutôt que dans l'objet **MobileServiceTable**.

### <a name="json_instance"></a>Procédure de création d'une instance de table non typée

Comme pour le modèle typé, vous commencez par obtenir une référence de table, mais il s'agit dans ce cas d'un objet **MobileServicesJsonTable**. Obtenez la référence en appelant la méthode **getTable** sur une instance du client :

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Une fois que vous avez créé une instance de l’objet **MobileServiceJsonTable**, celui-ci a pratiquement la même API disponible qu’avec le modèle de programmation typé. Dans certains cas, les méthodes utilisent un paramètre non typé au lieu d’un paramètre typé.

### <a name="json_insert"></a>Procédure d’insertion dans une table non typée

Le code suivant vous explique comment effectuer une insertion. La première étape est de créer un objet [JsonObject][1], qui fait partie de la bibliothèque [gson][3].

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

Ensuite, utilisez **insert()** pour insérer l’objet non typé dans la table.

    mJsonToDoTable.insert(jsonItem).get();

Si vous avez besoin d’obtenir l’ID de l’objet inséré, utilisez la méthode **getAsJsonPrimitive()**.

	jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Procédure de suppression dans une table non typée

Le code qui suit montre comment supprimer une instance, dans ce cas la même instance de l'objet **JsonObject** créé dans l'exemple *insert* précédent. Le code est identique à la casse typée, mais la méthode a une signature différente puisqu’elle fait référence à un objet **JsonObject**.

         mToDoTable.delete(jsonItem);

Vous pouvez aussi directement supprimer une instance avec son ID :

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

Les mêmes méthodes de filtrage et de pagination disponibles pour le modèle typé le sont également pour le modèle non typé.

##<a name="custom-api"></a>Procédure : appel d’une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON.

À partir d’un client Android, vous appelez la méthode **invokeApi** pour appeler le point de terminaison de l’API personnalisée. L’exemple suivant montre comment appeler un point de terminaison d’API nommé **completeAll**, qui retourne une classe de collection nommée **MarkAllResult**.

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

La méthode **invokeApi** est appelée sur le client pour envoyer une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles. Les autres versions de la méthode **invokeApi** vous permettent éventuellement d’envoyer un objet dans le corps de la demande, de spécifier la méthode HTTP et d’envoyer des paramètres de requête avec la demande. Des versions non typées de la méthode **invokeApi** sont également fournies.

##<a name="authentication"></a>Ajout d’une authentification à votre application

Les didacticiels décrivent déjà en détail l’ajout de ces fonctionnalités.

App Service prend en charge [l’authentification des utilisateurs d’applications](app-service-mobile-android-get-started-users.md) via divers fournisseurs d’identité externes : Facebook, Google, compte Microsoft, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour implémenter des règles d’autorisation dans votre serveur principal.

Deux flux d'authentification sont pris en charge : un flux **serveur** et un flux **client**. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface Web des fournisseurs d’identité. Aucun SDK supplémentaires n’est requis pour implémenter l’authentification de flux serveur. L’authentification de flux serveur ne fournit pas une intégration approfondie dans l’appareil mobile et elle est uniquement recommandée dans des scénarios de preuve de concept.

Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l'authentification unique, car il repose sur des Kits de développement logiciel (SDK) proposés par le fournisseur d’identité. Par exemple, vous pouvez intégrer le SDK Facebook dans votre application mobile. Le client mobile permute dans l’application Facebook et confirme votre ouverture de session avant de rebasculer vers votre application mobile.

Quatre étapes sont nécessaires pour activer l'authentification dans votre application :

- inscription de votre application pour l’authentification auprès d’un fournisseur d’identité ;
- configuration de votre backend App Service ;
- restriction des autorisations de table aux utilisateurs authentifiés uniquement sur le backend App Service ;
- ajout de code d’authentification à votre application.

Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser le SID d’un utilisateur authentifié pour modifier des demandes. Pour plus d’informations, consultez la rubrique [Prise en main de l’authentification] et les instructions pour le Kit de développement logiciel (SDK) Server.

### <a name="caching"></a>Procédure d’ajout de code d’authentification à votre application

Le code suivant démarre un processus de connexion du flux de serveur à l’aide du fournisseur Google :

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obtenez l’ID de l’utilisateur connecté à partir d’un **MobileServiceUser** à l’aide de la méthode **getUserId**. Pour obtenir un exemple de la manière d’utiliser Futures pour appeler les API de connexion asynchrones, consultez la rubrique [Prise en main de l’authentification].

### <a name="caching"></a>Procédure de mise en cache des jetons d'authentification

Pour cela, vous devez stocker localement l'ID utilisateur et le jeton d'authentification sur l'appareil. Au prochain démarrage de l'application, vous vérifiez le cache, et si ces valeurs sont présentes, vous pouvez ignorer la procédure d'ouverture de session et rafraîchir le client avec ces données. Mais ces données sont sensibles et elles doivent être stockées sous forme chiffrée au cas où le téléphone serait volé.

Vous pouvez obtenir un exemple complet illustrant la mise en cache des jetons d’authentification dans la section [Mise en cache de jetons d’authentification sur le client][7].

Lorsque vous tentez d’utiliser un jeton qui a expiré, vous recevez un message *401 - Connexion non autorisée*. Vous pouvez gérer les erreurs d’authentification à l’aide de filtres. Les filtres interceptent les requêtes adressées au backend App Service. Le code de filtre teste la réponse pour une erreur 401, déclenche le processus de connexion, puis reprend la demande qui a généré l’erreur 401.

## <a name="adal"></a>Procédure : authentifier des utilisateurs avec la bibliothèque Active Directory Authentication Library

Vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL) pour authentifier des utilisateurs dans votre application à l’aide d’Azure Active Directory. L’utilisation d’une connexion par flux de client est souvent préférable à l’utilisation des méthodes `loginAsync()`, car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le backend de votre application mobile pour utiliser la connexion AAD, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Bien que cette étape soit facultative, veillez à inscrire une application cliente native.

2. Installez la bibliothèque ADAL en modifiant votre fichier build.gradle pour inclure les définitions suivantes :

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Ajoutez le code suivant à votre application, en procédant aux remplacements suivants :

* Remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez approvisionné votre application. Vous devez utiliser le format https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée depuis l’onglet Domaine de votre Azure Active Directory dans le [portail Azure Classic].

* Remplacez **INSERT-RESOURCE-ID-HERE** par l’ID client du serveur principal de votre application mobile. Vous pouvez obtenir l’ID client sur le portail, sous l’onglet **Avancé** du menu **Paramètres Azure Active Directory**.

* Remplacez **INSERT-CLIENT-ID-HERE** par l’ID client que vous avez copié depuis l’application cliente native.

* Remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison _/.auth/login/done_ de votre site, en utilisant le modèle HTTPS. Cette valeur doit être similaire à \_https://contoso.azurewebsites.net/.auth/login/done_.

		private AuthenticationContext mContext;

		private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
		}

		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
			public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
		};

		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
		}

## Procédure : ajout d’une notification Push à votre application

Vous pouvez [lire une présentation][6] qui explique comment Microsoft Azure Notification Hubs prend en charge un large éventail de notifications Push. Dans [ce didacticiel][5], une notification Push est envoyée à tous les appareils à chaque fois qu’un enregistrement est inséré.

## Procédure : ajout d’une synchronisation hors connexion à votre application

Le didacticiel de démarrage rapide contient le code qui implémente la synchronisation hors connexion. Recherchez le code précédé de commentaires :

	// Offline Sync

En supprimant les commentaires sur les lignes de code suivantes, vous pouvez implémenter la synchronisation hors connexion et ajouter un code similaire à un autre code Mobile Apps.

##<a name="customizing"></a>Procédure : personnalisation du client

Plusieurs méthodes permettent de personnaliser le comportement par défaut du client.

### <a name="headers"></a>Procédure de personnalisation des en-têtes de requête

Configurez un **ServiceFilter** pour ajouter un en-tête HTTP personnalisé à chaque requête :

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

### <a name="serialization"></a>Procédure : personnalisation de la sérialisation

Le client part du principe que les noms de table, les noms de colonne et les types de données sur le backend correspondent tous exactement aux objets de données définis dans le client. Pour diverses raisons, les noms sur le serveur et sur le client peuvent ne pas correspondre. Dans ce scénario, vous pouvez effectuer les personnalisations suivantes :

- Les noms de colonnes utilisés dans la table App Service ne correspondent pas à ceux que vous utilisez dans le client.
- Utiliser une table App Service qui porte un autre nom que celui de la classe sur laquelle elle est mappée dans le client.
- Activer la mise en majuscules automatique des propriétés.
- Ajouter des propriétés complexes à un objet.

### <a name="columns"></a>Procédure de mappage de noms de client et de serveur différents

Supposons que le code de votre client Java utilise des noms de style Java standard pour les propriétés d'objets **ToDoItem**, comme ce qui suit :

- mId
- mText
- mComplete
- mDuration

Sérialisez les noms du client en noms JSON correspondant aux noms des colonnes de la table **ToDoItem** sur le serveur. Le code suivant utilise la bibliothèque [gson][3] pour annoter les propriétés :

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Procédure de mappage de noms de table différents entre le client et le serveur principal

Mappez le nom de la table cliente à un autre nom de table Mobile Services en utilisant un remplacement de la méthode [getTable()][4] \:

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### </a>Procédure d’automatisation du mappage des noms de colonnes<a name="conversions">

Vous pouvez spécifier une stratégie de conversion qui s’applique à chaque colonne à l’aide de l’API [gson][3]. La bibliothèque cliente Android utilise [gson][3] en arrière-plan pour sérialiser les objets Java en données JSON, qui sont ensuite envoyées à Azure App Service. Le code suivant utilise la méthode **setFieldNamingStrategy()** pour définir la stratégie. Cet exemple supprimera le premier caractère (un « m »), puis mettra le caractère suivant en minuscule, ce pour tous les noms de champ. Par exemple, il changera « mId » en « id ».

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        });

Ce code doit être exécuté avant d’utiliser **MobileServiceClient**.

### <a name="complex"></a>Procédure de stockage d'un objet ou d'une propriété de tableau dans une table

Jusqu'à présent, nos exemples de sérialisation impliquaient des types primitifs comme des entiers et des chaînes. Les types primitifs sont facilement sérialisés en JSON. Si vous souhaitez ajouter un objet complexe qui n’est pas automatiquement sérialisé en JSON, nous devons fournir la méthode de sérialisation JSON. Pour voir un exemple sur la façon d’effectuer une sérialisation JSON personnalisée, consultez le billet de blog [Personnalisation de la sérialisation avec la bibliothèque gson dans le client Android Mobile Services][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Retourner tous les éléments]: #showAll
[Filtrer les données renvoyées]: #filtering
[Trier les données renvoyées]: #sorting
[Renvoyer les données de pages]: #paging
[Sélectionner des colonnes spécifiques]: #selecting
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
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[portail Azure]: https://portal.azure.com
[Prise en main de l’authentification]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html

<!---HONumber=AcomDC_0928_2016-->