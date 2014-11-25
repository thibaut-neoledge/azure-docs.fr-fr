Votre service mobile étant prêt, vous pouvez mettre à jour l'application pour stocker les éléments dans Mobile Services et non dans la collection locale.

1.  Si vous n'avez pas déjà le [Kit de développement logiciel (SDK) Mobile Services pour Android][Kit de développement logiciel (SDK) Mobile Services pour Android], téléchargez-le maintenant et développez les fichiers compressés.

2.  Copiez le répertoire `.jar` du dossier `mobileservices` du Kit de développement logiciel (SDK) vers le dossier `libs` du projet GetStartedWithData.

3.  Dans l'Explorateur de package d'Eclipse, cliquez avec le bouton droit sur le dossier `libs` , puis cliquez sur **Actualiser** pour que les fichiers .jar copiés s'affichent.

    La référence au Kit de développement logiciel (SDK) Mobile Services est ajoutée à l'espace de travail.

4.  Ouvrez le fichier AndroidManifest.xml et ajoutez la ligne suivante, qui permet à l'application d'accéder à Mobile Services dans Azure.

        <uses-permission android:name="android.permission.INTERNET" />

5.  Dans l'Explorateur de package, ouvrez le fichier TodoActivity.java situé dans le package com.example.getstartedwithdata, puis supprimez les marques de commentaire sur les lignes de code suivantes :

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  Nous allons supprimer la liste en mémoire actuellement utilisée par l'application pour la remplacer par un service mobile. Dans la classe **ToDoActivity**, placez la ligne de code suivante en commentaire, qui définit la liste **toDoItemList** existante.

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  Enregistrez le fichier et le projet indiquera des erreurs de build. Recherchez les trois emplacements restants où la variable `toDoItemList` est utilisée et placez les sections indiquées en commentaire. Supprimez également `import java.util.ArrayList`. Cela supprime complètement la liste en mémoire.

8.  Nous ajoutons maintenant notre service mobile. Annulez les marques de commentaire sur les lignes de code suivantes :

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  Recherchez la classe ProgressFilter en bas du fichier et annulez les marques de commentaire sur cette dernière. Cette classe affiche un indicateur « chargement » lorsque MobileServiceClient exécute des opérations réseau.

10. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile que vous venez de créer.

11. Cliquez sur l'onglet **Tableau de bord** et notez la valeur **URL du site**, puis cliquez sur **Gérer les clés** et notez la valeur de **Clé de l'application**.

    ![][0]

    Ces valeurs sont nécessaires pour accéder au service mobile à partir de votre code d'application.

12. Dans la méthode **onCreate**, annulez les marques de commentaire sur les lignes de code suivantes qui définissent la variable **MobileServiceClient** :

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

    Cela crée une instance de MobileServiceClient utilisée pour accéder au service mobile. Cela crée également l'instance de MobileServiceTable utilisée pour traiter par proxy le stockage des données dans le service mobile.

13. Dans le code ci-dessus, remplacez `MobileServiceUrl` and `AppKey` par l'URL et la clé d'application de votre service mobile, dans cet ordre.

14. Annulez les marques de commentaire sur ces lignes de la méthode **checkItem** :

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    Cela permet d'envoyer une mise à jour de l'élément au service mobile et de supprimer les éléments cochés de l'adaptateur.

15. Annulez les marques de commentaire sur ces lignes de la méthode **addItem** :

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    Ce code permet de créer un élément et de l'insérer dans la table du service mobile distant.

16. Annulez les marques de commentaire sur ces lignes de la méthode **refreshItemsFromTable** :

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    Cela permet d'interroger le service mobile et de renvoyer tous les éléments qui ne sont pas marqués comme Complete. Les éléments sont ajoutés à l'adaptateur pour la liaison.



  [Kit de développement logiciel (SDK) Mobile Services pour Android]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/download-android-sample-code/mobile-dashboard-tab.png
