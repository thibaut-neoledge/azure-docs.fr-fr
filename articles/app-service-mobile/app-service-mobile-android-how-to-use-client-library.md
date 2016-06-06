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
	ms.date="05/24/2016"
	ms.author="ricksal"/>


# Utilisation de la bibliothèque cliente Android pour Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide décrit comment utiliser le kit de développement logiciel (SDK) client Android pour Mobile Apps pour implémenter des scénarios courants, tels que l’interrogation de données (insertion, mise à jour et suppression), l’authentification des utilisateurs, la gestion des erreurs et la personnalisation du client. Il propose également une exploration détaillée du code de client couramment utilisé dans la plupart des applications mobiles.

Ce guide est axé sur le kit de développement logiciel Android côté client. Pour plus d’informations sur les kits de développement côté serveur pour Mobile Apps, consultez les articles [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) ou [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Documentation de référence

La référence de l’API Javadocs pour la bibliothèque cliente Android se trouve [sur GitHub](http://azure.github.io/azure-mobile-apps-android-client/).

## Configuration et conditions préalables

Le kit de développement logiciel (SDK) Mobile Services pour Android prend en charge Android 2.2 ou version ultérieure, mais nous vous recommandons de concevoir votre application avec la version 4.2 minimum.

Suivez le didacticiel [Démarrage rapide de Mobile Apps](app-service-mobile-android-get-started.md) pour vous assurer que vous avez bien installé Android Studio. Ce didacticiel vous permettra de configurer votre compte et de créer votre premier backend Mobile Apps. Si vous procédez ainsi, vous pouvez ignorer le reste de cette section.

Si vous décidez de ne pas suivre le didacticiel de démarrage rapide et que vous souhaitez connecter une application Android à un backend Mobile Apps, vous devez effectuer les opérations suivantes :

- [créer un backend Mobile Apps](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend) à utiliser avec votre application Android (à moins que votre application n’en possède déjà un) ;
- dans Android Studio, [mettre à jour les fichiers de construction Gradle](#gradle-build) et
- [activer les autorisations Internet.](#enable-internet)

Après ces étapes, vous devez suivre la procédure décrite dans la section de présentation approfondie.

###<a name="gradle-build"></a>Mise à jour du fichier de construction Gradle

Modifiez les deux fichiers **build.gradle** :

1. ajoutez ce code au fichier **build.gradle** de niveau *Project* à l’intérieur de la balise *buildscript* :

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. Ajoutez ce code au fichier **build.gradle** de niveau *Module app* à l’intérieur de la balise *dependencies* :

		compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

	La version la plus récente est la 3.1.0. Les versions prises en charge sont répertoriées [ici](http://go.microsoft.com/fwlink/p/?LinkID=717034).

###<a name="enable-internet"></a>activer les autorisations Internet.
Pour accéder à Azure, l’autorisation INTERNET doit être activée sur votre application. Si ce n’est pas déjà fait, ajoutez la ligne de code suivante à votre fichier **AndroidManifest.xml** :

	<uses-permission android:name="android.permission.INTERNET" />

## Présentation approfondie des principes de base

Cette section présente une partie du code de l’application de démarrage rapide. Si vous n’avez pas suivi le didacticiel de démarrage rapide, vous devez ajouter ce code à votre application.

> [AZURE.NOTE] La chaîne « MobileServices » apparaît fréquemment dans le code : le code référence le SDK Mobile Apps ; il agit comme un simplement report temporaire du passé.


###<a name="data-object"></a>Définition des classes de données client

Pour accéder aux données à partir des tables SQL Azure, vous devez définir des classes de données client qui correspondent aux tables du backend Mobile Apps. Les exemples de cette rubrique reposent sur une table nommée *ToDoItem*, qui contient les colonnes suivantes :

- id
- text
- terminé

L’objet côté client typé correspondant est le suivant :

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

Le code réside dans un fichier appelé **ToDoItem.java**.

Si votre table SQL Azure contient davantage de colonnes, vous devez ajouter les champs correspondants à cette classe.

Pour exemple, si elle comporte une colonne d’entiers Priority, vous pouvez ajouter ce champ, ainsi que ses méthodes getter et setter :

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

Pour savoir comment créer des tables supplémentaires dans votre backend Mobile Apps, consultez [Définir un contrôleur de table](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller) (backend .NET) ou [Procédure : définir des tables à l’aide d’un schéma dynamique](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) (backend Node.js). Pour un backend Node.js, vous pouvez également utiliser les **Easy Tables** dans le [portail Azure].

###<a name="create-client"></a>Création du contexte client

Le code suivant permet de créer l’objet **MobileServiceClient** utilisé pour accéder à votre backend Mobile Apps. Le code est inséré dans la méthode `onCreate` de la classe **Activity** spécifiée dans le fichier *AndroidManifest.xml* comme action **MAIN** dans la catégorie **LAUNCHER**. Dans le code de démarrage rapide, il est placé dans le fichier **ToDoActivity.java**.

		MobileServiceClient mClient = new MobileServiceClient(
			"MobileAppUrl", // Replace with the above Site URL
			this)

Dans ce code, remplacez `MobileAppUrl` par l’URL de votre backend Mobile Apps, qui se trouve dans le panneau de votre backend Mobile Apps du [portail Azure](https://portal.azure.com/). Pour pouvoir compiler cette ligne de code, vous devez également ajouter l’instruction **import** suivante :

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Procédure : création d'une référence de table

La façon la plus simple d’interroger des données ou de les modifier dans le backend consiste à utiliser le *modèle de programmation typé*, car Java comporte des types forts (nous aborderons plus tard le modèle *non typé*). Ce modèle fournit une sérialisation et une désérialisation transparentes de JSON en utilisant la bibliothèque [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) pendant l’envoi des données entre les objets client et les tables dans le backend Azure SQL : le développeur n’a rien à faire, tout est géré par l’infrastructure.

Pour accéder à une table, vous devez d’abord créer un objet [MobileServiceTable](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html) en appelant la méthode **getTable** sur le [MobileServiceClient](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html). Cette méthode comporte deux surcharges :

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

Dans le code suivant, *mClient* est une référence à votre objet MobileServiceClient.

La [première surcharge](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html#getTable-java.lang.String-) est utilisée quand le nom de la classe et le nom de la table sont identiques. Elle est reprise dans le didacticiel de démarrage rapide :

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


La [deuxième surcharge](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html#getTable-java.lang.String-java.lang.Class-) est utilisée quand le nom de la table est différent du nom de la classe : le premier paramètre correspond au nom de la table.

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Liaison des données dans l’interface utilisateur

La liaison des données nécessite trois composants :

- la source de données ;
- la mise en page à l’écran ;
- l’adaptateur qui lie ces deux éléments.

Dans notre exemple de code, nous renvoyons les données de la table SQL Azure Mobile Apps *ToDoItem* dans un tableau. Il s’agit d’un modèle très courant pour les applications de données : les requêtes de base de données retournent souvent une collection de lignes que le client récupère dans une liste ou un tableau. Dans cet exemple, le tableau est la source des données.

Ce code spécifie une mise en page à l'écran qui définit la façon dont les données seront affichées sur l'appareil.

Les deux sont liés par un adaptateur, qui dans ce code est une extension de la classe *ArrayAdapter&lt;ToDoItem&gt;*.

#### <a name="layout"></a>Procédure de définition de la mise en page

La mise en page est définie par plusieurs éléments de code XML. En nous basant sur une mise en page existante, nous partons du principe que le code qui suit représente la vue **ListView** que nous souhaitons remplir avec les données du serveur.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

Dans ce code, l'attribut *listitem* spécifie l'ID de la mise en page de chaque ligne dans la liste. Voici ce code, qui spécifie une case à cocher et le texte associé. Ce contenu est créé pour chaque élément de la liste. Cette mise en page n’affiche pas le champ **id** ; une mise en page plus complexe spécifierait d’autres champs dans l’affichage. Le code se trouve dans le fichier **row\_list\_to\_do.xml**.

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

Comme la source de données de notre vue est un tableau de *ToDoItem*, nous créons une sous-classe de notre adaptateur à partir de la classe *ArrayAdapter&lt;ToDoItem&gt;*. Cette sous-classe produit une vue pour chaque élément *ToDoItem* utilisant la mise en page *row\_list\_to\_do*.

Dans notre code, nous définissons la classe suivante, qui est une extension de la classe *ArrayAdapter&lt;E&gt;* :

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


Vous devez ignorer la méthode *getView* de l'adaptateur. À cet effet, vous pouvez utiliser cet exemple de code : les détails varient en fonction de votre application.

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

Notez que le deuxième paramètre du constructeur ToDoItemAdapter est une référence à la mise en page. L'appel au constructeur est suivi du code ci-dessous, qui obtient tout d'abord une référence à **ListView**, puis appelle ensuite *setAdapter* pour se configurer lui-même pour utiliser l'adaptateur que nous venons de créer :

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>Structure de l'API

Les opérations de table Mobile Apps et les appels d’API personnalisés étant asynchrones, vous devez utiliser les objets [future](http://developer.android.com/reference/java/util/concurrent/Future.html) et [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) dans toutes les méthodes asynchrones impliquant des requêtes, des insertions, des mises à jour et des suppressions. Cela permet d’effectuer plus facilement plusieurs opérations sur un thread d’arrière-plan sans avoir à gérer plusieurs rappels imbriqués.

Pour visualiser la façon dont ces API asynchrones sont utilisées dans votre application Android et la présentation des données dans l’interface utilisateur, consultez le fichier **ToDoActivity.java** dans le projet de démarrage rapide Android à partir du [portail Azure].


#### <a name="use-adapter"></a>Procédure d’utilisation de l'adaptateur

Vous êtes désormais prêt à utiliser la liaison des données. Le code qui suit montre comment obtenir les éléments dans la table du service mobile, nettoyer l’adaptateur, puis appeler la méthode *add* de l’adaptateur pour y placer les éléments renvoyés.

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

Vous devez également appeler l'adaptateur à chaque fois que vous modifiez la table *ToDoItem* si vous voulez afficher les résultats de cette modification. Comme les modifications se font enregistrement par enregistrement, vous ne composez qu'avec une seule ligne, et non une collection. Lorsque vous insérez un élément, vous appelez la méthode *add* de l'adaptateur et lorsque vous supprimez un élément, vous appelez la méthode *remove*.

##<a name="querying"></a>Procédure : interrogation des données à partir de votre back-end Mobile Apps

Cette section explique comment émettre des requêtes à destination du backend Mobile Apps, qui inclut les tâches suivantes :

- [Retourner tous les éléments]
- [Filtrer les données renvoyées]
- [Trier les données renvoyées]
- [Renvoyer les données de pages]
- [Sélectionner des colonnes spécifiques]
- [Concaténation de méthodes de requête](#chaining)

### <a name="showAll"></a>Procédure de renvoi de tous les éléments d’une table

La requête suivante renvoie tous les éléments de la table *ToDoItem*.

	List<ToDoItem> results = mToDoTable.execute().get();

La variable *results* renvoie l’ensemble de résultats de la requête sous forme de liste.

### <a name="filtering"></a>Procédure : filtrage des données renvoyées

L’exécution de la requête suivante retourne tous les éléments de la table *ToDoItem* dont le paramètre *complete* est égal à *false*. Il s’agit du code déjà utilisé dans le didacticiel de démarrage rapide.

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* est la référence à la table des services mobiles que vous venez de créer.

Vous allez définir un filtre en appelant la méthode **where** sur la référence de table. Il est suivi par un appel de méthode **field**, suivi d’un appel de méthode qui spécifie le prédicat logique. Méthodes de prédicat possibles : **eq** (égal à), **ne** (différent de), **gt** (supérieur à), **ge** (supérieur ou égal à), **lt** (inférieur à), **le** (inférieur ou égal à), etc. Ces méthodes vous permettent de comparer les champs de nombre et de chaîne à des valeurs spécifiques.

Vous pouvez activer des filtres sur les dates. Les méthodes suivantes vous permettent de comparer le champ de date complet ou des parties de la date : **year**, **month**, **day**, **hour**, **minute** et **second**. L’exemple suivant ajoute un filtre pour les éléments dont la date d’échéance *due date* est égale à 2013.

	mToDoTable.where().year("due").eq(2013).execute().get();

Les méthodes suivantes prennent en charge des filtres complexes sur les champs de chaîne : **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** et **length**. L’exemple suivant filtre les lignes de la table dans lesquelles la colonne *text* commence par « PRI0 ».

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

Les méthodes d’opérateur suivantes sont prises en charge sur les champs numériques : **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** et **round**. L’exemple suivant filtre les lignes de la table dans lesquelles la colonne *duration* est un nombre pair.

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Vous pouvez combiner les prédicats avec les méthodes logiques **and**, **or** et **not**. L’exemple suivant combine deux des exemples ci-dessus.

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

Vous pouvez également regrouper et imbriquer les opérateurs logiques, comme suit :

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

Pour obtenir plus de détails et des exemples de filtres, consultez la page [Exploring the richness of the Android client query model](http://hashtagfail.com/post/46493261719/mobile-services-android-querying) (Exploration de la richesse du modèle de requête client Android).

### <a name="sorting"></a>Procédure : tri des données renvoyées

Le code qui suit renvoie tous les éléments de la table *ToDoItem* triés par ordre croissant sur le champ *text*. *mToDoTable* est la référence à la table de back-end créée précédemment :

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Le premier paramètre de la méthode **orderBy** est une chaîne équivalente au nom du champ sur lequel effectuer le tri.

Le second paramètre utilise l’énumération **QueryOrder** pour spécifier si le tri doit être croissant ou décroissant.

Notez que si vous filtrez avec la méthode ***where***, celle-ci doit être appelée avant la méthode ***orderBy***.

### <a name="paging"></a>Procédure de renvoi de données dans les pages

Le premier exemple présente comment sélectionner les 5 premiers éléments d'une table. Cette requête renvoie les éléments d’une table *ToDoItem*. *mToDoTable* est la référence à la table de back-end créée précédemment :

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Voici maintenant une requête qui ignore les 5 premiers éléments, puis renvoie les 5 suivants :

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>Procédure : sélection de colonnes spécifiques

Le code qui suit illustre comment renvoyer tous les éléments d’une table *ToDoItems*, mais uniquement en affichant les champs *complete* et *text*. *mToDoTable* est la référence à la table de back-end créée précédemment.

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


Ici, les paramètres associés à la fonction select sont les noms de chaîne des colonnes de table à renvoyer.

La méthode **select** doit suivre les méthodes telles que **where** et **orderBy** si elles sont présentes. Elle peut être suivie de méthodes de pagination telles que **top**.

### <a name="chaining"></a>Procédure de concaténation de méthodes de requête

Comme vous avez pu le voir, les méthodes utilisées dans les requêtes de tables de backend peuvent être concaténées. Vous pouvez ainsi sélectionner des colonnes spécifiques de lignes filtrées, qui sont triées et paginées. Vous pouvez créer des filtres logiques assez complexes.

Tout ceci fonctionne grâce aux méthodes de requête utilisées qui renvoient des objets **MobileServiceQuery&lt;T&gt;**, qui à leur tour font l’objet de méthodes appelées. Pour mettre fin à la série de méthodes et exécuter la requête, appelez la méthode **execute**.

Voici un exemple de code dans lequel *mToDoTable* est une référence à la table *ToDoItem*.

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

La principale exigence lors du chaînage de méthodes est que la méthode *where* et les prédicats doivent figurer en premier. Après cela, vous pouvez appeler toutes les méthodes suivantes dans l'ordre qui convient le mieux à votre application.


##<a name="inserting"></a>Procédure : insertion de données dans le back-end

Le code suivant montre comment insérer une nouvelle ligne dans une table.

Vous créez tout d'abord une instance de la classe *ToDoItem* et définissez ses propriétés.

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

Ensuite, vous exécutez le code suivant :

	ToDoItem entity = mToDoTable.insert(item).get();

L’entité renvoyée correspond aux données insérées dans la table principale, incluant l’ID et toutes les autres valeurs définies sur le backend.

Mobile Apps nécessite que chaque table comporte une colonne nommée **id** et utilisée pour indexer la table. Par défaut, cette colonne est un type de données chaîne, qui permet une prise en charge de la synchronisation hors connexion. La valeur par défaut de la colonne ID est un GUID, mais vous pouvez fournir d’autres valeurs uniques, telles que des adresses de messagerie ou des noms d’utilisateur. Lorsqu’aucune valeur d’ID de chaîne n’est fournie pour un enregistrement inséré, le backend génère une nouvelle valeur GUID.

Les valeurs d’ID de chaîne offrent les avantages suivants :

+ Les ID peuvent être générés sans effectuer d’aller-retour vers la base de données.
+ Il est plus facile de fusionner des enregistrements de plusieurs tables ou bases de données.
+ Les valeurs d’ID s’intègrent mieux à la logique d’une application.

##<a name="updating"></a>Mise à jour des données dans une application mobile

Le code suivant montre comment mettre à jour les données d'une table.

    mToDoTable.update(item).get();

Dans cet exemple, l’*élément* est une référence à une ligne dans la table *ToDoItem*, qui a été modifiée.

##<a name="deleting"></a>Suppression de données dans une application mobile

Le code suivant montre comment supprimer les données d’une table en spécifiant l’objet de données.

	mToDoTable.delete(item);

Vous pouvez également supprimer un élément en spécifiant le champ **id** de la ligne à supprimer.

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);


##<a name="lookup"></a>Recherche d’un élément spécifique

Ce code montre comment rechercher un élément avec un *id* spécifique.

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>Procédure : utilisation de données non typées

Le modèle de programmation non typé vous offre un contrôle total de la sérialisation JSON, ce dont vous aurez peut-être besoin dans certains scénarios, par exemple si la table de votre backend contient un grand nombre de colonnes et que vous n’avez besoin de faire référence qu’à quelques-unes d’entre elles. Avec le modèle typé, vous devez définir toutes les colonnes de la table Mobile Apps dans votre classe de données. Mais avec le modèle non typé, vous ne définissez que les colonnes dont vous avez besoin.

La plupart des appels d'API permettant d'accéder aux données sont similaires à ceux des appels de programmation typés. La principale différence est que dans le modèle non typé, vous appelez des méthodes dans l'objet **MobileServiceJsonTable** plutôt que dans l'objet **MobileServiceTable**.


### <a name="json_instance"></a>Procédure de création d'une instance de table non typée

Comme pour le modèle typé, vous commencez par obtenir une référence de table, mais il s'agit dans ce cas d'un objet **MobileServicesJsonTable**. Cette référence s’obtient en appelant la méthode **getTable** sur une instance du client, par exemple :

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Une fois que vous avez créé une instance de **MobileServiceJsonTable**, vous pouvez appeler presque toutes les méthodes utilisables avec le modèle de programmation typé. Dans certains cas, les méthodes prennent un paramètre non typé, comme nous allons le voir dans les exemples qui suivent.

### <a name="json_insert"></a>Procédure d’insertion dans une table non typée

Le code suivant vous explique comment effectuer une insertion. La première étape est de créer un objet [**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html), qui fait partie de la bibliothèque [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801).

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

L'étape suivante est l'insertion de l'objet.

    mJsonToDoTable.insert(jsonItem).get();


Si vous avez besoin d’obtenir l’ID de l’objet inséré, utilisez cet appel de méthode :

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>Procédure de suppression dans une table non typée

Le code qui suit montre comment supprimer une instance, dans ce cas la même instance de l'objet **JsonObject** créé dans l'exemple *insert* précédent. Notez que le code est identique à la casse typée, mais la méthode a une signature différente puisqu’elle fait référence à un objet **JsonObject**.


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

Vous pouvez filtrer, trier et paginer en concaténant des méthodes portant le même nom que celles utilisées dans le modèle de programmation typé.



##<a name="custom-api"></a>Procédure : appel d’une API personnalisée

Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON.

À partir d’un client Android, vous appelez la méthode **invokeApi** pour appeler le point de terminaison de l’API personnalisée. L’exemple suivant montre comment appeler un point de terminaison d’API nommé *completeAll*, qui retourne une classe de collection nommée MarkAllResult.

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

Deux flux d'authentification sont pris en charge : un flux *serveur* et un flux *client*. Le flux serveur fournit l'authentification la plus simple, car il repose sur l'interface d'authentification Web du fournisseur. Le flux client permet une intégration approfondie avec les fonctionnalités propres aux appareils, telles que l’authentification unique, car il repose sur des kits de développement logiciel (SDK) propres aux appareils et aux fournisseurs et requiert une programmation.

Trois étapes sont nécessaires pour activer l'authentification dans votre application :

- inscription de votre application pour l’authentification auprès d’un fournisseur et configuration de votre backend Mobile Apps ;
- restriction des autorisations de table aux utilisateurs authentifiés uniquement ;
- ajout de code d’authentification à votre application.

Vous pouvez définir des autorisations sur les tables pour limiter l'accès à certaines opérations aux seuls utilisateurs authentifiés. Vous pouvez également utiliser le SID d’un utilisateur authentifié pour modifier des demandes.

Ces deux premières tâches sont effectuées via le [portail Azure](https://portal.azure.com/). Pour plus d'informations, consultez la page [Prise en main de l'authentification].

### <a name="caching"></a>Procédure d’ajout de code d’authentification à votre application

Le code suivant démarre le processus de connexion du flux de serveur à l’aide du fournisseur Google :

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Vous pouvez obtenir l’ID de l’utilisateur connecté à partir d’un **MobileServiceUser** à l’aide de la méthode **getUserId**. Pour obtenir un exemple de la manière d’utiliser Futures pour appeler les API de connexion asynchrones, consultez la rubrique [Prise en main de l’authentification].


### <a name="caching"></a>Procédure de mise en cache des jetons d'authentification

Pour cela, vous devez stocker localement l'ID utilisateur et le jeton d'authentification sur l'appareil. Au prochain démarrage de l'application, vous vérifiez le cache, et si ces valeurs sont présentes, vous pouvez ignorer la procédure d'ouverture de session et rafraîchir le client avec ces données. Mais ces données sont sensibles et elles doivent être stockées sous forme chiffrée au cas où le téléphone serait volé.

Vous pouvez obtenir un exemple complet illustrant la mise en cache des jetons d’authentification dans la section [Mise en cache de jetons d’authentification sur le client](app-service-mobile-android-get-started-users.md#cache-tokens).

Lorsque vous tentez d’utiliser un jeton qui a expiré, vous obtenez un message *401 -Connexion non autorisée*. L'utilisateur doit alors se connecter pour obtenir de nouveaux jetons. Vous pouvez éviter d’écrire du code pour gérer cette opération partout où votre application appelle votre service mobile en utilisant des filtres, ce qui vous permet d'intercepter les appels envoyés à Mobile Services ainsi que les réponses émises par ce service. Le code de filtre teste ensuite la réponse pour une erreur 401, déclenche le processus de connexion si nécessaire, puis reprend la demande qui a généré l’erreur 401. Vous pouvez également inspecter le jeton pour en vérifier la date d’expiration.


## <a name="adal"></a>Procédure : authentifier des utilisateurs avec la bibliothèque Active Directory Authentication Library

Vous pouvez utiliser la bibliothèque d’authentification Active Directory (ADAL) pour authentifier des utilisateurs dans votre application à l’aide d’Azure Active Directory. Cette approche est souvent préférable à l’utilisation des méthodes `loginAsync()`, car elle offre une interface UX native plus simple et permet une personnalisation supplémentaire.

1. Si vous souhaitez configurer le serveur principal de votre application mobile pour utiliser la connexion AAD, suivez le didacticiel [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Bien que cette étape soit facultative, veillez à inscrire une application cliente native.

2. Installez la bibliothèque ADAL en modifiant votre fichier build.gradle pour inclure les éléments suivants :

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. Ajoutez le code ci-dessous à votre application, en procédant aux remplacements suivants :

* Remplacez **INSERT-AUTHORITY-HERE** par le nom du client dans lequel vous avez déployé votre application. Vous devez utiliser le format https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée depuis l’onglet Domaine de votre Azure Active Directory dans le [portail Azure Classic].

* Remplacez **INSERT-RESOURCE-ID-HERE** par l’ID client du serveur principal de votre application mobile. Vous pouvez obtenir cet identifiant sur le portail, sous l’onglet **Avancé** du menu **Paramètres Azure Active Directory**.

* Remplacez **INSERT-CLIENT-ID-HERE** par l’ID client que vous avez copié depuis l’application cliente native.

* Remplacez **INSERT-REDIRECT-URI-HERE** par le point de terminaison _/.auth/login/done_ de votre site, en utilisant le modèle HTTPS. Cette valeur doit être similaire à \__https://contoso.azurewebsites.net/.auth/login/done_.

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

Vous pouvez [lire une présentation](../notification-hubs/notification-hubs-overview.md#integration-with-app-service-mobile-apps) qui explique comment Microsoft Azure Notification Hubs prend en charge un large éventail de notifications Push.

Dans [ce didacticiel](app-service-mobile-android-get-started-push.md), une notification Push est transmise à chaque fois qu’un enregistrement est inséré.

## Procédure : ajout d’une synchronisation hors connexion à votre application
Le didacticiel de démarrage rapide contient le code qui implémente la synchronisation hors connexion. Recherchez le code précédé de commentaires du type :

	// Offline Sync

En supprimant les commentaires sur les lignes de code suivantes, vous pouvez implémenter la synchronisation hors connexion et ajouter un code similaire à un autre code Mobile Apps.

##<a name="customizing"></a>Procédure : personnalisation du client

Plusieurs méthodes permettent de personnaliser le comportement par défaut du client.

### <a name="headers"></a>Procédure de personnalisation des en-têtes de requête

Vous pouvez joindre un en-tête personnalisé à chaque demande sortante. Pour ce faire, vous pouvez configurer un **ServiceFilter** de la façon suivante :

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

Le client part du principe que les noms de table, les noms de colonne et les types de données sur le backend correspondent tous exactement aux objets de données définis dans le client. Mais pour diverses raisons, les noms sur le serveur et sur le client peuvent ne pas correspondre. Dans ce scénario, vous pouvez effectuer les personnalisations suivantes :

- Les noms de colonnes utilisés dans la table du service mobile ne correspondent pas à ceux que vous utilisez dans le client.
- Utiliser une table de service mobile qui porte un autre nom que celui de la classe sur laquelle elle est mappée dans le client.
- Activer la mise en majuscules automatique des propriétés.
- Ajouter des propriétés complexes à un objet.

### <a name="columns"></a>Procédure de mappage de noms de client et de serveur différents

Supposons que le code de votre client Java utilise des noms de style Java standard pour les propriétés d'objets *ToDoItem*, comme ce qui suit.

- mId
- mText
- mComplete
- mDuration


Vous devez sérialiser les noms du client en noms JSON correspondant aux noms des colonnes de la table *ToDoItem* sur le serveur. Le code qui suit, qui utilise la bibliothèque [gson](http://go.microsoft.com/fwlink/p/?LinkId=290801), effectue cette sérialisation.

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>Procédure de mappage de noms de table différents entre le client et le back-end

Le mappage du nom de table client vers un autre nom de table de services mobiles est simple. Il suffit d'utiliser un des remplacements de la fonction <a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a>, comme dans le code qui suit.

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### </a>Procédure d’automatisation du mappage des noms de colonnes<a name="conversions">

Le mappage des noms de colonnes pour une table avec peu de colonnes n'est pas compliqué, comme nous l'avons vu dans la section précédente. Mais supposons que notre table contient un grand nombre de colonnes, par exemple 20 ou 30. Il s'avère que nous pouvons appeler l'API <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> et spécifier une stratégie de conversion qui s'applique à chaque colonne, ce qui évite d'avoir à annoter chaque nom de colonne individuellement.

Pour cela, nous utilisons la bibliothèque <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> que la bibliothèque cliente Android utilise en arrière-plan pour sérialiser les objets Java en données JSON, qui sont ensuite envoyées à Azure Mobile Services.

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
[Prise en main de l'authentification]: app-service-mobile-android-get-started-users.md
[Prise en main de l’authentification]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0525_2016-->