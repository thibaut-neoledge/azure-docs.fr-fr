
Lorsque vous vous inscrivez aux notifications, vous devez par la suite le modifier afin de vous assurer que l'utilisateur est bien authentifié avant toute tentative d'inscription.


1. Dans l’explorateur de projets dans Android Studio, ouvrez le fichier ToDoActivity.java et recherchez la méthode `onCreate`. Déplacez le code suivant de la méthode `onCreate` au début de la méthode `createTable`.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

     La méthode `createTable` est appelée lorsque la méthode `authenticate` est terminée. Votre méthode complète `createTable` doit être similaire à la méthode suivante.

        private void createTable() {
        
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
            
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
            
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
            
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }	

<!---HONumber=Oct15_HO3-->