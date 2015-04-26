
1. Dans le fichier MainPage.xaml.cs, ajoutez les instructions using suivantes ou supprimez leurs marques de commentaire : 

		using Microsoft.WindowsAzure.MobileServices;

2. Remplacez la définition de la classe TodoItem par le code suivant : 

	    public class TodoItem
	    {
	        public string Id { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "text")]  
	        public string Text { get; set; }
	
	        [Newtonsoft.Json.JsonProperty(PropertyName = "complete")]  
	        public bool Complete { get; set; }
	    }
	
	**JsonPropertyAttribute** est utilisé pour définir le mappage entre les noms des propriétés dans le type de client et les noms des colonnes dans la table de données sous-jacente.

	>[AZURE.NOTE] Dans un projet d'application Windows universelle, la classe TodoItem est définie dans le fichier de code distinct dans le dossier DataModel partagé.

3. Dans MainPage.xaml.cs, mettez en commentaire ou supprimez la ligne définissant la collection des éléments existants, puis annulez les marques de commentaire ou ajoutez les lignes de commande suivantes, en remplaçant _&lt;yourClient&gt;_ par le champ `MobileServiceClient` ajouté dans le fichier App.xaml.cs lorsque vous avez connecté votre projet au service mobile : 

		private MobileServiceCollection<TodoItem, TodoItem> items;
		private IMobileServiceTable<TodoItem> todoTable = 
		    App.<yourClient>.GetTable<TodoItem>();
		  
	Ce code crée une collection de liaisons (éléments) prenant en charge des services mobiles et une classe proxy pour la table de la base de données (todoTable). 

4. Dans la méthode **InsertTodoItem**, supprimez la ligne de code définissant la propriété **TodoItem.Id**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire dans la ligne de code suivante : 

		await todoTable.InsertAsync(todoItem);


	Ce code permet d'insérer un nouvel élément dans la table. 

5. Remplacez la méthode **RefreshTodoItems** par le code suivant : 

		private async void RefreshTodoItems()
        {
            MobileServiceInvalidOperationException exception = null;
            try
            {
                // Query that returns all items.   
                items = await todoTable.ToCollectionAsync();             
            }
            catch (MobileServiceInvalidOperationException e)
            {
                exception = e;
            }
            if (exception != null)
            {
                await new MessageDialog(exception.Message, "Error loading items").ShowAsync();
            }
            else
            {
                ListItems.ItemsSource = items;
                this.ButtonSave.IsEnabled = true;
            }    
        }

	Cela définit la liaison sur la collection d'éléments dans `todoTable`, qui contient tous les objets **TodoItem** renvoyés depuis le service mobile. Si un problème survient lors de l'exécution de la requête, un message apparait pour afficher les erreurs. 

6. Dans la méthode **UpdateCheckedTodoItem**, ajoutez le modificateur **async** à la méthode, puis supprimez les marques de commentaire de la ligne de code suivante : 

		await todoTable.UpdateAsync(item);

	Cela permet d'envoyer une mise à jour de l'élément au service mobile. 

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.


<!--HONumber=42-->
