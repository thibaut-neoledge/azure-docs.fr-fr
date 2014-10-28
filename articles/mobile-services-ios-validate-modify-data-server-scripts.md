<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-ios" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate and modify data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-validate-modify-data-server-scripts" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-validate-modify-data-server-scripts" title="iOS" class="current">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-validate-modify-data-server-scripts" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-validate-modify-data-server-scripts" title="HTML" class="current">HTML</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application iOS pour tirer profit de ces nouveaux comportements.

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne][]
2.  [Mise à jour du client pour la prise en charge de la validation][]

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][]. Avant de commencer, vous devez suivre le didacticiel [Prise en main des données][].

## <a name="string-length-validation"></a>Ajout de la validation

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1.  Connectez-vous au [portail de gestion Azure][], cliquez sur **Mobile Services**, puis sur l'application.

    ![][]

2.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][1]

3.  Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

    ![][2]

4.  Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Ce script vérifie la longueur de la propriété **text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Sous l'onglet <strong>Script</strong>, vous pouvez supprimer un script inscrit en cliquant sur <strong>Effacer</strong>, puis sur <strong>Enregistrer</strong>.</p></div>

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1.  Dans Xcode, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données][].

2.  Appuyez sur le bouton **Exécuter** (Commande + R) pour générer le projet et démarrer l'application, puis tapez un texte de plus de 10 caractères dans la zone de texte et cliquez sur l'icône représentant un signe plus (**+**).

    Notez que l'application génère une exception non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.

3.  Dans le fichier QSTodoService.m, recherchez la ligne de code suivante dans la méthode **addItem** :

        [self logErrorIfNotNil:error]; 

    Après cette ligne de code, remplacez le reste du bloc completion par le code suivant :

        BOOL goodRequest = !((error) && (error.code == MSErrorMessageErrorCode));

        // detect text validation error from service.
        if (goodRequest) // The service responded appropriately
        {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:result atIndex:index];

            // Let the caller know that we finished
            completion(index);
        }
        else{

            // if there's an error that came from the service
            // log it, and popup up the returned string.
            if (error && error.code == MSErrorMessageErrorCode) {
                NSLog(@"ERROR %@", error);
                UIAlertView *av =
                [[UIAlertView alloc]
                 initWithTitle:@"Request Failed"
                 message:error.localizedDescription
                 delegate:nil
                 cancelButtonTitle:@"OK"
                 otherButtonTitles:nil
                 ];
                [av show];
            }
        }

    Ce code enregistre l'erreur dans la fenêtre de sortie et l'affiche pour l'utilisateur.

4.  Régénérez et démarrez l'application.

    ![][3]

    Notez que l'erreur est traitée et que le message d'erreur est affiché pour l'utilisateur.

<!--## <a name="add-timestamp"></a>Add a timestamp  The previous tasks validated an insert and either accepted or rejected it. Now, you will update inserted data by using a server script that adds a timestamp property to the object before it gets inserted.  1. In the **Scripts** tab in the [Management Portal], replace the current **Insert** script with the following function, and then click **Save**.          function insert(item, user, request) {             if (item.text.length > 10) {                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');             } else {                 item.createdAt = new Date();                 request.execute();             }         }      This function augments the previous insert script by adding a new **createdAt** timestamp property to the object before it gets inserted by the call to **request**.**execute**.       <div class="dev-callout"><b>Note</b>     <p>Dynamic schema must be enabled the first time that this insert script runs. With dynamic schema enabled, Mobile Services automatically adds the <strong>createdAt</strong> column to the <strong>TodoItem</strong> table on the first execution. Dynamic schema is enabled by default for a new mobile service, and it should be disabled before the app is published.</p>     </div>  2. In Visual Studio, press the **F5** key to run the app, then type text (shorter than 10 characters) in **Insert a TodoItem** and click **Save**.      Notice that the new timestamp does not appear in the app UI.  3. Back in the Management Portal, click the **Browse** tab in the **todoitem** table.         Notice that there is now a **createdAt** column, and the new inserted item has a timestamp value.    Next, you need to update the iOS app to display this new column.  ## <a name="update-client-timestamp"></a>Update the client again  The Mobile Service client will ignore any data in a response that it cannot serialize into properties on the defined type. The final step is to update the client to display this new data.  1. In Visual Studio, open the file MainPage.xaml.cs, then replace the existing **TodoItem** class with the following definition:          public class TodoItem         {             public int Id { get; set; }                        [DataMember(Name="text")]             public string Text { get; set; }              [DataMember(Name="complete")]             public bool Complete { get; set; }                          [DataMember(Name="createdAt")]             public DateTime? CreatedAt { get; set; }         }          This new class definition includes the new timestamp property, as a nullable DateTime type.        <div class="dev-callout"><b>Note</b>     <p>The <strong>DataMemberAttribute</strong> tells the client to map the new <strong>CreatedAt</strong> property in the app to the <strong>createdAt</strong> column defined in the TodoItem table, which has a different casing. By using this attribute, your app can have property names on objects that differ from column names in the SQL Database. Without this attribute, an error would occur because of the casing differences.</p>     </div>  5. Add the following XAML element just below the **CheckBoxComplete** element in the MainPage.xaml file:                    <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>      This displays the new **CreatedAt** property in a text box.       6. Press the **F5** key to run the app.      Notice that the timestamp is only displayed for items inserted after you updated the insert script.  7. Replace the existing **RefreshTodoItems** method with the following code:           private void RefreshTodoItems()         {              // This query filters out completed TodoItems and              // items without a timestamp.              items = todoTable                .Where(todoItem => todoItem.Complete == false                    && todoItem.CreatedAt != null)                .ToCollectionView();              ListItems.ItemsSource = items;         }      This method updates the query to also filter out items that do not have a timestamp value.      8. Press the **F5** key to run the app.      Notice that all items created without timestamp value disappear from the UI.  You have completed this working with data tutorial.-->

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination][].

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs avec des scripts][]

    En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-validate-modify-data-server-scripts "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-validate-modify-data-server-scripts "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-validate-modify-data-server-scripts "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-android-validate-modify-data-server-scripts "Android"
  [HTML]: /fr-fr/documentation/articles/mobile-services-html-validate-modify-data-server-scripts "HTML"
  [Xamarin.iOS]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/documentation/articles/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts "Xamarin.Android"
  [Ajout de la validation de longueur de chaîne]: #string-length-validation
  [Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-ios
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-ios
  [Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-ios
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-ios
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
