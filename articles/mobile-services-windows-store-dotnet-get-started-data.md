<properties linkid="develop-mobile-tutorials-get-started-with-data-dotnet-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Prise en main des données dans Mobile Services
==============================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

[.NET](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/ ".NET") | [JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/ "JavaScript")

Cette rubrique montre comment utiliser Azure Mobile Services pour utiliser les données dans une application Windows Store. Dans ce didacticiel, vous allez télécharger un projet Visual Studio 2013 pour une application qui stocke les données en mémoire, créer un service mobile, intégrer le service mobile avec l'application, puis vous connecter au portail de gestion Azure pour afficher les modifications effectuées sur les données lors de l'exécution de l'application.

**Remarque**

Ce didacticiel requiert Visual Studio 2013, ce qui facilite la connexion de votre application Windows Store à Mobile Services. Pour effectuer la même procédure de base à l'aide de Visual Studio 2012, suivez la procédure de la rubrique [Prise en main des données dans Mobile Services à l'aide de Visual Studio 2012](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet-vs2012/).

Ce didacticiel vous familiarise avec ces étapes de base :

1.  [Téléchargement d'un projet d'application Windows Store](#download-app)
2.  [Création du service mobile à partir de Visual Studio](#create-service)
3.  [Ajout d'une table de données pour le stockage et mise à jour de l'application](#add-table)
4.  [Test de l'application avec Mobile Services](#test-app)

**Remarque**

Pour effectuer ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://www.windowsazure.com/fr-fr/pricing/free-trial/?WT.mc_id=AE564AB28).

Téléchargement du projetTéléchargement du projet GetStartedWithData
-------------------------------------------------------------------

Ce didacticiel est basé sur l'[application GetStartedWithMobileServices](http://go.microsoft.com/fwlink/p/?LinkId=328660), qui est un projet d'application Windows Store dans Visual Studio 2013. L'interface utilisateur de cette application est identique à l'application générée par le démarrage rapide de Mobile Services, sauf que les éléments ajoutés sont stockés en local dans la mémoire.

1.  Téléchargez la version C\# de l'exemple d'application GetStartedWithMobileServices sur le [site d'exemples de code développeur](http://go.microsoft.com/fwlink/p/?LinkId=328660).

	![][10]

2.  Dans Visual Studio 2013, ouvrez le projet téléchargé et examinez le fichier MainPage.xaml.cs.

	Notez que les objets **TodoItem** ajoutés sont stockés dans un **ObservableCollection<TodoItem>** en mémoire.

3.  Appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.

4.  Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

	![][0]  

	Notez que le texte enregistré est affiché dans la deuxième colonne sous **Query and update data**.

Création du service mobileCréation d'un service mobile à partir de Visual Studio
--------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-vs2013](../includes/mobile-services-create-new-service-vs2013.md)]

1.  Dans l'Explorateur de solutions, ouvrez le fichier de code App.xaml.cs et notez que le nouveau champ static a été ajouté à la classe \*\*App\*\*, comme dans l'exemple suivant :

		public static Microsoft.WindowsAzure.MobileServices.MobileServiceClient 
					todolistClient = new Microsoft.WindowsAzure.MobileServices.MobileServiceClient(
		        		"https://todolist.azure-mobile.net/",
		        		"XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");
		
    Ce code fournit un accès à votre nouveau service mobile dans votre application à l'aide d'une instance de la [classe MobileServiceClient](http://go.microsoft.com/fwlink/p/?LinkId=302030). Le client est créé en fournissant l'URI et la clé de l'application du nouveau service mobile. Ce champ static est disponible pour toutes les pages de votre application.

Ajout d'une nouvelle tableAjout d'une nouvelle table au service mobile et mise à jour de l'application
------------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-table-vs2013](../includes/mobile-services-create-new-table-vs2013.md)]

1.  Dans le fichier MainPage.xaml.cs, ajoutez les instructions using suivantes ou supprimez leurs marques de commentaire :

         using Microsoft.WindowsAzure.MobileServices;
         using Newtonsoft.Json;

2.  Dans ce même fichier, remplacez la définition de classe TodoItem par le code suivant :

         public class TodoItem
         {
             public string Id { get; set; }
            
             [JsonProperty(PropertyName = "text")]
             public string Text { get; set; }
            
             [JsonProperty(PropertyName = "complete")] 
             public bool Complete { get; set; }
         }

    Le **JsonPropertyAttribute** est utilisé pour définir le mappage entre les noms des propriétés dans le type de client et les noms des colonnes dans la table de données sous-jacente.

3.  Mettez en commentaire la ligne définissant la collection des éléments existants, puis annulez les marques de commentaire ou ajoutez les lignes de commande suivantes et remplacez *<yourClient\>* par le champ `MobileServiceClient` ajouté dans le fichier App.xaml.cs lorsque vous avez connecté votre projet au service mobile :

         private MobileServiceCollection<TodoItem data-morhtml="true", TodoItem> items;
         private IMobileServiceTable<TodoItem> todoTable = 
             App.<yourClient>.GetTable<TodoItem>();

    Ce code crée une collection de liaisons (éléments) prenant en charge des services mobiles et une classe proxy pour la table de la base de données (todoTable).

4.  Dans la méthode **InsertTodoItem**, supprimez la ligne de code définissant la propriété **TodoItem.Id**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire dans la ligne de code suivante :

         await todoTable.InsertAsync(todoItem);

    Ce code permet d'insérer un nouvel élément dans la table.

    **Remarque**

    Des tables sont créées avec les colonnes Id, \_\_createdAt, \_\_updatedAt et \_\_version. Lorsqu'un schéma dynamique est activé, Mobile Services génère automatiquement de nouvelles colonnes basées sur l'objet JSON dans la requête d'insertion ou de mise à jour. Pour plus d'informations, consultez la page [Schéma dynamique](http://msdn.microsoft.com/fr-fr/library/windowsazure/jj193175.aspx).

5.  Dans la méthode **RefreshTodoItems**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

         items = await todoTable.ToCollectionAsync();

    Cela définit la liaison sur la collection d'éléments dans `todoTable`, qui contient tous les objets **TodoItem** renvoyés depuis le service mobile.

6.  Dans la méthode **UpdateCheckedTodoItem**, ajoutez le modificateur **async** à la méthode, puis annulez les marques de commentaire sur la ligne de code suivante :

         await todoTable.UpdateAsync(item);

    Cela permet d'envoyer une mise à jour de l'élément au service mobile.

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, le moment est venu de tester l'application avec Mobile Services.

Test de l'applicationTest de l'application avec votre nouveau service mobile
----------------------------------------------------------------------------

1.  Dans Visual Studio, appuyez sur la touche F5 pour exécuter l'application.

2.  Comme auparavant, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.

	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

3.  Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Mobile Services**, puis sur le service mobile.

4.  Cliquez sur l'onglet **Données**, puis sur **Parcourir**.

	![][9]
          
	Notez que la table **TodoItem** contient désormais des données, avec des valeurs d'ID générées par Mobile Services et que des colonnes ont été automatiquement ajoutées à la table pour correspondre à la classe TodoItem de l'application.

5.  Dans l'application, cochez un des éléments dans la liste, puis revenez à l'onglet Parcourir dans le portail et cliquez sur **Actualiser**.

	Notez que la valeur terminée est passée de **false** à **true**.

6.  Dans le fichier projet MainPage.xaml.cs, remplacez la méthode **RefreshTodoItems** existante par le code suivant qui filtre les éléments terminés :

         private async void RefreshTodoItems()
         {                       
             // Cette requête filtre les TodoItems terminés. 
             items = await todoTable
                .Where(todoItem => todoItem.Complete == false)
                .ToCollectionAsync();

             ListItems.ItemsSource = items;            
         }

7.  Dans l'application, cochez un autre élément dans la liste, puis cliquez sur le bouton **Actualiser**.

	Notez que l'élément coché n'est plus affiché dans la liste. Chaque actualisation provoque un aller-retour vers le serveur mobile, qui renvoie les données filtrées.

Cela conclut le didacticiel **Prise en main des données**.

Étapes suivantes
----------------

Ce didacticiel a présenté les bases de l'activation d'une application Windows Store pour utiliser les données dans Mobile Services. Ensuite, pensez à suivre l'un des didacticiels suivants basés sur l'application GetStartedWithData que vous avez créée dans ce didacticiel :

-   [Validation et modification des données avec des scripts](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    En savoir plus sur l'utilisation des scripts serveur dans Mobile Services pour valider et modifier les données envoyées à partir de votre application.

-   [Affinage des requêtes à la pagination](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    En savoir plus sur l'utilisation de la pagination dans les requêtes pour contrôler la quantité de données traitées dans une seule requête.

Une fois que vous avez terminé les séries de données, essayez l'un de ces autres didacticiels :

-   [Prise en main de l'authentification](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    En savoir plus sur l'authentification des utilisateurs de votre application.

-   [Prise en main des notifications Push](../mobile-services-windows-store-dotnet-get-started-push/)
    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)
    En savoir plus sur l'utilisation de Mobile Services avec .NET.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Create the mobile service from Visual Studio]: #create-service
[Add a data table for storage and update the app]: #add-table
[Update the app to use Mobile Services]: #update-app
[Test the app against Mobile Services]: #test-app
[Next Steps]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-quickstart-startup.png








[9]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-data-sample-download-dotnet-vs12.png


<!-- URLs. -->
[Validate and modify data with scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Refine queries with paging]: /en-us/develop/mobile/tutorials/add-paging-to-data-dotnet
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: ../mobile-services-windows-store-dotnet-get-started-push/
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-data-js

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services .NET How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
