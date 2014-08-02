<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-Xamarin-iOS" urlDisplayName="" pageTitle="Use server scripts to validate and modify data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Xamarin iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

Validation et modification de données dans Mobile Services à l'aide de scripts serveur
======================================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application iOS pour tirer profit de ces nouveaux comportements. Le code terminé est disponible dans l'exemple d'application [ValidateModifyData](http://go.microsoft.com/fwlink/p/?LinkId=331330).

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne](#string-length-validation)
2.  [Mise à jour du client pour la prise en charge de la validation](#update-client-validation)
3.  [Ajout d'un horodatage lors d'une insertion](#add-timestamp)
4.  [Mise à jour du client pour l'affichage de l'horodatage](#update-client-timestamp)

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios). Avant de commencer, vous devez suivre le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios).

Ajout de la validation
----------------------

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur l'application.

    ![](./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png)

2.  Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

    ![][1]

3.  Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

    ![][2]

4.  Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

    function insert(item, user, request) { 
		if (item.text.length \> 10) { 
				request.respond(statusCodes.BAD\_REQUEST, 'Text length must be 10 characters or less.'); 
			} else { 
				request.execute(); 
		} 
	}

    Ce script vérifie la longueur de la propriété **text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

    **Remarque**

    Sous l'onglet **Script**, vous pouvez supprimer un script inscrit en cliquant sur **Effacer**, puis sur **Enregistrer**.

Mise à jour du client
---------------------

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1.  Dans Xamarin Studio, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios).

2.  Cliquez sur **Exécuter** pour générer le projet et démarrer l'application, puis tapez un texte de plus de 10 caractères dans la zone de texte et cliquez sur l'icône plus (**+**).

    Notez que l'application génère une exception non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.

3.  Dans le fichier TodoService.cs, recherchez la gestion d'exception `try/catch` actuelle dans la méthode **InsertTodoItemAsync** et remplacez le code `catch` par :

    catch (Exception ex) { 
		var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException); Console.WriteLine(exDetail.Message);

        UIAlertView alert = new UIAlertView() { 
				Title = "Error", 
                Message = exDetail.Message
        } ;
        alert.AddButton("Ok");
        alert.Show();

        return -1;
        }

    Ce code affiche une fenêtre contextuelle qui présente l'erreur à l'utilisateur.

4.  Recherchez la méthode **OnAdd** dans **TodoListViewController.cs**. Mettez à jour la méthode pour vous assurer que le code `index` renvoyé n'est pas égal à `-1`, tel que renvoyé par la gestion des exceptions dans **InsertTodoItemAsync**. Dans ce cas, il ne faut pas ajouter de nouvelle ligne dans le code `TableView`.

    if (index != -1) { 
		TableView.InsertRows(new [] { NSIndexPath.FromItemSection(index, 0) }, 
			UITableViewRowAnimation.Top); itemText.Text = ""; 
	}

5.  Régénérez et démarrez l'application.

    ![](./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png)

    Notez que l'erreur est traitée et que le message d'erreur est affiché pour l'utilisateur.

Étapes suivantes
----------------

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios).

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs avec des scripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios)
    <br/>En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.


<!-- Anchors. -->
[Add string length validation]: #string-length-validation
[Update the client to support validation]: #update-client-validation
[Add a timestamp on insert]: #add-timestamp
[Update the client to display the timestamp]: #update-client-timestamp
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png

[4]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-ios
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Management Portal]: https://manage.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330
