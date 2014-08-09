<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-dotnet" urlDisplayName="Validate and Modify Data" pageTitle="User server scripts to validate and modify data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

Validation et modification de données dans Mobile Services à l'aide de scripts serveur
======================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#") [Windows Store JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript") [Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone") [iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS") [Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML") [Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")[.NET backend](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/ ".NET backend") | [JavaScript backend](fr-fr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/ "JavaScript backend")

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application Windows Store pour tirer profit de ces nouveaux comportements.

[regarder le didacticiel](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services) [Lire la vidéo](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services) 09:54:00

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne](#string-length-validation)
2.  [Mise à jour du client pour la prise en charge de la validation](#update-client-validation)
3.  [Ajout d'un horodatage lors d'une insertion](#add-timestamp)
4.  [Mise à jour du client pour l'affichage de l'horodatage](#update-client-timestamp)

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet). Avant de commencer, vous devez suivre le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet).

Ajout de la validation
----------------------

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png)

3.  Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

     ![](./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png)

4.  Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 request.execute();
             }
         }

  Ce script vérifie la longueur de la propriété **TodoItem.text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

    **Remarque**

    Sous l'onglet **Script**, vous pouvez supprimer un script inscrit en cliquant sur **Clear**, puis sur **Enregistrer**.

Mise à jour du client
---------------------

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1.  Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet).

2.  Appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte dépassant 10 caractères dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

     Notez que l'application génère une exception **MobileServiceInvalidOperationException** non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.    

3.  Ouvrez le fichier MainPage.xaml.cs, puis ajoutez l'instruction **using** suivante :

        using Windows.UI.Popups;

4.  Remplacez la méthode **InsertTodoItem** existante par le code suivant :

         private async void InsertTodoItem(TodoItem todoItem)
         {
             // Ce code insère un nouvel élément TodoItem dans la base de données.
             // Lorsque l'opération est terminée et que Mobile Services a
             // attribué un ID, l'élément est ajouté à la collection.
             try
             {
                 await todoTable.InsertAsync(todoItem);
                 items.Add(todoItem);
             }
             catch (MobileServiceInvalidOperationException e)
             {
                 MessageDialog errormsg = new MessageDialog(e.Message, 
                     string.Format("{0} (HTTP {1})",                     
                     e.Response.ReasonPhrase,
                     (int)e.Response.StatusCode));
                 var ignoreAsyncOpResult = errormsg.ShowAsync();
             }
         }

     Cette version de la méthode inclut la gestion des erreurs de type **MobileServiceInvalidOperationException** ; la réponse indiquant une erreur s'affiche dans une fenêtre contextuelle.

Ajout d'un horodatage
---------------------

Les tâches précédentes ont permis de valider une insertion, qui est acceptée ou rejetée. Maintenant, vous allez mettre à jour les données insérées en utilisant un script serveur qui ajoute une propriété d'horodatage à l'objet avant son insertion.

**Remarque**

La propriété d'horodatage **createdAt** illustrée ici est désormais redondante. Mobile Services crée automatiquement une propriété système **\_\_createdAt** pour chaque table. Vous pouvez utiliser cette propriété système dans votre application en ajoutant simplement le membre suivant à la classe TodoItem :

``` {}
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
```

1.  Sous l'onglet **Scripts** du [portail de gestion](https://manage.windowsazure.com/), remplacez le script **Insert** actuel par la fonction suivante, puis cliquez sur **Enregistrer**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Cette fonction enrichit le script d'insertion précédent en ajoutant une nouvelle propriété d'horodatage **createdAt** à l'objet avant son insertion par l'appel à **request**.**execute**.

    **Remarque**

    Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne **createdAt** à la table **TodoItem**. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé avant que l'application soit publiée sur le Windows Store.

2.  Dans Visual Studio, appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte (inférieure à 10 caractères) dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

    Notez que le nouvel horodatage n'apparaît pas dans l'interface utilisateur de l'application.

3.  De retour dans le portail de gestion, cliquez sur l'onglet **Parcourir** dans la table **todoitem**.

    Notez qu'une colonne **createdAt** apparaît désormais à l'écran et qu'une valeur d'horodatage est associée au nouvel élément inséré.

L'application Windows Store doit ensuite être mise à jour pour afficher cette nouvelle colonne.

Nouvelle mise à jour du client
------------------------------

Le client Mobile Services ignore les données d'une réponse qu'elle ne peut pas sérialiser en propriétés de type défini. L'étape finale consiste à mettre à jour le client de manière à afficher ces nouvelles données.

1.  Dans Visual Studio, ouvrez le fichier MainPage.xaml.cs, puis remplacez la classe **TodoItem** existante par la définition suivante :

         public class TodoItem
         {
             public string Id { get; set; }

             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }

             [JsonProperty(PropertyName = "complete")]
             public bool Complete { get; set; }
            
             [JsonProperty(PropertyName = "createdAt")]
             public DateTime
         CreatedAt { get; set; }
         }

    Cette nouvelle définition de classe inclut la nouvelle propriété d'horodatage en tant que type DateTime nullable.

    **Remarque**

    L'attribut `DataMemberAttribute` indique au client de mapper la nouvelle propriété `CreatedAt` dans l'application vers la colonne `createdAt` définie dans la table TodoItem (dont la casse est différente). En utilisant cet attribut, votre application peut avoir des noms de propriétés pour les objets qui diffèrent des noms de colonnes dans la base de données SQL. Sans cet attribut, une erreur peut se produire en raison de la différence de casse.

2.  Ajoutez l'élément XAML suivant juste en dessous de l'élément **CheckBoxComplete** dans le fichier MainPage.xaml :

         <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

     La nouvelle propriété **CreatedAt** s'affiche dans une zone de texte. 

3.  Appuyez sur **F5** pour lancer l'application.

    Notez que l'horodatage ne s'affiche que pour les éléments insérés une fois que le script d'insertion a été mis à jour.

4.  Remplacez la méthode **RefreshTodoItems** existante par le code suivant :

         private async void RefreshTodoItems()
         {
             // Cette requête élimine les éléments TodoItems terminés et 
             // les éléments sans horodatage. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false
                    && todoItem.CreatedAt != null)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;
         }

     Cette méthode met à jour la requête pour éliminer également les éléments non associés à une valeur d'horodatage.

5.  Appuyez sur **F5** pour lancer l'application.

    Notez que tous les éléments créés sans valeur d'horodatage disparaissent de l'interface utilisateur.

Vous avez maintenant terminé ce didacticiel.

Étapes suivantes
----------------

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet).

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d’informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    
    En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet)
    
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    
    En savoir plus sur l'utilisation de Mobile Services avec .NET.


