<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-Xamarin-iOS" urlDisplayName="" pageTitle="Use server scripts to validate and modify data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Xamarin iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="donnam" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone">Windows Phone</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application iOS pour tirer profit de ces nouveaux comportements. Le code terminé est disponible dans l'exemple d'application [ValidateModifyData][].

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Ajout de la validation de longueur de chaîne][]
2.  [Mise à jour du client pour la prise en charge de la validation][]
3.  [Ajout d'un horodatage lors d'une insertion][]
4.  [Mise à jour du client pour l'affichage de l'horodatage][]

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
     if (item.text.length \> 10) {
     request.respond(statusCodes.BAD\_REQUEST, 'La propriété text ne doit pas comporter plus de 10 caractères.');
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

1.  Dans Xamarin Studio, ouvrez le projet que vous avez modifié avec le didacticiel [Prise en main des données][].

2.  Cliquez sur **Exécuter** pour générer le projet et démarrer l'application, puis tapez un texte de plus de 10 caractères dans la zone de texte et cliquez sur l'icône plus (**+**).

    Notez que l'application génère une exception non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.

3.  Dans le fichier TodoService.cs, recherchez la gestion d'exception `try/catch` actuelle dans la méthode **InsertTodoItemAsync** et remplacez le code `catch` par :

    catch (Exception ex) {
     var exDetail = (ex.InnerException.InnerException as MobileServiceInvalidOperationException);
     Console.WriteLine(exDetail.Message);

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
     UITableViewRowAnimation.Top);
     itemText.Text = "";
    }

5.  Régénérez et démarrez l'application.

    ![][3]

    Notez que l'erreur est traitée et que le message d'erreur est affiché pour l'utilisateur.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination][].

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

-   [Autorisation des utilisateurs avec des scripts][]

    En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

  [Windows Store C#]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [ValidateModifyData]: http://go.microsoft.com/fwlink/p/?LinkId=331330
  [Ajout de la validation de longueur de chaîne]: #string-length-validation
  [Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
  [Ajout d'un horodatage lors d'une insertion]: #add-timestamp
  [Mise à jour du client pour l'affichage de l'horodatage]: #update-client-timestamp
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [3]: ./media/partner-xamarin-mobile-services-ios-validate-modify-data-server-scripts/mobile-quickstart-data-error-ios.png
  [Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
  [Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-xamarin-ios
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
