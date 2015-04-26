<properties 
	pageTitle="Utilisation du service .NET principal pour valider et modifier des données (Windows Phone 8) | Centre de développement mobile" 
	description="Découvrez comment valider, modifier et compléter les données pour votre application Windows Phone avec Microsoft Azure Mobile Services sur le serveur principal .Net." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Validation et modification de données dans Mobile Services à l'aide du service principal .NET

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

Cette rubrique montre comment utiliser le code dans vos services principaux .NET Azure Mobile Services pour valider et modifier des données. Le service principal .NET est un service HTTP intégré à l'infrastructure API web. Si vous connaissez la classe  `ApiController` définie avec l'infrastructure API Web, la classe  `TableController` fournie par Mobile Services s'avèrera très intuitive. `TableController` est dérivée de la classe `ApiController` et fournit des fonctionnalités supplémentaires pour créer une interface avec une table de base de données. Vous pouvez l'utiliser pour effectuer des opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier, comme cela est expliqué dans ce didacticiel. 

Ce didacticiel vous familiarise avec ces étapes de base ::

1. [Ajout de la validation de longueur de chaîne]
2. [Mise à jour du client pour la prise en charge de la validation]
3. [Test de la longueur de chaîne]
4. [Ajout d'un horodatage pour CompleteDate]
5. [Mise à jour du client pour l'affichage de CompleteDate]

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Ajout de Mobile Services à une application existante)]/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/). Avant de commencer ce didacticiel, vous devez suivre intégralement celui-ci :  

## <a name="string-length-validation"></a>Ajout de la validation

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile est configuré pour valider les données et envoyer des réponses d'erreur pour une longueur de texte incorrecte, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation. L'erreur sera levée comme `MobileServiceInvalidOperationException` à partir de l'appel de l'application cliente vers `IMobileServiceTable<TodoItem].InsertAsync()`.

1. Dans la fenêtre de l'explorateur de solutions dans Visual Studio, accédez au projet client, puis ouvrez le fichier MainPage.xaml.cs. Ajoutez l'instruction using suivante au fichier :

        using Newtonsoft.Json.Linq;

2. Dans MainPage.xaml.cs, remplacez la méthode **InsertTodoItem** existante par le code suivant :

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                ite
	ms.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageBox.Show(string.Format("{0} (HTTP {1})",
                    (string)responseContent["message"], (int)invalidOpException.Response.StatusCode),
                    invalidOpException.Message, MessageBoxButton.OK);
            }
        }

   	Cette version de la méthode inclut la gestion des erreurs de type **MobileServiceInvalidOperationException** ; la réponse indiquant le message d'erreur désérialisée à partir du contenu de la réponse s'affiche dans une zone de message.

## <a name="test-length-validation"></a>Test de la longueur de chaîne

1. Dans Visual Studio, configurez la cible de déploiement Windows Phone de votre choix. Puis, dans la fenêtre de l'Explorateur de solutions, cliquez avec le bouton droit sur le projet d'application cliente, puis cliquez sur **Déboguer**, **Démarrer une nouvelle instance**.

2. Entrez le texte du nouvel élément todo, dont la longueur doit dépasser les 10 caractères, puis cliquez sur **Enregistrer**.

    ![][1]

3. En case de texte incorrect, vous obtiendrez une boîte de dialogue de message semblable à la suivante.

    ![][2]

## <a name="add-timestamp"></a>Ajout d'un horodatage pour CompleteDate

[AZURE.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]


## <a name="update-client-timestamp"></a>Mise à jour du client pour l'affichage de CompleteDate

L'étape finale consiste à mettre à jour le client de manière à afficher les nouvelles données **CompleteDate**. 


1. Dans l'Explorateur de solutions pour Visual Studio, dans le projet client todolist, ouvrez le fichier MainPage.xaml et remplacez l'élément **StackPanel** par la définition ci-dessous. Puis enregistrez le fichier. Le gestionnaire d'événement sur **CheckBoxComplete** est modifié pour pouvoir gérer l'événement `click`. Ajoutez un bloc de texte en regard de la case à cocher, et liez-le à la date d'horodatage complète.
	      
        <StackPanel Orientation="Horizontal">
          <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}"
            Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" 
            VerticalAlignment="Center"/>
          <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" 
            VerticalAlignment="Center" />
        </StackPanel>


2. Dans l'explorateur de solutions pour Visual Studio, dans le projet client todolist, ouvrez le fichier MainPage.xaml.cs et remplacez l'événement  `CheckBoxComplete_Checked` par le gestionnaire d'événement  `CheckBoxComplete_Clicked` ci-dessous. Vous pouvez donc afficher la date complète une fois l'élément terminé.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }


3. In the MainPage.xaml.cs file, then replace the existing **TodoItem** class with the following definition that includes the new **CompleteDate** property as a nullable type.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }
	
    >[AZURE.NOTE] L'attibut <code>DataMemberAttribute</code> indique au client de mapper la nouvelle propriété <code>CompleteDate</code> dans l'application à la colonne <code>CompleteDate</code> définie dans la table TodoItem. En utilisant cet attribut, votre application peut avoir des noms de propriétés pour les objets qui diffèrent des noms de colonnes dans la base de données SQL.
    

	


4. Dans le fichier MainPage.xaml.cs, supprimez ou commentez la fonction clause `.Where` dans la méthode **RefreshTodoItems** existante, pour que les todoitems terminés soient inclus dans les résultats.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();


5. Dans MainPage.xaml.cs, mettez à jour la méthode **UpdateCheckedTodoItem** comme suit, pour que les éléments soient actualisés après une mise à jour et que les éléments terminés ne soient pas supprimés de la liste. Puis enregistrez le fichier.	

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //ite
	ms.Remove(item);
            RefreshTodoItems();
        }


6. Dans la fenêtre de l'explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur la **Solution**, puis cliquez sur **Régénérer la solution** pour régénérer le client et le service principal .NET. Vérifiez que les deux projets ont été générés sans erreurs.


	
7. Appuyez sur **F5** pour lancer l'application cliente et le service en local. Ajoutez certains nouveaux éléments et cliquez pour indiquer que certains éléments sont terminés, pour afficher l'horodatage **CompleteDate** en cours de mise à jour.


8. Dans l'explorateur de solutions pour Visual Studio, cliquez avec le bouton droit sur le projet de service todolist, puis cliquez sur **Publier**. Publiez votre service .NET principal sur Microsoft Azure en utilisant le fichier de configuration de publication que vous avez téléchargé à partir du portail Azure.

9. Mettez à jour le fichier App.xaml.cs pour le projet client en annulant les marques de commentaire de la connexion à l'adresse du service mobile. Testez l'application par rapport au .NET principal hébergé dans votre compte Azure.


## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination]

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

* [Autorisation côté service des utilisateurs]
  <br/>En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET

<!-- Anchors. -->
[Ajout de la validation de longueur de chaîne]: #string-length-validation
[Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
[Test de la longueur de chaîne]: #test-length-validation
[Ajout d'un horodatage pour CompleteDate]: #add-timestamp
[Mise à jour du client pour l'affichage de CompleteDate]: #update-client-timestamp
[Étapes suivantes]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png



<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Autorisation côté service des utilisateurs]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/
[Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
[Mise en route]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portail de gestion]: https://manage.windowsazure.com/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
