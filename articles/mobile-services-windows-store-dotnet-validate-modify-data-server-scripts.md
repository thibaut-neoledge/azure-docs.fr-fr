<properties urlDisplayName="Validate and Modify Data" pageTitle="Utilisation de scripts serveur pour valider et modifier des données (Windows Store) | Centre de développement mobile" metaKeywords="" description="Learn how to use server scripts to validate, modify, and augment data for your Windows Store app with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="glenga" />

# Validation et modification de données dans Mobile Services à l'aide de scripts serveur

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>Cette rubrique vous présente l'utilisation des scripts serveur dans Azure Mobile Services. Il est possible d'utiliser les scripts serveur inscrits dans un service mobile pour effectuer diverses opérations sur les données insérées et mises à jour, qu'il s'agisse de les valider ou de les modifier. Ce didacticiel vous apprend à définir et à inscrire les scripts serveur qui valident et modifient les données. Le comportement des scripts serveur ayant souvent un impact sur le client, vous allez également mettre à jour votre application Windows Store pour tirer profit de ces nouveaux comportements.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="label">regarder le didacticiel</a> <a style="background-image: url('/media/devcenter/mobile/videos/validate-data-windows-store-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Validate-and-Modify-Data-with-Server-Scripts-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Lire la vidéo</span></a> <span class="time">09:54:00</span></div>
</div>

Ce didacticiel vous familiarise avec ces étapes de base :

1. [Ajout de la validation de longueur de chaîne]
2. [Mise à jour du client pour la prise en charge de la validation]
3. [Ajout d'un horodatage lors d'une insertion]
4. [Mise à jour du client pour l'affichage de l'horodatage]

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer, vous devez suivre le didacticiel [Prise en main des données].  

## <a name="string-length-validation"></a>Ajout de la validation

Il est toujours souhaitable de valider la longueur des données soumises par les utilisateurs. Vous devez d'abord inscrire un script qui valide la longueur des données de chaîne envoyées au service mobile et refuse les chaînes trop longues, en l'occurrence, celles qui font plus de 10 caractères.

1. Connectez-vous au  [portail de gestion Azure], cliquez sur **Mobile Services**, puis sur votre application. 

   	![][0]

2. Cliquez sur l'onglet **Données**, puis sur la table **TodoItem**.

   	![][1]

3. Cliquez sur **Script**, puis sélectionnez l'opération **Insert**.

   	![][2]

4. Remplacez le script existant par la fonction suivante, puis cliquez sur **Enregistrer**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                request.execute();
            }
        }

    Ce script vérifie la longueur de la propriété **TodoItem.text** et envoie une réponse indiquant une erreur lorsque la chaîne dépasse 10 caractères. Sinon, la méthode **execute**  est appelée pour effectuer l'insertion.

    <div class="dev-callout"> 
	<b>Remarque</b> 
	<p>Vous pouvez supprimer un script inscrit dans l'onglet <strong>Script</strong>  en cliquant sur <strong>Effacer</strong>, puis sur <strong>Enregistrer</strong>.</p></div>

## <a name="update-client-validation"></a>Mise à jour du client

Maintenant que le service mobile valide les données et envoie des réponses d'erreur, vous devez mettre à jour l'application afin qu'elle traite les réponses d'erreur de la validation.

1. Dans Visual Studio 2012 Express pour Windows 8, ouvrez le projet que vous avez modifié en suivant le didacticiel [Prise en main des données].

2. Appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte dépassant 10 caractères dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

   	Notez que l'application génère une exception **MobileServiceInvalidOperationException** non prise en charge suite à la réponse 400 (Requête incorrecte) renvoyée par le service mobile.	

6. 	Ouvrez le fichier MainPage.xaml.cs, puis ajoutez l'instruction **using**  suivante :

        using Windows.UI.Popups;

7. Remplacez la méthode **InsertTodoItem** existante par le code suivant :

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
                MessageDialog errormsg = new MessageDialog(e.Message, 
                    string.Format("{0} (HTTP {1})",                     
                    e.Response.ReasonPhrase,
                    (int)e.Response.StatusCode));
                var ignoreAsyncOpResult = errormsg.ShowAsync();
            }
        }

   	Cette version de la méthode inclut la gestion d'erreurs pour l'exception **MobileServiceInvalidOperationException** qui affiche la réponse indiquant une erreur dans une fenêtre contextuelle.

## <a name="add-timestamp"></a>Ajout d'un horodatage

Les tâches précédentes ont permis de valider une insertion, qui est acceptée ou rejetée. Maintenant, vous allez mettre à jour les données insérées en utilisant un script serveur qui ajoute une propriété d'horodatage à l'objet avant son insertion.


<div class="dev-callout"><b>Remarque</b>
<p>La propriété d'horodatage <b>createdAt</b> illustrée ici est désormais redondante. Mobile Services crée automatiquement une propriété système <b>__createdAt</b> pour chaque table. Vous pouvez utiliser cette propriété système dans votre application en ajoutant simplement le membre suivant à la classe TodoItem :</p>
<pre><code>
[JsonProperty(PropertyName = "__createdAt")]
public DateTime createdAt { set; get; }
</code></pre>
</div>


1. Sous l'onglet **Scripts** du [portail de gestion], remplacez le script **Insert** actuel par la fonction suivante, puis cliquez sur **Enregistrer**.

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
	<p>Le schéma dynamique doit être activé la première fois que ce script d'insertion s'exécute. Avec le schéma dynamique activé, Mobile Services ajoute automatiquement la colonne <strong>createdAt</strong> à la table <strong>TodoItem</strong> lors de la première exécution. Par défaut, le schéma dynamique est activé pour un nouveau service mobile. Il doit être désactivé avant que l'application soit publiée sur le Windows Store.</p>
    </div>

2. Dans Visual Studio, appuyez sur la touche **F5** pour exécuter l'application, puis tapez une chaîne de texte de moins de 10 caractères dans **Insert a TodoItem** et cliquez sur **Enregistrer**.

   	Notez que le nouvel horodatage n'apparaît pas dans l'interface utilisateur de l'application.

3. De retour dans le portail de gestion, cliquez sur l'onglet **Parcourir** dans la table **TodoItem**.
   
   	Notez qu'une colonne **createdAt** apparaît désormais à l'écran et qu'une valeur d'horodatage est associée au nouvel élément inséré.
  
L'application Windows Store doit ensuite être mise à jour pour afficher cette nouvelle colonne.

## <a name="update-client-timestamp"></a>Nouvelle mise à jour du client

Le client Mobile Services ignore les données d'une réponse qu'elle ne peut pas sérialiser en propriétés de type défini. L'étape finale consiste à mettre à jour le client de manière à afficher ces nouvelles données.

1. Dans Visual Studio, ouvrez le fichier MainPage.xaml.cs, puis remplacez la classe **TodoItem** existante par la définition suivante :

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
	<p>L'annotation  <code>DataMemberAttribute</code> indique au client de mapper la nouvelle propriété <code>CreatedAt</code> de l'application sur la colonne <code>createdAt</code> définie dans la tableTodoItem, qui porte un autre nom. En utilisant cet attribut, votre application peut avoir des noms de propriétés pour les objets qui diffèrent des noms de colonnes dans la base de données SQL. Sans cet attribut, une erreur peut se produire en raison de la différence de casse.</p>
    </div>

5. Ajoutez l'élément XAML suivant juste en dessous de l'élément **CheckBoxComplete** dans le fichier MainPage.xaml :
	      
        <TextBlock Name="WhenCreated" Text="{Binding CreatedAt}" VerticalAlignment="Center"/>

   	La nouvelle propriété **CreatedAt** s'affiche dans une zone de texte. 
	
6. Appuyez sur la touche **F5** pour exécuter l'application. 

   	Notez que l'horodatage ne s'affiche que pour les éléments insérés une fois que le script d'insertion a été mis à jour.

7. Remplacez la méthode **RefreshTodoItems** existante par le code suivant :

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
	
8. Appuyez sur la touche **F5** pour exécuter l'application.

   	Notez que tous les éléments créés sans valeur d'horodatage disparaissent de l'interface utilisateur.

Vous avez maintenant terminé ce didacticiel.

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez terminé ce didacticiel, pensez à suivre le dernier didacticiel dans la série des données : [Affinage des requêtes au moyen de la pagination].

Les scripts serveur sont également utilisés dans le cadre du processus d'autorisation des utilisateurs et pour l'envoi de notifications Push. Pour plus d'informations, reportez-vous aux didacticiels suivants :

* [Autorisation des utilisateurs avec des scripts]
  <br/>En savoir plus sur le filtrage des données en fonction de l'ID d'un utilisateur authentifié.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Référence de script serveur Mobile Services]
  <br/>En savoir plus sur l'enregistrement et l'utilisation des scripts serveur.

* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET.

<!-- Anchors. -->
[Ajout de la validation de longueur de chaîne]: #string-length-validation
[Mise à jour du client pour la prise en charge de la validation]: #update-client-validation
[Ajout d'un horodatage lors d'une insertion]: #add-timestamp
[Mise à jour du client pour l'affichage de l'horodatage]: #update-client-timestamp
[Étapes suivantes]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-windows-store-dotnet-validate-modify-data-server-scripts/mobile-insert-script-users.png


<!-- URLs. -->
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
[Prise en main de Mobile Services]: /fr-fr/develop/mobile/tutorials/get-started/#create-new-service
[Autorisation des utilisateurs avec des scripts]: /fr-fr/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[Affinage des requêtes à la pagination.]: /fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet
[Prise en main des données]: /fr-fr/develop/mobile/tutorials/get-started-with-data-dotnet
[Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
[Prise en main des notifications Push]: /fr-fr/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript et HTML]: /fr-fr/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Portail de gestion]: https://manage.windowsazure.com/
[Portail de gestion Azure]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library
