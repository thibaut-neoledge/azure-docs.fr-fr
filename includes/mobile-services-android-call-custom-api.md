
##<a name="update-app"></a>Mise à jour de l'application pour appeler l'API personnalisée

1. Nous allons ajouter un bouton intitulé « Complete All » en regard du bouton existant et déplacer ces deux boutons d'une ligne. Dans Android Studio, ouvrez le fichier *res\\layout\\activity_to_do.xml* dans votre projet de démarrage rapide et recherchez l'élément **LinearLayout** contenant l'élément **Button** appelé `buttonAddToDo`. Copiez l'élément **LinearLayout** et collez-le immédiatement après l'élément d'origine. Supprimez l'élément **Button** du premier élément **LinearLayout**.

2. Dans le second élément **LinearLayout**, supprimez l'élément **EditText** et ajoutez le code suivant immédiatement après l'élément **Button** existant :

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

	Ce code ajoute un nouveau bouton à la page, sur une ligne distincte, en regard du bouton existant.

3. Le second élément **LinearLayout** se présente maintenant comme suit :

	     <LinearLayout
	        android:layout_width="match_parent" 
	        android:layout_height="wrap_content" 
	        android:background="#71BCFA"
	        android:padding="6dip"  >
	        <Button
	            android:id="@+id/buttonAddToDo"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="addItem"
	            android:text="@string/add_button_text" />
	        <Button
	            android:id="@+id/buttonCompleteItem"
	            android:layout_width="wrap_content"
	            android:layout_height="wrap_content"
	            android:onClick="completeItem"
	            android:text="@string/complete_button_text" />
	    </LinearLayout>
	

4. Ouvrez le fichier res\\values\\string.xml et ajoutez la ligne de code suivante :

    	<string name="complete_button_text">Complete All</string>



5. Dans l’Explorateur de projet, cliquez avec le bouton droit sur le nom du projet dans le dossier *src* (`com.example.{your projects name}`), choisissez **Nouveau**, puis **Classe**. Dans la boîte de dialogue, entrez **MarkAllResult** dans le champ du nom de la classe, choisissez OK et remplacez la définition de classe qui en résulte par le code suivant :

		import com.google.gson.annotations.SerializedName;
		
		public class MarkAllResult {
		    @SerializedName("count")
		    public int mCount;
		    
		    public int getCount() {
		        return mCount;
			}
		
			public void setCount(int count) {
			        this.mCount = count;
			}
		}

	Cette classe permet de conserver la valeur de nombre de lignes renvoyée par l'API personnalisée.

6. Recherchez la méthode **refreshItemsFromTable** dans le fichier **ToDoActivity.java** et assurez-vous que la première ligne de code dans le bloc `try` ressemble à ceci :

        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();

	Les éléments sont filtrés de manière à ce que les éléments terminés ne soient pas renvoyés par la requête.

7. Assurez-vous que **ToDoActivity.java** contient les importations suivantes au début du fichier :

		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;

8. Dans le fichier **ToDoActivity.java**, ajoutez la méthode suivante :

	public void completeItem(View view) {
	    
	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
	    	
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
	
	Cette méthode gère l'événement **Click** pour le nouveau bouton. La méthode **invokeApi** est appelée sur le client pour envoyer une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles.

## Test de l'application

1. Dans le menu **Exécuter**, cliquez sur **Exécuter l’application** pour démarrer le projet dans l’émulateur Android ou dans un appareil Android connecté.

	Cela permet d'exécuter votre application, créée avec le Kit de développement logiciel (SDK) Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.


2. Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Ajouter**.

3. Répétez l'étape précédente jusqu'à ce que vous ayez ajouté plusieurs éléments todo dans la liste.

4. Cliquez sur le bouton **Complete All**.

  	![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

	Un message s'affiche pour indiquer le nombre d'éléments marqués comme terminés, puis la requête filtrée est de nouveau exécutée pour supprimer tous les éléments de la liste.
<!--HONumber=54-->