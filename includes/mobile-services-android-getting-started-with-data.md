Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale. 

1. Vérifiez que les lignes suivantes se trouvent dans la balise **Dépendances** du fichier *build.gradle (Module app)* ou ajoutez-les. Cela ajoute des références au Kit de développement logiciel (SDK) Mobile Services Android Client.

		compile 'com.android.support:support-v4:21.0.3'
    	compile 'com.google.code.gson:gson:2.2.2'
	    compile 'com.google.guava:guava:18.0'
	    compile 'com.microsoft.azure:azure-mobile-services-android-sdk:2.0.2-beta'


2. Maintenant régénérez le projet en cliquant sur **Synchronisation du projet avec les fichiers Gradle**.

3. Ouvrez le fichier AndroidManifest.xml et ajoutez la ligne suivante, qui permet à l'application d'accéder à Mobile Services dans Azure.

		<uses-permission android:name="android.permission.INTERNET" />


6. Ouvrez le fichier AndroidManifest.xml et ajoutez la ligne suivante, qui permet à l'application d'accéder à Mobile Services dans Azure.

		<uses-permission android:name="android.permission.INTERNET" />

5. Dans l'Explorateur de projets, ouvrez le fichier TodoActivity.java situé dans le dossier **GetStartedWithData => app => src => java** et supprimez les marques de commentaire des lignes de code suivantes : 



		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
5. Mettez les lignes suivantes en commentaire :

		import java.util.ArrayList;
		import java.util.List;

6. Nous allons supprimer la liste en mémoire actuellement utilisée par l'application pour la remplacer par un service mobile. Dans la classe **ToDoActivity**, placez la ligne de code suivante en commentaire, qui définit la liste **toDoItemList** existante.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Enregistrez le fichier et le projet indiquera des erreurs de build. Recherchez les trois emplacements restants où la variable toDoItemList est utilisée, et placez les sections indiquées en commentaire. Cela supprime complètement la liste en mémoire. 

8. Nous ajoutons maintenant notre service mobile. Annulez les marques de commentaire sur les lignes de code suivantes :

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Recherchez la classe *ProgressFilter* en bas du fichier et annulez les marques de commentaire sur cette dernière. Cette classe affiche un indicateur 'loading' lorsque *MobileServiceClient* exécute des opérations réseau.


10. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

11. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

12. Dans la méthode **onCreate**, annulez les marques de commentaire sur les lignes de code suivantes qui définissent la variable **MobileServiceClient** :

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	Cela crée une instance de *MobileServiceClient* utilisée pour accéder au service mobile. Cela crée également l'instance de *MobileServiceTable* utilisée pour traiter par proxy le stockage des données dans le service mobile.

13. Dans le code ci-dessus, remplacez MobileServiceUrl et AppKey par l'URL et la clé d'application de votre service mobile, dans cet ordre.



14. Annulez les marques de commentaire sur ces lignes de la méthode **checkItem** :

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

   	Cela permet d'envoyer une mise à jour de l'élément au service mobile et de supprimer les éléments cochés de l'adaptateur.
    
15. Annulez les marques de commentaire sur ces lignes de la méthode **addItem** :
	
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
		

  	Ce code permet de créer un élément et de l'insérer dans la table du service mobile distant.

16. Annulez les marques de commentaire sur ces lignes de la méthode **refreshItemsFromTable** :

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
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

	Cela permet d'interroger le service mobile et de renvoyer tous les éléments qui ne sont pas marqués comme Complete. Les éléments sont ajoutés à l'adaptateur pour la liaison.
		

<!-- URLs. -->
[Kit de développement logiciel (SDK) Mobile Services pour Android]: http://aka.ms/Iajk6q

<!--HONumber=49-->