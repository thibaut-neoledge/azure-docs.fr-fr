<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Store app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Validation et modification de données dans Mobile Services à l'aide du .Net principal

<div class="dev-center-tutorial-selector sublanding">
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Windows Store&nbsp;C#" class="current">Windows Store&nbsp;C#</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone">Windows Phone</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title=".NET backend" class="current">Serveur principal .NET</a> | 
    <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/"  title="JavaScript backend">JavaScript backend</a>
</div>

Cette rubrique montre comment utiliser le code dans les Azure Mobile Services .Net principaux pour valider et modifier des données. Le service .Net principal est un service HTTP intégré à l'infrastructure API Web et à Entity Framework. Si vous connaissez la classe `ApiController` définie avec l'infrastructure API Web, la classe `TableController` fournie par Mobile Services s'avèrera très intuitive. `TableController` est dérivée de la classe `ApiController` et fournit des fonctionnalités supplémentaires pour créer une interface avec une table de base de données. Vous pouvez l'utiliser pour effectuer des opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier, comme cela est expliqué dans ce didacticiel.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne][]
2.  [Mise à jour du client pour la prise en charge de la validation][]
3.  [Test de la longueur de chaîne][]
4.  [Ajout d'un horodatage pour CompleteDate][]
5.  [Mise à jour du client pour l'affichage de CompleteDate][]

Ce didacticiel s'appuie sur la procédure et l'exemple de code présentés dans le didacticiel précédent, [Prise en main][] ou [Prise en main des données][]. Avant de commencer, vous devez suivre le didacticiel [Prise en main][] ou [Prise en main des données][].

## <a name="string-length-validation"></a>Ajout de la validation

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation][]]

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile est configuré pour valider les données et envoyer des réponses d'erreur pour une longueur de texte incorrecte, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation. L'erreur sera levée comme `MobileServiceInvalidOperationException` à partir de l'appel de l'application cliente vers `IMobileServiceTable<TodoItem].InsertAsync()`.

1.  Dans la fenêtre de l'explorateur de solutions dans Visual Studio, accédez au projet client, puis ouvrez le fichier MainPage.xaml.cs. Ajoutez les instructions **using** suivantes au fichier :

        using Windows.UI.Popups;
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;

2.  Dans MainPage.xaml.cs, remplacez la méthode **InsertTodoItem** existante par le code suivant :

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch(MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageDialog errormsg = new MessageDialog(string.Format("{0} (HTTP {1})", 
                    (string)responseContent["message"],(int)invalidOpException.Response.StatusCode), 
                    invalidOpException.Message);
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

    Cette version de la méthode inclut la gestion des erreurs de type **MobileServiceInvalidOperationException** ; la réponse indiquant le message d'erreur désérialisée à partir du contenu de la réponse s'affiche dans une boîte de dialogue.

## <a name="test-length-validation"></a>Test de la longueur de chaîne

1.  Dans l'explorateur de solutions pour Visual Studio, cliquez avec le bouton droit sur le projet de service todolist, puis cliquez sur **Définir comme projet de démarrage**. Puis appuyez sur la touche **F5** pour démarrer l'application hébergeant le service en local dans IIS Express.

2.  Entrez le texte du nouvel élément todo, dont la longueur doit dépasser les 10 caractères, puis cliquez sur **Enregistrer**.

    ![][]

3.  En case de texte incorrect, vous obtiendrez une boîte de dialogue de message semblable à la suivante.

    ![][1]

## <a name="add-timestamp"></a>Ajout d'un horodatage pour CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate][]]

## <a name="update-client-timestamp"></a>Mise à jour du client pour l'affichage de CompleteDate

L'étape finale consiste à mettre à jour le client de manière à afficher les nouvelles données **CompleteDate**.

1.  Dans l'explorateur de solutions pour Visual Studio, dans le projet client todolist, ouvrez le fichier MainPage.xaml et remplacez l'élément **CheckBoxComplete** par la définition ci-dessous. Puis enregistrez le fichier. Le gestionnaire d'événement sur **CheckBoxComplete** est modifié pour pouvoir gérer l'événement `click`. Ajoutez un bloc de texte en regard de la case à cocher, et liez-le à la date d'horodatage complète.

        <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
          Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
        <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" VerticalAlignment="Center"/>

2.  Dans l'explorateur de solutions pour Visual Studio, dans le projet client todolist, ouvrez le fichier MainPage.xaml.cs et remplacez l'événement `CheckBoxComplete_Checked` par le gestionnaire d'événement `CheckBoxComplete_Clicked` ci-dessous. Vous pouvez donc afficher la date complète une fois l'élément terminé.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }

3.  Dans le fichier MainPage.xaml.cs, remplacez la classe **TodoItem** par la définition suivante incluant la nouvelle propriété **CompleteDate** en tant que type nullable.

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

    > [WACOM.NOTE] The `DataMemberAttribute` tells the client to map the new `CompleteDate` property in the app to the `CompleteDate` column defined in the TodoItem table. En utilisant cet attribut, votre application peut avoir des noms de propriétés pour les objets qui diffèrent des noms de colonnes dans la base de données SQL.

4.  Dans le fichier MainPage.xaml.cs, supprimez ou commentez la fonction clause `.Where` dans la méthode **RefreshTodoItems** existante, pour que les todoitems terminés soient inclus dans les résultats.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

5.  Dans MainPage.xaml.cs, mettez à jour la méthode **UpdateCheckedTodoItem** comme suit, pour que les éléments soient actualisés après une mise à jour et que les éléments terminés ne soient pas supprimés de la liste. Puis enregistrez le fichier.

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }

6.  Dans la fenêtre de l'explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur la **Solution**, puis cliquez sur **Régénérer la solution** pour régénérer le client et le service principal .NET. Vérifiez que les deux projets ont été générés sans erreurs.

    ![][2]

7.  Appuyez sur **F5** pour lancer l'application cliente et le service en local. Ajoutez certains nouveaux éléments et cliquez pour indiquer que certains éléments sont terminés, pour afficher l'horodatage **CompleteDate** en cours de mise à jour.

    ![][3]

8.  Dans l'explorateur de solutions pour Visual Studio, cliquez avec le bouton droit sur le projet de service todolist, puis cliquez sur **Publier**. Publiez votre service .NET principal sur Microsoft Azure en utilisant le fichier de configuration de publication que vous avez téléchargé à partir du portail Azure.

9.  Mettez à jour le fichier App.xaml.cs pour le projet client en annulant les marques de commentaire de la connexion à l'adresse du service mobile. Testez l'application par rapport au .NET principal hébergé dans votre compte Azure.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination][].

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs côté service][]

    En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET][]

    En savoir plus sur l'utilisation de Mobile Services avec .NET.

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/ "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "JavaScript backend"
  [Ajout de la validation de longueur de chaîne]: #string-length-validation
  [Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
  [Test de la longueur de chaîne]: #test-length-validation
  [Ajout d'un horodatage pour CompleteDate]: #add-timestamp
  [Mise à jour du client pour l'affichage de CompleteDate]: #update-client-timestamp
  [Prise en main]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-rebuild-solution.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/mobile-services-final-local-app-run.png
  [Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Autorisation des utilisateurs côté service]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
