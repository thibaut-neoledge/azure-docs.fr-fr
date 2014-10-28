<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-wp8" urlDisplayName="Validate Data" pageTitle="Use server scripts to validate data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Windows Phone 8 app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

<div class="dev-center-tutorial-selector sublanding"> 
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet" title="Windows Store&nbsp;C#">Windows Store&nbsp;C#</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8" title="Windows Phone" class="current">Windows Phone</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title=".NET backend">Serveur principal .NET</a> | 
    <a href="/fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8"  title="JavaScript backend" class="current">JavaScript backend</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Cette rubrique vous montre comment exploiter les scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses op&eacute;rations sur les donn&eacute;es ins&eacute;r&eacute;es et mises &agrave; jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend &agrave; d&eacute;finir et &agrave; inscrire les scripts serveur qui valident et modifient les donn&eacute;es. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez &eacute;galement mettre &agrave; jour votre application Windows Phone&nbsp;8 pour tirer profit de ces nouveaux comportements.</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-validate-modify-data-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298629" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vid&eacute;o</span></a> <span class="time">11:36:00</span></div>

</div>

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
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Ce script vérifie la longueur de la propriété **TodoItem.text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute** est appelée pour effectuer l'insertion.

    <div class="dev-callout"> 
<b>Remarque</b> 
<p>Sous l'onglet <strong>Script</strong>, vous pouvez supprimer un script inscrit en cliquant sur <strong>Effacer</strong>, puis sur <strong>Enregistrer</strong>.</p></div>

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1.  Dans Visual Studio 2012 Express pour Windows Phone, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main des données][].

2.  Appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte de plus de 10 caractères dans la zone de texte, puis cliquez sur **Enregistrer**.

    Notez que l'application génère une exception **MobileServiceInvalidOperationException** non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.

3.  Ouvrez le fichier MainPage.xaml.cs, puis remplacez la méthode **InsertTodoItem** par le code suivant :

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. 
            // When the operation completes and Mobile Services has 
            // assigned an Id, the item is added to the collection.
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                MessageBox.Show(e.Message,
                    string.Format("{0} (HTTP {1})",
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode), 
                    MessageBoxButton.OK);
            }
        }

    Cette version de la méthode inclut la gestion d'erreurs pour l'exception **MobileServiceInvalidOperationException** qui affiche la réponse indiquant une erreur dans un MessageBox.

## <a name="add-timestamp"></a>Ajout d'un horodatage

Les tâches précédentes ont permis de valider une insertion, qui est acceptée ou rejetée. Maintenant, vous allez mettre à jour les données insérées en utilisant un script serveur qui ajoute une propriété d'horodatage à l'objet avant son insertion.

<div class="dev-callout"><b>Remarque</b>
<p>La propri&eacute;t&eacute; d'horodatage <b>createdAt</b> illustr&eacute;e ici est d&eacute;sormais redondante. Mobile Services cr&eacute;e automatiquement une propri&eacute;t&eacute; syst&egrave;me <b>__createdAt</b> pour chaque table. Vous pouvez utiliser cette propri&eacute;t&eacute; syst&egrave;me dans votre application en ajoutant simplement le membre suivant &agrave; la classe TodoItem&nbsp;:</p>
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
</code></pre>
</div>

1.  Sous l'onglet **Scripts** du [portail de gestion][portail de gestion Azure], remplacez le script **Insert** actuel par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Cette fonction enrichit le script d'insertion précédent en ajoutant une nouvelle propriété d'horodatage **createdAt** à l'objet avant son insertion par l'appel à **request**.**execute**.

    <div class="dev-callout"><b>Remarque</b>
<p>Le sch&eacute;ma dynamique doit &ecirc;tre activ&eacute; la premi&egrave;re fois que ce script d'insertion s'ex&eacute;cute. Avec le sch&eacute;ma dynamique activ&eacute;, Mobile Services ajoute automatiquement la colonne <strong>createdAt</strong> &agrave; la table <strong>TodoItem</strong>. Par d&eacute;faut, le sch&eacute;ma dynamique est activ&eacute; pour un nouveau service mobile. Il doit &ecirc;tre d&eacute;sactiv&eacute; avant que l'application soit publi&eacute;e sur le Windows Phone Store.</p>
</div>

2.  Dans Visual Studio, appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte (de moins de 10 caractères) dans la zone de texte, puis cliquez sur **Enregistrer**.

    Notez que le nouvel horodatage n'apparaît pas dans l'interface utilisateur de l'application.

3.  De retour dans le portail de gestion, cliquez sur l'onglet **Parcourir** dans la table **todoitem**.

    Notez qu'une colonne **createdAt** apparaît désormais à l'écran et qu'une valeur d'horodatage est associée au nouvel élément inséré.

L'application Windows Phone doit ensuite être mise à jour pour afficher cette nouvelle colonne.

## <a name="update-client-timestamp"></a>Nouvelle mise à jour du client

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
            public DateTime? CreatedAt { get; set; }
        }

    Cette nouvelle définition de classe inclut la nouvelle propriété d'horodatage en tant que type DateTime nullable.

    <div class="dev-callout"><b>Remarque</b>
<p>L'attribut <strong>DataMemberAttribute</strong> indique au client de mapper la nouvelle propri&eacute;t&eacute; <strong>CreatedAt</strong> dans l'application vers la colonne <strong>createdAt</strong> d&eacute;finie dans la table TodoItem (dont la casse est diff&eacute;rente). En utilisant cet attribut, votre application peut avoir des noms de propri&eacute;t&eacute;s pour les objets qui diff&egrave;rent des noms de colonnes dans la base de donn&eacute;es SQL. Sans cet attribut, une erreur se produirait en raison de la diff&eacute;rence de casse.</p>
</div>

2.  Ajoutez l'élément XAML suivant juste en dessous de l'élément **CheckBoxComplete** dans le fichier MainPage.xaml :

        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

    La nouvelle propriété **CreatedAt** s'affiche dans une zone de texte.

3.  Appuyez sur **F5** pour lancer l'application.

    Notez que l'horodatage ne s'affiche que pour les éléments insérés une fois que le script d'insertion a été mis à jour.

4.  Remplacez la méthode **RefreshTodoItems** existante par le code suivant :

        private async void RefreshTodoItems()
        {
            // This query filters out completed TodoItems and 
            // items without a timestamp. 
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

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données :

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants : [Affinage des requêtes au moyen de la pagination][].

-   [Autorisation des utilisateurs avec des scripts][]

    En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Référence de script serveur Mobile Services][]

    En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

  [Windows Store C#]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone"
  [iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS"
  [Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android"
  [HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML"
  [Xamarin.iOS]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS"
  [Xamarin.Android]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android"
  [Serveur principal .NET]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/ ".NET backend"
  [JavaScript backend]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "JavaScript backend"
  [regarder le didacticiel]: http://go.microsoft.com/fwlink/?LinkId=298629
  [Ajout de la validation de longueur de chaîne]: #string-length-validation
  [Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
  [Ajout d'un horodatage lors d'une insertion]: #add-timestamp
  [Mise à jour du client pour l'affichage de l'horodatage]: #update-client-timestamp
  [Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-wp8
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-portal-data-tables.png
  [2]: ./media/mobile-services-windows-phone-validate-modify-data-server-scripts/mobile-insert-script-users.png
  [Affinage des requêtes au moyen de la pagination]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-wp8
  [Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-wp8
  [Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-wp8
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
